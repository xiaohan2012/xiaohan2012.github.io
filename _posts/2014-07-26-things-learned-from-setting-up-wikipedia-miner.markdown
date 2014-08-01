---
layout: post
title:  "Things learned from setting up Wikipedia miner"
date:   2014-07-26 16:01:41
categories: experience
tags: wikipedia nlp hadoop lesson
---

I was recently using [Wikipedia miner][wm-github] [dump extraction module][wm-extraction]. This module requires `Hadoop 0.20.2` to run.

I initially set out with setting up `Hadoop 2.4.0`, which became a painful process for me. Becauase:

- I almost knows nothing about Hadoop. 
- The dump extraction is based on `Hadoop 0.20.2`, which is different from `Hadoop 2.4.0`. According to Apache Hadoop guy, the newer one [has undergone a complete overhaul][overhaul].

The reason to use 2.4.0 is simple. It is the newest and using it is fashionable.

After playing with `Hadoop 2.4.0` and trying to make it work for Wikipedia Miner for 3 days, I decided to give up and switch to what Wikipedia miner is built upon, `Hadoop 0.20.2`.

Finally, the beautiful console information pops up. `Hadoop 0.20.2` rules!

The lesson I have learned is: try to use the appropriate tool, not the coolest!

[overhaul]: https://hadoop.apache.org/docs/r2.2.0/hadoop-yarn/hadoop-yarn-site/YARN.html
[wm-github]: https://github.com/dnmilne/wikipediaminer/wiki/Downloads
