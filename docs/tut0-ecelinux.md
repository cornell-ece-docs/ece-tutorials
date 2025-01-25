# ECE Linux Server Remote Access
Most of your ECE coursework will be completed by remotely logging into a cluster of ecelinux servers. The ecelinux servers all run the Red Hat Enterprise Linux operating system with identical setups. You do not need to do anything special to create an ecelinux account. You will be using your NetID and Cornell password to login, and an ecelinux account will be automatically created for you when you first login. Any student enrolled in any ECE class should automatically be granted access to the ecelinux servers. Having said this, if you cannot log into the ecelinux servers please reach out to the course staff for assistance.

Later tutorials will discuss how to use Linux and the Git distributed version control system. In this tutorial, we focus on how to setup remote access to the ecelinux servers by first connecting to the Cornell VPN and then opening a remote shell. Any terminal emulator should work, but we recommend using PowerShell on Windows or Mac Terminal. We will also be leveraging Visual Studio Code (VS Code), which provides a very nice graphical interface to supplement the command line.

## 1. Connecting to the Cornell VPN
If you are logging into the ecelinux servers from on campus (i.e., using the Cornell wired or wireless network), then you do not need to enable the Cornell virtual private network (VPN). However, if you are off campus, then you will need to enable the Cornell VPN whenever you want to log into the ecelinux servers. The VPN provides very secure access to all on-campus network resources. More information about the Cornell VPN is available here:

[https://it.cornell.edu/landing-page-kba/2605/5273](https://it.cornell.edu/landing-page-kba/2605/5273)

Simply follow the instructions at the above link to install the Cisco VPN software for the appropriate operating system you use on your laptop/workstation. Basic troubleshooting steps can be found at the following link.

[https://it.cornell.edu/landing-page-kba/2605/823](https://it.cornell.edu/landing-page-kba/2605/823)


## 2. Remote Access via Terminal Emulator
PowerShell is part of Windows OS and Mac Terminal is part of Mac OS X. Both enable interacting with your system from the command line (i.e., a powerful text-based environment where users type commands to manipulate files and directories and execute applications). Both also enable remotely accessing other systems (e.g., the ecelinux servers) via the command line using SSH, a highly secure network protocol and associated client/server program. Both will enable you to log into the ecelinux servers and to then manipulate files and directories and execute applications remotely on the ecelinux servers using the Linux command line.

### 2.1. Starting PowerShell or Mac Terminal
First, if you are off campus, then you must be connected to the Cornell VPN before attempting to use X2Go to access the ecelinux servers (see Section 1). To start PowerShell click the Start menu then search for Windows PowerShell. To start Mac Terminal go to your Applications folder and choose Utilities > Terminal, or open Spotlight, type Terminal, and press enter.

### 2.2. Logging into ecelinux Servers with PowerShell or Mac Terminal
After starting PowerShell or Mac Terminal, type in the following command at the prompt to log into the ecelinux servers using SSH.


```bash
$ ssh <netid>@ecelinux.ece.cornell.edu
```
Replace 'netid' with your Cornell NetID in the command above. You should not enter the dollar sign. This is just the bash shell prompt. Executing this command correctly will prompt you to enter your Cornell NetID password, and then you should be connected to the ecelinux servers.

The very first time you log into the ecelinux servers you may see a warning like this:

``` bash
 The authenticity of host ’ecelinux.ece.cornell.edu (128.253.51.206)’
 can’t be established. ECDSA key fingerprint is
 SHA256:smwMnf9dyhs5zW5I279C5oJBrTFc5FLghIJMfBR1cxI.
 Are you sure you want to continue connecting (yes/no)?
```

### 2.3. Nano Text Editor
Now that you are on, let's try to use a simple command line program. Nano is a text-based editor installed on all linux machines. To experiment with `nano`, we will first grab a text file to edit from the internet with the `wget` command.

```bash
$ wget http://www.csl.cornell.edu/courses/ece2300/overview.txt
```

Notice that the file is now in on working directory:

```bash
$ ls
overview.txt
```

We can now open this file and edit it with `nano`:

```bash
$ nano overview.txt
```

Notice that the editor specifies most of the useful commands at the bottom of the terminal. The symbol \^ indicates the CONTROL key. To type any text you want, just move the insertion point using your arrow keys. To exit and save your changes, press CONTROL+X, then Y to save, then ENTER to use the same file name. Congrats!

## 3. Remote Access via VS Code
While you can complete all your coursework with just a terminal, it is not the most productive setup. We strongly recommend using VS Code as your primary option for code development on ecelinux.

VS Code runs the GUI interface on your personal computer, but executes your actions on the remote computer. This enables you to use ecelinux as if it is your own computer. All you need is VS Code plus the [remote ssh extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). But first, here is more information on how to install VS Code in the first place. 

### 3.1. Installing VS Code on Your Laptop/Workstation
You can download VS Code by simply going to the main VS Code webpage: [https://code.visualstudio.com](https://code.visualstudio.com)

There are no special steps required. However, you may want to consider [disabling telemetry](https://code.visualstudio.com/docs/getstarted/telemetry#_disable-telemetry-reporting) for security concerns.

### 3.2. Starting and Configuring VS Code
First, if you are off campus, then you must be connected to the Cornell VPN before attempting to use VS Code to access the ecelinux servers (see Section 1). Start by opening VS Code. The exact way you do this will depend on whether you are using a Windows OS or Mac OS X laptop/workstation.

The key to VS Code is installing the correct extensions. You will need extensions for the Verilog hardware description language (HDL). We also want to install a special extension which will enable remotely accessing the ecelinux servers using SSH. Choose View > Extensions from the menubar. Enter the name of the extension in the 
Search Extensions in Marketplace'' and then click the blue \IT{Install} button. Here are the names of the extensions to install:

Remote - SSH (use the one from Microsoft)
Verilog (use the one from Masahiro Hiramori)
Surfer (use the one from surfer-project)

### 3.3. Logging into ecelinux Servers with VS Code
After starting VS Code, choose View > Command Palette from the menubar. Enter the following command in the command palette:


Remote-SSH: Connect Current Window to Host..
As you start typing matching commands will be displayed and you can just click the command when you see it. VS Code will then ask you to Select configured SSH host or enter user@host, and you should enter the following:


netid@ecelinux.ece.cornell.edu
Replace netid with your Cornell NetID in the command above. If you are on a Windows OS laptop/workstation, then you may see a pop-up which stays that the Windows Defender Firewall as blocked some features of this app. This is not a problem. Simply click Cancel.

You might also see a drop down which asks you to choose the operating system of the remote server with options like Linux and Windows. Choose Linux.

Finally, the very first time you log into the ecelinux servers you may see a warning like this:


"ecelinux.ece.cornell.edu" has fingerprint
"SHA256:YCh2FiadeTXEzuSkC0AOdglBgPciwc8WvcCPncvr2Fs"
Are you sure you want to continue?
Continue
Cancel
The very first time you log into the ecelinux servers it is okay to enter yes, but from then on if you continue to receive this warning please contact the course staff.

Hopefully, VS Code will now prompt you to enter your Cornell NetID password, and then you should be connected to the ecelinux servers.

Also the very first time you log into the ecelinux servers you will see a pop up dialog box in the lower right-hand corner which says Setting up SSH host ecelinux.ece.cornell.edu (details) Initializing.... It might take up to a minute for everything to be setup; please be patient! Once the pop up dialog box goes away and you see SSH: ecelinux.ece.cornell.edu in the lower left-hand corner of VS Code then you know you are connected to the ecelinux servers.

The final step is to make sure your extensions for the Verilog HDL are also installed on the server. Choose View > Extensions from the menubar. Use the "Search Extensions in Marketplace" to search for the same Verilog HDL extensions that we installed earlier. Instead of saying Install it should now say Install in SSH: ecelinux.ece.cornell.edu. Install the Verilog HDL extension on the ecelinux servers. You only need to do this once, and then next time this extension will already be installed on the ecelinux servers.

### 3.4. Using VS Code
VS Code includes an integrated file explorer which makes it very productive to browse and open files. Choose View > Explorer from the menubar, and then click on Open Folder. VS Code will then ask you to Open File Or Folder with a default of /home/netid. Click OK.

You might see a pop-up which asks you Do you trust the authors of the files in this folder? Since you will only be browsing your own files on the ecelinux server, it is fine to choose Yes, I trust the authors.

This will reload VS Code, and you should now you will see a file explore in the left sidebar. You can easily browse your directory hierarchy, open files by clicking on them, create new files, and delete files.

VS Code includes an integrated terminal which will give you access to the Linux command line on the ecelinux servers. Choose Terminal > New Terminal from the menubar. You should see the same kind of Linux command line prompt that you saw when using either PowerShell or Mac Terminal. The very first thing you need to do after logging into the ecelinux servers is source the course setup script. This will ensure your environment is setup with everything you need for working on the laboratory/programming assignments. Enter the following command on the command line:


% source setup-ece2300.sh
Again, you should not enter the % character. You should now see a blue ECE 2300 in your prompt which means your environment is setup for the course. See the final section of this tutorial for more on how to automatically source the setup script every time you log into the ecelinux servers.

To experiment with VS Code, we will first grab a text file using the wget command. The next tutorial discusses this command in more detail. Type the following command in the VS Code integrated terminal.


% wget http://www.csl.cornell.edu/courses/ece2300/overview.txt
You can open a file in the integrated text editor using the code command like this:


% code overview.txt
The following figure shows what VS Code should look like if you have sourced the course setup script and opened the overview.txt file. Notice how the overview.txt file opened in a new tab at the top and the terminal remains at the bottom. This enables you to have easy access to editing files and the Linux command line at the same time.



### 3.5. Troubleshooting Remote Access via VS Code
There may be issues where sometimes VS Code just keeps asking you for your password or VS Code just hangs when you try and connect to the ecelinux servers. This might mean VS Code is getting wedged. You can definitely ask the course staff to help, but you can also try to fix it on your own.

One thing to try is to kill the VS Code server on the host. Choose View > Command Palette from the menubar. Enter the following command in the command palette:


Remote-SSH: Kill VS Code Server on Host...
Another thing to try is to delete the .vscode-server directory on the sever. Of course, how can you delete this directory if you cannot use VS Code to access the ecelinux servers? You can use PowerShell or Mac Terminal to log into the ecelinux servers (see Section 2 or 3).

Once you have gained access to the Linux command line on the ecelinux servers using either PowerShell or Mac Terminal, then you can delete the .vscode-server directory like this:


% rm -rf .vscode-server
Be very careful with the rm command since it can permanently delete files!

Another option is to directly log into a specific ecelinux server. So instead of loging into ecelinux.ece.cornell.edu (which will automatically pick an ecelinux server for you to use) you can log into ecelinux-01.ece.cornell.edu, ecelinux-02.ece.cornell.edu, and so on.

Note that using the Remote-SSH: Add new SSH host... option does not always seem to work on Microsoft Windows OS laptops/workstations. This is why we recommend just using Remote-SSH: Connect Current Window to Host... directly.

Sometimes VS Code can take a very long time to save a file. This is usually because VS Code is trying to auto-format the file on the ecelinux servers. To turn off auto-formatting, open the VS Code settings menu. On Windows OS, choose File > Preferences > Settings from the menubar. On Mac OS X, choose Code > Preferences > Settings from the menubar. Click on Text Editor and then Formatting. Make sure Format On Save is not checked.

## 4. Sourcing Course Setup Script with Auto Setup
The previous sections have demonstrated how to remotely access the ecelinux servers, get to the Linux command line, and source the course setup script. Again, you must source the course setup script before doing any work related to this course! The course setup script configures everything so you have the right environment to work on the laboratory/programming assignments.

Since it can be tedious to always remember to source the course setup script, you can also use auto setup which will automatically source the course setup for you when you open a terminal. Note that if the environment for ECE 2300 conflicts with the environment required by a different course then you will need to manually source the setup script when you are working on this course. Enter the following command on the command line to use auto setup:


 % source setup-ece2300.sh --enable-auto-setup
Now completely logout of the ecelinux servers then log back in and get to the Linux command line. The exact way to do this depends on which remote access option you using. You should see ECE 2300 in the prompt meaning your environment is automatically setup for the course. If at anytime you need to disable auto setup you can use the following command:


 % source setup-ece2300.sh --disable-auto-setup
Again, if for any reason running the setup script prevents you from using tools for another course, you cannot use the auto setup. You will need to run the setup script manually every time you want to work on a laboratory/programming assignment for this course.