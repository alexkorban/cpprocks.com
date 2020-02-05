---
title: "44 C++11 bugs fixed in Visual Studio 2013"
tags: c++11 vs2013
---

The following bugs which I described in the VS2012 edition of my book, _C++11 Rocks_, have been fixed in Visual Studio 2013, so you have more freedom to use C++11 features as they were intended to be used. If you used workarounds for these bugs, now you can remove them and clean up your code!

However, as I was researching VS2013, I was disappointed to learn that many bugs migrated from VS2012 unscathed, and new bugs have been introduced too. I'm working on describing these bugs in the VS2013 edition of [C++11 Rocks](/vs2013-edition).

Read on to find out which VS2012 bugs have been fixed!

## No more type argument limits on variadic templates

This fixes a whole set of variadic templates in the standard library. As Visual Studio 2013 supports variadic templates, things like `std::function` or `make_shared` no longer have a limit on the number of template arguments they can take. There used to be a max of about 5 arguments by default in Visual Studio 2012. 

## Fixed type inference bugs

### auto lost alignment specifiers

If you used _auto_ to declare a variable of a type that's qualified with `__declspec(align(...))`, the qualifier may not have been preserved in VS2012, which resulted in incorrect alignment and subsequent crashes.

### decltype wasn't always allowed in place of a type

Even though `decltype` is considered to be a type specifier, and the intention is for you to be able to use `decltype(expr)` in place of a type name anywhere, VS2012 didn't allow code like this: 

{% highlight cpp %}
vector<int> a; 
decltype(a)::iterator iter = a.end(); // error in VS2012
{% endhighlight %}

### declval caused compilation errors

Some correct code failed to compile in VS2012 because of `declval` internals (using `add_rvalue_reference` template).

Suppose you want to define an `is_comparable<T>` template:

{% highlight cpp %}
template<typename, typename = true_type>
struct is_comparable : public false_type 
{};

template<typenameT>
struct is_comparable<T,
    typename is_convertible<decltype(declval<T>() > declval<T>()), 
        bool>::type> : public true_type 
{};
{% endhighlight %}

This didn't compile because `declval` failed to handle _T_. 

## Fixed smart pointer bugs

### Using a lambda as a custom deleter for unique_ptr breaks conversion to bool

If you used a lambda as a custom deleter in VS2012, and specified the type of the lambda as the deleter type (using `decltype`), you weren't able to use the pointer in a boolean context:

{% highlight cpp %}
auto stream_deleter = [](ofstream* os) { os->close(); };
unique_ptr<ofstream, decltype(stream_deleter)> p_log(&log_file, stream_deleter);

if (!p_log)     // compile error 
    cout << "Couldn't open file" << endl;
{% endhighlight %}

### Calling unique_ptr::reset could cause a double delete

The order of operations in the `reset` method didn't follow the order prescribed by the standard before VS2013. This could result in a double delete. The following (contrived) example shows how this happened:

{% highlight cpp %}
class SelfReferential
{
    unique_ptr<SelfReferential>& _p_self;
public:
    SelfReferential(unique_ptr<SelfReferential>& p) : _p_self(p)
    {}
    ~SelfReferential() 
    {
        _p_self.reset();
    }
};

unique_ptr<SelfReferential> p;
p = unique_ptr<SelfReferential>(new SelfReferential(p));
p.reset();  // double delete via ~SelfReferential
{% endhighlight %}

The call to `reset` invokes `SelfReferential`'s destructor, which in turns calls `reset` again. The double delete happened because the `reset` method reset the pointer to the owned object after deleting it instead of before.

### shared_ptr, protected destructor and nullptr

You could't construct a `shared_ptr` of a class with a protected destructor with `nullptr`:

{% highlight cpp %}
class Interface
{
public:
    virtual void do_stuff() = 0;

protected:
    ~Interface() {}
};

class Implementation : public Interface
{
public:
    void do_stuff() override
    {
        // ...
    }
};

shared_ptr<Interface> ptr1 = make_shared<Implementation>(); // OK
shared_ptr<Interface> ptr2 = nullptr;                       // error
{% endhighlight %}

In Visual Studio 2013, this code compiles as expected.

## Fixed bugs in the type traits library

### Wrong answers given for functions

`is_function` yielded the wrong result if given a function with too many arguments:

{% highlight cpp %}
typedef void f(int, bool, int*, int[], int, int, int, int, int, int, int);
is_function<f>::value;     // false but should be true
{% endhighlight %}

It also incorrectly yielded _false_ for functions with a non-default calling convention.

Similarly, `is_member_function_pointer` failed to detect member functions with an explicitly specified calling convention.

`is_member_pointer`, in contrast, failed to detect a `__cdecl` member:

{% highlight cpp %}
typedef void (__cdecl A::*ccall_proc)(int, long, double);
is_member_pointer<ccall_proc>::value;   // false but should be true
{% endhighlight %}

`is_object` was defined in terms of `is_function`, so it produced an incorrect result (falsely detecting an object) if it was given a function with too many arguments (i.e. a function which also tripped up `is_function` as described above).

### is_scalar refused to acknowledge nullptr_t

`is_scalar<nullptr_t>` incorrectly returned _false_ in VS2012 – the standard requires `nullptr_t` to be considered a scalar type.

### is_pod misidentified void

`is_pod<void>` incorrectly returned _true_ in VS2012, even though _void_ isn't a POD type.

### is_constructible gave wrong answers for reference types

`is_constructible` behaved incorrectly with reference types, returning _false_ for things like this:

{% highlight cpp %}
is_constructible<const string&, string>::value;
is_constructible<const string&, string&&>::value;
{% endhighlight %}

### Bugs in alignment related type traits

`alignment_of` in VS2012 generatee a spurious warning about inaccessible destructor if you used it on a type with a private destructor.

Additionally, the `aligned_union` template wasn't well behaved in VS2012. It returned incorrect results in some cases:

{% highlight cpp %}
typedef aligned_union<16, string>::type StorageType;
sizeof(string);       // produces 24
sizeof(StorageType);  // produces 16 but should be 24 or more
{% endhighlight %}

In addition to the member typedef _type_, `aligned_union` is also supposed to have a static member variable `alignment_value` containing the value of the strictest alignment of template arguments *T1, ..., Tn*. However, it wasn't present in the VS2012 implementation.

### common_type incorrectly produced void

Instead of failing to compile as the definition of `common_type` implies in the standard, it could yield _void_ in VS2012:

{% highlight cpp %}
common_type<int, string>::type;     // void
{% endhighlight %}

`common_type` also yielded _void_ incorrectly for user defined types where conversion *is* possible:

{% highlight cpp %}
struct A {};

struct AWrapper {
    AWrapper() {}
    AWrapper(const A&) {}
};

common_type<A, AWrapper>::type;     // void
{% endhighlight %}

### result_of failed to compile in some cases

If you attempt to use move-only argument types with this template in VS2012, you ran into trouble:

{% highlight cpp %}
result_of<Copyable(MoveOnly&&)>::type;  // compile error
{% endhighlight %}

## Fixed STL container and algorithm bugs

### minmax_element algorithm didn't work

There are two versions of this algorithm defined in the standard:

{% highlight cpp %}
pair<Iter, Iter> minmax_element(Iter first, Iter last)
pair<Iter, Iter> minmax_element(Iter first, Iter last, Compare comp)
{% endhighlight %}

They should return the **first** iterator in _[first, last)_ pointing to the smallest element, and the **last** iterator pointing to the largest element (in terms of comp if supplied), or `make_pair(first, first)` if the range is empty. However, in VS2012, it returned `make_pair(min_element(first, last), max_element(first, last))` instead.

### Containers incorrectly required element move constructors

All of the container move constructors incorrectly require element types to provide a move constructor: 

{% highlight cpp %}
struct A
{
    A() {}
private: 
    A(A&&);
    A(const A&);
};

deque<A> source;
deque<A> target(move(source));    // compile error
{% endhighlight %}

Additionally, they didn't preserve the allocator during the move operation. 

Similarly, `map` and `unordered_map` element access operators unnecessarily required the value type to be move constructible:

{% highlight cpp %}
map<string, A> m;
A& elem = m["abc"];  // compile error
{% endhighlight %}

## Fixed bugs in concurrency libraries

A bunch of bugs were fixed in the various parts of the concurrency libraries.

### shared_future constructed from future

Another bug in VS2012 was in the implementation of the specializations of `future` and `shared_future` for reference types and _void_. This bug allowed code like this to compile (which is obviously wrong as `future` is a move-only type):

{% highlight cpp %}
future<int&> f_ref; 
shared_future<int&> sf_ref(f_ref);    // compiles but shouldn't 

future<void> f_void;
shared_future<void> sf_void(f_void);  // compiles but shouldn't 
{% endhighlight %}

### Memory leak in the thread class

There is a bug which could lead to memory leaks being reported on program termination. This was due to the thread constructing and never destroying `at_thread_exit_mutex`, as well as some internal allocations in mutex and `condition_variable` which could be incorrectly reported as leaks. 

### Useless wait functions in a future provided by promise

There was a significant downside to using a `future` provided by a `promise`. Due to a bug in Visual Studio 2012, the `wait_for` and `wait_until` methods of such `future` objects returned `future_status::deferred` instead of `future_status::timeout` or `future_status::ready`, rendering these methods useless.

### Incorrect messages in future_error exceptions

There was an off-by-one error between error codes and error messages returned via `future_error`, so you couldn't rely on the message of `future_error`. For example, when you got a *"broken promise"* exception, the message would be *"future already retrieved"*. The error code was correct, however.

### atomic template couldn't be instantiated with a non-default-constructible type

You received an unwarranted compilation error if you tried to use a type without a default constructor with _atomic_. 

### atomics were slow

In VS2012, many atomic operations always enforced sequential consistency, which made them unnecessarily slow. While this didn't violate the standard, it mostly defeated the purpose of having relaxed memory ordering - as it's specifically meant to be used where high performance is required. VS2013 provides a different implementation of atomic operations which is much faster.

## Fixed random number facility bugs

In debug mode, `mersenne_twister_engine` produced an incorrect assert if you tried to use zero as a seed. 

The streaming operator for `subtract_with_carry_engine` contained an off-by-one error which triggered undefined behavior. 

Both `independent_bits_engine` and `shuffle_order_engine` failed to initialize member variables in their move constructors, which sometimes resulted in infinite loops.

## Fixed rational arithmetic library bugs

This library suffered from a remarkable number of defects. 

Due to the lack of alias templates in VS2012, you couldn't write 

{% highlight cpp %}
ratio_add<ratio<1, 2>, ratio<1, 3>>::num;
ratio_add<ratio<1, 2>, ratio<1, 3>>::den;
{% endhighlight %}

Instead, you had to refer to the numerator and denominator of the result via the type member:

{% highlight cpp %}
ratio_add<ratio<1, 2>, ratio<1, 3>>::type::den;
{% endhighlight %}

Another issue was due to how comparison was implemented. It used _ratio_ template arguments instead of using _num_ and _den_ static members as the standard requires. Because of this, if you used non-normalized numerator and denominator as _ratio_ arguments, comparisons would provide an incorrect result:

{% highlight cpp %}
cout << "2/60 < -1/3: " << ratio_less<r2_60, r1_3>::value << endl;  // false

cout << "2/60 < 1/-3: " << ratio_less<r2_60, ratio<1, -3>>::value 
    << endl;    // true but should also be false
{% endhighlight %}

So in VS2012, you had to make sure the denominator passed to _ratio_ was always a positive number.

Another bug was that `ratio_equal` detected inequality but wasn't very good at detecting equality:

{% highlight cpp %}
ratio_equal<ratio<1, 4>, ratio<4, 16>>::value;   // false but should be true
{% endhighlight %}

Here is another bug. When you have a `ratio<N, D>`, if _D_ is zero or either argument overflowed the boundaries of `intmax_t`, then your program is ill-formed. However, Visual Studio 2012 mostly failed to detect these issues:

{% highlight cpp %}
typedef ratio<1, 0> r_error;
cout << r_error::den << endl;   // shouldn't compile but does

typedef ratio<INTMAX_MIN, 1> r_error2; 
cout << r_error2::num << endl;  // shouldn't compile but does
{% endhighlight %}

In the Visual Studio implementation, the `static_assert` statements which are supposed to trigger compilation errors in these situations reside in the _ratio_ constructor. However, the compiler only triggers the `static_assert`'s in a constructor when the class is instantiated. You wouldn't normally create instances when using _ratio_, so be aware that errors won't be triggered at compile time. 

Similarly, some of the operations compile incorrectly when they shouldn't:

{% highlight cpp %}
// shouldn't compile but does – with a warning about overflow
ratio_multiply<ratio<1, INTMAX_MAX>, ratio<1, 2>>::type;
{% endhighlight %}

Note that the statement `ratio<1, 0>` should actually compile whereas `ratio<1, 0>::num` shouldn't. This is because errors are only detected when one of the members is evaluated. 

## Other bugs fixed in Visual Studio 2013

### tuple_element bounds checking wasn't done

`tuple_element<I, array<T, N>>` is supposed to check that `I < N` and fail to compile if it isn't. This wasn't done until VS2013.

### Wrong conversion to bool for std::function

In some cases, the conversion could produce an incorrect result in VS2012 due to the function object not being empty when it should be. For example: 

{% highlight cpp %}
// JetPlane derives from Plane
function<bool(JetPlane*)> plane_ready_func = function<bool(Plane*)>();
if (plane_ready_func)   // should yield false but doesn't
{
    plane_ready_func(nullptr);   // gets called and throws bad_function_call
}
{% endhighlight %}

### Assignment to rvalues

Visual Studio 2012 didn't forbid assignment to rvalues as required by the standard:

{% highlight cpp %}
struct Dummy 
{ 
    int _x; 
};
Dummy get_dummy() 
{ 
    Dummy d = { 10 }; 
    return d; 
}
get_dummy()._x = 20;     // compiles even though it shouldn't
{% endhighlight %}

### align() updated out parameters incorrectly

The function correctly calculated the address it returns, but updated the last two parameters incorrectly:

{% highlight cpp %}
void* p = (void*)0x1;

// try to align 200 bytes to the 32 byte boundary 
// with 230 bytes of storage space available
size_t space = 230;
void* res = align(32, 200, (void*&)p, space);  
// res is null as 31 bytes are needed for alignment
// but at most 30 are available

space = 256;  // increase available space to 256 bytes
res = align(32, 200, (void*&)p, space);
// res is now 0x20 (aligned to 32 byte boundary)
// p is 0xE8 (200 + 32) but should be 0x20
// space is 25 but should be 225
{% endhighlight %}

### time_put didn't work with wchar_t

`time_put` didn't produce output when instantiated with `wchar_t`. 

## Conclusion

For a full list of library changes in VS2013 (not just those related to C++11), you can check out <a href="http://blogs.msdn.com/b/vcblog/archive/2013/06/28/c-11-14-stl-features-fixes-and-breaking-changes-in-vs-2013.aspx">this post</a> by Stephan Lavavej.

In addition to adding new C++11 features, Visual Studio 2013 fixes a significant number of bugs in the existing C++11 functionality in the compiler and libraries, from incorrect compiler errors to memory leaks to poor performance. This is definitely a good thing. 

Unfortunately, VS2013 still contains a large number of bugs carried over from VS2012. It also introduces new bugs related to C++11 features. If you don't want to get stung by these bugs, check out the VS2013 edition of my [C++11 Rocks book](/vs2013-edition). Even though it's currently in beta, it already describes a significant number of Visual Studio bugs. 