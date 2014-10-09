---
title: "JetBrains CLion C++ IDE First Impressions"
tags: tools ide
---

<img src = "/img/Screen-Shot-2014-09-11-at-8.05.51-am.png" width = "169" height = "60" style = "float: left; margin-right: 1em"/> This post describes my first impressions from taking the new JetBrains [CLion C++ IDE](http://www.jetbrains.com/clion/) (currently in alpha) for a spin. 

I've been a loyal user of another IDE made by JetBrains - RubyMine, which I've been using for Ruby on Rails and Node.js projects. This IDE is one of the best that I've used, so I was excited to hear about the public release of an alpha version of their C++ IDE.

It was particularly interesting to me as I mostly use a Mac but I haven't looked at using XCode for C++ projects, so getting a familiar-feeling IDE on a Mac would be great.

## What do I want from an IDE?

There are a few things I want from an IDE, and that's what I look for every time I try out a new IDE. 

1. It needs to have a good customizable editor (colors, fonts and code formatting).

2. It needs to have good code completion, and ideally interactive error notifications (e.g. with squiggly lines).

3. It needs to have easy project navigation, which includes switching between files, and context sensitive navigation (e.g. switching between declaration/definition or header/implementation). 

4. Finally, it needs to have a fast debugger which makes it easy to view program state. I hold up Visual Studio's example as the best example of that. 

## Getting Started with CLion

CLion works on Windows, Linux and Mac which is quite convenient for multi-platform projects. 

The install went fine, I created a new project and CLion set up a minimal file structure. I added some code to main.cpp, and was able to get it compiled and running after adding a line to CMakeLists.txt (see Builds section below).

I also downloaded the source of the C++14 Streams library ([https://github.com/jscheiny/Streams](https://github.com/jscheiny/Streams)) which has a CMake build script. I was able to simply open its directory from CLion, hit Build and then Run, and had the tests run successfully without any hassle. 

So that was a good start.

## The Editor

Normally the first thing I do with a new piece of software is go into Settings and see what I can customize. CLion is built on top of the IntelliJ platform, so it has a massive set of configuration options (and I think an IDE can never have enough options so this is a good thing).

<img src = "/img/Screen-Shot-2014-09-10-at-11.53.11-am-1024x666.png" width = "683" height = "444"/>

I could configure formatting options, folding options, tab behavior, code completion and shortcuts. There are predefined keymaps matching other software, including Visual Studio, XCode and Emacs. There is fine grained configuration of syntax highlighting, but font selection appears to be disabled at this point.

**UPDATE:** Font selection actually works fine, I just got confused. A nice thing with fonts is that CLion also lets me adjust line spacing (I like 1.2). 

The list of code formatting options is insanely detailed:

<img src = "/img/Screen-Shot-2014-09-10-at-12.02.57-pm.png" width = "840" height = "641" />

There are tons of settings for spaces (do you want spaces specifically within array index braces?), wrapping and braces (do you want to align function parameters on multiple lines?), and for blank lines (do you want N blank lines after the includes?). I could configure the placement of braces for lambdas differently from everything else, which was very nice.

The display of templates initially confused me a bit: 

<img src = "/img/Screen-Shot-2014-09-10-at-12.05.19-pm.png" width = "616" height = "194" />

Turns out, this is just an aspect of code folding. Template declarations are folded by default and can be expanded easily:

<img src = "/img/Screen-Shot-2014-09-10-at-12.07.23-pm.png" width = "552" height = "193" />

It would be good if the editor evaluated macros and greyed out any code that isn't going to get compiled (the way Visual Studio does). That doesn't seem to be implemented though:

<img src = "/img/Screen-Shot-2014-09-10-at-12.19.29-pm.png" width = "581" height = "174"

## Code Completion and Interactive Feedback

Support for some C++11 features isn't implemented yet. This includes user defined literals, _constexpr_ and _enum class_. 

I noticed the squiggly line error highlights in the editor being incorrect for some other things (e.g. a declaration using _decltype_) while the code compiled and ran just fine. Other issues included an unused macro warning even though it was used several lines down in the file, as well as unwarranted complaints about lambda return type. Hopefully these are just alpha issues and will get fixed. 

An interesting feature is parameter placeholders in function calls inserted via code completion: 

<img src = "/img/Screen-Shot-2014-09-10-at-12.23.01-pm.png" width = "706" height = "57" />

The whole placeholder is selected as soon as the cursor goes over it, and it can be typed over. This is quite helpful, although as you can see, placeholders can be _really_ long as they include automatically generated type names.

Another feature is automatic addition of _#include_s based on what I'm typing. E.g. if I'm writing a function declaration and add a _chrono::time_point_ parameter, a `#include <chrono>` statement is automatically added to the top of the file. I don't think this feature is complete so I don't want to bash it too much, but it didn't work for me. 

For one thing, it added includes outside the header guard. For another, it didn't seem reliable and I couldn't figure out when it does or doesn't add an include. In any case, seeing as includes can cause significant issues with cycles and build times, I'm not sure this can be automated well, especially in a large project. But I'll be happy if it is :)

## Project navigation

CLion has an awesome feature: double-Shift pops up a project file search box. I just need to enter a few characters to get to any file in the project. It also shows a list of opened files sorted by recency, so to switch repeatedly between two files (e.g. .h and .cpp) all I need to press is double-Shift followed by Enter:

<img src = "/img/Screen-Shot-2014-09-10-at-12.42.17-pm.png" width = "412" height = "307" />

The result of this is that I don't actually need file tabs. Luckily, CLion allows me to hide them altogether, so I get extra editor space! I also kept the project tree closed most of the time - it's only needed if I don't remember the name of the file at all.

There is also an alternative to the tree view in the form of a breadcrumb bar with file/directory dropdowns at each level. It allows adding new files too, but I was disappointed that I couldn't find an option to create a corresponding .h or .cpp counterpart for an existing file.

<img src = "/img/Screen-Shot-2014-09-10-at-12.44.33-pm.png" width = "654" height = "143" />

The net result is that I can have most of the window dedicated to the editor, which is how I like to work:

<img src = "/img/Screen-Shot-2014-09-10-at-12.46.16-pm.png" width = "759" height = "821" />

Navigation in the editor seemed to work flawlessly in my short test. The IDE found the standard header locations and allowed me to go to declarations of standard types (_vector_) and standard functions (_push_back_). It also picked up the location of an external library from CMakeLists.txt and parsed that, so I could go to those declarations too. It all worked really fast. 

## Builds

The IDE supports both GCC and Clang. On Windows, this means using MinGW or Cygwin.

The documentation says that most C++11 features are supported at IDE level. Both libc++ and libstdc++ are supported but Boost isn't fully supported.

Only CMake is supported as "project format" right now, although there are plans to expand that (e.g. support Makefiles, Autotools and Qt projects). CMake integration isn't particuarly advanced either, for example, when files are copied in, CMakeLists.txt isn't updated automatically. Additionally, multiple build targets aren't supported either.

The IDE found my install of Clang, so it was easy to get going. I just needed to drop 

{% highlight cmake %}
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -stdlib=libc++")
{% endhighlight %}

into _CMakeLists.txt_ to get C++11 code to compile, and that was it. 

Program output goes into a pane in the IDE, which is handy.

## Debugging

Debugging worked out of the box, but it should be noted that at this point it's based on GDB, with LLDB integration planned for later.

There are popups showing variable values on hover, which I consider a must. However, they don't work very well. For one thing, when hovering on an object, all I get is the variable name with a plus sign which I have to click:

<img src = "/img/Screen-Shot-2014-09-10-at-12.49.06-pm.png" width = "416" height = "138" />

I can't imagine the circumstances when that's useful. Why not expand it automatically?

The other problem is that the debugger doesn't seem to have any knowledge of standard C++ types. I couldn't even see the contents of _std::string_. For a _vector_, I got an expansive representation of all its internals - not at all what I was looking for, which was a simple list of its elements.

That said, the debugger has useful functionality: stack views per thread, watches, stepping over, into and out, and breakpoints. Conditional breakpoints are possible. A handy option is tracing when a breakpoint is reached. 

<img src = "/img/Screen-Shot-2014-09-10-at-12.54.42-pm.png" width = "830" height = "291" />

## Refactoring and Code Fixes

I just tried these features out but didn't give them any kind of extensive testing. I'm lukewarm about refactoring features, with only Rename getting consistent use from me. Renaming worked quite well within a single file. For example, when renaming a class, CLion was also able to rename references to the class in comments. 

In addition to manual refactoring operations, some refactorings are suggested automatically with a lightbulb icon popping up next to the current line in the editor. These include things like removing unnecessary parentheses and moving function definition out of class. They seemed to work fine.

In my experience, refactoring support often falls short when multiple files and more complex constructs are involved, so it remains to be seen how well CLion will hold up to more serious use.

## Extras

The IDE has a lot of other helpful features which aren't in my "minimal feature set for a useful IDE". 

It's got excellent source control integration, with Git, SVN and even TFS support available.

It has configurable parametrized code templates.

The fact that the IDE is built on the IntelliJ platform means that there are many further customisations and plugins available for this IDE. 

It also means that more than one language can be supported, which could be great, for example, if you have a mix of C++ and Lua, or C++ and Python in your project. 

## Conclusion

I think this is an extremely promising product. It's got plenty of rough edges at the moment, but hey - it's alpha. There are also many features which are working great. 

The build process, code analysis and debugging still need a lot of work, but the editor is already great.

I know that JetBrains makes excellent IDEs so I have high hopes for CLion. 