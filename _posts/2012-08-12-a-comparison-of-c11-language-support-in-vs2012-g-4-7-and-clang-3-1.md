---
title: "A comparison of C++11 language support in VS2012, g++ 4.7 and Clang 3.1"
tags: c++11 vs2012 gcc clang
---

If you need an excuse for celebration, today happens to be an anniversary! The C++11 standard was approved by ISO on 12 August last year, exactly one year ago. I decided to take a look at the state of C++11 language support one year on across three compilers: the upcoming VS11 (Visual Studio 2012), g++ 4.7 and Clang 3.1.

Please note I didn't detail the non-language concurrency changes. Generally, support for those remains limited. 

<table style = "width: 100%">

<tr><th>Feature</th><th>VS11</th><th>g++ 4.7</th><th>Clang 3.1</th></tr>

<tr><td>auto</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>decltype</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Rvalue references and move semantics</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Lambda expressions</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>nullptr</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>static_assert</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Range based for loop</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Trailing return type in functions</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>final method keyword</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>override method keyword</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Strongly typed enums</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Forward declared enums</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>extern templates</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>&gt;&gt; for nested templates</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Local and unnamed types as template arguments</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Variadic macros</td><td>Yes</td><td>Yes</td><td>Yes</td></tr>

<tr><td>New built-in types</td><td><strong>Partial</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Initializer lists</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>explicit type conversion operators</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Inline namespaces</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>sizeof on non-static data members without an instance</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Changed restrictions on union members</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Raw string literals</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>User defined literals</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Encoding support in literals</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Arbitrary expressions in template deduction contexts</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Defaulted methods</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Deleted methods</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Non-static data member initializers</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Variadic templates</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Default template arguments in function templates</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Template aliases</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Forwarding constructors</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>noexcept</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>constexpr</td><td><strong>No</strong></td><td>Yes</td><td>Yes</td></tr>
<tr><td>Alignment support</td><td><strong>Partial</strong></td><td><strong>No</strong></td><td>Yes</td></tr>
<tr><td>Rvalue references for *this</td><td><strong>No</strong></td><td><strong>No</strong></td><td>Yes</td></tr>
<tr><td>C99 compatibility</td><td><strong>Partial</strong></td><td><strong>Partial</strong></td><td><strong>Partial</strong></td></tr>
<tr><td>Thread local storage</td><td><strong>Partial</strong></td><td><strong>Partial</strong></td><td><strong>No</strong></td></tr>
<tr><td>Inheriting constructors</td><td><strong>No</strong></td><td><strong>No</strong></td><td><strong>No</strong></td></tr>
<tr><td>Generalized attributes</td><td><strong>No</strong></td><td><strong>No</strong></td><td><strong>No</strong></td></tr>

</table>

Clang is leading with the most C++11 features implemented, and Visual Studio is unfortunately lagging behind. Still, there is a decent subset of features already available for cross-platform development using these three compilers. 

You can use type inference, move semantics and rvalue references, _nullptr_, _static_assert_, and range-based _for_ loop. 

You can have finer control over inheritance using _final_ and _override_ keywords. Enums can be strongly typed and forward declared. There are several improvements to templates, including the _extern_ keyword. 

Sadly, the much requested variadic templates aren't available in Visual Studio. Variadic macros, on the other hand, are implemented in all three compilers for C99 compatibility.

The features that aren't implemented anywhere are inheriting constructors and generalized attributes. Thread local storage is at best partially supported (via non-standard keywords). 

Overall, I think this represents good progress and shows that at least a subset of C++11 can be used today for cross-platform projects.