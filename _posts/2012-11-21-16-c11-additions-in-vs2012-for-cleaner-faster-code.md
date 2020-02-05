---
title: "16 C++11 additions in VS2012 for cleaner & faster code"
tags: c++11 vs2012
---

Some people were disappointed with the level of C++11 support in Visual Studio 2012. Let's look on the bright side though. There are still lots of useful C++11 additions you can use to make your code cleaner and faster.

Language support has been improved in little ways. More significantly, the standard library is now meant to be complete (minus the stuff relying on non-implemented language features). 

Here's an overview of the C++11 additions...

## Language additions and changes

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">1</span> The most visible new language feature is the range based for-loop. It made it into the release quite unexpectedly at the last moment. This feature provides a convenient way to iterate over a whole container:

{% highlight cpp %}
vector<double> v;
for (auto d : v)
    cout << d << endl;
for (auto& d : v)
    ++d; // when using a reference, the value can be modified
{% endhighlight %}

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">2</span> Improved support for lambda expressions (which means fewer workarounds!):

*   Anonymous lambdas can be converted into function pointers
*   Lambdas support arbitrary calling conventions
*   nested lambda expression will not be able to access members of the enclosing lambda expression's closure object
*   Some bugs to do with lambdas referring to static members will be fixed

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">3</span> Improved support for more complex uses of _decltype_ - it's more reliable now.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">4</span> Strongly typed enums and forward declaration semantics for enums are fully supported - so you can improve type safety.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">5</span> While the keywords _alignas_/_alignof_ still aren't supported, _aligned_union_ and _align()_ are implemented.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">6</span> _final_ keyword replaced the non-standard _sealed_. It can be used either to disallow inheritance from a class, or to forbid overriding a method.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">7</span> The overloading rules for lvalue/rvalue reference overloads combined with string literals (which are lvalues) have been fixed to match the final standard, so code like this performs better:

{% highlight cpp %}
vector<string> v;
v.push_back("abc"); // calls v.push_back(string&&)
{% endhighlight %}

Don’t forget that significant and widely anticipated language features such as type inference, lambda expressions and move semantics have been available since the previous release. And of course, there are all the new library goodies...

## Standard library additions and changes

You can now accomplish a lot more without touching any third party libraries.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">8</span> **`<chrono>`**
This library provides convenient classes for working with time. It supports time durations, as well as arithmetic and comparison operations on them:

{% highlight cpp %}
// duration specified in minutes
duration<long, ratio<60>> quarter_hour(15);
duration<long, ratio<60>> twenty_min(20);
// duration specified in half seconds
duration<long, ratio<1, 2>> half_second(1);    

cout << (twenty_min < quarter_hour) << endl;             // outputs 0
cout << (twenty_min + 2 * quarter_hour).count() << endl; // outputs 50
{% endhighlight %}

It also defines three clocks (_system_clock_, _steady_clock_ and _high_resolution_clock_), which can be used to get time:

{% highlight cpp %}
auto start = system_clock::now();
// do a long operation
auto end = system_clock::now();
cout << (end - start).count() << endl;  // outputs duration in seconds
{% endhighlight %}

Finally, it has a class to represent points in time. These are always associated with a clock and store a duration relative to the origin of the clock. 

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">9</span> **`<ratio>`**
This library provides support for compile-time representation of rational numbers, as well as compile-time arithmetic and comparison operations on them:
{% highlight cpp %}
typedef ratio<1, 60> r1_60;
typedef ratio<1, 3> r1_3;
typedef ratio_add<r1_60, r1_3>::type r_sum;         // r_sum is 7/20
bool compare_res = ratio_less<r1_60, r1_3>::value;  // false
{% endhighlight %}

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">10</span> **`<atomic>`**
This library provides components for fine grained (i.e. synchronized) access. The access is provided via operations on atomic objects:

{% highlight cpp %}
atomic<int> var(10);
var += 5;
int res = var.fetch_add(20);    // returns 15, the previous value
{% endhighlight %}

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">11</span> **`<thread>`**
This library provides support for parallel code execution using threads, as well as facilities for thread synchronization and thread local storage. Creating a thread is simple:

{% highlight cpp %}
int main() {
    double res1, res2;
    thread t1 {[&]{ res1 = long_calculation(); }};   
    thread t2 {[&]{ res2 = another_long_calculation(); }};   
    t1.join();
    t2.join();
    cout << res1 << ' ' << res2 << '\n';
    return 0;
}
{% endhighlight %}

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">12</span> **`<mutex>`**
Support for recursive and non-recursive mutexes. Recursive mutexes can be acquired more than once by a thread. 

{% highlight cpp %}
mutex m;
int cnt; // shared across threads
// ...
m.lock();
++cnt;
m.unlock();
{% endhighlight %}

Another convenient facility is timed mutexes which can attempt to acquire a lock either until a certain time, or for a period of time: 

{% highlight cpp %}
timed_mutex m;
int cnt; // shared across threads
// ...
if (m.try_lock_for(chrono::seconds(10))) 
{
    ++cnt;
    m.unlock();
}
else 
{
     // didn't get the mutex within 10 seconds; handle the failure
}
{% endhighlight %}

The library also provides the lock class which implements the RAII pattern for mutexes. In addition, locks have the ability to acquire multiple mutexes at once.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">13</span> **`<future>`**
This library is dedicated to higher level abstractions for asynchronous computation, such as _future_/_promise_ for returning a value from a task spawned on a thread, _packaged_task_ to launch tasks (by combining _future_ and _promise_), and _async_ for a simple/automated way to spawn threads as needed. 

{% highlight cpp %}
vector<double> v;
// ... put lots of elements into v ...
auto res1 = async(long_calculation, v);
auto res2 = async(another_long_calculation, v);
// ...
cout << *res1.get() << ' ' << *res2.get() << '\n'; // get can block if the result isn't ready yet
{% endhighlight %}

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">14</span> **`<condition_variable>`**
Support for conditional variables, which allow blocking a thread or a group of threads for a period of time or until a notification is received from another thread.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">15</span> **`<scoped_allocator>`**
Provides _scoped_allocator_adaptor_ template which specifies the allocator to be used by a container, as well as zero or more inner allocators which are passed to the constructor of each element of the container. If the elements are also containers, then the _second_ inner allocator is passed to _their_ elements' constructors, and so on.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">16</span> Compliance with the standard has also been improved for the libraries that were already present in Visual Studio 2010. For example, rvalue reference support bugs were fixed in the type traits library. This means fewer workarounds - and cleaner code!

Generally, Visual Studio 2012  supports all the standard library features, as long as they don't require as yet unimplemented language features such as variadic templates. The exception to this is the C99 standard library which didn't get any new additions.

The final question is...

## What about bugs?

Yes, there are still bugs and cases of non-standard behavior in VS2012. They are mostly different from the ones in VS2010. 

You can learn all the C++11 features in VS2012 in intricate detail - bugs and all - with the [VS2012 edition of my C++11 Rocks book](/vs2012-edition "VS2012 Edition") (currently in beta). It isn't complete yet, but you'll be able to get a lot of valuable information from it nonetheless. The first 13 chapters include descriptions of 29 bugs and cases of non-standard behavior in the Microsoft compiler.