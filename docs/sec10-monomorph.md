# Section 10: Static Polymorphism Lab

In this discussion, we will implement, test, and evaluate a generic list
data structure that uses static polymorphism and then compare it to an
object-oriented list that uses dynamic polymorphism and an
object-oriented list that is monomorphic.

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
$ git clone git@github.com:cornell-ece2400/ece2400-sec11 ece2400-sec10
$ cd ece2400-sec10
$ tree
```

The repository includes the following files:

 - `CMakeLists.txt` : CMake configuration script to generate Makefile
 - `src/ece2400-stdlib.h` : Header file for course standard library
 - `src/ece2400-stdlib.cc` : Source code for course standard library
 - `src/types-dpoly.h` : Header file `IObject` types
 - `src/types-spoly.cc` : Source code for `IObject` types
 - `src/SListInt.h` : Header file for monomorphic list
 - `src/SListInt.cc` : Source code for monomorphic list
 - `src/SListIObj.h` : Header file for dynamic polymorphic list
 - `src/SListIObj.cc` : Source code for dynamic polymorphic list
 - `src/SList.h` : Header file for static polymorphic list
 - `src/SList.inl` : Inline source code for static polymorphic list
 - `src/slist-int-adhoc.cc` : Ad-hoc test program for monorphic list
 - `src/slist-dpoly-adhoc.cc` : Ad-hoc test program for dynamic polymorphic list
 - `src/slist-spoly-adhoc.cc` : Ad-hoc test program for static polymorphic list
 - `test/types-spoly-directed.cc` : Directed test cases for `IObject` types
 - `test/slist-int-directed-test.cc` : Directed test cases for monomorphic list
 - `test/slist-dpoly-directed-test.cc` : Directed test cases for dynamic polymorphic list
 - `test/slist-spoly-directed-test.cc` : Directed test cases for static polymorphic list
 - `eval/slist-int-eval.cc` : Evaluation program for monomorphic list
 - `eval/slist-dpoly-eval.cc` : Evaluation program for dynamic polymorphic list
 - `eval/slist-spoly-eval.cc` : Evaluation program for static polymorphic list
 - `scripts/slist-*.sh` : Bash shell scripts for running evaluation
 - `scripts/slist-*.py` : Python scripts for plotting

Take a look at the `SList.h` header file to understand all of the
public member functions we will be implementing in this lab.

  - `SList()`: default constructor
  - `~SList()`: destructor
  - `SList( const SList<T>& lst )`: copy constructor
  - `void swap( SList<T>& lst )`: swap this list with given list
  - `SList<T>& operator=( const SList<T>& lst )`: assignment operator
  - `void push_front( const T& v )`: push item on front of list
  - `int size() cont`: return number of items in the list
  - `const T& at( int idx ) const`: return copy of item at given index (for reading)
  - `T& at( int idx )`: return reference to item at given index (for writing)
  - `void reverse_v1()`: reverse all items in list using algorithm v1
  - `void reverse_v2()`: reverse all items in list using algorithm v2
  - `void print() const`: print the list

## 2. Implementing a Generic Swap

Before starting work on a static polymorphic list, we will implement a
generic swap function with the following function prototype:

```cpp
  template <typename T> void swap(T &a, T &b);
```

Implement this function in the `src/swap.inl` file. When you are finished
build and run the directed tests like this:

```bash
$ ./scripts/build.sh
$ ./build/test/swap-directed-test
```

## 3. Implementing the Constructor, Destructor, Push Front

Now let's take a look at these functions in `src/SList.inl`:

  - `SList()`: default constructor
  - `~SList()`: destructor
  - `void push_front( const T& v )`: push item on front of list

We have implemented the default constructor for you. Implement
`push_front` based on the code discussed in lecture. We have started the
implementation of the destructor for you. Add the code to delete the
node. You can also look at the implementation for a monomorphic list from
the previous discussion section in `src/SListInt.cc`. When you are
finished use adhoc testing to quickly see if your data structure is
basically working:

```bash
$ ./build/src/slist-spoly-adhoc
```

## 4. Implementing Size and At

Obviously, we want to do more than just ad-hoc testing, but we need some
way to check what is in the list before we can write real directed test
cases. Let's take a look at these functions:

  - `int size() cont`: return number of items in the list
  - `const T& at( int idx ) const`: return copy of item at given index (for reading)
  - `T& at( int idx )`: return reference to item at given index (for writing)

We have already implemented these member functions for you, but you
should still take a close look. Notice how we have two versions of `at`:
the `const` version is automatically used by the compiler when reading an
item and the non-`const` version is automatically used by the compiler
when writing an item. You can also look at the implementation of `at` for
a monomorphic list from the previous discussion section in
`src/SListInt.cc`.

When you are finished, take a look at the first two directed test cases
in `slist-spoly-directed-test.c`. Build and run the test cases like this:

```bash
$ ./build/test/slist-spoly-directed-test 1
$ ./build/test/slist-spoly-directed-test 2
```

## 5. Implementing the Copy Constructor, Swap, and Assignment Operator

Because we need to implement a destructor, the rule of three tells us we
also need to implement a copy constructor and assignment operator. We
have implemented the copy constructor for you, but spend some time
reviewing it. We will be using a "copy-and-swap" pattern to implement the
assignment operator, so we need to start by implementing the `swap`
member function. This member functions swaps the internal state between
this list and the given list. You should be able implement this swap in
constant time. You need to use `::swap` to call the generic swap function
developed earlier in this discussion section, to ensure the compiler
knows to call the `swap` free function and not the `swap` member
function.

Once you have implemented `swap` you can now use the _copy-and-swap_
pattern which reuses the functionality already implemented in the copy
constructor and destructor to implement the overloaded assignment
operator:

```cpp
  template <typename T> SList<T> &SList<T>::operator=(const SList<T> &lst) {
    SList<T> tmp(lst); // create temporary copy of given list
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
$ ./build/test/slist-spoly-directed-test 3
$ ./build/test/slist-spoly-directed-test 4
$ ./build/test/slist-spoly-directed-test 5
$ ./build/test/slist-spoly-directed-test 6
```

## 6. Implementing Reverse v1

Take a look at the `SList.cc` source file to find the `reverse_v1` member
function. This function should reverse all of the items in the list.
Let's use a simple algorithm inspired by the following pseudocode used
for reversing an array:

```python
    def reverse( x, n ):
        for i in 0 to n / 2:
            swap(x[i], x[(n - 1) - i])
```

You can use the `size` member function to get the number of items in the
list and you can use the `at` member function to read and write items in
the list by index. You should use the `swap` free function you developed
earlier in the discussion esction. Note that you need to use `::swap` to
specify global scope (`::`) to ensure the compiler knows to call the
`swap` free function and not the `swap` member function.

When you are finished use the corresponding directed test cases to verify
your implementation is working:

```bash
$ ./scripts/build.sh
$ ./build/test/slist-spoly-directed-test 7
$ ./build/test/slist-spoly-directed-test 8
$ ./build/test/slist-spoly-directed-test 9
```

Using the generic `swap` function can reduce the number of times we need
to traverse the linked list, **so to have a fair comparison we need to go
back and modify the implementation of `reverse_v1` for the monomorphic
list and the dynamic polymorphic list to also use the generic `swap`
function exactly as you have done for the static polymorphic linked
list.** Do that now and rerun the tests for the previous data structures.

```bash
$ ./build/test/slist-int-directed-test
$ ./build/test/slist-dpoly-directed-test
```

## 7. Evaluating Reverse v1

We have provided you an evaluation program to quantitatively evaluate the
execution time of the linked list data structure and reverse v1
algorithm. Take a look at this evaluation program in
`slist-spoly-reverse-v1-eval.cc`. You can build and run this evaluation
programs along with the evaluation programs from the previous discussion
section using the provided bash script.

```bash
$ ./scripts/eval.sh
$ source ../scripts/slist-int-reverse-v1-eval.sh
$ source ../scripts/slist-dpoly-reverse-v1-eval.sh
$ source ../scripts/slist-spoly-reverse-v1-eval.sh
$ source ../scripts/slist-int-reverse-v1-eval.sh
$ source ../scripts/slist-dpoly-reverse-v1-eval.sh
$ source ../scripts/slist-spoly-reverse-v1-eval.sh
```

As in the previous discussion section, you can easily plot the result
data with a 0th, 1st, and 2nd order polynomial fit using the script:

```bash
# You should have a python environment created
$ source env-ece2400/bin/activate
$ python3 ../scripts/slist-plot.py ./slist-int-reverse-v1-eval.txt
$ python3 ../scripts/slist-plot.py ./slist-dpoly-reverse-v1-eval.txt
$ python3 ../scripts/slist-plot.py ./slist-spoly-reverse-v1-eval.txt
$ python3 ../scripts/slist-plot-all.py
```

Then you can download the PDF file using VS Code and then open the PDF
files on your local workstation or laptop. Do these quantitative results
match your qualitative expectations given what you know about the
asymptotic time complexity of the reverse v1 algorithm? Do these
quantitative results match your qualitative expectations given what you
know about the performance of monomorphic, dynamic polymorphic, and
static polymorphic data structures?

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
algorithm. We also modified the `CMakeLists.txt` file to provide a new
target that will: (1) build all of the evaluation programs; (2) run all
of the experiments; and (3) generate all the plots. This demonstrates the
power of using automation through Bash scripts, Python scripts, and CMake
to enable much more productive workflows.

```bash
    % # TODO: command here
```

Then you can download the PDF files for both plots using VS Code and then
open the PDF file on your local workstation or laptop. Do these
quantitative results match your qualitative expectations given what you
know about the asymptotic time complexity of reverse v1 vs v2 algorithms?

## 10. Extensions to Try On Your Own

The code used in this discussion section serves as an excellent framework
for continuing to explore generic programming with static polymorphism on
your own. Consider copying over the code for a C++ string class presented
elsewhere in the course so you can implement a list of strings
(`SList<String>`) data structure or implement a list of lists of integers
(`SList<SList<int>>`) data structure. Alternatively, try to implement the
`SListIObj` using an internal `SList<IObject*>` list. The internal list
can manage the nodes, and the `SListIObj` data structure just needs to
clone objects before pushing back the correponding pointer and delete all
objects in the destructor.
