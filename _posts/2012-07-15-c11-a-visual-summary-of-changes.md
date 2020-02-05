---
title: "C++11: a visual summary of the additions and changes since C++03"
tags: c++11
---

Following up on the [previous post](/cpp-ruby-coffeescript-language-complexity/ "C++, Ruby, CoffeeScript: a visual comparison of language complexity"), I thought it would be cool to highlight all the areas which have been added or updated in the C++03 to C++11 transition. I think it's a nice way to show how much of the language has changed.

Here is the diagram (green indicates new stuff, and yellow shows changed items):

<img src = "/img/cpp11.png" width = "100%" />

Out of 18 primary categories, 2 new ones have been added and 12 have changes in them, so most of the language has been touched up, expanded and added to.

There are **44 new** concepts and **35 changed** concepts. Given that there are 189 concepts in total, that's **23% new concepts**, and another **19% updated concepts**.

This is a very significant language update! Just those C++11 features which are implemented in Visual Studio 2010 required 175 pages to cover in my [C++11 Rocks book](/books "C++11 Rocks"), even though VS2010 doesn't support a lot of things such as variadic templates or most of the concurrency features.

Even so, a recent survey on the C++ sub-Reddit showed that 37% of programmers are already using C++11 features in their projects, which shows a good uptake.

If you're interested in a summary of the new STL containers and algorithms, check out the [C++11 STL additions cheatsheet](/cpp11-stl-additions/ "C++11 STL additions") I made.