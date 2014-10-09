---
title: Spotlight on libraries relying on C++14
tags: c++14
---

While working on the C++14 coverage for the [Clang edition of my book](/clang-edition), I'm also taking a look at the C++14 code in the wild, and I thought I'd write up what I've found.

There are, of course, implementations of the C++ standard library which by necessity rely on C++14 features. libc++ is complete with regards to C++14, and libstdc++ & Microsoft's implementation have implemented the changes partially at this stage. 

There aren't many other libraries available at this point in time, but I did find a few. A lot of them aren't fit for production use as yet, but it's worth reading the code to see how people use C++14.

I included some code samples from the documentation but bear in mind that I haven't actually used these libraries, so I don't know how well they function in practice.

If you use Clang, you should have no problem compiling the libraries. GCC 4.9 may also work. Check out [this post](/c1114-compiler-and-library-shootout/ "C++11/14 compiler and library shootout") for a comparison of C++14 support across compilers.

## [Streams](https://github.com/jscheiny/Streams)

Streams is a header-only C++14 library for functional style transformations and lazy evaluation. It allows you to do things like this:

{% highlight cpp %}
int heads = stream::MakeStream::coin_flips() // Uniform random bools
    .limit(1000) // Take the first 1000 coin flips
    .filter()    // Take only the true ones (heads)
    .count();
{% endhighlight %}

It also provides _set_ and _vector_ operations:

{% highlight cpp %}
std::set<Gadget> left = /* ... */
std::set<Gadget> right = /* ... */

std::set<Gadget> result =
    Stream<Gadget>(left).intersect_with(Stream<Gadget>(right))
    .to_set();
{% endhighlight %}

{% highlight cpp %}
std::vector<double> vec1 = /* ... */
std::vector<double> vec2 = /* ... */;

auto stream = [](auto& x) {
    return stream::MakeStream::from(x);
};

auto sum = (stream(vec1) + stream(vec2)).to_vector();
auto scale = (stream(vec1) * 5).to_vector();
auto translate = (stream(vec1) + 10).to_vector();
int dot_product = (stream(vec1) * stream(vec2)).sum();
{% endhighlight %}

## [Sprout](https://github.com/bolero-MURAKAMI/Sprout)

This is a compile-time library which implements _constexpr_ containers and algorithms. 

It allows you to do things like this:

{% highlight cpp %}
SPROUT_STATIC_CONSTEXPR auto input = sprout::array<int, 10>{ {1, 2, 3, 4, 5, 6, 7, 8, 9, 10} };

// count odd elements
SPROUT_STATIC_CONSTEXPR auto result = sprout::count_if(begin(input), end(input), bind2nd(modulus<>(), 2));
static_assert(result == 5, "counted 5 elements of odd number from input.");

// find min and max elements
SPROUT_STATIC_CONSTEXPR auto result = sprout::minmax_element(begin(input), end(input));
static_assert(*result.first == 1, "min element is 1.");
static_assert(*result.second == 10, "max element is 10.");
{% endhighlight %}

It also implements compile-time strings:

{% highlight cpp %}
#include <sprout/string.hpp>
using namespace sprout;

SPROUT_STATIC_CONSTEXPR auto x = to_string("hello ");
SPROUT_STATIC_CONSTEXPR auto y = to_string("world");
SPROUT_STATIC_CONSTEXPR auto z = x + y;
static_assert(z == "hello world", "Should be: hello world.");
{% endhighlight %}

## [Mettle](https://github.com/jimporter/mettle)

Mettle is a unit testing library which uses C++14's generic lambdas for test cases.

Here is an example from its documentation:

{% highlight cpp %}
#include <mettle.hpp>
using namespace mettle;

suite<> basic("a basic suite", [](auto &_) {

    _.test("a test", []() {
        expect(true, equal_to(true));
    });

    _.skip_test("a skipped test", []() {
        expect(3, any(1, 2, 4));
    });

    for(int i = 0; i < 4; i++) {
        _.test("test number " + std::to_string(i), [i]() {
           expect(i % 2, less(2));
        });
    }

});
{% endhighlight %}

## [JeayeSON](https://github.com/jeaye/jeayeson)

JeayeSON is a header-only library for reading, writing and interacting with JSON with a typesafe C++14 interface. Here is an example from its readme:

{% highlight cpp %}
/* To start with, create a map and load a file. */
json_map map{ json_file{ "src/tests/json/main.json" } };

/* We can look at some specify top-level values with "get".
     Notice that "get" returns a reference to the object. */
std::string &str(map.get<std::string>("str")); // Get "str" as a mutable string reference.
std::cout << "str = " << str << std::endl;
auto &arr(map.get<json_array>("arr"));

/* A fallback value can also be specified with "get". It does two things:
       1. Helps deduce the type so that an explicit invocation is not needed
       2. Provides a default fallback value, should anything go wrong while accessing
     Note that these functions do NOT return references, due to incompatibilities with the fallback. */
std::string const str_copy{ map.get("str", "Default awesomeness") }; // Second param is the default

/* Delving into maps using dot-notated paths works, too.
     The type can be explicitly specified, or implicit based on the provided fallback.
     They default to json_value, which offers op==, op<<, et cetera. */
std::cout << map.get_for_path("person.name") << " has " // No fallback, returns json_value&
          << map.get_for_path("person.inventory.coins", 0) << " coins\n"; // Fallback is 0

/* A less verbose way is to just use op[] on the json_values; this is more convenient,
   * but it comes at the cost of less type-safety and more runtime checks. */
std::cout << map["person"]["inventory"]["coins"] << std::endl;
std::cout << map["arr"][1] << std::endl;

/* Iterators work as expected, based on the C++ stdlib. (const and non-const) */
for(auto const &it : arr) { std::cout << it.as<json_float>() << " "; }
std::cout << std::endl;
{% endhighlight %}

## [jbson](https://github.com/chrismanning/jbson)

jbson is a header-only library for building & iterating BSON data, and JSON documents in C++14. It also parses MongoDB extended JSON.

Here is what it looks like:

{% highlight cpp %}
// building a document
using namespace jbson;
auto str = "str"s;
int num = 123;

document doc = builder
    ("some string", str)
    ("some int", num)
    ("some obj", builder
        ("child bool", false)
    );
{% endhighlight %}

{% highlight cpp %}
// parsing JSON
using jbson::literal;
auto doc = R"({
    "some json": 123
})"_json_doc;

// iteration
for_each(doc.begin(), doc.end(), [](auto&& v) { something(v); });
{% endhighlight %}

## [FP](https://github.com/mizvekov/fp)

This is a C++14 header-only arithmetic library. It provides a couple of template wrappers for numeric types.

The first template, _fp_, provides fixed point arithmetic support:

{% highlight cpp %}
fp<int, 4> x = 3.25;
fp<char, 8> y = 0.75;

auto z = x * y;
//now z is of type fp<int, 12>
std::cout << double(z);
//prints 2.4375
{% endhighlight %}

The second template, _range_, provide range checking for the wrapped type by implementing interval arithmetic at compile time:

{% highlight cpp %}
auto test1 = ranged<int32_t, -20, 50>{ 10 };
auto test2 = ranged<int32_t, 1000, 10000>{ 2000 };
auto test3 = ranged<int32_t, -3000, 8000>{ 100 };
// the following will cause a compile error because the result 
// can be as big as 4'000'000'000, which is above MAX_INT:
auto test4 = test1 * test2 * test3;

auto test1 = ranged<int, -20, 50>{ 10 };
// the following assignment will cause a compile error 
// because test1 could have a value up to 50, 
// but test2 only accepts values up to 49:
ranged<int, -20, 49> test2 = test1;
{% endhighlight %}

## [Polyop](https://github.com/Manu343726/Polyop)

The goal of this library is to provide a universal mechanism for operator overloading (including built-in operators) with no runtime overhead. It also allows partial operator application and storing operator expressions for later evaluation.

Here is an example:

{% highlight cpp %}
int a = 1, b = 2;

auto comp = pop::wrap(a) == b; // The comparison is not executed but stored in comp.
bool r1 = comp(); // Call the comparison.
auto expression = __ == __;          // Store a naked comparison expression
auto partial_call = expression(a); // Pass the first argumment to the expression
bool r2 = partial_call(b);         // Pass the last argumment to the expression (Then calling the operator).

bool lex_result = (pop::wrap(a) == b).context(lexicographical); // Applies a "lexicographical" comparison context.
{% endhighlight %}

## [Video++](https://github.com/matt-42/vpp)

Video++ "is a video and image processing library that takes advantage of the C++11 and C++14 standard to ease the writing of fast parallel real-time video and image processing." 

This is a sample from its documentation:

{% highlight cpp %}
// A parallel implementation of a box_filter using Video++.

image2d<int> A(1000, 1000);
image2d<int> B(A.domain());

auto nbh = box_nbh2d<int, 3, 3>(A);

// Parallel Loop over pixels of in and out.
pixel_wise(A, B) << [&] (int& a, int& b) {
    int sum = int;

    // Loop over neighbors wrt nbh to compute a sum.
    nbh(a) < [&] (int& n) sum += n;

    // Write the sum to the output image.
    b = (sum / 3);
};
{% endhighlight %}

One of the features it provides is compatibility with with OpenCV image types:

{% highlight cpp %}
// Load JPG image in a vpp image using OpenCV imread.
image2d<vuchar3> img = from_opencv<vuchar3>(cv::imread("image.jpg"));

// Write a vpp image using OpenCV imwrite.
cv::imwrite("in.jpg", to_opencv(img));
{% endhighlight %}

## [iod](https://github.com/matt-42/iod)

This library takes an interesting approach of providing a DSL for inline object definitions and declarations with static introspection for C++11/14. 
It allows you to define and instantiate a C++ object inline, with no runtime overhead. It also allows extending such objects as you'll see in the example below. 

One obvious downside is that all attribute names have to be declared beforehand:

{% highlight cpp %}
// Declaration of the attributes used by the inline object definitions.
iod_define_attribute(name);
iod_define_attribute(age);
iod_define_attribute(cars);
iod_define_attribute(model);
iod_define_attribute(cities);
iod_define_attribute(lastname);

int main()
{
    // Inline object definition.
    auto person = iod(
        *name = "Philippe", // Stared fields are serialized.
        *age = 42,
        inc_age = [] (auto& self, int inc) { self.age += inc; }, // Requires C++14.
        *cities = {"Paris", "Toronto", "New York City"},
        *cars = {
            iod(*name = "Renault", model = "Clio"), // All elements of an array must have the same type.
            iod(*name = "Mercedes", model = "Class A")
        }
    );

    // Access to the content of the object.
    std::cout << person.name << std::endl;
    std::cout << person.cars[1].model << std::endl;

    // Serialize an object to json.
    std::string json = iod_to_json(person);
    std::cout << json << std::endl;

    // Extend and object. (todo)
    auto extended_person = iod_extend(person, iod(lastname = "Doe"));
}
{% endhighlight %}

## [formatstring](https://github.com/panzi/formatstring)

This is a C++14 type safe format string library heavily inspired by Python's _str.format()_ function:

{% highlight cpp %}
// create a temp. formatting object that gets written to cout
std::cout << format("hex: {:#x}, centerd: {:_^20}, padded: {:+010.3f}\n",
                    1234, "test", 3.14159);

// create a temp. formatting object the gets converted into a string
std::string s1 = format("{:d} {:c}", 'A', 66);

// convert a single value to a string
std::string s2 = hex(1234).fill('0', 20).alt();
{% endhighlight %}

## [Hana](https://github.com/ldionne/hana)

This library is an attempt to merge Boost.Fusion and the Boost.MPL into a single metaprogramming library. It carries an explicit disclaimer that it isn't fit for production use, and it seems to be under active development.

It allows you to perform advanced compile time computation, for example:

{% highlight cpp %}
BOOST_HANA_STATIC_ASSERT(
    zip_with(_ * _, list(1, 2, 3, 4), list(5, 6, 7, 8, "ignored"))
    ==
    list(5, 12, 21, 32)
);
{% endhighlight %}

## [cpp-react](https://github.com/edvorg/cpp-react)

This is a C++1y micro-framework for functional reactive programming.

## [cpp-blend](https://github.com/edvorg/cpp-blend)

cpp-blend is another C++14 string formatting library which aims to be extensible and typesafe.

## [{% raw %}{{ bustache }}{% endraw %}](https://github.com/jamboree/bustache)

{% raw %}{{ bustache }}{% endraw %} is a C++1y implementation of {% raw %}{{ mustache }}{% endraw %}, a template language for text substitution.

{% highlight cpp %}
bustache::format format{"{% raw %}{{ mustache }}{% endraw %} templating"};
bustache::object data{ {"mustache", std::string("bustache")} };
std::cout << format(data); // prints "bustache templating"
{% endhighlight %}

## [ETL](https://github.com/ambroise-leclerc/ETL)

ETL is a C++11/14 Embedded Template Library for AVR 8-bit microcontrollers which contains an implementation of a subset of STL.

## [MNMLSTC Core](https://github.com/mnmlstc/core)

Finally, I thought it's worth mentioning MNMLSTC Core - a library which implements various proposals for C++14 and beyond in C++11. It's useful if you want to start using C++14 features but don't have a C++14 compliant compiler as yet. 

This library includes the following (among a lot of other stuff):

{% highlight cpp %}
variant<Ts...>
optional<T>
expected<T>
deep_ptr<T>
poly_ptr<T>
string_view
range<T>
any
{% endhighlight %}

That's it for today! 