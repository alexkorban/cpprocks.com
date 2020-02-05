---
title: "C++11 concurrency cheatsheet"
tags: c++11
---

Parallel execution. It's where programming is shifting - slowly but inexorably - and, of course, C++11 is on the concurrency bandwagon. Futures/promises, threads, condition variables and so on - it's all part of the standard library.

I thought it would be handy to have a summary of the concurrency classes and their methods on a single page for quick reference. So I made a cheatsheet:

<p style = "text-align: center">
<a href = "/files/C++-concurrency-cheatsheet.pdf"><img src = "/img/concurrency-cheatsheet-450.jpg" width="450" height="319" /></a>
<br/>
<a href = "/files/C++-concurrency-cheatsheet.pdf">Download C++ concurrency cheatsheet</a>
</p>

I hope you'll find it useful. Note that it isn't exhaustive - e.g. I had to omit atomics because of space constraints. The majority of concurrency features are supported across Visual Studio 2012, GCC 4.8 and Clang 3.3 (see [this post](/c11-compiler-support-shootout-visual-studio-gcc-clang-intel/) for more details). This means that it's possible to write cross-platform code that runs on multiple cores without relying on third-party libraries. I think that's pretty cool!

Are there other parts of C++ you would like to see summarized, documented or explained? Leave a comment below, I'm always looking for ideas.