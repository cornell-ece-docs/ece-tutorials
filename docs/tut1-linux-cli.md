# Linux Command Line Basics

This tutorials serves as gentle introduction to the basics of
using the Linux command line on the `ecelinux` servers. Given you know how to 
log into the servers, this tutorial will cover basic Linux command line usage.
In the following tutorial, we will cover [Git](https://git-scm.com/).

## 1. Starting a Remote Shell

We will be using the `ecelinux` servers for all of the programming
assignments. The `ecelinux` servers all run the Red Hat Enterprise Linux 
operating system.Linux is the
operating system of choice for both cloud and IoT systems, so becoming
familiar with Linux will pay dividends beyond just this course.

We encourage you to use PowerShell on Windows and Terminal on MacOS to log into
the `ecelinux` servers. You can also use VS Code to log into
the `ecelinux` servers. You can learn more about the various remote access options
in this tutorial:

 - [ECELinux Access](tut0-ecelinux.md)

### 1.1. Logging into `ecelinux` Servers

To start PowerShell on Windows, click the _Start_ menu then choose _Windows
PowerShell > Windows PowerShell_, or click the _Start_ menu, type
_PowerShell_, and choose _Windows PowerShell_. Follow the equivalent steps for MacOS.

Once you've opened your terminal app, the instructions are the same.
Type in the following command at the prompt to
log into the ecelinux servers:

```bash
$ ssh <netid>@ecelinux.ece.cornell.edu
```

Replace `<netid>` with your Cornell NetID in the command above. You should
not enter the `$` character. That is just the shell prompt. Executing the command will
ask you for your Cornell NetID password. Then, you should be
connected to the ecelinux servers.

The very first time you log into the ecelinux servers you may see a
warning like this:

```
The authenticity of host ’ecelinux.ece.cornell.edu (128.253.51.206)’ can’t be established.
ECDSA key fingerprint is SHA256:smwMnf9dyhs5zW5I279C5oJBrTFc5FLghIJMfBR1cxI.
Are you sure you want to continue connecting (yes/no)?
```

This is fine and will only occur the first time you log in.

## 2. Linux Command Line
The command line is one of the core ways to use your computer. It is a
text-based console where you can enter commands to interact with the
operating system.

### 2.1 Hello World

We will begin with the ubiquitous "Hello World!" example. To display the
message "Hello, World" in our terminal, we will use the `echo` command. The `echo` command
simply "echoes" its input to the console.

```bash
$ echo "Hello World!"
```

The string we provide to the echo command is called a _command line
argument_. We use command line arguments to tell commands what they
should operate on.

### 2.2. Man(ual) Pages

You can learn more about any Linux command by using the `man` command.
This resource is often call just "the man pages." This command has its origins in the 1970s, before Google or ChatGPT.
However, it is still useful today.
Try using `man` to learn more about the `echo` command.

```bash
$ man echo
```

You can use the arrow keys to scroll the manual one line at a time, the
space bar to scroll down one page at a time, and the 'Q' key to quit
viewing the manual.

### 2.3. Create, View, and List Files

We can use `echo` and a feature called _redirection_ to create simple text files. Redirection allows use to redirect the output from a command into file. In other words, instead of displaying in our terminal the results are written to a file. The greater-than sign, `>`, is used to signify that we are redirecting the output.
The following commands will create a new file named
`ece2400-sec01.txt` that simply contains the text "Computer Systems
Programming".

```bash
$ echo "Computer Systems Programming" > ece2400-sec01.txt
```

We can use the `cat` (concatenate) command to quickly display the contents of a file.

```bash
$ cat ece2400-sec01.txt
```

`cat` always outputs the entire file to the console, so for larger files we may resort to other options.
We can use the `less` command to show the text one screen at a time. You can use the
up/down keys to scroll the file one line at a time, the space bar to
scroll down one page at a time, and the 'Q' key to quit viewing the file.

```bash
$ less ece2400-sec01.txt
```

The `>` redirection operator will always create a brand
new file (even if the target output file already exists). You can use the
`>>` operator to append lines to the end of a file. Let's add another
line to our text file using the `>>` operator.

```bash
$ echo "Using C/C++" >> ece2400-sec01.txt
$ cat ece2400-sec01.txt
```

Now we should cover how to navigate the file system.
You can use the `ls` command to list the filenames of the files in your working directory.

```bash
$ ls
```

We can provide command line arguments to the `ls` command to modify the
command’s behavior. For example, we can use the `-1` (a dash
followed by the number one) command line option to list one file per
line. Moreover, we can we can use the `-l` (a dash followed by the letter
l) command line option to provide a longer listing with more information
about each file.

### 2.4. Create, Change, and List Directories

Obviously, having all files in a single location would be hard to manage.
We can use directories (i.e. folders) to logically
organize our files. The mechanism for organizing files and
directories is called the file system. When you first login to an
`ecelinux` machine, you will be in your home directory. This is your own
private space on the server where you can work on your programming assignments. You can use the `pwd` command to print
the directory in which you are currently working, which is known as the
current 'working directory.'

```bash
$ pwd
/home/netid
```

The `pwd` command shows the full file path to the working directory. 
A directory path is a list of nested directory names; it
describes the "path" to get to a specific file or directory. The above
path indicates that there is a toplevel directory named `/home` that
contains a directory inside named `netid`. This is the path to your
home directory. As an aside, notice that Linux uses a forward slash (`/`)
to separate directories, while Windows uses a back slash (`\`) for the
same purpose.

We can use the `mkdir` command to make new directories. The following
command will make a new directory named `ece2400` within your home
directory.

```bash
$ mkdir ece2400
```

We can use the `cd` command to change our current working directory. The
following command will change the current working directory to be the
newly created `ece2400` directory.

```bash
$ cd ece2400
$ pwd
/home/netid/ece2400
```

Now, use the `mkdir`, `cd`, and `pwd` commands to make another subdirectory.

```bash
$ mkdir sec01
$ cd sec01
$ pwd
/home/netid/ece2400/sec01
```

We would say that `sec01` is a subdirectory of
the `ece2400` directory. We would also say that `ece2400` is the parent directory
of the `sec01`. Use the following
command to copy our text file in this child directory.

```bash
$ cd /home/netid/ece2400/sec01
$ cp /home/netid/ece2400-sec01.txt /home/netid/ece2400/sec01
$ ls
```

One short note: we can use `~/` in place of `/home/netid` on Linux. 
With that said, let's use the `tree` command to visualize the directory layout and
where files are located:

```bash
$ cd ~/ece2400
$ tree
```

The tilde (`~`) is a shortcut which always refers to
your home directory. Moreover, there are a few other very useful shortcuts. You can
use a single dot (`.`) to refer to the current working directory, and you
can use a double dot (`..`) to refer to the parent directory of the
current working directory.

```bash
$ cd ~/ece2400/sec01
$ cd ..
$ cd ..
$ pwd
```

### 2.5. Copy, Move, and Remove Files and Directories

We can use the `cp` command to copy files. The first argument is the name
of the file you want to copy, and the second argument is the new name to
give to the copy. The following commands will make two copies of the
files we created in the previous section.

```bash
$ cd ~/ece2400/sec01
$ cp ece2400-sec01.txt ece2400-sec01-a.txt
$ cp ece2400-sec01.txt ece2400-sec01-b.txt
$ ls
```

Instead of copying we can also move a file with the `mv` command:

```bash
$ cd ~/ece2400/sec01
$ mv ece2400-sec01.txt ece2400-sec01-c.txt
$ ls
```

Finally, we can use the `rm` command to remove files.

```bash
$ cd ~/ece2400/sec01
$ ls
$ rm ece2400-sec01-a.txt
```

As a tip, we have installed a simple program called `trash` which moves files you
wish to delete into a special subdirectory located
at `${HOME}/tmp/trash`. The following commands create a file and then
deletes it using trash.

```bash
$ cd ${HOME}
$ echo "This file will be deleted." > testing.txt
$ trash testing.txt
$ echo "This file will also be deleted." > testing.txt
$ trash testing.txt
$ ls ${HOME}/tmp/trash
```

If you look in `${HOME}/tmp/trash`, you will see subdirectories organized
by date. Look in the subdirectory with today's date and you should two
files corresponding to the ones you deleted. We recommend using the `trash` command over of `rm`,
so you can recover any accidentally deleted work.

### 2.6. Text Editors

Students are free to use any text editor they want, although we recommend
students use VS Code. VS Code is both a remote access option _and_ a text editor.
If you want to stay inside of your terminal, you can still use `nano` or `vim`.
this:

```bash
$ nano ece2400-sec01-b.txt
```

When you are finished, you can go ahead and trash the `sec01` directory to keep
things tidy.

```bash
$ trash ~/ece2400/sec01
```

We hope this tutorial was not too mundane, but eventually these commands will become intuitive.
Over time, you will be able to use a command line more proficiently than any GUI!
