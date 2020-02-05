---
title: "C++11/14 compiler and library shootout"
tags: c++11 c++14
---

It's been almost a year since my [last comparison](/c11-compiler-support-shootout-visual-studio-gcc-clang-intel/ "C++11 compiler shootout") of C++11 support across different compilers, so I decided to take a break from working on my book about [C++11/14 features in VS2013](/vs2013-edition), and see how things have changed. 

Once again, I'd like to see how different compilers stack up based on the documentation for the pre-release versions of these compilers. I've also included some of the current released versions for comparison.

Of course, now we have **C++14** to think about in addition to C++11, so I've included  C++14 in this comparison too. 

The pre-release versions of the compilers are as follows: GCC 4.9, Clang 3.4 and Visual Studio Nov 2013 CTP.

First, let's look at the C++11 language features. **Clang 3.3 and above**, and **GCC 4.8 and later** have complete support, so there was no point including them in the table.

## C++11 language features support

<style media="screen" type="text/css">
table.shootoutTable td {
  color: #00aa00;
  text-align: center;
}
table.shootoutTable td.no, table.shootoutTable td.no > strong {
  color: #aa0000;
}
table.shootoutTable td.label {
  color: #000000;
  text-align: left;
}
</style>

<table class = "shootoutTable">

<tr><th>Feature</th><th>VS Nov
2013 CTP</th><th>VS2013</th><th>Intel 14.0</th></tr>

<tr><td class = "label">auto</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">decltype</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Lambda expressions</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">nullptr</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">static_assert</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Range based for loop</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Trailing return type
in functions</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">extern templates</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">&gt;&gt; for nested templates</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Local and unnamed types
as template arguments</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Variadic macros</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Variadic templates</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Default template arguments
in function templates</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">final method keyword</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">override method keyword</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Strongly typed enums</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Forward declared enums</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Explicit type conversion
operators</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Raw string literals</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Delegating constructors</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Template aliases</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Non-static data member
initializers</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Deleted methods</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Initializer lists</td><td>Yes</td><td><strong>Partial</strong></td><td>Yes</td></tr>

<tr><td class = "label">Rvalue references and
move semantics</td><td>Yes</td><td><strong>Partial</strong></td><td>Yes</td></tr>

<tr><td class = "label">Defaulted methods</td><td>Yes</td><td><strong>Partial</strong></td><td>Yes</td></tr>

<tr><td class = "label">C99 compatibility</td><td><strong>Partial</strong></td><td><strong>Partial</strong></td><td>Yes</td></tr>

<tr><td class = "label">New built-in types</td><td><strong>Partial</strong></td><td><strong>Partial</strong></td><td><strong>Partial</strong></td></tr>

<tr><td class = "label">Member function
ref qualifiers</td><td>Yes</td><td class = "no"><strong>No</strong></td><td>Yes</td></tr>

<tr><td class = "label">noexcept</td><td><strong>Partial</strong></td><td class = "no"><strong>No</strong></td><td>Yes</td></tr>

<tr><td class = "label">constexpr</td><td><strong>Partial</strong></td><td class = "no"><strong>No</strong></td><td>Yes</td></tr>

<tr><td class = "label">Alignment support</td><td>Yes</td><td><strong>Partial</strong></td><td class = "no"><strong>No</strong></td></tr>

<tr><td class = "label">Magic statics</td><td>Yes</td><td class = "no"><strong>No</strong></td><td><strong>Partial</strong></td></tr>

<tr><td class = "label">Thread local storage</td><td><strong>Partial</strong></td><td><strong>Partial</strong></td><td class = "no"><strong>No</strong></td></tr>

<tr><td class = "label">Generalized attributes</td><td class = "no"><strong>No</strong></td><td class = "no"><strong>No</strong></td><td>Yes</td></tr>

<tr><td class = "label">Inline namespaces</td><td class = "no"><strong>No</strong></td><td class = "no"><strong>No</strong></td><td>Yes</td></tr>

<tr><td class = "label">sizeof on non-static data
members without an instance</td><td>Yes</td><td class = "no"><strong>No</strong></td><td class = "no"><strong>No</strong></td></tr>

<tr><td class = "label">Encoding support in literals</td><td class = "no"><strong>No</strong></td><td class = "no"><strong>No</strong></td><td>Yes</td></tr>

<tr><td class = "label">Arbitrary expressions in
template deduction contexts</td><td class = "no"><strong>No</strong></td><td class = "no"><strong>No</strong></td><td>Yes</td></tr>

<tr><td class = "label">Inheriting constructors</td><td>Yes</td><td class = "no"><strong>No</strong></td><td class = "no"><strong>No</strong></td></tr>

<tr><td class = "label">Changed restrictions on
union members</td><td class = "no"><strong>No</strong></td><td class = "no"><strong>No</strong></td><td><strong>Partial</strong></td></tr>

<tr><td class = "label">User defined literals</td><td class = "no"><strong>No</strong></td><td class = "no"><strong>No</strong></td><td class = "no"><strong>No</strong></td></tr>

</table>

It looks like the Intel compiler is on par with the Visual Studio CTP. But Intel's 14.0 compiler has been out for some time, while VS CTP is an alpha quality compiler, which means that Visual Studio trails behind everybody else, unfortunately. 

How about C++14 support? The C++ standard is still in draft, but some compilers already have extensive support for C++14 language features:

## C++14 language features support

<table class = "shootoutTable">

<tr><th>Feature</th><th>Clang 3.4</th><th>GCC 4.9</th><th>VS Nov
2013 CTP</th><th>Intel 14.0</th></tr>

<tr><td class = "label">Return type deduction for regular functions</td><td>Yes</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>

<tr><td class = "label">Binary literals</td><td>Yes</td><td>Yes</td><td class = "no">No</td><td>Yes</td></tr>

<tr><td class = "label">Generic lambdas</td><td>Yes</td><td>Yes</td><td><strong>Partial</strong></td><td class = "no">No</td></tr>

<tr><td class = "label">Tweaked wording for contextual conversions</td><td>Yes</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td></tr>

<tr><td class = "label">Runtime sized arrays with automatic storage duration</td><td>Yes</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td></tr>

<tr><td class = "label">Initialized lambda captures</td><td>Yes</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td></tr>

<tr><td class = "label">[[deprecated]] attribute</td><td>Yes</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td></tr>

<tr><td class = "label">Single quotation mark as digit separator</td><td>Yes</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td></tr>

<tr><td class = "label">C++ sized deallocation</td><td>Yes</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td></tr>

<tr><td class = "label">Variable templates</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td><td class = "no">No</td></tr>

<tr><td class = "label">Relaxed requirements on constexpr functions</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td><td class = "no">No</td></tr>

<tr><td class = "label">Member initializers and aggregates</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td><td class = "no">No</td></tr>

<tr><td class = "label">Avoiding/fusing memory allocations</td><td>Yes</td><td class = "no">N/A</td><td class = "no">No</td><td class = "no">No</td></tr>

</table>

As far as libraries go, libstdc++, libc++ and Microsoft's libraries all have at least partial support for all the features introduced in C++11. So instead of detailing those, I'll focus on C++14 features. Once again, this is for the latest pre-release versions of the libraries available today:

## C++14 library features support

<table class = "shootoutTable">

<tr><th>Feature</th><th>libc++</th><th>libstdc++</th><th>VS Nov
2013 CTP</th></tr>

<tr><td class = "label">make_unique</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Improved operator functors</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Additional template aliases for transformation type traits</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td class = "label">Fixing constexpr member functions without const</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>

<tr><td class = "label">exchange() utility function</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">Retrieving tuple elements by type</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">std::result_of and SFINAE</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">Improvements to integral_constant</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">User-defined literals for standard library types
</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">More robust non-modifying sequence operations</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">Quoted string I/O manipulator</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">constexpr library additions: chrono</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">constexpr library additions: containers</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">constexpr library additions: utilities</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">constexpr library additions: complex</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td></tr>
<tr><td class = "label">constexpr library additions: functional</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td></tr>
<tr><td class = "label">Compile-time integer sequences</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">Shared locking</td><td>Yes</td><td>Yes</td><td class = "no">No</td></tr>
<tr><td class = "label">Heterogeneous comparison lookup in associative containers</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td></tr>
<tr><td class = "label">Null forward iterators</td><td>Yes</td><td class = "no">No</td><td class = "no">No</td></tr>
<tr><td class = "label">Sized deallocation</td><td class = "no">No</td><td class = "no">No</td><td class = "no">No</td></tr>
<tr><td class = "label">Consistent metafunction aliases</td><td class = "no">No</td><td class = "no">No</td><td class = "no">No</td></tr>
<tr><td class = "label">Discouraging rand() in C++14</td><td class = "no">No</td><td class = "no">No</td><td class = "no">No</td></tr>

</table>

## Conclusion

Clang is obviously leading on the C++14 conformance front. There are also quite a few features implemented in GCC as well. 

The interesting thing is that the process of building compilers to conform to the latest C++ standard has accelerated in recent years. With C++14, Clang has complete language support even before the standard has been finalized. 

But it would be really great if Microsoft and Intel picked up their game, and brought their compilers' conformance to the level of Clang and GCC. 