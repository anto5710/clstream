## clstream

A demonstration of [output stream << overloading](https://docs.microsoft.com/en-us/cpp/standard-library/overloading-the-output-operator-for-your-own-classes?view=msvc-170).

#### Introduction
This utility provides an integrated interface to printing colored or typesetted texts 
in terminal or desired destination. Color coding scheme is based on [ANSI Escape Codes](https://en.wikipedia.org/wiki/ANSI_escape_code), a set of special characters reserved to manipulate graphic
interface in Console/Terminal screen.
It is mainly employed in Unix-like systems (including Mac OS) but hardly supported in Windows 10 or less. However, given that many higher-education institutions prefer Linux as server-side O.S., 
this will be of minor issue, but of major advantage, considering full 256-RGB color gradient support
in some popular Linux distros.

#### Documentation

> Full documentation is archived at <a href="/dox/html/index.html" target="_blank" rel="noopener noreferrer">[dox/html/index.html]</a> in this project folder.
> For further details & implementations, please refer to it.

#### Funcitonality
This utility provides 3 main modules in overall, each being: `color_map.h`, `color_stream.h`,
`color_tag.h`, and one auxiliary string util.

##### cout

First, in `color_map.h` is all default ANSI color-code constants,
some examples being `C_BLACK`, `ST_ITALIC`, or `BG_GREEN`.
These `ANSISequence` constants can be printed using any output stream, such as:

```cpp
#include "color_map.h"
cout << C_GREEN << "Hi, green " << C_YELLOW << "bulb!" << C_RESET << endl; 
```

which will print the message "<span style="color:green">Hi, green</span><span style="color:yellow">bulb!</span>" in a line, ending it after resetting all color changes.


##### ANSI sequences

ANSI sequence can be effectively categorized into three: `font-color`, `background-color`, and `typesetting`, and any of them can be concatenated using semicolon `;`, such as
`\e31;1m` (<span style="color:red">red</span>; **bold**) being combination of `\e31m` (<span style="color:red">red</span>) and `\e1m` (**bold**), where `\e` and `m` each mark the beginning and the end of sequence(s).

Exact color codes and effects are listed on [the Wikipedia page](https://en.wikipedia.org/wiki/ANSI_escape_code#DOS,_OS/2,_and_Windows), and on [understudy.net](http://www.understudy.net/custom.html#table1).

Similarly, ANSI constants in `color_map.h` can be concatenated indefinately using `+` or `+=` operators:

```cpp
ANSISequence italic_blue_screen = ST_ITALIC + BG_BLUE + C_WHITE;
```

Then add it before any text to apply the saved effect:
```cpp
    cout << italic_blue_screen << "Blue Screen!" << R_RESET << endl;
```
> Output:
> <span style="color:white; background-color:blue"><i> Blue Screen! </i></span>

Or, it could be done in-line:

```cpp
    cout << (ST_BOLD + BG_YELLOW + C_GREEN) << "Blue Screen!" << R_RESET << endl;
```

##### ColorStream
The main feature of this util. 
`ColorStream` is a writer object that will keep forwarding any given output to selected destination.
Its constructor accepts a reference to any output stream (`std::ostream`) object or subclass,
meaning you could declare one from `std::cout`:

```cpp

    ColorStream cstream(cout);
```

or a stringstream:

```cpp
    stringstream sstream;
    ColorStream cstream2(sstream);
```
A `ColorStream` object behaves and prints output to the said path in the exact same manner,
but has embedded in few more useful properties, namely:

1. It keeps track of every forwarded `ANSISequence`.
   Note that in:
    ```cpp
    cout << C_GREEN << BG_RED << "HI!" << R_RESET << "MORE TEXT";
    ```
   will lose all color coding in the middle, once `R_RESET` is called. This makes it highly
   inefficient to nest multiple layers of color schemes.

   By passing through `ColorStream`, every instance called after `<<` is memoized in an internal
   color stack, enalbing adding/and removing of individual color codes in `Ctrl-Z` like-fashion. 

    A nested example without `ColorStream`:
    ```cpp
    cout << C_GREEN << ST_STRIKE << "strikethrough " << BG_RED << "baseline" << ST_UNDERLINE << "HI!" 
         << R_RESET << BG_RED << C_GREEN << ST_STRIKE << "more strike" << R_RESET << ST_STRIKE 
         << C_GREEN << "MORE TEXT";
    ```
    A nested example with `ColorStream`:
    ```cpp
    ColorStream(cout) << (C_GREEN + ST_STRIKE) << "strikethrough" 
                      << BG_RED << "baseline" 
                      << ST_UNDERLINE << "HI!" << u_undo 
                      << "more strike" << u_undo 
                      << "MORE TEXT";
    ```
   
#### Installation
Download from github and place needed header files into an accessible folder.

Use in `C++` by adding following Make-directive:

```bash
#include "clstream.h"
```
for importing entirety of the utility
or
```bash
#include "color_map.h"
#include "color_stream.h"
// ...
```
for individual header files.

Thank you for reading.
