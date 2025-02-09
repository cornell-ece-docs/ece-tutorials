# ECE2400 Section 3: C Build and Test Frameworks

In the previous discussion section, you learned how to explicitly compile
and run C programs from the command line. You learned how to use the GNU
C Compiler (`gcc`) to compile both a single-file that calculated the
average of two integers and multi-file program that calculated the square
of an integer. You probably noticed that it can be tedious to have to
carefully enter the correct commands on the command line. We also need to
carefully track which steps need to be redone whenever we change a C
source file. In this discussion section, we will explore using a _build
framework_ based on CMake to automate this process. In the previous
discussion section, you also learned how to do ad-hoc testing by
executing a function and then simply printing out the result to the
terminal. In this discussion section, we will explore using a _test
framework_ to automate this process. Using a build and test framework is
critical to productive system-level programming in C and C++.

## 1. Logging Into `ecelinux` with VS Code

Once again, we will be using VS Code to log into the ecelinux servers:

 - Start VS Code
 - Use _View > Command Palette_ to execute _Remote-SSH: Connect Current Window to Host..._
 - Enter `netid@ecelinux.ece.cornell.edu`
 - Use _View > Explorer_ to open folder on `ecelinux`
 - Use _View > Terminal_ to open terminal on `ecelinux`

Now clone the GitHub repo we will be using in this section using the
following commands:

```bash
$ git clone git@github.com:cornell-ece2400/ece2400-sec02-2025 ece2400-sec03
$ cd ece2400-sec03
$ tree
```

The directory includes the following files:

 - `ece2400-stdlib.h` : ECE 2400 course standard library header
 - `ece2400-stdlib.c` : ECE 2400 course standard library implementation
 - `avg-main.c`: source and main for single-file `avg` program
 - `square.h`: header file for the `square` function
 - `square.c`: source file for the `square` function
 - `square-directed-test.c` : directed test cases for `square` function

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

We will start by using a new tool called GNU make which was specifically
designed to help automate the process of building C programs. The key to
using `make` is developing a `Makefile`. A `Makefile` is a plain text
file which contains a list of _rules_ which together specify how to
execute commands to accomplish some task. Each rule has the following
syntax:

```
    target : prerequisite0 prerequisite1 prerequisite2
    <TAB>command
```

A rule specifies how to generate the target file using the list of
prerequisite files and the given Linux command. `make` is smart enough to
know it should rerun the command if any of the prerequisites change, and
it also knows that if one of the prerequisites does not exist then it
needs to look for some other rule to generate that prerequisite first. It
is very important to note that `make` requires commands in a rule to
start with a real TAB character. So you should not type the letters
`<TAB>`, but you should instead press the TAB key and verify that it has
inserted a real TAB character (i.e., if you move the left/right arrows
the cursor should jump back and forth across the TAB). This is the only
time in the course where you should use a real TAB character as opposed
to spaces.

Let's create a simple `Makefile` to compile a single-file C program. Use
VS Code to create a file named `Makefile` with the following content:

```makefile
avg-main: avg-main.c
<TAB>gcc -Wall -o avg-main avg-main.c

clean:
<TAB>rm -rf avg-main
```

We can use the newly created `Makefile` like this:

```bash
$ make avg-main
$ ./avg-main
```

`make` will by default use the `Makefile` in the current directory.
`make` takes a command line argument specifying what you want "make". In
this case, we want to make the `avg-main` executable. `make` will look
at all of the rules in the `Makefile` to find a rule that specifies how
to make the `avg-main` executable. It will then check to make sure the
prerequisites exist and that they are up-to-date, and then it will run
the command specified in the rule for `avg-main`. In this case, that
command is `gcc`. `make` will output to the terminal every command it
runs, so you should see it output the command line which uses `gcc` to
generate the `avg-main` executable.

Try running `make` again:

```bash
$ make avg-main
$ ./avg-main
```

`make` detects that the prerequisite (i.e., `avg-main.c`) has not
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

`make` will automatically detect that the prerequisite has changed and
recompile the executable appropriately. This ability to automatically
track dependencies and recompile just what is necessary is a key benefit
of using a tool like `make`. `Makefiles` can also include targets which
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
`Makefiles` can quickly grow to be incredibly complicated. Creating and
maintaining these `Makefiles` can involve significant effort. It can be
particularly challenging to ensure all of the dependencies between the
various source and header files are always correctly captured in the
`Makefile`. It can also be complicated to add support for code coverage,
memory checking, and debug vs. evaluation builds.

New tools have been developed to help _automate_ the process of managing
`Makefiles` (which in turn _automate_ the build process). Automation is
the key to effective software development methodologies. In this course,
we will be using CMake as a key step in our build framework. CMake takes
as input a simple `CMakeLists.txt` file and _generates_ a sophisticated
`Makefile` for us to use. A `CMakeLists.txt` is a plain text file with a
list of commands that specify what tasks we would like the generated
`Makefile` to perform.

Before getting started let's remove any files we have generated and also
remove the `Makefile` we developed in the previous section.

```bash
$ make clean
$ rm -f Makefile
```

Let's create a simple `CMakeLists.txt` that can be used to generate a
`Makefile` which will in turn be used to compile a single-file C program.
Use VS Code to create a file named `CMakeLists.txt` with the following
content:

```cmake
cmake_minimum_required(VERSION 2.8...3.19)
enable_language(C)
add_executable( avg-main avg-main.c )
```

Line 1 specifies the CMake version we are assuming, and line 2 specifies
that we will be using CMake with a C project. Line 3 specifies that we
want to generate a `Makefile` that can compile an executable named
`avg-main` form the `avg-main.c` source file. Now let's run the `cmake`
command to generate a `Makefile` we can use to compile `avg-main`:

```bash
$ cmake .
$ ls
$ less Makefile
```

NOTE: THERE IS A DOT AFTER `cmake`! The `cmake` command will by default
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

Writing a `CMakeLists.txt` is simpler than writing a `Makefile`,
especially when we start working with many files.

## 4. Using CTest for Systematic Unit Testing

So far we have been using "ad-hoc testing". For example, the `main`
function in `avg-main.c` will execute the `avg` function with one set of
inputs and then print the result to the terminal. If it is not what we
expected, we can debug our program until it meets our expectations.
Unfortunately, ad-hoc testing is error prone and not easily reproducible.
If you later make a change to your implementation, you would have to take
another look at the output to ensure your implementation still works. If
another developer wants to understand your implementation and verify that
it is working, he or she would also need to take a look at the output and
think hard about what is the expected result. Ad-hoc testing is usually
verbose, which makes it error prone, and does not use any kind of
standard test output. While ad-hoc testing might be feasible for very
simple implementations, it is obviously not a scalable approach when
developing the more complicated implementations we will tackle in this
course.

New tools have been developed to help automate the process of testing
implementations. These tools provide a _systematic_ way to do automated
unit testing including standardized naming conventions, test output, and
test drivers. In this course, we will be using CTest as a key step in our
test framework. CTest elegantly integrates with CMake to create a unified
built and test framework. Each unit test will be a stand-alone test
program. The following is an example of a unit test program for the
`square` function we saw in the last discussion section:

```c
#include "square.h"
#include "ece2400-stdlib.h"
#include <stdio.h>

void test_case_1_basic()
{
  printf("\n%s\n", __func__  );
  ECE2400_CHECK_INT_EQ( square( 2 ), 4 );
}

int main( int argc, char* argv[] )
{
  __n = ( argc == 1 ) ? 0 : atoi( argv[1] );

  if ( (__n <= 0) || (__n == 1) )
    test_case_1_basic();

  printf( "\n" );
  return __failed;
}
```

Our test programs will consist of a number of _test cases_. Each test
case is a separate function which should focus on testing a specific
subset of inputs. In this example, test case 1 tests very basic
functionality. Each test case should start with a statement similar to
line 7. `__func__` is a built-in variable which contains the function
name, so these lines basically print out the name of the function. Each
test case should then use a series of `ECE2400_CHECK` macros to check
that the implementation produces the expected results. For example, on
line 8 we check that the average of 10 and 20 is 15. If the check passes,
then the macro prints out the values, and we move on to the next test
check. If the check fails, then the macro prints out an error message,
sets the global `__failed` variable, and returns ending that test case.
The return value enables our test program to inform CTest of whether or
not all of our test cases passed of failed. The main function's job is to
simply call each test case function. Note that we get a single command
line argument which specifies which test case we want to run. If we do
not specify a command line argument then we run all of the test cases.

We have provided the above test program in the repository for this
discussion section. To use CTest, we need to tell it about this new test
program. We can do this by simply adding a new line to our
`CMakeLists.txt` file. Here is an example `CMakeLists.txt` file:

```cmake
cmake_minimum_required(VERSION 2.8...3.19)
enable_language(C)
enable_testing()

add_executable( square-directed-test square-directed-test.c square.c ece2400-stdlib.c )
add_test( square-directed-test square-directed-test )
```

Line 3 tells CMake to turn on support for testing with CTest. Line 5
specifies how to build the `square-directed-test` test program. Note we
need to link in the implementation of the course library because
`square-directed-test.c` uses it. Line 9 tells CMake that
`square-directed-test` is a test that should be managed by CTest. Modify
your `CMakeLists.txt` file to look like what is given above, rerun cmake,
build the test, and run it.

```bash
$ cmake .
$ make square-directed-test
$ ./square-directed-test
$ ./square-directed-test 1
```

The command `./square-directed-test` runs all test cases defined in
`square-directed-test` and does not print anything when the test case
passes. If you want to see more information about the passing assertions,
you can zoom in to a specific test case (e.g., test case 1) using
`./square-directed-test 1`. We will also be doing extensive _directed
testing_ and _random testing_. In directed testing, you explicitly use
test assertions to test as many corner cases as possible. In random
testing, you use random input values and compare the output to some
golden "reference" implementation to hopefully catch bugs missed in your
directed testing.

CMake provides a `test` target which can run all of the tests and
provides a summary.

```bash
$ make test
```

It is always a good idea to occasionally force a test to fail to ensure
your test framework is behaving correctly. Use VS Code to change file
`square.c` to look like this:

```c
#include "avg.h"

int square( int x )
{
  return x * x * x;
}
```

Note how we are returning the cube instead of the square of `x`. Then
rebuild and rerun the test like this:

```bash
$ make square-directed-test
$ make test
$ ./square-directed-test
```

You should see the test failing in the test summary, and then see
additional information about the failing test assertion when you
explicitly run the test program.

The ECE 2400 course standard library provides `ECE2400_DEBUG`, a macro
that allows you to print out extra information for debugging purposes.
One advantage of using the debug macro over `printf` is that this macro
incurs zero run time overhead in the evaluation build. Indeed, this macro
is simply replaced with a semi-colon when the build type is specified as
`eval`. That means the same implementation of your `sqrt` or `pow`
function can be reused for evaluation purposes without removing all
debugging `printf`s first. To use the debug macro, change file `square.c`
to look like this:

```c
#include "square.h"
#include "ece2400-stdlib.h"

int square( int x )
{
  ECE2400_DEBUG("square( %d )", x);
  int result = x * x;
  ECE2400_DEBUG("result is %d", result);
  return result;
}
```

Then rebuild and rerun the test like this:

```bash
$ make square-directed-test
$ ./square-directed-test
$ ./square-directed-test 1
```

Note how the debug info is suppressed when you are not zooming into a
specific test case. You should see two lines starting with `[ -info- ]`
in the output, which corresponds to the debug macro we added in
`square.c`. This info line tells the inputs and outputs to the `square`
function.
