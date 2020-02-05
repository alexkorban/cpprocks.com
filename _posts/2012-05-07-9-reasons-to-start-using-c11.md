---
title: "9 reasons to start using C++11"
tags: c++11
---

If your code is working fine and performing well, you might be wondering why you should jump on the C++11 bandwagon. Sure, it feels nice to be using the latest technology, but is it actually worthwhile?

In my opinion, the answer is a definite yes. I discuss 9 reasons to get into C++11 below. They fall into two broad categories: performance benefits and developer productivity.

## Get performance benefits

**Reason 1** is move semantics. To explain the concept very briefly, it's a way to optimize copying. Sometimes copying is obviously wasteful. If you're copying from a temporary string object, simply copying the pointer to the character buffer would be much more efficient than creating a new buffer and copying the character. It would work because the source object is about to go out of scope. 

However, previously there was no mechanism in C++ to figure out whether the source object is a temporary or not. Move semantics provide this mechanism by allowing you to have a move constructor and a move assignment operator in addition to the copy operations.

Did you know that if you’re using classes from the standard library like _string_ or _vector_ in Visual Studio 2010, they already support move semantics? This helps prevent unnecessary copying and therefore improve performance.

By implementing move semantics in your own classes you can get additional performance improvements, for example when you store them in STL containers. Also, keep in mind that move semantics can be applied not only to constructors, but also to methods (such as _vector_'s _push_back_).

**Reason 2**. With the use of type traits (e.g. _is_floating_point_) and template metaprogramming (e.g. the _enable_if_ template), you can specialize your templates for types with particular characteristics and thus implement optimizations.

**Reason 3**. The hash tables which have now become standard provide faster insertion, deletion and lookup than their ordered counterparts, which can be very useful when handling large amounts of data. You now have _unordered_map_, _unordered_multimap_, _unordered_set_ and _unordered_multiset_ at your disposal. 

## Improve your productivity

It’s not all about the performance of your code though. Developer time is valuable too, and C++11 can make you more productive by making your code shorter, clearer and easier to read.

**Reason 4**. The _auto_ keyword provides type inference, so instead of

{% highlight cpp %} 
vector<vector<MyType>>::const_iterator it = v.begin();
{% endhighlight %}

you can now simply write

{% highlight cpp %}
auto it = v.cbegin();
{% endhighlight %}

Although some people complain that it obscures type information, in my opinion the benefits are more important, as it reduces visual clutter and reveals the behavior that the code expresses. And there's a lot less typing!

**Reason 5**. Lambda expressions provide a way to define anonymous function objects (which are actually closures) right where they are used, thus making the code more linear and easier to follow. This is very convenient in combination with STL algorithms:

{% highlight cpp %}
bool is_fuel_level_safe()
{
    return all_of(_tanks.begin(), _tanks.end(), 
        [this](Tank& t) { return t.fuel_level() > _min_fuel_level; });
}
{% endhighlight %}

**Reason 6**. The new smart pointers which have replaced the problematic _auto_ptr_ allow you to stop worrying about memory cleanup, and to remove the cleanup code. It's good for clarity, and for avoiding memory leaks and the associated time spent to hunt them down.

**Reason 7**. Having functions as first class objects is a very powerful feature that allows your code to be flexible and generic. C++11 makes a step in this direction with _std::function_. _function_ provides a way to wrap and pass around anything callable - function pointers, functors, lambdas and more. 

**Reason 8**. There are many other smaller features such as the _override_ and _final_ (or the non-standard _sealed_ in Visual Studio) keywords and _nullptr_ allow you to to be clearer the intent of your code.

For me, less visual clutter and being able to express my intentions in code more clearly means I’m happier and more productive.

Another aspect of developer productivity is error detection. If your errors happen at runtime, it means that at the very least you need to run the software, and probably perform a series of steps to reproduce the error. This takes time.

C++11 provides a way to check preconditions and catch errors at the earliest possible opportunity - during compilation, before you even run the code, which is **Reason 9**.

This is achieved by using _static_assert_ and the type traits templates. Another advantage of this approach is that such checks don’t incur any runtime overhead - one more point for performance!

## Start mastering C++11 today

There are many more changes and additions in the C++11 standard than what is described above, and it takes a whole book to describe all of them. However, I believe that it's a good investment of time to learn them. 

You will recover the time you've spent through productivity gains. There is already a substantial common base of C++11 support across the mainstream compilers. The potential for performance gains seals the deal. So don't wait, [get into it](/books)!