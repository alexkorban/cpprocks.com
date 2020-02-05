---
title: "An overview of C++14 language features"
tags: c++14
---

In this post, I’m going to highlight some of the new language features in the draft of the C++14 standard. This is an excerpt from my book [C++11 Rocks: VS2013 Edition](/vs2013-edition). I looked at the level of C++14 support in different compilers in a [previous post](/c1114-compiler-and-library-shootout/).

## Return type deduction for functions

The compiler will be able to deduce the return type of functions:

{% highlight cpp %}
auto square(int n) 
{
    return n * n;
}
{% endhighlight %}

To get the compiler to figure out the return type, you start the declaration with _auto_ but don’t specify a trailing return type. This is basically extending the return type deduction VS2013 does for lambdas to regular functions.

## Generic lambdas

When specifying lambda parameters, you’ll be able to use _auto_ instead of a type:

{% highlight cpp %}
auto lambda = [](auto a, auto b) { return a * b; };
{% endhighlight %}

In C++ pseudocode, this definition is equivalent to the following:

{% highlight cpp %}
struct lambda1
{
    template<typename A, typename B>
    auto operator()(A a, B b) const -> decltype(a * b)
    {
        return a * b;
    }
};

auto lambda = lambda1();
{% endhighlight %}

So using _auto_ for parameters effectively makes the function call operator templated. The types of the parameters will be determined using the rules for template argument deduction rather than the type inference that happens with regular _auto_ variables.

## Extended capturing in lambdas

Another change to lambdas involves capturing variables. In C++14, captured variables can have an initializing expression:

{% highlight cpp %}
auto timer = [val = system_clock::now()] { return system_clock::now() - val; };
// ... do stuff ...
timer();   // returns time since timer creation
{% endhighlight %}

In this example, _val_ is assigned the current time which is then returned by the lambda expression. _val_ doesn’t need to be an existing variable, so this is in effect a mechanism for adding data members to the lambda. The types of these members are inferred by the compiler.

This allows capturing move-only variables which isn’t possible in C++11:

{% highlight cpp %}
auto p = make_unique<int>(10);
auto lmb = [p = move(p)] { return *p; }
{% endhighlight %}

It is equivalent to capturing a variable via move, although what’s happening under the hood is a bit more tricky. In reality, the capture block declares a new data member in the lambda called _p_. This member is initialized with _p_ from outside the lambda which is cast to an rvalue reference.

## Revised restrictions on _constexpr_ functions

The proposal lists the following things which are going to be allowed in _constexpr_ functions:

*   declarations in _constexpr_ functions with the exception of _static_, _thread_local_ or uninitialized variables
*   _if_ and _switch_ statements
*   looping constructs, including range-based _for_
*   modification of objects whose lifetime began within the constant expression evaluation.

This should make _constexpr_ functions a lot more versatile.

## _constexpr_ variable templates

In addition to type and function templates, C++14 will allow _constexpr_ variables to be templated. Here is how it will work:

{% highlight cpp %}
template<typename T>
constexpr T pi = T(3.1415926535897932385);
{% endhighlight %}

There is no new syntax, the already existing template syntax is simply applied to a variable 
here. This variable can then be used in generic functions:

{% highlight cpp %}
template<typename T>
T area_of_circle_with_radius(T r) 
{
    return pi<T> * r * r;
}
{% endhighlight %}

The idea is that despite having a single initializing expression, it’s sometimes useful to vary the type of a variable used in a generic function.

## More changes

There are more language changes on the way. They include:
[list style="arrow" color="blue"]

*   Another alternative for type inference which will allow you to use _decltype_ inference rules for _auto_ variables.
*   Aggregate initialization will work for classes with in-class member initializers.
*   Built-in binary literals prefixed with _0b_.
*   It’ll be possible to separate digits in a numeric literal with a single quote.
*   Another standard attribute called _[[deprecated]]_ will be added with the purpose of marking deprecated parts of the code.
[/list]

The standard is expected to be finalized some time this year. The good thing is that the work on adding new features into compilers is already well under way, and some of the features I described in this post can already be used.