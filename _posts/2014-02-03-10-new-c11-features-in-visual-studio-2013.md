---
title: "10 new C++11 features in Visual Studio 2013"
tags: c++11 vs2013
---

The Visual Studio 2013 C++ compiler has a number of new C++11 features. I'd like to give you a quick overview of these features using examples from the upcoming VS2013 edition of my book, <a href = "/vs2013-edition">C++11 Rocks</a>.

## 1. Variadic templates

Before C++11, if you wanted a template which could work with a variable number of type (or non-type) parameters, you had no choice but to limit such a template to a fixed maximum number of parameters, and the implementation usually involved fairly nasty preprocessor use. To solve this problem, C++11 introduces variadic templates - that is, templates which can have an arbitrary number of type parameters:

{% highlight cpp %}
template<typename Stream, typename... Columns>
class CSVPrinter
{
public:
    void output_line(const Columns&... columns);

    // other methods, constructors etc. not shown
}; 
{% endhighlight %}

The template can be instantiated with _int_, _double_ and _string_, for example:

{% highlight cpp %}
CSVPrinter<decltype(stream),
     int, double, string> printer;
{% endhighlight %}

The `output_line` method of this instantiation is equivalent to this:

{% highlight cpp %}
void output_line(const int& col1,
     const double& col2,
     const string& col3);
{% endhighlight %}

## 2. Alias templates

Template aliases allow you to create an alias for a template, but unlike a _typedef_, you only have to provide some of the template arguments:

{% highlight cpp %}
template<typename T>
using StrKeyMap = map<string, T>;

StrKeyMap<int> counters;
{% endhighlight %}

_StrKeyMap_ is an alias for the map template with the key type set to string, and is itself a template. Aliases allow you to simplify your code. 

## 3. In-class initializers for non-static data members

C++11 gives you a new option for initializing non-static data members:

{% highlight cpp %}
class JetPlane
{
public:
    string _model = "Unknown";
};
{% endhighlight %}

Instead of initializing members in constructors, you can add an in-class initializer to the member declaration. This allows you to avoid duplication when you have multiple constructors.

## 4. Defaulted functions

The compiler can generate a default constructor, destructor, copy constructor and copy assignment operator.

However, if you declare your own constructor, it prevents the generation of the default version. If you declare your own copy operations, then the compiler won’t generate the move operations, and so on.

But sometimes, it’s convenient to preserve some of these default operations, so C++11 provides a way to explicitly request default implementations from the compiler: 

{% highlight cpp %}
class JetPlane
{
public:
    JetPlane() = default;
    JetPlane(const JetPlane& other); 
}; 
{% endhighlight %}

## 5. Deleted functions

You can also apply the delete keyword to methods, which is sort of the opposite to default. delete means that the function doesn’t have an implementation, it’s uncallable and can’t be used in any way. One use for it is to disable compiler-generated methods:

{% highlight cpp %}
class JetPlane
{
public:
    JetPlane() = default;
    JetPlane(const JetPlane&) = delete;
    JetPlane& operator=(const JetPlane&) = delete;
}; 
{% endhighlight %}

This example disables copying of _JetPlane_ class instances. 

## 6. Delegating constructors

C++11 allows a constructor to call another constructor (i.e. delegate to it):

{% highlight cpp %}
class JetPlane
{
public:
    JetPlane() : JetPlane(2, "Unknown", "Unknown")
    {}
}; 
{% endhighlight %}

This can be a good way to reduce duplication in initialization code.

## 7. Raw string literals

Raw literals can’t contain any special characters, allowing you to have things like backslashes, quotes or what would normally be escape sequences like _\n_ in your string literals:

{% highlight cpp %}
cout << R”(use “\n” to represent newline)” << endl;
{% endhighlight %}

This is going to output the string literal verbatim, without any translation, so you'll see the quotes and _\n_ in the output. 

This is a convenient way to write regular expressions. Instead of  
{% highlight cpp %}
"\”\\w+\\\\\\w+\”"
{% endhighlight %}

you can simply write

{% highlight cpp %}
R"(”\w+\\\w+”)".
{% endhighlight %}

## 8. Explicit conversion operators

In addition to explicit constructors, you can now declare conversion operators explicit too. It allows you to prevent unwanted implicit conversion. For example, the standard template _std::function_ has an explicit _operator bool_:

{% highlight cpp %}
explicit operator bool() const noexcept;
{% endhighlight %}

## 9. Default values for function template parameters

Before C++11, you could provide default values for class template parameters, but not for function templates. This has been rectified, so you can create templates like this one:

{% highlight cpp %}
template<typename T, typename Cont = vector<T>> Cont get_batch(T seed);
{% endhighlight %}

The second template parameter provides a default value for the return type. 

## 10. Uniform initialization & initializer_list

C++11 makes an attempt to make initialization more uniform by introducing . All of the examples I’ve shown you, both working and not working, can now be written using brace initialization:

{% highlight cpp %}
int x {5};
int* p_values = new int[3] {1, 2, 3}; 
{% endhighlight %}

Unfortunately, this mechanism has plenty of caveats of its own, so I can't recommend it as a replacement for the traditional initialization syntax.

## Other changes

In addition, the STL implementation has been updated to use these features where necessary. Previously it was using various workaround to simulate the behavior required by the C++11 standard. Numerous changes have been made to fix bugs in the standard library, including an overhaul of `<type_traits>` and `<ratio>` libraries. `<atomic>` had a rewrite in order to improve its performance. 

## VS2013 bugs & C++11 Rocks VS2013 edition

I'm getting ready to release the beta version of _C++11 Rocks VS2013 edition_ which will provide detailed information about all of these changes. 

My preliminary testing indicates that many of the new language features come with their share of bugs in the Visual Studio implementation, and the final version of the book will explain what they are, and how to work around them if possible. 

The book will be available as a PDF, and in EPUB and Mobi formats. The beta version will contain all the chapters, but it won't have information on the compiler/library bugs. It will also require some editing and proofreading. 

However, while the book is in beta you'll be able to get it at a discounted price. And once it's finished, you'll get the final release at no extra charge!