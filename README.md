# Overview

RapidXml is an attempt to create the fastest XML parser possible, while retaining useability, portability and reasonable W3C compatibility. It is an in-situ parser written in modern C++, with parsing speed approaching that of strlen function executed on the same data.

RapidXml has been around since 2006, and is being used by lots of people. HTC uses it in some of its mobile phones.

If you are looking for a stable and fast parser, look no further. Integration with your project will be trivial, because entire library is contained in a single header file, and requires no building or configuration.

This is a CMake compatible and restructured mirror of http://rapidxml.sourceforge.net/.

# Integration
It is recommended to use [CPM](https://github.com/cpm-cmake/CPM.cmake) for integrating this libray. After adding [CPM.cmake](https://github.com/cpm-cmake/CPM.cmake/blob/master/cmake/CPM.cmake) to your `cmake/` directory, simply add the following to your top-level `CMakeLists.txt` to include the library in your project:
```cmake
include(cmake/CPM.cmake)
CPMAddPackage("gh:CodeFinder2/rapidxml@1.13")

add_executable(my_app ...)
target_link_libraries(my_app rapidxml)
```

# Usage
Here's a small example on how to use it to parse a [GraphML](https://en.wikipedia.org/wiki/GraphML):
```c++
#include <iostream>

#include <rapidxml/rapidxml.hpp>
#include <rapidxml/rapidxml_utils.hpp>

using namespace std;

int main(int argc, char *argv[])
{
  try {
    namespace rx = rapidxml;
    // Read graph properties manually:
    rx::file<> file(argv[1]); // (default template is char)
    rx::xml_document<> doc;
    doc.parse<0>(file.data());

    rx::xml_node<> *n_graphml = doc.first_node("graphml"); // root tag of the document
    if (n_graphml) {
      for (rx::xml_node<> *n = n_graphml->first_node("key"); n; n = n->next_sibling("key")) {
        rx::xml_attribute<> *intent = n->first_attribute("for");
        if (!intent || !intent->value()) {
          continue;
        }
        // Get an attribute from <key> tag:
        rx::xml_attribute<> *id = n->first_attribute("id"); 

        // If everything is valid, try to extract the data:
        string data;
        getData(n->first_node("default"), data, false);
        // ...
      }
    }
  } catch (exception &e) {
    cerr << e.what() << endl;
    return EXIT_FAILURE;
  }
  return EXIT_SUCCESS;
}
```
