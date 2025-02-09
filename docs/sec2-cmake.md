# ECE2400 Section 3: C Build and Test Frameworks

In the previous discussion section, you learned how to explicitly compile
and run C programs from the command line. You learned how to use the GNU
C Compiler (`gcc`) to compile both a single-file program, as well as a multi-file program that calculated the square
of an integer. You probably noticed that it can be tedious to have to
carefully enter the correct commands on the command line. 
In this discussion section, we will more closely exam the _GNU Make build framework_
and _CMake build framework_ to automate the C build process.

## 1. Logging Into `ecelinux` with VS Code

Once again, we will be using VS Code to log into the ecelinux servers:

 - Start VS Code
 - Use _View > Command Palette_ to execute _Remote-SSH: Connect Current Window to Host..._
 - Enter `netid@ecelinux.ece.cornell.edu`
 - Use _View > Explorer_ to open folder on `ecelinux`
 - Use _View > Terminal_ to open terminal on `ecelinux`

Now clone the [GitHub repo](https://github.com/cornell-ece2400/ece2400-sec02-2025) using the following commands:

```bash
$ git clone git@github.com:cornell-ece2400/ece2400-sec02-2025 ece2400-sec03
$ cd ece2400-sec03
$ tree
```

The directory includes the following files:

 - `avg-main.c`: source and main for single-file `avg` program
 - `square.h`: header file for the `square` function
 - `square.c`: source file for the `square` function
 - `square-adhoc.c` : test driver for `square` function

## 2. Using Makefiles to Compile C Programs

Let's remind ourselves how to explicitly compile and run a single-file C
program on the command line:

```bash
$ gcc -Wall -o avg-main src/avg-main.c
$ ./avg-main
```

Let's now remove the binary so we are back to a clean directory:

```bash
$ rm -r avg-main
```

We will start by using a new tool called GNU Make which was specifically
designed to help automate the process of building C programs. The key to
using `make` is developing a `Makefile`. A `Makefile` is a plain text
file which contains a list of _rules_ which together specify how to
execute commands to accomplish some task. Each rule has the following
syntax:

```
    target : prerequisite0 prerequisite1 prerequisite2
    <TAB>command
```

A rule show specify how to generate the target file using the list of
prerequisite files combined with a shell command. `make` is smart enough to
know it should re-make the target if any of the prerequisites change, and
it also knows that if one of the prerequisites does not exist, then it
should try to build it first. This process occurs recursively. It
is very important to note that `make` requires commands in a rule to
start with a real TAB character. **So you should not type the letters
`<TAB>`, but you should instead press the TAB key and verify that it has
inserted a real TAB character (i.e., if you move the left/right arrows
the cursor should jump back and forth across the TAB).** This is the only
time in the course where you should use a real TAB character as opposed
to spaces.

Let's create a simple `Makefile` to compile a single-file C program. Use
VS Code to create a file named `Makefile` with the following content:

```makefile
avg-main: src/avg-main.c
<TAB>gcc -Wall -o avg-main $^

clean:
<TAB>rm -rf avg-main
```

In this case `$^` refers to `src/avg-main.c`.
See [GNU Automatic Variables](https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html) for more info.
We can use the newly created `Makefile` like this:

```bash
$ make avg-main
$ ./avg-main
```

`make` will by default use the `Makefile` in the current directory.
`make` takes a command line argument specifying what you want "make". `make` will look
at all of the rules in the `Makefile` to find a rule that specifies how
to make the `avg-main` executable. It will then check to make sure the
prerequisites exist and that they are up-to-date. If that condition is met, it will run
the command specified in the rule for `avg-main`. In this case, that
command is `gcc`. `make` will output to the terminal every command it
runs, so you should see it output the command line which uses `gcc` to
generate the `avg-main` executable.

Try running `make` again:

```bash
$ make avg-main
$ ./avg-main
```

`make` detects that the prerequisite (i.e., `src/avg-main.c`) has not
changed and so it does not recompile the executable. Now let's try making
a change in the `avg-main.c` source file. Modify the `printf` statement
as follows:

```c
printf("avg( %d, %d ) == %d\n", a, b, c );
```

You can recompile and re-execute the program like this:

```bash
$ make avg-main
$ ./avg-main
```

In this case, `make` detects that the prerequisite has changed, and hence
recompile the executable. The ability to automatically
track dependencies and recompile only what is necessary is the key benefit
of using a tool like GNU Make. Makefiles can also include targets which
are not actually files. Our example `Makefile` includes a `clean` target
which will delete any generated executables. Let's clean up our directory
like this:

```bash
$ ls
$ make clean
$ ls
```

## 3. Using CMake to Generate Makefiles for Compiling C Programs

While using `make` can help automate the build process, the corresponding
Makefiles can grow to be incredibly complicated. Creating and
maintaining these Makefiles can involve significant effort. It can be
particularly challenging to ensure all of the dependencies between the
various source and header files are always correctly captured in the
`Makefile`. 

New tools have been developed to help _automate_ the process of managing
Makefiles (which in turn _automate_ the build process). Automation is
the key to effective software development methodologies. In this course,
we will be using CMake as a key step in our build framework. CMake takes
as input a simple `CMakeLists.txt` file and _generates_ a more sophisticated
`Makefile` to use.

Before getting started let's remove any files we have generated and also
remove the `Makefile` we developed in the previous section.

```bash
$ make clean
$ rm -f Makefile
```

Let's inspect the provided `CMakeLists.txt` that can be used to generate a
`Makefile` which will in turn be used to compile a single-file C program:

```bash
cat CMakeLists.txt

cmake_minimum_required(VERSION 3.1)
project(sec02 C)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

include_directories(include)
add_subdirectory(src)
```

Line 1 specifies the CMake version we are assuming, and line 2 specifies
that we will be using CMake with a C project called `sec02`.
Line 3 states that we will be generating the database file, so that `clangd` can work.
The last two lines tell CMake where our code and header files live (`src/` and `include/`, respectively).

Notice, that there is another required `CMakeLists.txt` inside `src/` with more information about which source files to use:

```bash
cat src/CMakeLists.txt

add_library(sec02-lib 
    square.c
)

add_executable(square-adhoc square-adhoc.c)
target_link_libraries(square-adhoc PUBLIC
  sec02-lib
)

add_executable(avg-main avg-main.c)
```

Now, let's use CMake to generate a GNU Makefile.

```bash
$ cmake .
$ ls
$ less Makefile
```

**NOTE: THERE IS A DOT AFTER `cmake`!** The `cmake` command will by default
use the `CMakeLists.txt` in the directory given as a command line
argument. CMake takes care of figuring out what C compilers are available
and then generating the `Makefile` appropriately. You can see that CMake
has automatically generated a pretty sophisticated `Makefile`. Let's go
ahead and use this `Makefile` to build `avg-main`.

```bash
$ make avg-main
$ ./avg-main
```

CMake will automatically create some useful targets like `clean`.

```bash
$ make clean
```

With some practice, writing a `CMakeLists.txt` is simpler than writing a `Makefile`,
especially when we start working with a large codebase.
