# Section 7: Sorting Lab

In this discussion, we will implement, test, and evaluate a simple
selection sort algorithm.

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
$ git clone https://github.com/cornell-ece2400/ece2400-sec07-2025.git ece2400-sec07
$ cd ece2400-sec07
$ tree
```

The repository includes the following files:

 - `CMakeLists.txt` : CMake configuration script to generate Makefile
 - `src/ece2400-stdlib.h` : Header file for course standard library
 - `src/ece2400-stdlib.c` : Source code for course standard library
 - `src/selection-sort-int.h` : Header file for selection sort
 - `src/selection-sort-int.c` : Source code for selection sort
 - `src/selection-sort-int-adhoc.c` : Ad-hoc test program for selection sort
 - `test/selection-sort-int-helper-test.c` : Whitebox test cases for selection sort
 - `test/selection-sort-int-directed-test.c` : Directed test cases for selection sort
 - `eval/sort.dat` : Input dataset for evaluation
 - `eval/selection-sort-int-eval.c` : Evaluation program for selection sort
 - `scripts/selection-sort-int-eval.py` : Python script for plotting
 - `scripts/build.sh` : Script to build entire project
 - `scripts/eval.sh` : Run evaluation program
 - `scripts/test.sh` : Run all unit tests

## 2. Implementing Find Minimum Helper Function

Take a look at the `src/selection-sort-int.c` source file to find the
`find_minimum` function is a helper function. It takes as input an array
and a range to work on `[begin, end)`. Note that the range is inclusive
of begin and exclusive of end. So if we have an array of eight elements
and we want to find the minimum of the entire array we would set begin to
0 and end to 8. The function should return the index of where the minimum
value is stored in the given array.

Go ahead and implement `find_minimum` using VS Code. When you are
finished use adhoc testing to quickly see if your algorithm is basically
working:

```bash
$ chmod +x ./scripts/*.sh
$ ./scripts/build.sh
$ ./build/src/selection-sort-int-adhoc
```

## 3. Testing Find Minimum Helper Function

Before starting to work on the sorting algorithm, **you must rigorously
test your helper function!** We have provided you a directed test case
to test just the helper function. Take a look at this directed test in
`test/selection-sort-int-directed-test.c`. You can build and run this
test case like this:

```bash
$ ./scripts/build.sh
$ ./build/test/selection-sort-int-helper-test 1
```

Add at least one more directed test before continuing.

## 4. Implementing Selection Sort

Take a look at the `src/selection-sort-int.c` source file to find the
`selection_sort_int` function. It takes as input an array and the size of
that array. Go ahead and implement `selection_sort_int` using VS Code.
The function should use `find_minimum` helper function. When you are
finished modify the adhoc test to test selection sort, then quickly see
if your algorithm is basically working:

```bash
$ ./scripts/build.sh
$ ./build/src/selection-sort-int-adhoc
```

Or feel free to just move on to using the test framework in the next
section.

## 5. Testing Selection Sort

Obviously, we want to do more than just ad-hoc testing. We have provided
you two very simple directed test cases to test selection sort. Take a
look at this directed test in `test/selection-sort-int-directed-test.c`.
You can build and run this test case like this:

```bash
$ ./scripts/build.sh
$ ./build/test/selection-sort-int-directed-test 1
$ ./build/test/selection-sort-int-directed-test 2
```

Add at least one more directed test before continuing. Try and think of a
tricky corner case.

## 6. Evaluating Selection Sort

We have provided you an evaluation program to quantitatively evaluate the
execution time and space usage of your selection sort implementation.
Take a look at this evaluation program in
`eval/selection-sort-int-eval.c`. You can build and run this evaluation
program like this:

```bash
$ ./scripts/eval.sh
```

You will see that the evaluation program takes two command line
arguments. The first is what pattern to use when generating the data for
the input array, and the second is the size of the input array. Let's do
an experiment to see how the execution time of your selection sort
implementation grows as a function of the input array size for an input
array with uniform random input values.

```bash
$ ./scripts/eval.sh urandom 10000
```

You can use a Bash for loop to run a command multiple times with
different parameters in a single step like this:

```bash
$ for i in {1000,2000,4000,6000,8000,10000,12000}; do \
    ./scripts/eval.sh urandom $i; \
  done
```

Or even better, you can save the output from each run to a text file like
this:

```bash
$ rm -f results.txt
$ for i in {1000,2000,4000,6000,8000,10000,12000}; do \
    ./scripts/eval.sh urandom $i; \
  done
``` 

Each time `eval.sh` was ran, it appended new data to the bottom of `results.txt`.
Now you can use the standard `grep` and `cut` command
line tools to extract the average execution time for each experiment:

```bash
$ grep "Average: " results.txt | cut -d' ' -f5
```

Notice how learning to use Bash scripting and various Linux command line
tools can help you be more productive! You can now copy and paste this
data into a spreadsheet program to plot the data and do a polynomial
regression. However, you might also consider using a Python script to
help automate this process. Take a look at an example Python script in
`scripts/selection-sort-int-eval-plot.py`. Find the following two lines:

```python
n = [  1000,  2000,  4000,  6000,  8000, 10000, 12000 ]
t = [     0,     0,     0,     0,     0,     0,     0 ]
```

Replace the zero values in the `t` array with the measured execution time
for each corresponding input array size. Now you can easily plot this
data long with a 0th, 1st, and 2nd order polynomial fit using the script:

```bash
$ python ../scripts/selection-sort-int-plot.py
```

Then you can download the PDF file using VS Code and then open the PDF
file on your local workstation or laptop. Do these quantitative results
match your qualitative expectations given what you know about the
asymptotic time complexity of selection sort?