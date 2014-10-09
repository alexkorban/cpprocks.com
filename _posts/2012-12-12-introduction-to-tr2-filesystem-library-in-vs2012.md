---
title: "An introduction to the TR2 filesystem library in Visual Studio 2012"
tags: vs2012
---

Visual Studio 2012 includes the filesystem library. It isn't part of the C++11 standard but it's one of the proposals for TR2. The proposal is based on the library included in _boost_.

The library provides a convenient (and cross platform, if supported by compilers) interface for file & directory traversing and manipulation, as well as querying filesystem information such as file sizes and disk space.

The main components of the library are:

*   path objects
*   directory iterators
*   free functions

Path objects encapsulate filesystem paths and provide convenient ways of assembling paths and extracting their components.

Directory iterators allow you to iterate over directory contents, both recursively and non-recursively.

Free functions mainly operate on paths and provide all sorts of operations, from checking whether a file exists, to deleting a directory and its contents.

Let's dive into some code to see the functionality of the library. 

## Checking if files exist & creating directories

It's common that you need to create a directory (or a tree of them) to store logs, the output of your application etc.:

{% highlight cpp %}
auto startup_path = initial_path(); 

auto log_path = startup_path / path("logs/current");
auto output_path = startup_path;
output_path /= "output";

if (!exists(log_path))
    create_directories(log_path);

if (!exists(output_path))
    create_directory(output_path);
{% endhighlight %}

As you can see, you can use the convenient _operator/_ and _operator/=_ to assemble paths from parts. 

`create_directories` can create a number of directories along the path chain if they don't exist (e.g. both _logs_ and _current_ in the example above).

## Iterating over directory contents & moving files

The next thing you might want to do is move log files over the size of 100 MB from _current_ directory to _archive_:

{% highlight cpp %}
for (auto it = directory_iterator(log_path); it != directory_iterator(); ++it)
{
    // file object contains relative path in the case of 
    // directory iterators (i.e. just the file name)
    const auto& file = it->path();  
    if (file.extension() == ".log" && file_size(log_path / file) > 100 * MB)
    {
        auto new_path = log_archive_path / file;
        rename(log_path / file, new_path);
    }
}
{% endhighlight %}

Note that you can't use the range based _for_ loop to iterate over the contents of a directory. This kind of iteration can only be used to extract the components of a path:

{% highlight cpp %}
for (auto& component : log_path)
    cout << component << endl;  // output each component of the path
{% endhighlight %}

## Iterating recursively & filtering files

It's often necessary to traverse a directory tree to find files in sub-directories. The _filesystem_ library has a really convenient `recursive_directory_iterator` for this purpose. 

It's also a common task to look for files of particular type or with a name matching a mask. The _filesystem_ library is of no particular help here, but it's easy to do this by adding the _regex_ library into the mix.

The example below shows how to do both of these things. It goes through all sub-directories of the _kml_ directory and prints out the file names and last write times for all KML and KMZ files which have the string _curletts_ in their name:

{% highlight cpp %}
auto kml_path = initial_path<path>() / path("kml");

for (auto it = recursive_directory_iterator(kml_path); 
    it != recursive_directory_iterator(); ++it)
{
    // file object contains absolute path in the case of recursive iterators
    const auto& file = it->path();

    auto matches_mask = [](const path& file) { 
        return regex_search(file.filename(), regex(".*curletts.*\\.km(l|z)"));
    };

    if (!is_directory(file) && matches_mask(file))
    {
        auto last_write = last_write_time(file);
        cout << file.filename() << " - " 
            << put_time(localtime(&last_write), "%Y/%m/%d %H:%M:%S") << endl;
    }
}
{% endhighlight %}

## Checking available space

Finally, you may need to check the available space, e.g. when your application produces large output files. It's easy to do:

{% highlight cpp %}
auto capacity_info = space(path("C:"));
cout << "Free space on C: " << endl
    << "Total: " << capacity_info.capacity << " bytes" << endl
    << "Free: " << capacity_info.free << " bytes" << endl
    << "Available: " << capacity_info.available << " bytes" << endl;
{% endhighlight %}

## Further reading

As you have seen, the _filesystem_ library provides a convenient interface for filesystem related operations, and allows you to handle real world tasks in just a few lines of code. 

The library contains many more useful functions not shown in the tutorial. You can find the MSDN documentation for them here: 
[http://msdn.microsoft.com/en-us/library/hh874694.aspx](http://msdn.microsoft.com/en-us/library/hh874694.aspx)

The current Microsoft implementation is based on a 2006 proposal which is based on the Boost filesystem v.2. You can see this version of the proposal here: 
[http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2006/n1975.html](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2006/n1975.html)

The proposal has later been updated to reflect the evolution of Boost filesystem to v.3. The latest version of the proposal can be found here: 
[http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2011/n3239.html](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2011/n3239.html).