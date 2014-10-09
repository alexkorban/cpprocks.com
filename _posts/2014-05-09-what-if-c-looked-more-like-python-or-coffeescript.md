---
title: "What if C++ looked more like Python or CoffeeScript?"
tags: c++14 daydreaming
---

I'm quite fond of languages with minimal syntax. Not only it is easier to read and write code in these languages - it also provides an opportunity to reduce errors (both at compile time and at run time), when you consider that _every character_ in a program has the potential to cause an error due to being misread or misplaced. In addition, long, dense lines of code littered with punctuation increase the cognitive burden on the programmer. 

In this post, I would like to sketch out an idea for a modified C++ syntax with reduced noise. I don't make claims about the practicality of implementing such syntax, and it's by no means a complete solution. However, I thought that it's an interesting thought experiment, and I'd enjoy a simpler syntax if it was available.

Another thing I should mention is that it _isn't_ meant to be a backward-compatible syntax change which could be incorporated into, say, C++17. What I'm exploring is a dialect of C++ with Python-like syntax but with most of C++ constructs and semantics intact. 

The basic idea is very straightforward, and of course already applied in other languages. Remove semicolons and curly braces used to denote code blocks, make parentheses in control structures optional, make indentation significant (as in Python and CoffeeScript) - _voila_, less noise.

The code could look like this: 

{% highlight cpp %}
#include <iostream>
using namespace std

namespace utils
    template<typename T>
    auto square(const T& n)
        return n * n

int main()
    auto input = 0
    auto message = "Please enter a positive number"

    cout << message << ":" << endl
    cin >> input

    if input > 0
        cout << "Result: " << utils::square(10) << endl
    else
        cout << message << endl
    return 0
{% endhighlight %}

Generally speaking, the newline becomes the end of a statement, and indentation indicates a code block. Curly braces can still be used in initialization constructs.

Some statements (like template declarations) will still require multiple lines. This kind of syntax requires some constraints to be placed on how statements can be split across multiple lines, but I think these can align pretty well with existing good code style.

The rest of this post is about dealing with various details, e.g. where this syntax can be ambiguous. 

## Newline and semicolon as statement separators

Generally, end of the line means the end of a statement:

{% highlight cpp %}
Point<int> x
Point<double> y
Point<double> pt = x + y
{% endhighlight %}

The semicolon can be repurposed to combine multiple statements on one line:

{% highlight cpp %}
Point<int> x; Point<double> y
Point<double> pt = x + y
{% endhighlight %}

The semicolon continues to be used in the _for_ loop, so it's mostly unchanged:

{% highlight cpp %}
sregex_token_iterator token_it(begin(input), end(input), regex, 1)

for (auto it = token_it; it != sregex_token_iterator(); ++it) 
    cout << *it << endl
{% endhighlight %}

## Multiline statements

Some statements can't practically fit on one line and need to be recognized as multiline. One example is templates:

{% highlight cpp %}
template<typename T> 
struct Point
    T x, y

template<typename T>
auto square(const T& n)
    return n * n    
{% endhighlight %}

The _template_ keyword and type list must be followed by a class or function declaration on the subsequent line. 

What about a function declaration which requires more than one line? For example the return type might need to go on a separate line:

{% highlight cpp %}
template<typename T1, typename T2>
auto operator+ (const Point<T1>& lhs, const Point<T2>& rhs) ->
    Point<decltype(lhs.x + rhs.x)> 
{
    // ... implementation ...
}
{% endhighlight %}

This can be parsed by introducing a set of non-terminating symbols, i.e. symbols which can't end a statement. The arrow can be one of those. 

Sometimes it will also be necessary for function parameters or arguments to span multiple lines:

{% highlight cpp %}
template<typename T1, typename T2>
auto operator+ (
    const Point<T1>& lhs, 
    const Point<T2>& rhs) ->
    Point<decltype(lhs.x + rhs.x)> 

    // ... implementation ...

{% endhighlight %}

This could be parsed in two ways: one is that the parser keeps concatenating lines until it finds the closing parenthesis; the other is that commas become part of the non-terminating symbol set, and the parser concatenates the next line as long as the current line ends in a comma. Arguments passed when calling a function can be treated in the same way. 

Finally, what about multiline string literals? For example:

{% highlight cpp %}
    // log entry regex
    R"(([A-Z][A-Z])(\d+) "  // Flight no. "ETA "
    "(\d+:\d+) "            // ETA
    "Actual "
    "(\d+:\d+))"            // Actual time
{% endhighlight %}

I think it should be OK to concatenate them just like it's currently done in C++. Even though this could be interpreted as separate literals on subsequent lines, such an interpretation wouldn't be of much use. 

## Optional parentheses in control structures

The parentheses in control structures are optional:

{% highlight cpp %}
if input > 0
    cout << "Result: " << utils::square(10) << endl
else
    cout << message << endl

switch color 
    case WHITE: cout << "#fff"; break
    case GRAY: cout << "#c0c0c0"; break
    case BLACK: cout << "#000"; break
    default: throw runtime_error("Unrecognized color value")

for auto& elem : elements
    elem = square(elem)

try
    Autopilot autopilot
    auto engage_task = async(launch::async, &Autopilot::engage, autopilot)
    // ...
    engage_task.get();  // throws an exception 
catch const runtime_error& e
    cout << e.what() << endl

auto i = 0
do
    cout << "*"
    cout << "-"
while i++ < 10
{% endhighlight %}

The handling of the _while_ in _do-while_ is similar to how this is handled in normal C++:

{% highlight cpp %}
do i++;
while (i < 10);
{% endhighlight %}    

Sometimes the expressions have to be quite long:

{% highlight cpp %}
while diagnostics_task.wait_for(seconds(0)) == future_status::timeout ||
    autopilot_task.wait_for(seconds(0)) == future_status::timeout ||
    controls_task.wait_for(seconds(0)) == future_status::timeout

    cout << "."
    this_thread::sleep_for(milliseconds(500))

cout << "System ready." << endl
{% endhighlight %}

There are two ways of handling this situation. The set of non-terminating symbols includes the operators, so lines will be concatenated as long as they end in operators. 

Alternatively, the condition can be enclosed in parentheses, which provides the option of breaking it up freely, just like in normal C++:

{% highlight cpp %}
while (diagnostics_task.wait_for(seconds(0)) == future_status::timeout 
      || autopilot_task.wait_for(seconds(0)) == future_status::timeout 
      || controls_task.wait_for(seconds(0))  == future_status::timeout)

    cout << "."
    this_thread::sleep_for(milliseconds(500))
{% endhighlight %}

## Declarations vs definitions

Suppose we have a function declaration and a definition:

{% highlight cpp %}
auto f(int n)
{}

auto g(int n);
{% endhighlight %}

After removing semicolons and braces they become ambiguous:

{% highlight cpp %}
auto f(int n)    // declaration or definition?

auto g(int n)
{% endhighlight %}

A backslash can be used to distinguish a function with an empty body:

{% highlight cpp %}
auto f(int n) \     // definition

auto g(int n)       // declaration
{% endhighlight %}

The only requirement is to have a blank line following the backslash as that will be considered the function body. One problem with the backslash is that using it this way conflicts with the currently existing translation rules, so perhaps another symbol is necessary. But in any case, the point is that I need some way of specifying an empty block instead of _{}_.

The same principle applies to classes: 

{% highlight cpp %}
class A \           // empty class

class B             // class declaration
{% endhighlight %}

## Method access specifiers

There are two options of formatting access specifiers. The obvious one is to indent them to the same level as the rest of the class body:

{% highlight cpp %}
class JetPlane : public Plane

    vector<Engine> _engines

    public:

    JetPlane(const string& model = "") : Plane(model) \

    auto require_service(const date_t& date) \

    protected:

    auto load_person(const Person& person) 
        _passengers.push_back(person)
{% endhighlight %}

However, the prevalent existing style appears to be to align access specifiers with the _class_ specifiers, so this could be allowed a special case instead: 

{% highlight cpp %}
class JetPlane : public Plane
    vector<Engine> _engines

public:    
    JetPlane(const string& model = "") : Plane(model) \

    auto require_service(const date_t& date) \

protected:
    auto load_person(const Person& person) 
        _passengers.push_back(person)
{% endhighlight %}

## Lambdas

A lot of the syntax can be translated in a straightforward manner. However, lambda expressions are one of the trickier cases. Let's take an example:

{% highlight cpp %}
template<typename Cont>
auto print(const Cont& cont)
    for_each(begin(cont), end(cont), 
        [] (auto elem) cout << elem << endl)
{% endhighlight %}

I've removed the braces around the body of the lambda given to _for_each_. There was actually a [proposal](http://www.open-std.org/JTC1/SC22/WG21/docs/papers/2013/n3560.pdf) to allow lambda body to be an expression which suggested introducing this kind of syntax into C++14, so this is possible to parse - subject to some restrictions. 

However, lambda expressions can be a lot more complex than this, with multiple statements in the body and so on.

In a more complex scenario, lambda bodies could be demarcated via indentation:

{% highlight cpp %}
// update the screens in an airplane cabin
for_each(cabins.begin(), cabins.end(), [=](auto& cabin) 
    for_each(cabin.seats().begin(), cabin.seats().end(),
        [=](auto& seat_screen)
            seat_screen.set_mode(mode)
            seat_screen.refresh()
    )
)
{% endhighlight %}

While we're at it, here is another potentially problematic situation: a function taking two callbacks as arguments. I could pass two lambdas to it:

{% highlight cpp %}
make_request([], [])
{% endhighlight %}

Note that an empty lambda is now reduced to the introducer. What if I want something more elaborate? For example:

{% highlight cpp %}
make_request([] 
    cout << "Request successful" << endl
    , 
    [] 
        cout << "Request failed" << endl
)
{% endhighlight %}

If I try to use indentation for lambda bodies, it looks pretty ugly and confusing. Instead, it's better to allow an unindented leading comma (this is the CoffeeScript solution):

{% highlight cpp %}
make_request([] 
    cout << "Request successful" << endl
, [] 
    cout << "Request failed" << endl
)
{% endhighlight %}

What about a lambda which is defined and called immediately?

{% highlight cpp %}
auto a = 10;
const auto const_val = [&] { return a; }();
{% endhighlight %}

After removing semicolons and braces, I get this:

{% highlight cpp %}
auto a = A(10)
const auto const_val = [&] return a ()  // potentially ambiguous
{% endhighlight %}

This is potentially ambiguous, so the lambda needs to be wrapped in parentheses:

{% highlight cpp %}
auto a = A(10)
const auto const_val = ([&] return a)()  // OK, the function call operator
                                         // is applied to the lambda
{% endhighlight %}

## Standalone code blocks

Standalone code blocks are occasionally useful for scoping. But without curly braces, code blocks like this would merge into one:

{% highlight cpp %}
auto test_log()
{
    {
        std::ifstream ifs(log_file_name_rotated.c_str());
        ASSERT_TRUE(stream.is_open());
    }

    {
        std::ifstream stream(log_file_name.c_str());
        ASSERT_TRUE(stream.is_open());
    }
}
{% endhighlight %}

Relying on indentation isn't going to be enough here, I need to tell the parser that a new block started. A backslash on its own line can be used for that (with the same caveat as before - another character may be better):

{% highlight cpp %}
auto test_log()
    \
        std::ifstream ifs(log_file_name_rotated.c_str())
        ASSERT_TRUE(stream.is_open())

    \
        std::ifstream stream(log_file_name.c_str())
        ASSERT_TRUE(stream.is_open())
{% endhighlight %}

## Things I don't know how to handle

One thing I don't know how to handle is unnamed types:

{% highlight cpp %}
struct
{
    int x;
    void print() const
    {
        cout << x; 
    }
} a;
{% endhighlight %}

I don't see an easy way of delimiting the type definition from the variable without adding a new keyword or a symbol to mark an anonymous type. Personally, I'd be happy to live without anonymous types in exchange for the other benefits, but this may not be a good tradeoff for other people.

## Syntactic constructs that wouldn't work in C++

There are a few other constructs which are useful in other languages, so I considered them briefly, but decided they wouldn't work or wouldn't add much value in the C++ context.

### Treating everything as an expression and optional returns

Another thing CoffeeScript and Ruby do is treating everything as an expression: 

{% highlight ruby %}
message = if lander_count > 10
    "Launching probe"
else
    "Waiting for probes to return"
end
{% endhighlight %}

Consequently, methods return the result of the last expression, and the _return_ keyword is optional:

{% highlight ruby %}
def can_land?
    landers.count > 0  # returns the result of evaluating expression
end
{% endhighlight %}

However, I think this kind of change would be too drastic, and it has performance implications which make it undesirable in C++. 

#### Array comprehensions

CoffeeScript's _for_ is a mechanism for array comprehensions rather than a simple loop:

{% highlight coffeescript %}
foods = ['broccoli', 'spinach', 'chocolate']
eat(food) for food in foods when food != 'chocolate'
{% endhighlight %}

While this is nice, I don't think this would add that much to the range-based _for_ syntax and functional style iteration already available in C++.

#### No distinction between definition and assignment

CoffeeScript doesn't make a distinction between variable definition and assignment, so that _a = 10_. It makes the syntax simpler, but it also makes shadowing impossible:

{% highlight coffeescript %}
a = 10  # new variable
func = -> 
    a = 20  # assigns to the same a as above
{% endhighlight %}

This is a deliberate choice on the part of CoffeeScript's creator. I think it's dubious even in CoffeeScript, and would be worse in C++. 

### A longer example

Finally, here is a longer example of the new syntax and the normal C++ syntax side by side. This is based on some code I borrowed from the MongoDB project. In addition to using the new syntax on the left side, I made a few C++11/14 style changes. 

<style>
.smallCode code {font-size:10px !important;}
</style>

<div class = "smallCode" style = "float: left; width: 49%">
{% highlight cpp %}
/*    Copyright 2013 10gen Inc.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *    http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */

#pragma once

#include <algorithm>
#include <cstdlib>

#include "mongo/base/status.h"
#include "mongo/logger/message_log_domain.h"

/*
 * Implementation of LogDomain<E>.  Include this in cpp files to instantiate new 
 * LogDomain types. See message_log_domain.h, e.g.
 */

namespace mongo 
    namespace logger 

        template <typename E>
        LogDomain<E>::LogDomain() :
            _minimumLoggedSeverity(LogSeverity::Log()), _abortOnFailure(false) \

        template <typename E>
        LogDomain<E>::~LogDomain() 
            clearAppenders()

        template <typename E>
        auto LogDomain<E>::append(const E& event) 
            for const auto& appender : _appenders
                if appender 
                    Status status = appender->append(event)
                    if !status.isOK()
                        if _abortOnFailure
                            ::abort()
                        return status

            return Status::OK()

        template <typename E>
        auto LogDomain<E>::attachAppender(
            typename LogDomain<E>::AppenderAutoPtr appender) 

            auto iter = std::find(_appenders.begin(), _appenders.end(), nullptr)

            if iter == _appenders.end()
                _appenders.push_back(appender.release())
                return AppenderHandle(_appenders.size() - 1)
            else 
                *iter = appender.release()
                return AppenderHandle(iter - _appenders.begin())

        template <typename E>
        auto LogDomain<E>::detachAppender(
            typename LogDomain<E>::AppenderHandle handle) 

            auto& appender = _appenders.at(handle._index)
            AppenderAutoPtr result(appender)
            appender = nullptr
            return result

        template <typename E>
        auto LogDomain<E>::clearAppenders() 
            for const auto& appender : _appenders
                delete appender
            _appenders.clear()
{% endhighlight %}
</div>
<div class = "smallCode" style = "float: right; width: 50%">
{% highlight cpp %}
/*    Copyright 2013 10gen Inc.
 *
 *    Licensed under the Apache License, Version 2.0 (the "License");
 *    you may not use this file except in compliance with the License.
 *    You may obtain a copy of the License at
 *
 *    http://www.apache.org/licenses/LICENSE-2.0
 *
 *    Unless required by applicable law or agreed to in writing, software
 *    distributed under the License is distributed on an "AS IS" BASIS,
 *    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *    See the License for the specific language governing permissions and
 *    limitations under the License.
 */

#pragma once

#include <algorithm>
#include <cstdlib>

#include "mongo/base/status.h"
#include "mongo/logger/message_log_domain.h"

/*
 * Implementation of LogDomain<E>.  Include this in cpp files to instantiate new 
 * LogDomain types. See message_log_domain.h, e.g.
 */

namespace mongo {
namespace logger {

    template <typename E>
        LogDomain<E>::LogDomain() 
        : _minimumLoggedSeverity(LogSeverity::Log()), _abortOnFailure(false) 
    {}

    template <typename E>
    LogDomain<E>::~LogDomain() {
        clearAppenders();
    }

    template <typename E>
    Status LogDomain<E>::append(const E& event) {
        for (typename AppenderVector::const_iterator iter = _appenders.begin();
             iter != _appenders.end(); ++iter) {

            if (*iter) {
                Status status = (*iter)->append(event);
                if (!status.isOK()) {
                    if (_abortOnFailure) {
                        ::abort();
                    }
                    return status;
                }
            }
        }
        return Status::OK();
    }

    template <typename E>
    typename LogDomain<E>::AppenderHandle LogDomain<E>::attachAppender(
            typename LogDomain<E>::AppenderAutoPtr appender) {

        typename AppenderVector::iterator iter = std::find(
                _appenders.begin(), _appenders.end(),
                static_cast<EventAppender*>(NULL));

        if (iter == _appenders.end()) {
            _appenders.push_back(appender.release());
            return AppenderHandle(_appenders.size() - 1);
        }
        else {
            *iter = appender.release();
            return AppenderHandle(iter - _appenders.begin());
        }
    }

    template <typename E>
    typename LogDomain<E>::AppenderAutoPtr LogDomain<E>::detachAppender(
            typename LogDomain<E>::AppenderHandle handle) {

        EventAppender*& appender = _appenders.at(handle._index);
        AppenderAutoPtr result(appender);
        appender = NULL;
        return result;
    }

    template <typename E>
    void LogDomain<E>::clearAppenders() {
        for(typename AppenderVector::const_iterator iter = _appenders.begin();
            iter != _appenders.end(); ++iter) {

            delete *iter;
        }

        _appenders.clear();
    }

}  // namespace logger
}  // namespace mongo
{% endhighlight %}
</div>

<div style = "clear: both"></div>

I think the code on the left is much easier to understand thanks to reduced clutter. It also means that bugs would be easier to spot, and it would be faster to scan through, e.g. when looking for a particular function. 

Another thing to note is that the new syntax results in substantial savings in terms of line count.  If I don't include the large comment at the top, the normal C++ code is 25% longer than my syntax. That's despite the fact that it uses Java-style formatting for curly braces. If opening curly braces were on their own lines, the difference would be even more significant. 

I believe this vertical compression is valuable, because it can make a difference between seeing a whole function or only part of it, a whole algorithm or only a portion. Seeing the whole thing at once makes it easier to understand it and reason about it. 

### The end

Are there other problems with parsing this syntax which I haven't thought of? This is C++, so I'm sure there are! Even without trying to make significant changes to the syntax, C++ parsing is a complicated affair. Plus of course, C++ was never designed with significant whitespace in mind. 

My intent was to see if this kind of change would be at all plausible, and what it would make the code look like. It seems that it could work, but it would likely require constraints to be placed on some of the things which can be written in regular C++.

If you spotted a particular problem, feel free to point it out in the comments.