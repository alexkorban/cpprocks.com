---
title: "C++11 compiler support shootout: Visual Studio, GCC, Clang, Intel"
tags: c++11 vs2012 gcc clang intel
---

It's been more than half a year since my [last comparison](/a-comparison-of-c11-language-support-in-vs2012-g-4-7-and-clang-3-1/ "A comparison of C++11 language support in VS2012, g++ 4.7 and Clang 3.1") of the C++11 support across different compilers. This time I'd like to see how different compilers stack up based on the documentation for the pre-release versions of these compilers.

The next release of GCC is 4.8 and the upcoming version of Clang is 3.3. If you use Visual Studio 2012, you can install an experimental CTP update released in November 2012 to get additional C++11 features. 

I've also thrown in v.13.0 of Intel's C++ compiler out of curiosity, although it isn't pre-release and there were a few features I couldn't find information about. I didn't find any information about the upcoming version of this compiler.

<table>

<tr><th>Feature</th><th>VS2012
Nov CTP</th><th>g++ 4.8</th><th>Clang 3.3</th><th>Intel 13.0</th></tr>

<tr><td>auto</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>decltype</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Rvalue references and move semantics</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Lambda expressions</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>nullptr</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>static_assert</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Range based for loop</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Trailing return type in functions</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>extern templates</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>&gt;&gt; for nested templates</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Local and unnamed types as template arguments</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Variadic macros</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Variadic templates</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Default template arguments in function templates</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>final method keyword</td><td>Yes</td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>
<tr><td>override method keyword</td><td>Yes</td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>
<tr><td>Strongly typed enums</td><td>Yes</td><td>Yes</td><td>Yes</td><td><strong>Partial</strong></td></tr>
<tr><td>Forward declared enums</td><td>Yes</td><td>Yes</td><td>Yes</td><td><strong>Partial</strong></td></tr>
<tr><td>Initializer lists</td><td>Yes</td><td>Yes</td><td>Yes</td><td><strong>Partial</strong></td></tr>
<tr><td>explicit type conversion operators</td><td>Yes</td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>
<tr><td>Raw string literals</td><td>Yes</td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>
<tr><td>Forwarding constructors</td><td>Yes</td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>

<tr><td>Template aliases</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Defaulted methods</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Deleted methods</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Generalized attributes</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td>New built-in types</td><td><strong>Partial</strong></td><td>Yes</td><td>Yes</td><td><strong>Partial</strong></td></tr>
<tr><td>Alignment support</td><td><strong>Partial</strong></td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>
<tr><td>Inline namespaces</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>
<tr><td>sizeof on non-static data members without an instance</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>
<tr><td>Changed restrictions on union members</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>
<tr><td>User defined literals</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>
<tr><td>Encoding support in literals</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td><strong>No</strong></td></tr>
<tr><td>Arbitrary expressions in template deduction contexts</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td><strong>Don't know</strong></td></tr>
<tr><td>Non-static data member initializers</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td><strong>Don't know</strong></td></tr>
<tr><td>noexcept</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td><strong>Partial</strong></td></tr>
<tr><td>constexpr</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td><td><strong>Partial</strong></td></tr>
<tr><td>C99 compatibility</td><td><strong>Partial</strong></td><td><strong>Partial</strong></td><td><strong>Partial</strong></td><td>Yes</td></tr>

<tr><td>Thread local storage</td><td><strong>Partial</strong></td><td>Yes</td><td><strong>No</strong></td><td><strong>Partial</strong></td></tr>
<tr><td>Inheriting constructors</td><td><strong>No</strong></td><td>Yes</td><td><strong>No</strong></td><td><strong>No</strong></td></tr>
<tr><td>Rvalue references for *this</td><td><strong>No</strong></td><td><strong>No</strong></td><td>Yes</td><td><strong>No</strong></td></tr>
</table>

It looks like GCC is overtaking Clang as the compiler with the best C++11 support. Visual Studio has added several significant C++11 features such as variadic templates, initializer lists and raw literals. 

I can't really comment on how complete and bug-free these implementations are at a more fine grained level (other than VS2012 - I detail a lot of the bugs in the initial VS2012 release in my book, [C++11 Rocks](/)). 

It's also useful to look at the state of library support. I'm not going to get very specific as there are lots of small changes across the standard library. I'm also going to omit Intel's library from this comparison. 

I can say that the major additions to the library are mostly provided by all three implementations (as shown in the table below), although with various caveats. 

Microsoft's library implementation lacks anything that requires unimplemented language features such as constexpr (as of the initial release of VS2012). The library hasn't been updated to use the features in the November 2012 CTP of the compiler such as initializer lists or variadic templates. 

GCC's libstdc++ lags somewhat as it doesn't support regular expressions and low-level concurrency features. It also doesn't implement constexpr methods in a lot of cases. std::function doesn't have support for custom allocators, as Gilad noted in the comments below. 

Clang's libc++ is 100% complete on Mac OS, however various parts of it don't yet work on Windows and Linux. 

<table>
<tr><th>Feature</th><th>MSVC</th><th>libstdc++</th><th>libc++</th></tr>
<tr><td>Concurrency: async/future/promise/packaged_task</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Concurrency: thread and related</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Concurrency: condition variables</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Concurrency: mutexes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Concurrency: atomic types and operations</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Concurrency: relaxed memory ordering and fences</td><td>Yes</td><td><strong>No</strong></td><td>Yes</td></tr>
<tr><td>Smart pointers</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Tuples</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>std::bind</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>std::function</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Regular expressions</td><td>Yes</td><td><strong>No</strong></td><td>Yes</td></tr>
<tr><td>Type traits</td><td>Yes</td><td><strong>Partial</strong></td><td>Yes</td></tr>
<tr><td>std::forward_list</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>std::array</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Hash tables</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Random number generation</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Compile time rational numbers (ratio)</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Time utilities (chrono)</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Initializer lists</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Diagnostics (system_error)</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>STL refinements and new algorithms</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>General purpose (move, forward, declval etc.)</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Minimal support for garbage collection</td><td>Yes</td><td><strong>No</strong></td><td><strong>No</strong></td></tr>
</table>

It's good to see that the language and library support is steadily improving. Clang and GCC are getting close to having full C++11 support. Visual Studio is also improving C++11 support, and I'm pleased that C++ compiler updates are happening between major releases. The list of supported features in Intel's compilers is growing longer as well.

Who knows, by next year all four compilers might support all the C++11 features!