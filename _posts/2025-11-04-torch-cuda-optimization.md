---
title: "Optimizing PyTorch CUDA performance: a 90% speedup journey"
---

<!-- # Optimizing PyTorch CUDA performance: a 90% speedup journey -->

In this blog post, I will talk about how I achieved a 90% speedup in PyTorch CUDA training through a case study of a research project called [STGym](https://github.com/xiaohan2012/stgym).


## Table of contents

* TOC
{:toc}

## STGym overview

[STGym](https://github.com/xiaohan2012/stgym) is a machine learning framework for spatial transcriptomics analysis using graph neural networks.
It is designed to enable efficient experimentation with different GNN architectures and datasets. 
The repository's main feature is its ability to systematically explore various combinations of:

- **GNN architectures**: Different message passing layers, pooling strategies, and readout functions
- **Spatial transcriptomics datasets**: Multiple real-world biological datasets for comprehensive evaluation

## Problem description

An unexpected performance issue emerged during development: **running the code on GPU was actually taking longer than on an A30 CPU**. 

| Compute environment         | Time per epoch | Total training time |
|-----------------------------|----------------|---------------------|
| CPU (MacBook Pro M2 chips ) | 10s            | 24s                 |
| CUDA (A30)                  | 19s            | 60s                 |

What is going on here? Isn't computation on GPU much faster than on CPU?

## Problem solving approach

To  address this issue, I employed the followiing strategy:

1. **Ask LLM**: Asked Claude Code for optimization guidance and best practices based on the code repo
2. **Documentation review**: Read the [PyTorch performance tuning guide](https://docs.pytorch.org/tutorials/recipes/recipes/tuning_guide.html#gpu-specific-optimizations)
3. **Profiler analysis**: Used PyTorch's built-in profiler to identify specific bottlenecks

## Benchmark setup

The running time benchmark used the following configuration:

- **Dataset**: [breast-cancer](https://github.com/xiaohan2012/stgym/blob/main/stgym/data_loader/breast_cancer.py) (spatial transcriptomics data)
- **Batch size**: 16
- **Training epochs**: 2 (sufficient for profiling and timing comparisons)
- **Model architecture**: [GIN convolution](https://pytorch-geometric.readthedocs.io/en/latest/generated/torch_geometric.nn.conv.GINConv.html) with [MinCut pooling](https://github.com/xiaohan2012/stgym/blob/2afb35d999445f2eb3839475af09e15f88e4f99a/stgym/pooling/mincut.py#L128) (20 clusters)
- **Hardware**: CUDA (A30)

The benchmark configuration can be found in [`conf/adhoc/benchmark.yaml`](https://github.com/xiaohan2012/stgym/blob/main/conf/adhoc/benchmark.yaml) and executed via [`scripts/benchmark.sh`](https://github.com/xiaohan2012/stgym/blob/main/scripts/benchmark.sh).

## A step-by-step optimization journey

### 1. Delayed `.cpu()` calls - reducing data transfers

**Problem identification**: The code was calling `.cpu()` too early in the computation pipeline, forcing unnecessary GPU-to-CPU data transfers during training.

**Root cause**: Tensors were being moved to CPU immediately after GPU computation, even when they might be used in subsequent GPU operations.

**Solution**: Move `.cpu()` calls to the latest possible point when CPU computation is actually required.

**Code changes** ([Git commit: 54afd04](https://github.com/xiaohan2012/stgym/commit/54afd04df8eddc9124f4dd1007243592d544f43b)):

```python
# Context: the following code is used for evaluation

# Before: Early CPU transfer
true = torch.cat([output["true"].cpu() for output in outputs])
pred = torch.cat([output["pred_score"].cpu() for output in outputs])

# After: Delayed CPU transfer
true = torch.cat([output["true"] for output in outputs])
pred = torch.cat([output["pred_score"] for output in outputs])
# ... later when CPU computation is actually needed:
roc_auc = roc_auc_score(true.cpu(), pred.cpu())
```

**Performance impact**:

| Metric                      | Before | After | Improvement    |
|-----------------------------|--------|-------|----------------|
| **Training time per epoch** | 21s    | 19s   | 9.5% reduction |
| **Self CPU time**           | 54.5s  | 47.1s | 7s reduction   |
| **Self CUDA time**          | 10.9s  | 10.9s | Minimal change |

**Key insight**: Minimize GPU-CPU data transfers by keeping tensors on GPU as long as possible.

> **Related files**: [`stgym/tl_model.py`](https://github.com/xiaohan2012/stgym/blob/main/stgym/tl_model.py)

### 2. Eliminating redundant `to(device)` calls - the game changer 🤩

Before describing the solution, let's first look at the PyTorch profiler output and reveal the major bottleneck.


**Understanding PyTorch profiler output**

PyTorch's profiler and the torch-lightening wrapper provide detailed timing information. To set it up:

```python
from pytorch_lightning.profilers import PyTorchProfiler
# Create the profiler
profiler = PyTorchProfiler(sort_by_key='cuda_time')

# Enable profiler in PyTorch Lightning in your trainer configuration
trainer = pl.Trainer(
    profiler=PyTorchProfiler(sort_by_key='cuda_time'),
    # ... other config
)
```

The full profiler output for the current code version:

```
-------------------------------------------------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------
                                                   Name    Self CPU %      Self CPU   CPU total %     CPU total  CPU time avg     Self CUDA   Self CUDA %    CUDA total  CUDA time avg    # of Calls
-------------------------------------------------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------
                                          ProfilerStep*         0.00%       0.000us         0.00%       0.000us       0.000us       47.453s       416.90%       47.453s        2.791s            17
[pl][module]stgym.pooling.mincut.MincutPoolingLayer:...         0.00%       0.000us         0.00%       0.000us       0.000us       40.769s       358.18%       40.769s        2.146s            19
                        [pl][profile]run_training_epoch         0.00%       0.000us         0.00%       0.000us       0.000us       27.991s       245.92%       27.991s       13.995s             2
                        [pl][profile]run_training_epoch       -45.41%  -25112927.636us        79.10%       43.743s       21.871s       0.000us         0.00%       10.078s        5.039s             2
        [pl][module]stgym.model.STNodeClassifier: model         0.01%       3.602ms        74.03%       40.941s        2.155s       0.000us         0.00%        9.834s     517.562ms            19
[pl][module]stgym.layers.GeneralMultiLayer: model.mp...         0.00%       1.253ms        73.94%       40.887s        2.152s       0.000us         0.00%        9.818s     516.761ms            19
[pl][module]stgym.layers.GeneralLayer: model.mp_modu...         0.01%       2.807ms        73.93%       40.886s        2.152s       0.000us         0.00%        9.818s     516.761ms            19
[pl][module]stgym.pooling.mincut.MincutPoolingLayer:...         5.38%        2.978s        73.88%       40.855s        2.150s       0.000us         0.00%        9.717s     511.431ms            19
                        [pl][profile]run_training_batch         0.01%       3.429ms        57.92%       32.030s        2.288s       0.000us         0.00%        8.113s     579.472ms            14
[pl][profile][LightningModule]STGymModule.optimizer_...         0.00%       1.796ms        57.91%       32.026s        2.288s       0.000us         0.00%        8.113s     579.472ms            14
                               Optimizer.step#Adam.step         2.36%        1.303s        57.91%       32.024s        2.287s       0.000us         0.00%        8.113s     579.472ms            14
[pl][profile][Strategy]SingleDeviceStrategy.training...         0.03%      18.516ms        55.55%       30.721s        2.194s       0.000us         0.00%        8.113s     579.472ms            14
                                            aten::copy_        39.12%       21.634s        52.86%       29.230s       3.507ms        7.001s        61.51%        7.001s     839.990us          8335
                                               aten::to         0.01%       5.650ms        13.91%        7.692s       1.798ms       0.000us         0.00%        6.805s       1.590ms          4279
                                         aten::_to_copy         0.11%      61.144ms        13.90%        7.686s       4.318ms       0.000us         0.00%        6.805s       3.823ms          1780
                       Memcpy HtoD (Pageable -> Device)         0.00%       0.000us         0.00%       0.000us       0.000us        6.541s        57.47%        6.541s       5.314ms          1231
                                               aten::mm         0.06%      31.101ms         9.83%        5.438s       9.094ms      37.332ms         0.33%        3.819s       6.387ms           598
[pl][profile][LightningModule]STGymModule.on_validat...         0.00%       0.000us         0.00%       0.000us       0.000us        3.694s        32.45%        3.694s        1.231s             3
                                           aten::matmul         0.00%       1.338ms         8.32%        4.602s      15.138ms       0.000us         0.00%        3.059s      10.064ms           304
                            aten::_sparse_sparse_matmul         0.20%     110.674ms         8.36%        4.625s      15.520ms        2.346s        20.61%        2.975s       9.984ms           298
-------------------------------------------------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------  ------------
Self CPU time total: 55.300s
Self CUDA time total: 11.382s
```

Here's how to interpret the key metrics/columns:

- **Self CPU/CUDA**: Time spent directly in that operation (excluding subcalls)
- **Total CPU/CUDA**: Time including all nested operations  
- **Self CPU %**: Percentage of total profiling time spent in this operation

**Bottleneck identification**

From the above, the profiler revealed that `aten::copy_` and `Memcpy HtoD (Pageable -> Device)` were consuming disproportionate time:

```
Name                                               Self CPU    Self CUDA
aten::copy_                                        21.634s     7.001s  
Memcpy HtoD (Pageable -> Device)                   0.000us     6.541s
```

**What these operations do**:

- `aten::copy_`: PyTorch's internal tensor copying operation, [torch.Tensor.copy_](https://docs.pytorch.org/docs/stable/generated/torch.Tensor.copy_.html), which may copy a tensor from one device (e.g., CPU) to another (e.g., GPU)
- `Memcpy HtoD`: CUDA memory copy from Host to Device (CPU to GPU)

These indicated excessive data movement between CPU and GPU memory.

**Root cause and addressing the bottle neck**

Functions were explicitly moving tensors to devices even when tensors were already on the correct device.

**Code changes** ([Git commit: ac134c6](https://github.com/xiaohan2012/stgym/commit/ac134c609867db35c8217cbe162a29cacd49d27a)):

```python
# Before: explicit device placement
# Remark: the statements below all create tensors on CPU, then copies it to GPU
# Which is unnecessary since all operations are supposed to happen on GPU
ind0 = torch.arange(n).repeat(k, 1).T.to(device)
ind1 = torch.arange(k).repeat(n, 1).to(device)
d_diag = torch.sparse_coo_tensor(
    torch.stack([range_n_sum, range_n_sum]).to(device),
    d,
    requires_grad=False,
)

# After: Inherit device from input tensors
ind0 = torch.arange(n, device=device).repeat(k, 1).T
ind1 = torch.arange(k, device=device).repeat(n, 1)
d_diag = torch.sparse_coo_tensor(
    torch.stack([range_n_sum, range_n_sum]),
    d,
    requires_grad=False,
)
```

**Performance impact**:

| Metric                      | Before | After | Improvement        |
|-----------------------------|--------|-------|--------------------|
| **Training time per epoch** | 20s    | 2s    | **90% reduction!** |
| **Self CPU time**           | 50.3s  | 7.0s  | 86% reduction      |
| **Self CUDA time**          | 11.9s  | 3.9s  | 67% reduction      |

**Key insight**: This was the most impactful optimization. Redundant device transfers create massive overhead, especially for sparse tensors with complex internal structures.

> **Related files**: [`stgym/utils.py`](https://github.com/xiaohan2012/stgym/blob/main/stgym/utils.py)

### 3. Avoiding unnecessary synchronization

Before proceeding, it is useful to discuss the asynchronous execution property of torch CUDA


**Understanding CUDA asynchronous execution**

CUDA operations are asynchronous by default, meaning the CPU can continue executing while GPU computes:

```python
import torch
import time

x = torch.randn(20000, 20000).cuda()
y = torch.randn(20000, 20000).cuda()

# Test asynchronous behavior
start = time.time()
z = x @ y  # Returns immediately if async
pre_sync_time = time.time() - start
torch.cuda.synchronize()  # Wait for GPU completion
total_time = time.time() - start

print(f"Time before sync: {pre_sync_time:.4f}s")  # ~0.08s
print(f"Time after sync: {total_time:.4f}s")      # ~1.8s
```

**`.item()` forces synchronization**

**Problem**: Using `.item()` forces synchronization between GPU and CPU, blocking asynchronous execution.

**Code changes** ([Git commit: 1398257](https://github.com/xiaohan2012/stgym/commit/139825795e2d13d222d5c4dee6bbf4b0667a1545)):

```python
# Before: Forces GPU-CPU synchronization
sqrt_K = torch.sqrt(torch.tensor(K, device=device, dtype=torch.float))
torch.full((K * B,), (1 / sqrt_K).item(), device=device, dtype=torch.float)

# After: Keep computation on GPU
sqrt_K = torch.sqrt(torch.tensor(K, device=device, dtype=torch.float))
torch.full((K * B,), 1.0, device=device) / sqrt_K  # No .item() call
```

**Performance impact**:

| Metric              | Before | After | Improvement     |
|---------------------|--------|-------|-----------------|
| **Self CPU time**   | 5.6s   | 5.6s  | Marginal change |
| **Self CUDA time**  | 3.0s   | 3.0s  | Minimal change  |

**Key insight**: Even small synchronization points can impact performance. Avoid `.item()`, unnecessary `.cpu()` calls, and print statements in training loops.


## What was tried that didn't work well

**1. `torch.autograd.set_detect_anomaly(False)`**

**What it does**: Disables gradient anomaly detection, which adds computational overhead for debugging purposes.

**Result**: No significant performance improvement. Not sure why.

**2. `set_float32_matmul_precision('medium')`**

**What it does**: Reduces precision of matrix multiplication operations to potentially speed up computation at the cost of numerical accuracy.

**Result**: No significant improvement for this workload. The precision-performance tradeoff didn't provide meaningful benefits in our benchmark setup.

**3. Increasing `num_workers > 1`**

**What it does**: Uses multiple CPU processes for data loading to parallelize data preprocessing and reduce I/O bottlenecks.

**Configuration tested**:
```yaml
data_loader:
  num_workers: 5  # Increased from 0
```

**Result**: Performance degraded significantly (CPU time increased to 32s). 

**Why it failed**: For this workload, the overhead of inter-process communication exceeded the benefits of parallel data loading. The dataset was small enough that single-process loading was more efficient.

**4. Setting `inplace=True` for activation functions**

**What it does**: Modifies tensors in-place rather than creating new tensors, potentially saving memory and reducing allocation overhead.

**Code example**:
```python
# Before
x = F.selu(x)

# After  
x = F.selu(x, inplace=True)
```

**Result**: No major performance improvement. Not sure why. 

## Summary

**Performance results**

The optimization journey delivered remarkable improvements:

| Metric                      | Before | After | Improvement       |
|-----------------------------|--------|-------|-------------------|
| **Training time per epoch** | 21s    | 2s    | **90% reduction** |
| **CPU time**                | 54.5s  | 5.6s  | **90% reduction** |
| **CUDA time**               | 10.9s  | 3.0s  | **73% reduction** |

**Useful optimization principles**

From this journey, I have learned the following useful optimization principles:

1. **Device transfer optimization**: The biggest gains came from eliminating unnecessary GPU-CPU transfers and redundant device placements.

2. **Profiler-driven development**: PyTorch's profiler is a very convenient tool for identifying bottlenecks, but understanding how to interpret the output is crucial.

3. **Asynchronous execution**: Leveraging CUDA's asynchronous nature by avoiding synchronization points yields significant performance improvements.

