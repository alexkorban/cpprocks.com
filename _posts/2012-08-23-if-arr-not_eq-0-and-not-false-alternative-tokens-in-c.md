---
title: "Alternative tokens in C++"
tags: c++
---

`if (arr<:0:> not_eq 0 and not false)`

This line of code is actually C++. I recently noticed a small section called _Alternative tokens_ in the standard (it's 2.6, for the curious) which allows this sort of shenanigans. 

The following code will probably look slightly alien to you, like a weird C++ knockoff, but it's a standard conforming C++ program:

{% highlight cpp %}
%:include <iostream>

using namespace std;

int main()
<%
        int arr<:2:> = <% 1, 1 %>;

        if (arr<:0:> not_eq 0 and not false)
                cout << "Hey, this works!" << endl;
        return 0;
%>
{% endhighlight %}

This program is compiled happily by GCC and Clang. Visual Studio 2010 allows this code when it's compiled with /Za. Without /Za, it's still possible to use the alternative operators (but not the digraphs) in Visual Studio by including &lt;iso646.h&gt; (thanks to Vasily Milanich for pointing it out to me). 

The C++ standard provides a set of alternative tokens for some operators and other symbols. Here is the complete list:

<table>
<tr><th>Primary</th><th>Alternative</th><th>Primary</th><th>Alternative</th></tr>
<tr>
<td>{</td><td>&lt;%</td><td>&</td><td>bitand</td>
</tr><tr>
<td>}</td><td>%&gt;</td><td>|</td><td>bitor</td>
</tr><tr>
<td>[</td><td>&lt;:</td><td>^</td><td>xor</td>
</tr><tr>
<td>]</td><td>:&gt;</td><td>~</td><td>compl</td>
</tr><tr>
<td>#</td><td>%:</td><td>&=</td><td>and_eq</td>
</tr><tr>
<td>##</td><td>%:%:</td><td>|=</td><td>or_eq</td>
</tr><tr>
<td>&&</td><td>and</td><td>!=</td><td>not_eq</td>
</tr><tr>
<td>||</td><td>or</td><td>^=</td><td>xor_eq</td>
</tr><tr>
<td>!</td><td>not</td>
</tr>
</table>

According to [http://www.lysator.liu.se/c/na1.html](http://www.lysator.liu.se/c/na1.html) (thanks to Matt Healy for finding the page), these tokens are the result of a "Danish proposal" to make C programs more visually appealing on terminals that only offered the 7-bit ISO 646 character set which didn't have curly braces, for example.

The advantages of using the alternative tokens are that they might make code look more appealing to curly brace haters, and the alternative logical operators will make Python programmers feel more at home. They will also come in handy for a code obfuscation contest. 

The disadvantages probably include a lot of WTF comments from other C++ developers.

Jokes aside, I haven't seen any of these in real world code. A few people on [r/cpp](http://www.reddit.com/r/cpp/comments/ypzrs/unusual_c_if_arr0_not_eq_0_and_not_false_cout/ "r/cpp discussion comments") said they use or advocate them, however. 

This made me wonder if the alternative operator tokens can or should be used more widely (I'm pretty sure using the digraph alternatives for curly braces etc. isn't a good idea). In particular, _not_, _and_ and _or_ are readable, obvious and not verbose.

For anyone who sees the alternative operators for the first time, I think there'll be quite a lot of confusion, followed by trying to figure out how they work. I'm not sure people will immediately check the standard, so they might waste some time on investigation, e.g. if they decide the operators work via preprocessor magic within the code base.

After the initial confusion though, it's really obvious what the alternative operators are. So perhaps it's not too big a deal if someone just starts using them?

On the other hand, the use of `!`, `&&` and `||` is so entrenched that it might be difficult for people to adjust to seeing English words in their place. 

Do you think alternative operator tokens should be used more?