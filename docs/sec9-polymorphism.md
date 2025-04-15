# Section 9: Dynamic Polymorphism Lab

In this discussion, we will implement, test, and evaluate an
object-oriented list data structure that uses dynamic polymorphism and
then compare it to an object-oriented list that is monomorphic.

## 1. Logging Into `ecelinux` with VS Code

Follow the same process as in the last section. Find a free workstation
and log into the workstation using your NetID and standard NetID
password. Then complete the following steps (described in more detail in
the last section):

 - Start VS Code
 - Use _View > Command Palette_ to execute _Remote-SSH: Connect Current Window to Host..._
 - Enter `netid@ecelinux.ece.cornell.edu`
 - Use _View > Explorer_ to open folder on `ecelinux`
 - Use _View > Terminal_ to open terminal on `ecelinux`

Now clone the GitHub repo we will be using in this section using the
following commands:

```bash
$ git clone git@github.com:cornell-ece2400/ece2400-sec09-2025 ece2400-sec09
$ cd ece2400-sec09
$ tree
```

The repository includes the following files:

 - `CMakeLists.txt` : CMake configuration script to generate Makefile
 - `include/ece2400-stdlib.h` : Header file for course standard library
 - `src/ece2400-stdlib.cc` : Source code for course standard library
 - `include/types-dpoly.h` : Header file `IObject` types
 - `src/types-dpoly.cc` : Source code for `IObject` types
 - `include/SListInt.h` : Header file for monomorphic list
 - `src/SListInt.cc` : Source code for monomorphic list
 - `include/SListIObj.h` : Header file for dynamic polymorphic list
 - `src/SListIObj.cc` : Source code for dynamic polymorphic list
 - `src/slist-int-adhoc.cc` : Ad-hoc test program for monorphic list
 - `src/slist-dpoly-adhoc.cc` : Ad-hoc test program for dynamic polymorphic list
 - `test/types-dpoly-directed.cc` : Directed test cases for `IObject` types
 - `test/slist-int-directed-test.cc` : Directed test cases for monomorphic list
 - `test/slist-dpoly-directed-test.cc` : Directed test cases for dynamic polymorphic list
 - `eval/slist-int-eval.cc` : Evaluation program for list
 - `eval/slist-dpoly-eval.cc` : Evaluation program for list
 - `scripts/slist-*.sh` : Bash shell scripts for running evaluation
 - `scripts/slist-*.py` : Python scripts for plotting
 - `scripts/build.sh` : Bash script for initial building of project

Take a look at the `SListIObj.h` header file to understand all of the
public member functions we will be implementing in this lab.

  - `SListIObj()`: default constructor
  - `~SListIObj()`: destructor
  - `SListIObj( const SListIObj& lst )`: copy constructor
  - `void swap( SListIObj& lst )`: swap this list with given list
  - `SListIObj& operator=( const SListIObj& lst )`: assignment operator
  - `void push_front( const IObject& v )`: push item on front of list
  - `int size() cont`: return number of items in the list
  - `IObject* at( int idx ) const`: return copy of pointer to item at given index (for reading)
  - `IObject*& at( int idx )`: return reference to pointer to item at given index (for writing)
  - `void reverse_v1()`: reverse all items in list using algorithm v1
  - `void reverse_v2()`: reverse all items in list using algorithm v2
  - `void print() const`: print the list

## 2. Implementing the Clone Virtual Member Function

Before starting work on a dynamic polymorphic list, we will finish
implementing a set of classes that all inherit from the `IObject` base
class. Take a look at the `Integer`, `Double`, and `Complex` classes in
`src/types-dpoly.h` and `src/types-dpoly.cc`. Implement the `clone`
member function for each of these clases based on the code discussed in
lecture.

When you are finished build and run the directed tests like this:

```bash
$ ./scripts/build.sh
$ ./scripts/test.sh
```

## 3. Implementing the Constructor, Destructor, Push Front

Now let's take a look at these functions in `src/SListIObj.cc`:

  - `SListIObj()`: default constructor
  - `~SListIObj()`: destructor
  - `void push_front( const IObject& v )`: push item on front of list

We have implemented the default constructor for you. Implement
`push_front` based on the code discussed in lecture. Think critically
about how to create a copy of the given `IObject`. We have started the
implementation of the destructor for you. Add the code to delete the
node. Do we need to do anything special about the `IObject`? You can also
look at the implementation for a monomorphic list from the previous
discussion section in `src/SListInt.cc`. When you are finished taking a
look use adhoc testing to quickly see if your data structure is basically
working:

```bash
$ ./scripts/test.sh
$ ./build/src/slist-dpoly-adhoc
```

## 4. Implementing Size and At

Obviously, we want to do more than just ad-hoc testing, but we need some
way to check what is in the slist before we can write real directed test
cases. Let's take a look at these functions:

  - `int size() cont`: return number of items in the list
  - `IObject* at(int idx) const`: return copy of pointer to item at given index (for reading)
  - `IObject*& at(int idx)`: return reference to pointer to item at given index (for writing)

We have already implemented these member functions for you, but you
should still take a close look. Notice how we have two versions of `at`:
the `const` version is automatically used by the compiler when reading an
item and the non-`const` version is automatically used by the compiler
when writing an item. Keep in mind that `at` returns an `IObject`
pointer. You cannot copy an `IObject` since it is an abstract base class;
you can only work with pointers and references to an `IObject`. You can
also look at the implementation of `at` for a monomorphic list from the
previous discussion section in `src/SListInt.cc`.

When you are finished, take a look at the first two directed test cases
in `slist-dpoly-directed-test.cc`. Build and run the test cases like this:

```bash
$ ./scripts/build.sh
$ ./build/test/slist-dpoly-directed-test 1
$ ./build/test/slist-dpoly-directed-test 2
```

## 5. Implementing the Copy Constructor, Swap, and Assignment Operator

Because we need to implement a destructor, the rule of three tells us we
also need to implement a copy constructor and assignment operator. We
have implemented the copy constructor for you, but spend some time
reviewing it. We will be using a "copy-and-swap" pattern to implement the
assignment operator, so we need to start by implementing the `swap`
member function. This member functions swaps the internal state between
this list and the given list.

Once you have implemented `swap` you can now use the _copy-and-swap_
pattern which reuses the functionality already implemented in the copy
constructor and destructor to implement the overloaded assignment
operator:

```cpp
SListIObj &SListIObj::operator=(const SListIObj &lst) {
  SListIObj tmp(lst); // create temporary copy of given list
  swap(tmp);          // swap this list with temporary list
  return *this;       // destructor called for temporary list
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
    % ./scripts/build.sh
    % ./build/test/slist-dpoly-directed-test 3
    % ./build/test/slist-dpoly-directed-test 4
    % ./build/test/slist-dpoly-directed-test 5
    % ./build/test/slist-dpoly-directed-test 6
```

## 6. Implementing Reverse v1

Take a look at the `SListIObj.cc` source file to find the `reverse_v1`
member function. This function should reverse all of the items in the
list. Let's use a simple algorithm inspired by the following pseudocode
used for reversing an array:

```python
def reverse(x, n):
    for i in 0 to n/2:
        lo = i
        hi = (n - 1) - i
    swap(x[lo], x[hi])
```

You can use the `size` member function to get the number of items in the
list and you can use the `at` member function to read and write items in
the list by index. Think critcally about how to compare and swap
`IObjects`. Can we just swap the pointers? You can also look at the
implementation of `reverse_v1` for a monomorphic list from the previous
discussion section in `src/SListInt.cc`. When you are finished use the
corresponding directed test cases to verify your implementation is
working:

```bash
$ ./scripts/build.sh
$ ./build/test/slist-dpoly-directed-test 7
$ ./build/test/slist-dpoly-directed-test 8
$ ./build/test/slist-dpoly-directed-test 9
```

## 7. Evaluating Reverse v1

We have provided you an evaluation program to quantitatively evaluate the
execution time of the linked list data structure and reverse v1
algorithm. Take a look at this evaluation program in
`slist-dpoly-reverse-v1-eval.c`. You can build this evaluation program
along with the evaluation program from last week like this:

```bash
$ ./scripts/eval.sh
```

As discussed in the previuod discussion section, you can easily run an
evaluation program using a bash script like this:

```bash
$ source ./scripts/slist-int-reverse-v1-eval.sh
$ source ./scripts/slist-dpoly-reverse-v1-eval.sh
```

We now have a single Python plotting script that can easily plot the
result data from any evaluation run with a 0th, 1st, and 2nd order
polynomial fit. We also have a single Python plotting script that can
plot all of the data on a single plot.

```bash
$ python ./scripts/slist-plot.py ./slist-int-reverse-v1-eval.txt
$ python ./scripts/slist-plot.py ./slist-dpoly-reverse-v1-eval.txt
$ python ./scripts/slist-plot-all.py
```

Then you can download the PDF file using VS Code and then open the PDF
files on your local workstation or laptop. Do these quantitative results
match your qualitative expectations given what you know about the
asymptotic time complexity of the reverse v1 algorithm? Do these
quantitative results match your qualitative expectations given what you
know about the performance of monomorphic vs dynamic polymorphic data
structures?

## 8. Implementing Reverse v2

Let's experiment with an alternative reverse algorithm. This reverse v2
algorithm should use the following steps:

 1. Create temporary singly linked list
 2. Push front all values from this list onto temporary list
 3. Swap this list with the temporary list

You can also look at the implementation of `reverse_v2` for a monomorphic
list from the previous discussion section in `src/SListInt.cc`. When you
are finished use the corresponding directed test cases to verify your
implementation is working:

```bash
$ ./scripts/build.sh
$ ./build/test/slist-spoly-directed-test 10
$ ./build/test/slist-spoly-directed-test 11
$ ./build/test/slist-spoly-directed-test 12
```

Your implementation should now pass all tests:

```bash
$ ./scripts/test.sh
```

## 9. Evaluating Reverse v2

We have provided you an evaluation program to quantitatively evaluate the
execution time of the linked list data structure and reverse v2
algorithm. You can build the evaluation programs for the reverse v2
algorithm on both a monomorphic and dynamic polymorphic list like this:

```bash
$ ./scripts/eval.sh
```

We have provided you a Bash script and two Python plotting scripts
similar in spirit to what we worked with in a previous section. Let's run
the bash script and create the plots.

```bash
$ source ./scripts/slist-int-reverse-v2-eval.sh
$ source ./scripts/slist-dpoly-reverse-v2-eval.sh
# You should have a python environment created
$ source env-ece2400/bin/activate
$ python ./scripts/slist-plot.py ./slist-int-reverse-v2-eval.txt
$ python ./scripts/slist-plot.py ./slist-dpoly-reverse-v2-eval.txt
$ python ./scripts/slist-plot-all.py
```

Then you can download the PDF files for both plots using VS Code and then
open the PDF file on your local workstation or laptop. Do these
quantitative results match your qualitative expectations given what you
know about the asymptotic time complexity of reverse v1 vs v2 algorithms?

## 10. Extensions to Try On Your Own

The code used in this discussion section serves as an excellent framework
for continuing to explore object-oriented programming with dynamic
polymorphism on your own. Consider copying over the code for a C++ string
class presented elsewhere in the course and making this class inherit
from `IObject`. Now you should be able to implement a list of strings
data structure or a list that stores a heterogeneous mix of integers,
doubles, complex numbers, _and_ strings! Think critically about how you
can use dynamic polymorphism to create lists of lists and try it out.
