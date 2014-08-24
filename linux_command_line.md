Those notes were created in process of learning linux, hence might be wrong, use at your own risk.

Linux Command Line CheatSheet Notes
=========

Getting Help
------------
There are multiple ways of getting help: primary one is man pages. To get the help page type:

```bash
$ man <topicname> 
```

To get just a short description of the command, type 1 of the following 2 commands (results are identical):

```bash
$ man -f <topicname>
$ whatis <topicname>
```

Lastly you can also type

```bash
$ info <topicname>
```

If you don't know the particular command you are trying to find you can search man pages via:

```bash
$ apropos "subject"
```

Admin Priviledges
-----------------
To have complete access to anything - you will require root access. On an equivalent via
```bash
$ su <command>
```
which is not recommended under most circumstances. A more "reduced" version would be using 
```bash
$ sudo <command>
```
before the commands requiring more access. Note that command executed with sudo will ask you for current user password, while command executed with su will require root password, which should not be given out to users.


Command Line History
--------------------
To see the last 50 commands executed in console type
```bash
$ history
```
Command line history can also be searched by pressing up arrow key.


Previous commands can be executed by either finding it via pressing up, then pressing enter when found.
Shortcut to execute previous command is:
```bash
$ !!
```
Earlier commands can be executed by typing:
```bash
$ !18
```
which will execute 18th command in history list (??)


Reboot, Sleep (Suspend), Shutdown Basics
----------------------------------------
Either of the following commands can be used to shutdown (halt) your system:

```bash
$ halt
$ shutdown -h
```

To reboot you computer type:
```bash
$ shutdown -r
```

There are multiple options available with shutdown, here are just some of them:

```bash
$ shutdown -r 10
```
this will restart your computer in 10 seconds

```bash
$ shutdown -r 17:02
```
this will restart your computer at 17:02

```bash
$ shutdown -r 17:02 "It's 17:02 already"
```
this will restart your computer at 17:02 with custom message


Users and Groups
----------------
To find out the username you are currently logged on as type:
```bash
$ whoami
```

A detailed list of users can be obtained via:
```bash
$ who -a
```

To find out the existing groups type:
```bash
$ id
```

### Adding and Removing Users
To create a new user, and then set his initial password type:

```bash
$ useradd <username>
$ passwd <username>
```

User can be later removed via:

```bash
$ userdel <username>
```

### Adding and Removing Groups
To add a new group type:

```bash
$ /usr/sbin/groupadd <groupname>
```

For each user you can check the groups he belongs to, and assign him to new groups as necessary:

```bash
$ groups <username>
$ /usr/sbin/usermod -G <groupname> <username>
```

Unused groups can be removed later:

```bash
$ /usr/sbin/groupdel <groupname>
```

Searching for Installed Applications
------------------------------------
To find any particular program type either of the following commands:

```bash
$ which <programname>
$ whereis <programname>
```

File System
------------
### Basic Navigation
To display your current directory use:
```bash
$ pwd
```

Navigation is done via 
```bash
$ cd <path>
```
where you can use both absolute path from the root node (/) or relative to your current folder.
Also every user has his own home directory which has a shortcut (~). Below are examples of using both absolute and relative paths.

Change to home directory:
```bash
$ cd ~
```

Change to parent directory (1 level up):
```bash
$ cd ..
```

Change to root node:
```bash
$ cd /
```

Change to previous directory (back):
```bash
$ cd -
```

To see the entire structure of the filesystem type:
```bash
$ tree
```
(might require to install it first)


When you are in the right directory you can show all files in the folder by typing:
```bash
$ ls
```

You can also include all hidden files by adding an extra argument
```bash
$ ls -a
```

Files can also be displayed as a list:
```bash
$ ls -l
```

Showing files can be combined with the basic search. For example to show all files with extention .pdf type:
```bash
$ ls *.pdf
```
Note that in linux extentions play less role than they do in say windows - file can have no extentions and be just as usefull

#### Directory Stack
If you need to keep track of your movement across directories and then trace it back (similar to 
```bash
$ cd -
```
but for multiple steps). You can use internal directory stack. To push and pop from the stack use:

```bash
$ pushd <directory>
$ popd
```

At any moment you can also show entire stack by typing: 
```bash
$ dirs
```

### File Permissions
To change permissions for user/group to read/write/execute use *chmod*
(see man chmod for detailed options to change permissions)

When creating files and directories by default it creates directories with chmod 777 (rwx for everybody) and files with chmod 666 (rw for everybody) when the file mask is subtracted. To find out what the file mask value is type:
```bash
$ umask
```

To change it type:
```bash
$umask <newvalue>
```

To change the owner/group of the file use *chown* and *chgrp*


### Links (~shortcuts) between files
You can create hard and soft links via respective commands:
```bash
$ ln <targetfile> <linkname>
$ ln -s <targetfile> <linkname>
```
Hard links can't be spanned across partitions, soft (symbolik) links can be used across partitions. Note that each regular file is in principle a hardlink. Symbolic links are always very small files, while hard links have the same size as the original file.

### Input and Output 
There are basic file descriptors which can be used for input/output redirection in command line:
 - stdin = 0
 - stdout = 1
 - stderr = 2
 - other = 3

In order to redirect input from the file to the program use "<". Example:

```bash
$ <input_required_program> < <input_file>
```

You can also direct the output of your program into the files in a similar fashion:

```bash
$ <output_generating_program> > <output_file>
```

Since "<" and ">" are generally used for stdin and stdout, to redirect all errors (for log purposes) use:

```bash
$ <program> 2> <log_file>
```
where 2 is referring to the file descriptors mentioned earlier

In case user wants to send everything (both output and error to the same files it can be combined in the following way):

```bash
$ <program> > <all_output_file> 2>&1
```

### Pipes
Sometimes you might have multiple programs getting input from previous program and generating output for consecutive program. (For example first program captures streaming data, second cleans it up, third calculates basic statistics). In this case input/output redirection can be simplified via piping, where <program1> will be run and it's output will passed on to <program2> as input, etc..

```bash
$ <program1> | <program2> | <program3>
```

### File Search
There are 2 main commands to run search across: locate and find. The former uses updatedb, instead of actually checking the file system, as a result it is faster, but it might be out of date if you're looking for something that was just created. (updatedb is typically updated daily). Find is checking the system itself, hence slower but more accurate.

To locate a file using locate type:
```bash
$ locate <searchname>
```

Search from locate can be further refined with grep:
```bash
$ locate <searchname> | grep <searchname2>
```

Alternatively you can just use Regex directly in locate:
```bash
$ locate --regex <regularexpression>
```

In case of find, you can speed it up by telling where to look, and type of file you're looking for. For example the following search looks for directories (type) in ~/Documents, containing <foldername>:

```bash
$ find ~/Documents -name <foldername> -type d
```

Results of find can also be used as an input for running some command, for example find all files containing <filename> and delete them:

```bash
$ find -name <filename> -exec rm {} ';'
```
where ';' terminates command. A safer approach would be to use -ok instead of -exec to ask for confirmation before deleting:

```bash
$ find -name <filename> -ok rm {} ';'
```

Another example: execute <command> for all files (type) 10+ Mb in /etc/ directory:

```bash
$ /etc/ -type f -size +10M -exec <command> {} ';'
```

#### Wildcards
To search for a wider range of names, where you don't remember full name and/or structure of the name you can use wildcards:
 - '?' replaces single unknown character
 - '*' replaces an unknown string of arbitrary length
 - '[set]' replaces a known set of characters
 - '[!set]' replaces a known excluded characters


### Standard Manipulation
To create a new directory in the current folder type:
```bash
$ mkdir <directoryname>
```

Also a full path can be created along with directory using (-p) option:
```bash
$ mkdir -p ~/my_notes/linux/new_folder/
```

To copy/move something (file/folder) from it's source to destination type:
```bash
$ cp <source> <destination>
$ mv <source> <destination>
```
A recursive (-r) option is useful when folder with its entire contents needs to be moved/copied.

Move (mv) can also be used to rename files in the folder:
```bash
$ mv <fileoldname> <filenewname>
```

To remove <source> type:
```bash
$ rm <source>
```

Note that it will not work properly on folders. In order for it to work you need to run it recursively:

```bash
$ rm -r <source>
```

You can also add forceful removal, or interactive removal with appropriate options:

```bash
$ rm -f <source>
$ rm -i <source>
```
Interactive option also applies to mv and cp (though it is less critical since file(s) are still around)

Options passed to the function can also be combined into 1 string (no spaces). For example forceful recursive deletion of the <source> will be:

```bash
$ rm -rf <source>
```


### Back Up
In addition to standard copy linux has a copy command that will only update the differences between the <source> and <destination>. So if the copy is incremental it will be faster, hence popular for backups, since most of the docs remain the same:

```bash
$ rsync -r <source> <destination>
```

### File Manipulation
To create an empty file <filename> type:
```bash
$ touch <filename>
```

To view file (works for small files, since command has no scroll) in forward or backward direction use 1 of the following commands:

```bash
$ cat <file>
$ tac <file>
```

Both commands can display multiple files:
```bash
$ cat <file1> <file2> <file3>
```

Output can also be redirected or appended to existing files:

```bash
$ cat <file1> <file2> > <file3>
$ cat <file1> <file2> >> <file3>
```

To write append all subsequent commands (create a "macro" with commands):

```bash
$ cat > <file>
$ cat >> <file>
```

To break out of writing/appending mode press CTRL + D

A similar procedure can be done via:
```bash
$ cat > <file> << EOF
```

In this case writing will break upon typing EOF



For big files you might want to check only beginning or end of the file. To do so run:

```bash
$ head <file>
 tail -n 20 <file>
```

By default it shows 10 lines of the file from corresponding side which can be changed as was done in the latter command

To view bigger files with scroll use:
```bash
$ less <file>
```

You can also view file type via:
```bash
$ file <file>
```

Multiple routine tasks for file manipulation are also available such as:
Sort: 
```bash
$ sort <filename>
```
To reverse sort add (-r), to sort and remove duplicates (-u)

You can also remove duplicates without sorting:
```bash
$ uniq <filename>
```
Add (-c) for counting duplicates

Pasting:
```bash
$ paste
```
Where (-d) specifies delimiters, and (-s) uses horizontal paste instead of vertical


Join:
You can also perform an inner join based on common field:
```bash
$ join <file1> <file2>
```

Split:
Large files can be into 1000 line segments for convinience (named as xFilename, prefix can be changed):
```bash
$ split <infile>
```

File can also be searched for patters via grep:
```bash
$ grep <pattern> <filename>
```
To display everything that doesn't match (-v)


Converting strings within file:
Convert everything to uppercase:
```bash
$ tr a-z A-Z < <inputfile>
```

Word Count:
```bash
$ wc <file>
```
To count lines (-l), words (-w), characters (-c)


To extract certain columns from the file use *cut* (default delimiter is tab)


To extract all printable strings from file for further parsing:
```bash
$ strings <myfile>
```

### File Compression
There are multiple ways to compress a file/folder some algorithms are faster, some are more space efficient. Most common one is gzip. To compress files type:
```bash
$ gzip -r <foldername>
$ gzip <file>
```
(Would recursive create a bunch of archieves?)

To decompress files type either of the first 2 commands, if you want to keep the archieve after decompression use 3rd command:

```bash
$ gzip -d <file.gzip>
$ gunzip <file.gzip>
$ gzip -dk <file.gzip>
```

Other compressions include bzip2, xz (most space efficient), zip (legacy from Win)

In order to create archieves containing multiple files, use
```bash
$ tar
```
utility to extract from the archieve (irrelevant how compressed for example it was .xz) type:

```bash
$ tar -xvf <file.tar.xz>
```

You can also compress files/directories using gzip, xz, bz2 directly from tar. Below you can see example for gzip.

```bash
$ tar -zcvf <archievename.tar.gz> <directory>
```

To perform operations on archieves there is a z-operations family: (zcat, zless, zgrep, zdiff)


### File Comparison and Patching
To find all differences between files (for example to check config file you might compare it with another copy or compare you solution to somebody else's when most of the solution is similar) type:
```bash
$ diff <file1> <file2>
```
Multiple options are allowed for the comparisons. For example:
 - To add context to comparisons add (-c) which will add a few lines before and after the line that is different for files
 - To do a recursive comparison (primary for folders) add (-r)
 - To ignore letter cases add (-i) (For example sql queries saved)
 - To ignore differences in whitespaces add (-w)

Files can also be compared in a 3-way fashion (1 main file, 2 different patches):

```bash
$ diff3 <file1> <originalfile> <file2>
```

Note that finding all differences is fundamentally important to do patching (you don't want to sent everything every time). Hence diff is commonly used to create patchfiles:

```bash
$ diff -Nur <file_v1> <file_v2> > <patchfile>
```

This patch can be later applied using either of the following commands:

```bash
$ patch <file_v1> <patchfile>
$ patch -p1 < <patchfile>
```


Text Editing
------------
There are multiple editors available in linux in most distributions. Most common are:
 - nano
 - gedit (for GNOME)
 - vim
 - emacs

Which can be called by respective commands (if <filename> doesn't exist it will be created):

```bash
$ nano <filename>
$ gedit <filename>
$ vi <filename>
$ emacs <filename>
```

Also vim has inbuilt basic tutor which can be accessed by:
```bash
$ vimtutor
```

Text files can also be created directly in console via redirection.

```bash
$ echo <dothings> > <newfile>
$ echo <domorethings> >> <newfile>
```

Note that first line will create/override <newfile> with <dothings>. On the second line we use '>>' which instead of simply writing over will append <domorethings> to the end of the file.


Networks
--------
To find your hostname:
```bash
$ hostname
```

To find host of an adress (ex: google.com):
```bash
$ host google.com
```

What do i do??
```bash
$ dig google.com
```

To ping:
```bash
$ ping <hostname>
```

To show current routing table:
```bash
$ route -n
```

To add or delete a static route:

```bash
$ route add -net <address>
$ route del -net <address>
```

To check all hops to particular domain:
```bash
$ traceroute <domain>
```

Other tools:
Setting various network parameters:
```bash
$ ethtool
```

Display current connections:
```bash
$ netstat
```

Scan for open ports on the network:
```bash
$ nmap
```

Dump network traffic for analysis:
```bash
$ tcpdump
```

Monitor traffic in text mode:
```bash
$ iptraf
```

If you want to save the page locally and read it later either of the following commands would work:

```bash
$ wget <url>
$ curl -o <localfile.html> <url>
```

FTP
---

To connect to ftp type:
```bash
$ ftp -p <ftp_address>
```

To get a file when connected type:
```bash
$ get <file>
```

Once done, disconnect by typing:
```bash
$ quit
```


SSH
---
To execute a command on remote system via SSH:
```bash
$ ssh <remotesystem> <command>
```

If you need to move a file from one system to another, a secure copy can be done via SSH:
```bash
$ scp <localfile> <user@remotesystem>:/home/user
```

PDFs
----
To view pdf use *xpdf*
To work with pdfs install pdftk


Processes
---------
To see processes (or entire tree):
```bash
$ ps
$ pstree
```

To kill a process:
```bash
$ kill <process>
```

To show top processes:
```bash
$ top
```

To view background processes:
```bash
$ jobs
```

To run a process in the background add (&) at the end:
```bash
$ <command> &
```

To schedule tasks use *cron* (important for scripting - for example set up regular backups)

Alternatively use *sleep* (works like wait):
```bash
$ sleep 1800; echo "3hrs passed"
```

or *at*:
```bash
$ at 0200
at> <dotasks>
```
To exit *at* utility press CTRL-D


Mounting
--------
Mount command, df -h command


Variables
---------
To check variable value type use echo (works like print):
```bash
$ echo $<variable>
```

You can also set variables via either of the following:

```bash
$ export <variable> = <value>
$ set <variable> to <value>
```


