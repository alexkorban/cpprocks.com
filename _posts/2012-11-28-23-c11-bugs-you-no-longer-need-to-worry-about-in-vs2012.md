---
title: "23 C++11 bugs you no longer need to worry about in VS2012"
tags: c++11 vs2012
---

Visual Studio 2012 has fixed a lot of bugs in C++11 features which were present in Visual Studio 2010.

Of course, VS2010 was released before the C++ standard was finalised. Now that the standard is final, Visual Studio doesn't have to follow a moving target, and its compliance with the standard is getting a lot better.

This means that you can drop a lot of the workarounds or start using features you've previously avoided. Your code can be cleaner and more straightforward.

Let's have a look at some of the bugs which are now history...

## Lambda bugs

Using lambdas in the previous version of Visual Studio required you to tread carefully.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">1</span> One bug in VS2010 was to do with **nested lambdas and capturing variables**. Consider this code:

{% highlight cpp %}
int power_level = 80;
vector planes;
for_each(planes.begin(), planes.end(), 
    [=](JetPlane& plane)
{
    for_each(plane.engines().begin(), plane.engines().end(), 
        [=](Engine& engine) { engine.set_power_level(power_level); });
});
{% endhighlight %}

If I change the capture lists from using the default mode to capturing _power_level_ explicitly, the code will no longer compile in VS2010 because the compiler thinks _power_level_ isn't accessible to the inner lambda:

{% highlight cpp %}
int power_level = 80;
vector planes;
for_each(planes.begin(), planes.end(), 
    [power_level](JetPlane& plane)
{
    for_each(plane.engines().begin(), plane.engines().end(), 
        [power_level](Engine& engine) { engine.set_power_level(power_level); });
});
{% endhighlight %}

The workaround is either to use a default capture mode (as in the original example), or to copy _power_level_ into a local variable in the outer lambda and capture the copy in the inner lambda.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">2</span> Another, more insidious problem occurred due to a **combination of storing lambdas in an array and instantiating a type with a constructor** in one of the lambdas. This code, for example, resulted in a crash (sometimes):

{% highlight cpp %}
function lambda_array[] = {
    [] () -> bool { cout << "a"; return true; },     
    [] () -> bool { string s; cout << "b"; return false; }
};

lambda_array[0]();  // may cause a crash at runtime
{% endhighlight %}

Note _string s_ constructed in the second lambda. This is what caused the crash.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">3</span> One more problematic example was a **combination of a templated class, a lambda expression and an initializer list**. This code didn't compile in VS2010:

{% highlight cpp %}
template<typename T>
struct A
{
    A() : _f([](const T& t) {})   // error here
    {}

    function _f;
};
{% endhighlight %}

The workaround was to initialize __f_ in the body of the constructor instead, but it's no longer necessary.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">4</span> You couldn't use a **static variable in a lambda within a template function**:

{% highlight cpp %}
template<typename T> 
void f(Iter)
{
    auto lambda = []() 
    {
        static typename iterator_traits::difference_type n; 
    };
}
{% endhighlight %}

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">5</span> Yet another bug related to static variables also involved **initializer lists**:

{% highlight cpp %}    
struct Foo {
    void *ptr;
};
auto str1 = ([]() -> int { 
    static const Foo val = { nullptr }; return 0; }());  // internal compiler error
auto str2 = ([]() -> int { 
    static const struct Foo val = { 0 }; return 0; }()); // fails to compile
{% endhighlight %}

## decltype bugs

You could expect a lot of issues with non-trivial uses of _decltype_. Its current implementation works a lot better. 

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">6</span> **_decltype_ for member types**

In addition to letting you use _decltype_ to declare variables using the type of a previous variable, the VS2012 compiler has been fixed to allow you to do the same with class member variables:

{% highlight cpp %}
class A
{
   int _m;
   decltype(_m) m2;
};
A a;
{% endhighlight %}

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">7</span> **_declval_**

_declval_ is now included, so you have the ability to use classes with no default constructor as _decltype_ expressions:

{% highlight cpp %}
class A
{
private:
    A();
};

decltype(declval<a>()); // compiles OK 
{% endhighlight %}

You should no longer need to roll your own _declval_.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">8</span> **Comma operator in _decltype_ expressions**

As you probably know, if you want _decltype_ to yield a reference type, you need to put the argument expression in parentheses:

{% highlight cpp %}
int a = 10;
is_reference<decltype((a))>::value;    // evaluates to true
{% endhighlight %}

However, before VS2012, if your expression included the comma operator, _decltype_ failed to return a reference type. This is now fixed:

{% highlight cpp %}
is_reference<decltype((a, a))>::value; // also evaluates to true
{% endhighlight %}

## unique_ptr bugs

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">9</span> You could compile this code in VS2010:

{% highlight cpp %}
unique_ptr<JetPlane> p;
JetPlane jet("Boeing 737");
p(&jet);
{% endhighlight %}

The last line invoked the deleter and in this instance caused a crash. The standard doesn't define _operator()_ for _unique_ptr_, however in the VS2010 implementation _unique_ptr_ derived from empty deleters to take advantage of the empty base class optimization, thus exposing the deleter's _operator()_. This is fixed in VS2012 by using private inheritance to prevent the empty deleter's function call operator from being part of the _unique_ptr_ interface.

## tuple bugs

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">10</span> It was possible to store function pointers in a tuple, but it **wasn't possible to use _make_tuple_ to store a reference to a function**. In theory, a tuple can be constructed manually to store a function reference, but that didn't work in VS2010:

{% highlight cpp %}
make_tuple(&get_radar_data);           // returns tuple <RadarData (*)(int)>
tuple<void(&)(int)> tuple_func_ref(f); // supposed to compile but doesn't
{% endhighlight %}

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">11</span> VS2010 **didn't have the _tuple_cat_ template** to concatenate tuples, so you couldn't do this:

{% highlight cpp %}	
auto t = tuple_cat(make_tuple(1, string("two")), make_tuple(3.0, string("four")));
get<3>(t);  // returns the string "four"
{% endhighlight %}

Both of these issues have been rectified.

## bind bugs

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">12</span> Since lambdas are essentially function objects, it should be possible to use them with _bind_. However, _bind_ relies on the _result_of_ template to figure out the return type of function objects. VS2010 has a pre-C++11 implementation of this template which can't evaluate the return type of lambdas, and it made **lambdas impossible to use with _bind_**.

The workaround is to wrap a lambda with _std::function_ and then pass it to _bind_.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">13</span> The VS2010 implementation of _bind_ **didn't allow the use of move-only types**, so you couldn't write this:

{% highlight cpp %}
unique_ptr<string> up(new string("abc"));
auto bound = bind(&string::size, move(up));
bound();
{% endhighlight %}

In VS2012, you can combine _bind_ with lambdas and move-only types to your heart's content.

## emplace member functions

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">14</span> _emplace()_/_emplace_back()_/_emplace_front()_ are intended to construct elements in place instead of moving/copying an object into the container, and they are supposed to take arguments for the element's constructor rather than the element itself as a parameter. However, due to the lack of variadic templates, Visual Studio 2010 provided these functions with non-standard signatures, merely as synonyms to the _insert_ and _push_*_ operations. With Visual Studio 2012, these functions became useful (although they still support a limited number of arguments):

{% highlight cpp %}
vector<pair<int, string>> pairs;
pairs.emplace_back(10, "ten");
pairs.emplace(pairs.begin(), 0, "zero");

unordered_map<int, string> hash;
hash.emplace(10, string("ten"));
{% endhighlight %}

## to_string/to_wstring

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">15</span> _to_string()_/_to_wstring()_ overloads for types other than _long long_, _unsigned long long_, and _long double_ were missing, causing ambiguity if you tried  to pass these functions an _int_ or a _double_. 

## Type traits bugs

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">16</span> The _type_traits_ library had quite a few issues with rvalue references.

_is_rvalue_reference_ failed when passed either an lvalue reference or an rvalue reference to a function:

{% highlight cpp %}
is_rvalue_reference<int (&)(int)>::value;   // true, but should be false
is_rvalue_reference<int (&&)(int)>::value;  // false, but should be true
{% endhighlight %}

_is_reference_ returned the wrong value when passed an rvalue reference to a function:

{% highlight cpp %}
is_reference<int (&&)(int)>::value;    // false, but should be true
{% endhighlight %}

_is_function_ and _is_object_ couldn't handle rvalue references:

{% highlight cpp %}
is_function<int&&>::value;  // doesn't compile
is_object<int&&>::value;  // doesn't compile
{% endhighlight %}

Other VS2010 type properties templates also had problems with rvalue reference support:

{% highlight cpp %}
is_const<const int&&>::value;       // doesn't compile (should be false)
is_volatile<int&&>::value;          // doesn't compile (should be false)
is_volatile<volatile int&&>::value; // doesn't compile (should be true)
{% endhighlight %}

All of these issues have been rectified.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">17</span> When both arguments of _common_type_ are the same type which is _char_, _unsigned char_, _short_ or _unsigned short_, the result of _common_type_ was **incorrectly evaluated to be _int_**:

{% highlight cpp %}
common_type<short, short>::type;    // yields int but should be short
{% endhighlight %}
This is no longer a problem.

In VS2010, _common_type_ only worked with precisely 2 arithmetic types instead of 1 or more types (presumably because of the lack of variadic templates). Now it works with 7-12 types, depending on compiler settings.

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">18</span> _is_trivial_ and _is_standard_layout_ templates were synonyms for _is_pod_ in VS2010, which isn't correct, and as a result the queries in the following example produced wrong results:

{% highlight cpp %}
struct Trivial          // trivial but not standard-layout
{ 
    int a;
private:
    int b;
};

struct StandardLayout   // standard-layout but not trivial
{
    int a;
    int b;
    ~StandardLayout();
};

is_trivial<Trivial>::value;                // false, but should be true
is_standard_layout<StandardLayout>::value; // false, but should be true
{% endhighlight %}

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">19</span> A lot of type property templates were missing in VS2010 and have been added to VS2012:

*   is_trivially_copyable

*   is_literal_type

*   is_constructible/is_default_constructible/
    is_copy_constructible/is_move_constructible

*   is_assignable/is_copy_assignable/is_move_assignable

*   is_destructible

*   is_trivially_constructible/is_trivially_default_constructible/
    is_trivially_copy_constructible/is_trivially_move_constructible

*   is_trivially_assignable/is_trivially_copy_assignable/
    is_trivially_move_assignable

*   is_trivially_destructible

*   is_nothrow_constructible/is_nothrow_default_constructible/
    is_nothrow_copy_constructible/is_nothrow_move_constructible

*   is_nothrow_assignable/is_nothrow_copy_assignable/
    is_nothrow_move_assignable

*   is_nothrow_destructible

*   has_virtual_destructor

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">20</span> The sign conversion templates weren't very well behaved in VS2010. 

_make_signed_ lost _const_ and _volatile_ qualifiers in all unsigned to signed type conversions:

{% highlight cpp %}
make_signed<const unsigned char>::type;     // type is char, const lost
make_signed<volatile unsigned int>::type;   // type is int, volatile lost
// type is int, both const & volatile lost
make_signed<const volatile unsigned long>::type;    
{% endhighlight %}

_make_signed_ converted some integer types incorrectly (although the result types happen to use the right number of bytes for storage):

{% highlight cpp %}
make_signed<unsigned long>::type; // VS2010 converts to int instead of long
make_signed<signed char>::type;   // type is char, but should be signed char
{% endhighlight %}

Similarly, _make_unsigned_ converted _long_ to _int_, and lost const and volatile qualifiers (in signed to unsigned conversions in this case).

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">21</span> _underlying_type_ for determining the underlying type of an enum wasn't available.

## unordered_set

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">22</span> Instead of specifying the number of buckets, you should be able to specify the expected number of elements with _reserve()_, however this wasn't implemented:

{% highlight cpp %}
known_size.reserve(100);    // compile error
{% endhighlight %}

## make_exception_ptr

<span style="text-shadow: 0 1px 1px #444; font-size: 30px; color: #68a4be;">23</span> Instead of providing _make_exception_ptr()_ to create an _exception_ptr_ that refers to a copy of the function's argument, VS2010 provided non-standard _copy_exception()_ to perform the same job. 

The logical question to ask after all this is...

## Is Visual Studio 2012 C++11 support totally bug free?

The answer is no. Visual Studio 2012 still has bugs in C++11 features (luckily, most of them can be worked around). There isn't much overlap between VS2010 and VS2012 though - the bugs are mostly different.

I'm hard at work on the <a title="VS2012 Edition" href="/vs2012-edition">VS2012 edition of C++11 Rocks</a>. The first 13 chapters are already available. With this book, you can quickly learn all the intricate details of the C++11 features available in Visual Studio 2012 - bugs and all.