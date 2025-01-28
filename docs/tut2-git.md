# Using Git

Now that you know the [basics of the Linux command line](tut1-linux-cli.md),
you can now start to use [Git](https://git-scm.com) to manage your code.

## 1. GitHub Account Setup

We will be using GitHub for hosting our git repositories. You can check
to see if you have a GitHub account already by going to the following link:
`https://github.com/githubid` where `githubid` is your GitHub username.
If the link does not work, then you do not have an
GitHub account on `github.com`. **NOTE: We are using `github.com` not the
Cornell hosted GitHub!** You will need to create one here:

 - [GitHub Signup](https://github.com/signup)

Yo can use whichever username you want ont [GitHub.com](https://github.com).
However, be sure to use your Cornell University email address.

Once your account is setup, please make sure you set your full name so we
can know who you are on GitHub. Consider uploading a fun profile
picture to GitHub. Go to the following page and enter your
first and last name in the Name field.

- [https://github.com/settings/profile](https://github.com/settings/profile)

Before you can begin using GitHub, you need to generate an SSH key pair on
an `ecelinux` machine and upload the corresponding SSH public key to
GitHub. GitHub uses these keys for authentication. GitHub has its own tutorial on how to generate a key pair on Linux:

- [Generating a New SSH Key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

You can print the public key you've made to the terminal:

```bash
$ cat ~/.ssh/id_ed25519
```

Copy the output and use the following page to add the public key to GitHub:

 - [https://github.com/settings/ssh](https://github.com/settings/ssh)

Click on _New SSH Key_, and then paste the public key you
displayed using cat into the key textbox. Give the key the title
`ecelinux`. Then click _Add SSH key_. To test things out, try the
following command:

```bash
$ ssh -T git@github.com
```

You may see a warning about the authenticity of the host. Don’t worry,
this is supposed to happen the first time you access GitHub using your
new key. Just enter `yes`. The GitHub server should output some text
including your GitHub ID. Verify that the GitHub ID is correct, and then
you should be all set.

## 2. Git Version Control System

In this course, we will be using Git to version control the code we write.
Git will enable us to adopt an agile development
methodology so you (and your group) can rapidly collaborate and iterate
on the design, verification, and evaluation of the assignments.

### 2.1. Fork and Clone a Repo from GitHub

Fork'ing a repo means making a copy of that repo for your own local use.
We won't actually be forking repos for the programming assignments, but
it is an easy way for you to grab some example code for the discussion
section. Go to the example repo here:

 - [https://github.com/cornell-ece2400/ece2400-sec01](https://github.com/cornell-ece2400/ece2400-sec01)

Click on the "Fork" button. Wait a few seconds and then visit the new
copy of this repo in your own person GitHub workspace:

 - `https://github.com/githubid/ece2400-sec01`

Replace `githubid` with your GitHub username. Now let's clone your new repo to the
`ecelinux` machine.

```bash
$ cd ${HOME}/ece2400
$ git clone git@github.com:githubid/ece2400-sec01 sec01
$ cd sec01
$ cat README.md
```

where `githubid` is your GitHub username.

### 2.2. Adding and Committing Files to Local Repository

Now let's add some new files to the repository. Use `echo` to create a
file named `warm-colors.txt` with three warm colors:

```bash
$ echo red > warm-colors.txt
$ echo orange >> warm-colors.txt
$ echo yellow >> warm-colors.txt
```

Now use your favorite text editor again to create a file named
`cool-colors.txt` with three cool colors.

```bash
$ echo blue > cool-colors.txt
$ echo green >> cool-colors.txt
$ echo purple >> cool-colors.txt
```

Now let's add these files to our repository. First use the `git status`
command to check on the status of the repository.

```bash
$ cd ${HOME}/ece2400/sec01
$ git status
```

You should see that git noticed two "untracked files" which are in
the working directory but are not currently being tracked by git. Let's
"add" these two files to git's "staging" area, so it now knows it should
keep track of them:

```bash
$ git add warm-colors.txt
$ git add cool-colors.txt
$ git status
```

The status of these two files have changed. Git reports that both of the
new files are ready to be committed. Let's go ahead and commit these
changes into your local repository.

```bash
$ git commit -m "add some colors"
```

Let's now use `echo` and the `>>` command output redirection operator to
add `cyan` to the end of our `cool-colors.txt` file. We can then view our
changes from the command line using `cat`, and use `git status` and then
`git commit` to try and commit our changes.

```bash
$ echo "cyan" >> cool-colors.txt
$ cat cool-colors.txt
$ git status
$ git commit -m "add cyan"
```

Git will report that there are no changes added to the commit. So even
though Git is tracking `cool-colors.txt` and knows it has changed, we
still must explicitly add the files we want to commit. We recommend using
the `git add -u` command to tell Git to
add any file which has changed and was previously added to the repository.

```bash
$ git add -u
$ git commit -m "add cyan"
```

Now the changes are committed. You can use `git log` to see a log of the
commits in your local repository.

```bash
$ cd ${HOME}/ece2400/sec01
$ git log
```

### 2.3. Pushing Files to GitHub

Note that nothing has happened on GitHub yet. GitHub does _not_ know
anything about these local changes. We need to explicitly "push" our new
commits up to GitHub like this:

```bash
$ git push
```

Now go to the repository page using the GitHub web interface and verify
that there are two new files.

 - `https://github.com/githubid/ece2400-sec01`

### 2.4. Pulling Files from GitHub

Let's try making a change to this repository through the GitHub
web interface.

 - `https://github.com/githubid/ece2400-sec01`

Click on _Add file_ and then _Create new file_. Name the file
`languages.txt` and add a list of programming languages:

    C
    C++
    Python
    MATLAB
    Java

Now click _Commit new file_. Verify that there is a new file in the repo
using the GitHub web interface. Now let's "pull" these new changes from
GitHub to your local repo on `ecelinux`:

```bash
$ git pull
$ cat languages.txt
```

This will be the basic GitHub workflow. Students should:

1. `git clone` the repo to ecelinux
2. `git add -u` to add their code changes
3. Commit their changes with a message `git commit -m "message"`
4. Sync their changes to GitHub with `git push`
