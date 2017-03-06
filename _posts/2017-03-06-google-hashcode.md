---
title: Google Hashcode 2017 -- Streaming Videos
desc: My participation experience.
---

## What is Google Hashcode?

In short, a programming competition that focuses on *one* *real-life engineering* problem. [Official description](https://hashcode.withgoogle.com/).

This is my first participation of this event :)

## Task: Streaming Videos

Quoting from the [problem statement](https://hashcode.withgoogle.com/2017/tasks/hashcode2017_qualification_task.pdf), 
the general task is:

Given a description of cache servers,
network endpoints and videos, along with predicted requests for individual videos, decide **which videos to put in which cache server** in order to minimize the average waiting time (latency) for all requests.


## My solution: greedy approach

The algorithm does the following: 

1. at each iteration, it *greediy* selects the `(video, cache server)` pair that brings in the *maximum latency improvement* among all candidate pairs.
2. After each selection, the latency improvement for the rest candidate pairs are updated.
3. The loop continues unless cache servers have no more capacity

## Utility data structure: priority heap

Line 1 of the previous section requires getting the pair with **maximum** improvement among all candidate pairs. 


Initially, I actually **sorted** (OMG!) all the pairs at each iteration.
This turns out to be really slow (the competition would finish before the program finishes!).
So I reflected upon this "sorting" idea, which led me to using heap.

However, the build-in implementation [heapq](https://docs.python.org/2/library/heapq.html) does not perform efficient priority key update. 
So I turned out implementing my own based on [Introduction to Algorithms, Third Edition](https://mitpress.mit.edu/books/introduction-algorithms): 

{% highlight python %}
import math
from copy import copy

def build_heap(keys, es=None):
    if es is None:
        es = copy(keys)
    hp = heap(keys, es)
    for i in range(int(math.ceil(hp.size/2)), -1, -1):
        hp.max_heapify(i)

    return hp

class heap():
    def __init__(self, keys, es):
        self.size = len(keys)
        self.keys = keys
        self.es = es
        self.e2i = {e: i for i, e in enumerate(es)}

    def li(self, i):
        return 2*i

    def ri(self, i):
        return 2*i+1
    
    def parent(self, i):
        return int(i/2)

    def peep_max(self):
        return self.keys[0], self.es[0]
    
    def max_heapify(self, i):
        val = self.keys[i]
        largest = i
        li, ri = self.li(i), self.ri(i)
        if li < self.size and self.keys[li] > val:
            largest = li
        if ri < self.size and self.keys[ri] > self.keys[largest]:
            largest = ri
            
        if largest != i:
            self.e2i[self.es[largest]], self.e2i[self.es[i]] = i, largest
            self.keys[i], self.keys[largest] = self.keys[largest], self.keys[i]
            self.es[i], self.es[largest] = self.es[largest], self.es[i]
            self.max_heapify(largest)

    def pop_max(self):
        largest = self.keys[0]
        largest_e = self.es[0]
        del self.e2i[largest_e]
        
        self.keys[0] = self.keys[self.size-1]
        self.es[0] = self.es[self.size-1]
        self.size -= 1
        
        self.keys.pop()
        self.es.pop()

        if self.size > 0:
            self.max_heapify(0)

        return largest, largest_e

    def move_up(self, i):
        while True:
            p = self.parent(i)
            if self.keys[i] > self.keys[p]:
                self.e2i[self.es[i]], self.e2i[self.es[p]] = p, i
                self.keys[i], self.keys[p] = self.keys[p], self.keys[i]
                self.es[i], self.es[p] = self.es[p], self.es[i]
                i = p
            else:
                break
        
    def insert(self, key, e):
        self.size += 1
        self.keys.append(key)
        self.es.append(e)
        i = self.size - 1
        self.e2i[e] = i
        self.move_up(i)
        
    def increase_key(self, e, k):
        i = self.e2i[e]
        self.keys[i] = k
        self.move_up(i)

    def decrease_key(self, e, k):
        i = self.e2i[e]
        self.keys[i] = k
        self.max_heapify(i)

    def update_key(self, e, k):
        i = self.e2i[e]
        old_k = self.keys[i]
        if old_k < k:
            self.increase_key(e, k)
        elif old_k > k:
            self.decrease_key(e, k)
{% endhighlight %}

## My solution: code

### Parsing input + preprocessing 

{% highlight python %}

from collections import defaultdict
from tqdm import tqdm
from heap import build_heap


def split2int(s):
    return list(map(int, s.split()))

data = 'videos_worth_spreading.in'

outpath = 'outputs/{}.out'.format(data.split('.')[0])
reqs = {}
latency = {}
v2reqs = defaultdict(list)
c2ends = defaultdict(set)

CENTER = -1
with open('data/{}'.format(data), 'r') as f:
    V, E, R, C, X = split2int(f.readline())
    v2size = {i: int(size)
              for i, size in enumerate(f.readline().strip().split())}
    for endpoint_i in range(E):
        latency2center, n_caches = split2int(f.readline())
        latency[(endpoint_i, CENTER)] = latency2center

        for _ in range(n_caches):
            cache_i, l = split2int(f.readline())
            latency[(endpoint_i, cache_i)] = l
            c2ends[cache_i].add(endpoint_i)
        endpoint_i += 1
        
    for l in f:
        vid, eid, n = split2int(l)
        v2reqs[vid].append(Req(vid, eid, n))
        

vc2reqs = {}  # video, cache to list of requests
for v, reqs in tqdm(v2reqs.items()):
    for c in range(C):
        vc2reqs[(v, c)] = set([r for r in reqs if r.e in c2ends[c]])


cv_pairs = []
imps = []

for c in tqdm(range(C)):
    for v in range(V):
        if (v, c) in vc2reqs:
            cv_pairs.append((c, v))
            imps.append(sum(r.n * (latency[(r.e, CENTER)] - latency[(r.e, c)])
                            for r in vc2reqs[(v, c)]))

cv_heap = build_heap(imps, cv_pairs)  # the important (cache, video) heap

{% endhighlight%}


### Main algorithm

{% highlight python %}

def main(vc2reqs, v2size, latency, cv_heap, debug=False):

    caps = {i: X for i in range(C)}
    c2v = defaultdict(set)
    req2c = {}  # which cache serves *the* request
    
    while True:
        if cv_heap.size % 100 == 0:
            print(cv_heap.size)
            print('caps remaining: {}'.format(sum(caps.values()) / (X * C)))
        success = False
        while cv_heap.size > 0:
            imp, (cb, vb) = cv_heap.pop_max()
            if imp > 0 and caps[cb] >= v2size[vb]:

                if debug:
                    print('putting video {} (size {}) into cache {}'.format(vb, v2size[vb], cb))

                caps[cb] -= v2size[vb]

                if debug:
                    print('remaining capacity of cache {}: {}'.format(cb, caps[cb]))

                c2v[cb].add(vb)
                success = True
                break

        if not success:
            # not enough capacity, we are done
            break

        # the video vb being put into cache cb
        # will make some change to req2c
        for r in v2reqs[vb]:
            if r.e in c2ends[cb]:  # cb can serve r
                if r in req2c:
                    if latency[(r.e, req2c[r])] > latency[(r.e, cb)]:
                        req2c[r] = cb
                else:
                    req2c[r] = cb
                
                
        # update cv_heap
        for c in range(C):
            if (c, vb) in cv_heap.e2i and (vb, c) in vc2reqs:
                improvement = 0
                for r in vc2reqs[(vb, c)]:
                    # cb serve r and r is served by some c
                    if r in req2c and r.e in c2ends[cb]:
                        if latency[(r.e, req2c[r])] > latency[(r.e, c)]:
                            improvement += r.n * (latency[(r.e, req2c[r])] - latency[(r.e, c)])
                    else:
                        improvement += r.n * (latency[(r.e, CENTER)] - latency[(r.e, c)])
                cv_heap.update_key((c, vb), improvement)  #  / v2size[vb]

    if debug:
        print('remaining caps: {}'.format(caps))
    return c2v

c2v = main(vc2reqs, v2size, latency, cv_heap, debug=False)
{% endhighlight%}

## Received points and ranking

The points I got for each dataset (Extended Round):

- Kittens: 731205
- Me at the zoo: 490625
- Trending today: 499993
- Videos worth spreading: 514499
- **Total**: 2236322

The final ranking is **401**,
whereas the top teams scored ~2650000.

Still a long way to go!

PS: for the Online Qualification Round, my team did not perform very well (around ~1980000 points).

{% highlight ruby %}
def foo
  puts 'foo'
  end
{% endhighlight %}
  
## Possible improvement

- How to formulate the problem differently? 

## Lessons learned

- My coding/algorithmic thinking skills are still to be improved. 
- C/C++ is a good language to pick-up as I found Python is really slow (runs almost 1 hour for the large datasets).
- I am not a good team player. I should have communicated more with my teammates. 