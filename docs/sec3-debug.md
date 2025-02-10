# Section 4: C Debugging, Testing, Formatting

In the previous discussion section, you learned how to use C build and
test frameworks to help automate the process of compiling and verifying
your programs. In this discussion, we will continue to learn about new
tools that can help us better debug, test, and format our programs.

## 1. Getting the Code

Once again, we will be using VS Code to log into the ecelinux servers:

 - Start VS Code
 - Use _View > Command Palette_ to execute _Remote-SSH: Connect Current Window to Host..._
 - Enter `netid@ecelinux.ece.cornell.edu`
 - Use _View > Explorer_ to open folder on `ecelinux`
 - Use _View > Terminal_ to open terminal on `ecelinux`

First, fork the repositiory to your own GitHub profile: [https://github.com/cornell-ece2400/ece2400-sec02-2025/fork](https://github.com/cornell-ece2400/ece2400-sec02-2025/fork)

**Uncheck the box *Copy the main branch only*. We want all the branches!**

Now clone your forked version of the repo using the following commands (replace `your-github`):

```bash
$ git clone --branch sec04 git@github.com:your-github/ece2400-sec02-2025 ece2400-sec04
$ cd ece2400-sec04
$ tree
```

The directory includes the following files:

 - `avg-main.c`: source and main for single-file `avg` program
 - `square.h`: header file for the `square` function
 - `square.c`: source file for the `square` function
 - `square-adhoc.c` : test driver for `square` function


## 2. Using GDB for Debugging

There are two kinds of C/C++ programmers in the world: printf debuggers
and GDB debuggers. Students should use whichever debugging techniques
make them the most productive coders. However, students should also
not underestimate the power of attaching a debugger to your program.

Let's start by compiling the single-file program that to test our ubiquitous `avg`
function:

```bash
$ gcc -Wall -g -O0 -o avg-main src/avg-main.c
$ ./avg-main
```

Notice how we include the `-g -O0` options for debugging.
This code has a bug and should give the wrong output. Let's start by using printf
debugging. Add some extra printfs to observe the state of the program as
it executes.

```c
int avg(int x, int y) {
  printf("x = %d, y = %d\n", x, y);
  int sum = x - y;
  printf("sum = %d\n", sum);
  return sum / 2;
}
```

You should be able to see that the value for the `sum` variable is
incorrect, but the value for the `x` and `y` variables are correct. This
means we can narrow our focus to line 3 in the above code snippet.
Hopefully, you should be able to spot the bug. Fix the bug, recompile,
and rerun the program.

Let's now try tracing the execution of this program using GDB. First,
remove the extra printfs, undo your bug fix, and then recompile. Then you
can start GDB like this

```bash
$ gdb -tui avg-main
```

GDB will drop you into a GDB "prompt" which you can use to interactively
execute your program. Your source code will show up at the top, and the
GDB prompt is at the bottom. Here are some useful GDB commands:

  - `break location` : set a breakpoint
  - `run` : start running the program
  - `record` : start recording the execution for reverse debugging
  - `step` : execute the next C statement, step into a function call
  - `next` : execute the next C statement, do not step into a function call
  - `rs` : reserve step, undo the execution of current C statement
  - `print var` : print a C variable
  - `continue` : continue on to the next breakpoint
  - `quit` : exit GDB
  - `refresh` : refresh the source code display

GDB is very sophisticated so of course there are many more commands you
can use, but these are enough to get started. Let's start by just running
the program in GDB:

```
(gdb) run
```

Now let's try again, but first let's set a breakpoint to tell GDB to stop
at a certain function or line in the program. The following will set a
breakpoint at the beginning of the `main` function.

```
(gdb) break main
```

You can see a little `b+` marker in the margin next to the first
statement in the `main` function indicating the location of the
breakpoint. You might need to use `refresh` to get GDB to refresh the
source code display. We can now use `run` to start the program running.
The execution should stop at the beginning of the function `main`. You
should see the first line of the function highlighted.

```
(gdb) refresh
(gdb) run > temp
```

We are using `> temp` so that any output from printf goes to a file and
does not mess up the source code display. We can use `record` to save the computer's state after every instruction.
Then, we can step through the
execution of each C statement using the `step` command.

```
(gdb) record
(gdb) step
```

Keep using `step` until you get into the `avg` function You can print out
the value of any variable using the `print` command:

```
(gdb) print x
(gdb) print y
(gdb) print sum
```

You can also step backwards using the `rs` command:

```
(gdb) rs
```

Try stepping forward and backwards through the `avg` function and print
out various variables to see how they change during the execution. You
can use `quit` to exit.

```
(gdb) quit
```

Now fix the bug and rerun the test.

## 3. Using `clang-format` for Autoformatting

The course coding conventions are located here:

 - [https://cornell-ece-docs.github.io/ece-tutorials/doc0-coding-style/](https://cornell-ece-docs.github.io/ece-tutorials/doc0-coding-style/)

Following these conventions and indeed _any_ coding conventions can be
quite tedious. Many software companies and open-source software projects
use code formatters to automate the process of formatting their code
to the style guide. One such tool is called `clang-format`. This
tool takes a style file that specifies the coding conventions and then
tries to reformat your code so it adheres to the style file. We have
provided you a style file named `.clang-format` that adheres to the
course coding conventions. You can run `clang-format` like this;

```bash
$ clang-format -i src/avg-main.c
```

`clang-format` will output the autoformatted source code. To really see
this in action though we need to write some poorly formatted code! Modify
the `avg` function in `avg-main.c` to look like this:

```c
    int avg( int x, int y) {
      int sum = x + y;
        return sum/2; }
```

The space before/after parenthesis is not consistent, and the curly braces are
on the wrong lines. Run `clang-format` again like this:

```bash
$ clang-format src/avg-main.c
```

Verify that the code is beautiful again. Note that we are not actually
modifying the code, just outputting the autoformatted code to the console.
If you `cat` the source code it still is not formatted correctly:

```bash
$ cat src/avg-main.c
```

You can use the `-i` command line option to autoformat the code
"in-place"

```bash
$ clang-format -i src/avg-main.c
```

With autoformatting, students no longer have any excuse for poorly
formatted code! However, do keep in mind that the autoformatter does not
fix _everything_. This is where [clangd](https://clangd.llvm.org/) and `.gitignore` comes in.
Those two tools can help you with your variables names and prevent you from committing build files.
Students will still need to ensure:

 - no irrelevant instructor supplied comments are included
 - an appropriate amount of comments are included (not too little, not too much!)

## 4. Using GitHub Actions for Continuous Integration

What is the point of test cases and formatting standards if people can
push sloppy code to your GitHub Repo anyways?
Continuous integration is the process of _continually_ integrating your code 
changes with your tests. It enfornces some level of code quality, before it is committed to your main repository branch.
We will be using GitHub Actions to facilitate continuous integration. GitHub Actions will automatically run
all tests for a project every time code is pushed to GitHub.

To start, you need to enable GitHub Actions with a workflow file. We have already created one
of those files for you, and you can see it here:

```bash
$ cat .github/workflows/actions.yml
```

Although it is not important to understand the details of this YAML file,
you can still see that it includes steps to first compile
`avg-main.c`, run it, and check the output is `15`.


Go ahead and commit all of the work you have done in this tutorial.
Then, push your local commits to your forked repository on GitHub. As a
reminder these are the appropriate git commands:

```bash
$ git add -u
$ git commit -m "working on sec04"
$ git push
```

Revisit the GitHub Actions page for this repository.

 - `https://github.com/your-github/ece2400-sec02-2025/actions`

Click on the current commit "working on sec04", and then
watch as GitHub Actions brings up a virtual machine and runs all the
steps to compile and test your code. If you click on "build" job, you can see the specific
failure or pass message. If you still need to, fix your code on `ecelinux`, commit and
push your fix, and verify your code is now passing the tests on GitHub
Actions.

Note, you should _always_ test your code directly on `ecelinux` first. Do
not use GitHub Actions as the primary way to run your tests. GitHub
Actions is only for continuous integration testing. In this economy,
you can very quickly run out of [Actions minutes](https://docs.github.com/en/billing/managing-billing-for-your-products/managing-billing-for-github-actions/about-billing-for-github-actions).

## 5. To-Do On Your Own

If you have time, try compiling `src/square-adhoc.c` in the repository
and executing the resulting binary. You make need to use CMake. Use GDB
debugging to find the bug and fix it.

After fixing the bug, add to the GitHub Actions workflow file with
correctness checks for `src/square-adhoc.c`.

Finally, update the `.github/workflows/actions.yml` file to check that
any committed code is properly formatted. Maybe you could even use a GitHub
Action from the [Actions Marketplace](https://github.com/marketplace?type=actions).
