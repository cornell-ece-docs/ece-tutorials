# ECE 2400 Discussion Section Wk2: Compiling and Running C Programs


This discussion section serves as gentle introduction to the basics of
compiling and running C programs on the `ecelinux` machines.
The starter code for this tutorial is provided here:

  - [https://github.com/cornell-ece2400/ece2400-sec02-2025](https://github.com/cornell-ece2400/ece2400-sec02-2025)

## 1. Logging Into ecelinux with VS Code

As we learned in the last discussion section, we will be using the
`ecelinux` servers for all of the programming assignments. In the last
discussion section we used a terminal emulator to log into the `ecelinux` servers.

In this discussion section, we will use VS Code to log into the
`ecelinux` servers. VS Code
provides a nice GUI for navigating the directory hierarchy on `ecelinux`, 
syntax highlighting for C/C++ programs, the ability to open many
files across multiple tabs, and an integrated terminal for running
shell commands.

Note, if you have already installed VS Code on your laptop, then you
should feel free to use your laptop for this discussion section.
**However, if you have not already installed VS Code on your laptop and
verified it works, then please use the workstations in 225 Upson or find a partner.** We do
not have time to help you setup VS Code on your own laptop in this
discussion section.

For a full tutorial on using VS Code with ecelinux, follow [this tutorial](http://127.0.0.1:8000/tut0-ecelinux/#3-remote-access-via-vs-code).

## 2. Before You Begin

The [GitHub repo](https://github.com/cornell-ece2400/ece2400-sec02-2025) provides a quick checklist you should follow to download and setup the starter code:

- Be able to connect to ECELinux with VS Code (see [tutorial](https://cornell-ece-docs.github.io/ece-tutorials/tut0-ecelinux/#3-remote-access-via-vs-code))
- Set up ssh with GitHub (we did this in last week's discussion section)
- `git clone git@github.com:cornell-ece2400/ece2400-sec02-2025.git ece2400-sec02` this repo to ecelinux
- OPTIONAL BUT RECOMMENDED: Install [clangd extension](https://marketplace.visualstudio.com/items?itemName=llvm-vs-code-extensions.vscode-clangd) on the remote host
-  Open the repo with VS Code on ecelinux, by clicking *File > Open Folder*. Choose `ece2400-sec02`


## 3. Compiling and Running a Single-File C Program

We will begin by writing a single-file C program to calculate the average
of two integers similar to what we have studied in lecture. We have
provided you with a template in the `avg-main.c` file. Edit the
`avg-main.c` file to include an appropriate implementation of the `avg`
function.

```c
#include <stdio.h>

int avg(int x, int y) {
  int sum = x + y;
  return sum / 2;
}

int main() {
  int a = 10;
  int b = 20;
  int c = avg(a, b);
  printf("average of %d and %d is %d\n", a, b, c);
  return 0;
}
```

We use a _compiler_ to translate the C source code into an executable
binary (i.e., the actual sequence of bits) that the machine can understand. In this
discussion we will be using the GNU C compiler (`gcc`), but you could use `clang` too. Let's go ahead and
give this a try:

```bash
$ pwd # Should be ece2400-sec02
$ gcc -Wall -o avg-main src/avg-main.c
```

The `gcc` command takes as input the C source file to compile and the
command line option `-o` is used to specify the output exectutable binary
(i.e., the file with the machine instructions). We also use the `-Wall`
command line option to report all warnings. After running the `gcc`
command you should see a new `avg-main` file in the directory. We can
execute this binary by simply calling it as any other Linux command.

However, notice the error message we received:

```
src/avg-main.c: In function ‘avg’:
src/avg-main.c:5:1: warning: control reaches end of non-void function [-Wreturn-type]
    5 | }
```

We need to fill in the rest of the code. Open the code with `code src/avg-main.c` and fill in that function.
Then, we can check that it works:

```bash
$ pwd # Should be ece2400-sec02
$ gcc -Wall -o avg-main src/avg-main.c
$ ./avg-main
average of 10 and 20 is 15
```

Recall that a single dot (`.`) always refers to the current working
directory. Essentially we are telling Linux that we want to run the
executable binary named `avg-main` which is located in the current
working directory.

It can be tedious to to have to carefully enter the correct commands on
the command line every time we want to compile a C source file into an
executable binary. In the next discussion section, we will explore using
a _build framework_ to automate the process of building our C programs.

Before we move on, let's examine the machine instructions using the `objdump` command.

```bash
$ objdump -dC avg-main | less
```

The `objdump` command takes an executable binary and shows you the
machine instructions in a human readable format. We are piping it through
`less` so we can scroll through the output. Try and find how many machine
instructions are used to implement the `avg` function. Does it seem like
the compiler generated optimized code or unoptimized code? You can exit
`less` by pressing the `q` key. Let's recompile our program with
optimizations. We can do this by specifying `-O3` (optimization level 3).

```bash
$ gcc -Wall -O3 -o avg-main src/avg-main.c
$ objdump -dC avg-main | less
```

Now how many machine instructions are used to implement the `avg`
function?

## 4. Compiling and Running a Multi-File C Program
--------------------------------------------------------------------------

C programs are almost never contained in a single source file. They require
many files which must be individually compiled and then _linked_
together. Linking is the process of merging together different binary
files each with its own set of machine instructions. To illustrate this
process we will experiment with a function to square a given integer.
Our project will include three files:

 - `include/square.h`: header file with function prototype for `square` function
 - `src/square.c`: source file with function definition for `square` function
 - `src/square-adhoc.c`: adhoc test of `square` function which contains `main`

We will compile the `square.c` and `square-adhoc.c` files into their own
object files and then link these object files into a complete executable
binary.

An _object file_ is like a chunk of machine instructions. We cannot
execute an object file directly, because they contain only part of the entire program.
However, _linking_ the object files together creates an executable binary.

Start by inspecting the _header file_ `include/square.h`. Header files are the
key to multi-file C programs. The `square-adhoc.c` source file needs to
call the `square` function, but the `square` function is in a different
source file. When we compile the `square-adhoc.c` source file, how will
the compiler know that the `square` function exists to ensure the
programmer is not accidentally calling an undefined function? How will
the compiler know what parameters the `square` function takes, so it can
perform type checking? The `square-adhoc.c` source file cannot directly
include `square.c` since that would result in the same function being
compiled twice into two different object files (which would cause a
linker error). What we need to do is have a way to tell `square-adhoc.c`
the `square` _function prototype_ (i.e., the interface of the function
including its name, parameter list, and return type) but not the `square`
_function definition_. We do this with a **function declaration**. A
function definition specifies both the function prototype (interface) and
the implementation at the same time, while a function declaration just
specifies the function prototype without the implementation. A header
file contains all of the function declarations but no function
definitions. All of the function definitions are placed in a source file
that goes along with the header file. If we want to call a function that
is defined in a different source file, then we simply use the `#include`
directive to include the appropriate header file. The linker will take
care of making sure the machine instructions corresponding to every
function definition are linked together into the executable binary.

We have provided you the `square.h` file with the the following contents:

```c
int square( int x );
```

We have provided you with a template for the `square.c` file. Edit
`src/square.c` file to include an appropriate implementation of the `square`
function.

```c
#include "square.h"

int square(int x) {
  return x * x;
}
```

Notice how our `square.c` file includes the corresponding `square.h`
file. This is best practice which follows the course coding conventions.
Finally, take a look at the provided `square-adhoc.c` file:

```c
#include "square.h"
#include <stdio.h>

int main() {
  int a = 10;
  int b = square(a);
  printf("square of %d is %d\n", a, b);
  return 0;
}
```

Go ahead an fill in the implementation for `square()`.
Then, let's compile `square.c` and `square-adhoc.c` into their
corresponding object files:

```bash
$ pwd # Should be ece2400-sec02
$ gcc -Iinclude -Wall -c -o square.o src/square.c
$ gcc -Iinclude -Wall -c -o square-adhoc.o src/square-adhoc.c
```

We use the `-c` command line option to indicate that `gcc` should create
an _object file_ as opposed to a complete executable binary. An object
file is just a piece of machine instructions. Again, we cannot actually
execute an object file; we need to link multiple object files together to
create a complete executable binary. We usually use the `.o` filename
extension to indicate that these files are object files. 

Let's link these two object files together to create a complete
executable binary that we can actually run:

```bash
$ gcc -Wall -o square-adhoc square.o square-adhoc.o
```

Notice that the complete executable binary contains all of the machine
instructions for _both_ the `square` and `main` functions along with a
bunch of additional system-level code (e.g., for the `printf` function).
Let's go ahead and run the executable binary.

```bash 
$ ./square-adhoc
```

This of course begs the question. If we can compile a project with
multiple files simply by specifying all of the files on the command line,
then why did we learn about how to: (1) compile each file individually
into an object file; and (2) link these object files together? For small
projects with just 2-3 files there is no need to use object files.
However, in a project with thousands of files, specifying all files on a
single command line will cause each recompilation to take a very long
fixed amount of time---potentially hours.

Using object files enables modular compilation. In _modular compilation_,
we only need to recompile those source files what have changed. We can
simply reuse the previously compiled object files for those source files
that have not changed. Modulary compilation can drastically reduce
recompile times, so that it is proportional to just how many changes you
have made to the source files (e.g. less than a second). One challenge
with modular compilation is it drastically increases the build
complexity. In the next discussion section, we will
explore using a _build framework_ to automate the process of modular
compilation for complex C programs.

## 5. Automation with CMake

CMake is a tool that exists to essentially automate the process we just experimented with.
It also assists in making your build system more portable to different operating systems.
For example, Windows does not have [GNU Make](https://www.gnu.org/software/make/), so it uses [Visual Studio solutions](https://learn.microsoft.com/en-us/visualstudio/ide/solutions-and-projects-in-visual-studio?view=vs-2022#solutions). CMake can even use the [Ninja](https://ninja-build.org/) build system.

In short, CMake is a higher level scripting tool to manage how your C code is built.
Consequently, writing your own CMake scripts (`CMakeLists.txt`) can be difficult and CMake itself is not well documented.
Nonetheless, CMake makes the building process automated and very portable when done correctly.

If you want more information, investigate the `CMakeLists.txt` file. In this tutorial, we will only show you how to run our CMake project---not make new ones. With that said, let's rebuild the project using CMake:

```bash
pwd # Should be ece2400-sec02
mkdir -p build
cd build
cmake ..
```

If that worked, you should see `Build files have been written to: /home/netid/ece2400-sec02/build`.
We have not actually built the project yet. CMake simply establishes a database of how to build our object files, and how they are interdependent. For example, let's take a look at `compile_commands.json`:

```bash
$ cat compile_commands.json
{
  "directory": "/home/netid/ece2400-sec02/build/src",
  "command": "/usr/bin/cc  -I/home/netid/ece2400-sec02/include  -o CMakeFiles/sec02-lib.dir/square.c.o -c /home/netid/ece2400-sec02/src/square.c",
  "file": "/home/netid/ece2400-sec02/src/square.c"
},
{
  "directory": "/home/netid/ece2400-sec02/build/src",
  "command": "/usr/bin/cc  -I/home/netid/ece2400-sec02/include  -o CMakeFiles/square-adhoc.dir/square-adhoc.c.o -c /home/netid/ece2400-sec02/src/square-adhoc.c",
  "file": "/home/netid/ece2400-sec02/src/square-adhoc.c"
},
{
  "directory": "/home/netid/ece2400-sec02/build/src",
  "command": "/usr/bin/cc  -I/home/netid/ece2400-sec02/include  -o CMakeFiles/avg-main.dir/avg-main.c.o -c /home/netid/ece2400-sec02/src/avg-main.c",
  "file": "/home/netid/ece2400-sec02/src/avg-main.c"
}
```

It may not seem impressive that CMake just generates the same compile commands that we ran by hand. However, it would be infeasible to manage these commands for thousands of object files everytime we make a change (like changing optimization level). To execute all the build commands, just run make in the build directory:

```bash
pwd # Should be ece2400-sec02/build
make
```

To reiterate, `cmake` generates the files needed to run `make` (GNU Make).
If it worked, you should see the executable programs `ece2400-sec02/build/src/square-adhoc` and `ece2400-sec02/build/src/avg-main`. Try running them, and make sure they work the same. **Notice that `ece2400-sec02/build/src` is a different directory than `ece2400-sec02/src`.** The former holds our executables, and the latter is where the source code is located. CMake by default makes the build directory mirror the source file tree.

## 6. To-Do On Your Own

### 6.1. Try `clangd`

One benefit of using CMake is that it makes using `clangd` very easy. Simply install the [clangd extension](https://marketplace.visualstudio.com/items?itemName=llvm-vs-code-extensions.vscode-clangd) on the remote host in VS Code. Then, your code editor should provide helpful auto-corrections as you write your code. Clangd is the number one most useful tool when managing a gigantic codebase, so you may want to read into its capabilities: [https://clangd.llvm.org/](https://clangd.llvm.org/).

### 6.2. Add a new source file

If you have time, create a new source file named `avg3-main.c` in the
`${HOME}/ece2400-sec02/src` directory that contains an `avg3` function. This
function should calculate the average of three values instead of just
two. Modify the `main` function to properly call your updated function.
Finally, add your binary to `src/CMakeLists.txt`.
Compile your new program with the same build script and run it to verify it calculates the average
correctly.