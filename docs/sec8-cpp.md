# Section 8: Object-Oriented Programming Lab

In this discussion, we will implement, test, and evaluate an
object-oriented list data structure using C++.

## 1. Getting the Code

As usual, log into `ecelinux` with VS Code. You can clone the starter code from our GitHub:

```bash
$ git clone git@github.com:cornell-ece2400/ece2400-sec08-2025.git ece2400-sec08
$ cd ece2400-sec08
$ tree
```

The repository includes the following files:

 - `CMakeLists.txt` : CMake configuration script to generate Makefile
 - `include/ece2400-stdlib.h` : Header file for course standard library
 - `src/ece2400-stdlib.cc` : Source code for course standard library
 - `include/SListInt.h` : Header file for list
 - `src/SListInt.cc` : Source code for list
 - `src/slist-int-adhoc.cc` : Ad-hoc test program for list
 - `test/slist-int-directed-test.cc` : Directed test cases for list
 - `eval/slist-int-eval.cc` : Evaluation program for list
 - `scripts/*-eval.sh` : Bash shell scripts for running evaluation
 - `scripts/*-plot.py` : Python scripts for plotting
 - `scripts/build.sh` : Builds the project
 - `scripts/test.sh` : Builds and tests the project

Take a look at the `SListInt.h` header file to understand all of the
public member functions we will be implementing in this lab.

  - `SListInt()`: default constructor
  - `~SListInt()`: destructor
  - `SListInt(const SListInt& lst)`: copy constructor
  - `void swap(SListInt& lst)`: swap this list with given list
  - `SListInt& operator=(const SListInt& lst)`: assignment operator
  - `void push_front(int v)`: push item on front of list
  - `int size() cont`: return number of items in the list
  - `int at(int idx) const`: return copy of item at given index (for reading)
  - `int& at(int idx)`: return reference to item at given index (for writing)
  - `void reverse_v1()`: reverse all items in list using algorithm v1
  - `void reverse_v2()`: reverse all items in list using algorithm v2
  - `void print() const`: print the list

## 2. Implementing the Constructor, Destructor, Push Front

Let's start by taking a look at these functions:

  - `SListInt()`: default constructor
  - `~SListInt()`: destructor
  - `void push_front(int v)`: push item on front of list

We have implemented the default constructor for you. Implement
`push_front` based on the code discussed in lecture. We have started the
implementation of the destructor for you. Add the code to delete the
node. When you are finished taking a look use adhoc testing to quickly
see if your data structure is basically working:

```bash
$ g++ -Iinclude -Wall -o slist-int-adhoc src/slist-int-adhoc.cc src/SListInt.cc
$ ./slist-int-adhoc
```

From here on out, you should really just use the build script:

```bash
$ ./scripts/build.sh
$ ./build/src/slist-int-adhoc
```

Until you implement all the functions, you will see errors like this:

```
slist-int-adhoc: src/SListInt.cc:89: void SListInt::push_front(int): 
  Assertion `false && "Replace this with your push_front code"' failed.
```

## 3. Implementing Size and At

Obviously, we want to do more than just ad-hoc testing, but we need some
way to check what is in the slist before we can write real directed test
cases. Let's next implement these functions:

  - `int size() cont`: return number of items in the list
  - `int at(int idx) const`: return copy of item at given index (for reading)
  - `int& at(int idx)`: return reference to item at given index (for writing)

We have already implemented the `size` member functions for you. Notice
how we have two versions of `at`: the `const` version is automatically
used by the compiler when reading an item and the non-`const` version is
automatically used by the compiler when writing an item. The non-`const`
version returns a _reference_ which enables one to directly modify an
item that is stored in the list.

When you are finished, take a look at the first two directed test cases
in `slist-int-directed-test.cc`. Build and run the test cases like this:

```bash
$ ./scripts/build.sh
$ ./build/test/slist-int-directed-test 1
$ ./build/test/slist-int-directed-test 2
```

## 6. Implementing the Copy Constructor, Swap, and Assignment Operator

Because we need to implement a destructor, the rule of three tells us we
also need to implement a copy constructor and assignment operator. We
have implemented the copy constructor for you, but spend some time
reviewing it. We will be using a copy-and-swap pattern to implement the
assignment operator, so we need to start by implementing the `swap`
member function. This member functions swaps the internal state between
this list and the given list.

Once you have implemented `swap` you can now use the _copy-and-swap_
pattern which reuses the functionality already implemented in the copy
constructor and destructor to implement the overloaded assignment
operator:

```cpp
SListInt &SListInt::operator=(const SListInt &lst) {
  SListInt tmp(lst); // create temporary copy of given list
  swap(tmp);         // swap this list with temporary list
  return *this;      // destructor called for temporary list
}
```

We first use the copy constructor to make a temporary copy of the given
list. We then swap the head pointers between this list and the temporary
list. Now this list has a copy of all of the nodes in the given list, and
the temporary list has all of the nodes which used to be in this list.
When the temporary list goes out of scope, the destructor will handle
deleting all of the nodes which used to be in this list. Here is a good
article that discusses this pattern:

  - <https://mropert.github.io/2019/01/07/copy_swap_20_years>

When you are finished use the corresponding directed test cases to verify
your implementation is working:

```bash
$ ./scripts/build.sh
$ ./build/test/slist-int-directed-test 3
$ ./build/test/slist-int-directed-test 4
$ ./build/test/slist-int-directed-test 5
$ ./build/test/slist-int-directed-test 6
```

## 5. Implementing Reverse v1

Take a look at the `SListInt.cc` source file to find the `reverse_v1`
member function. This function should reverse all of the items in the
list. Let's use a simple algorithm inspired by the following pseudocode
used for reversing an array:

```python
def reverse( x, n ):
  for i in 0 to n/2:
    lo = i
    hi = (n-1) - i
    swap( x[lo], x[hi] )
```

You can use the `size` member function to get the number of items in the
list and you can use the `at` member function to read and write items in
the list by index. When you are finished use the corresponding directed
test cases to verify your implementation is working:

```bash
$ ./scripts/build.sh
$ ./build/test/slist-int-directed-test 7
$ ./build/test/slist-int-directed-test 8
$ ./build/test/slist-int-directed-test 9
```

## 6. Evaluating Reverse v1

We have provided you an evaluation program to quantitatively evaluate the
execution time of the linked list data structure and reverse v1
algorithm. Take a look at this evaluation program in
`slist-int-reverse-v1-eval.c`. You can build and run this evaluation
program like this:

```bash
$ ./scripts/eval.sh
```

You will see that the evaluation program takes one command line argument
specifying the size of the input array. Let's do an experiment to see how
the execution time of the reverse v1 algorithm grows as a function of the
input array size.

```bash
$ ./scripts/eval.sh 200
```

While we could run each experiment individually, let's automate this by
using a Bash for loop to run a command multiple times with different
parameters in a single step. We could enter the Bash for loop on the
command line, but let's put it in a Bash script instead so we can easily
run it. A Bash script is just a file that contains a sequence of Linux
commands that you can run together. We have provided you a Bash script
that runs several experiments and saves all of the results in a file
named `slist-int-reverse-v1-eval.txt`. You can look at the Bash script in
`scripts/slist-int-reverse-v1-eval.sh`. You can run the commands in the
Bash script by using the `source` command:

```bash
$ ./scripts/slist-int-reverse-v1-eval.sh
```

Instead of using the standard `grep` and `cut` command line tools to
extract the average execution time for each experiment, we can just
incorporate reading the results into the beginning of a Python plotting
script. Take a look at an example Python script in
`scripts/slist-int-reverse-v1-eval-plot.py`. You can easily plot the
result data with a 0th, 1st, and 2nd order polynomial fit using the
script. Before you run the Python script, activate your Python environment from last section, so that you have an install of `matplotlib`:

```bash
$ source env-ece2400/bin/activate
$ python3 ./scripts/slist-int-reverse-v1-plot.py
```

Then you can download the PDF file using VS Code and then open the PDF
file on your local workstation or laptop. Do these quantitative results
match your qualitative expectations given what you know about the
asymptotic time complexity of the reverse v1 algorithm?

## 7. Implementing Reverse v2

Let's experiment with an alternative reverse algorithm. This reverse v2
algorithm should use the following steps:

 1. Create temporary singly linked list
 2. Push front all values from this list onto temporary list
 3. Swap this list with the temporary list

We already implemented this function for you, so it should pass all the tests.

## 8. Evaluating Reverse v2

We have provided you an evaluation program to quantitatively evaluate the
execution time of the linked list data structure and reverse v2
algorithm. You can build and run this evaluation program like this:

```bash
$ ./scripts/eval.sh 2000
```

We have provided you a Bash script and two Python plotting scripts
similar in spirit to what we worked with in a previous section. Let's run
the bash script and create the plots.

```bash
$ ./scripts/slist-int-reverse-v2-eval.sh
$ python3 ./scripts/slist-int-reverse-v2-plot.py
$ python3 ./scripts/slist-plot-all.py
```

Then you can download the PDF files for both plots using VS Code and then
open the PDF file on your local workstation or laptop. Do these
quantitative results match your qualitative expectations given what you
know about the asymptotic time complexity of reverse v1 vs v2 algorithms?

## 10. Extensions to Try On Your Own

The code used in this discussion section serves as an excellent framework
for continuing to explore object-oriented programming on your own.
Consider copying over the code for a C++ string class presented elsewhere
in the course so you can implement a list of strings (`SListStr`) data
structure.
