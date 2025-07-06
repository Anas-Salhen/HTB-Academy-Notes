# Navigation
## Showing File Contents
Running `ls` shows contents of the file, but running `ls -l` shows more info:
```shell-session
username@htb[~]$ ls -l

total 32
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:37 Desktop
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Documents
drwxr-xr-x 3 username htbacademy 4096 Nov 15 03:26 Downloads
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Music
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Pictures
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Public
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Templates
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Videos
```
First we see that size used is 32 (measure in KB, also called 32 blocks). Then we see these columns:

| **Column Content** | **Description**                                                                                      |
| ------------------ | ---------------------------------------------------------------------------------------------------- |
| `drwxr-xr-x`       | Type and permissions                                                                                 |
| `2`                | Number of hard links to the file/directory                                                           |
| `username`         | Owner of the file/directory                                                                          |
| `htbacademy`       | Group owner of the file/directory                                                                    |
| `4096`             | Size of the file or the number of blocks used to store the directory information (measured in bytes) |
| `Nov 13 17:37`     | Date and time                                                                                        |
| `Desktop`          | Directory name                                                                                       |
However there are hidden files that start with a dot (.) like `.bashrc` and `.bash_history` we can see them using `-la` 
```shell-session
username@htb[~]$ ls -la

total 403188
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:37 .bash_history
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:37 .bashrc
...SNIP...
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:37 Desktop
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Documents
drwxr-xr-x 3 username htbacademy 4096 Nov 15 03:26 Downloads
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Music
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Pictures
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Public
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Templates
drwxr-xr-x 2 username htbacademy 4096 Nov 13 17:34 Videos
```
We also don't need to navigate to the directory then use `ls -l` we can just use `ls -l (path)`

## Navigating to a File
We can use `cd (path)` to do that. To return to previous directory use `cd -`. 
If you type `cd /dev/s` then press tab twice you get entries starting with s in the dev folder:
```shell-session
username@htb[~]$ cd /dev/s [TAB 2x]

shm/ snd/
```
Add h to the letter s and shell will complete the input:
```shell-session
username@htb[/dev/shm]$ ls -la /dev/shm

total 0
drwxrwxrwt  2 root root   40 Mai 15 18:31 .
drwxr-xr-x 17 root root 4000 Mai 14 20:45 ..
```
The first entry with a single dot (`.`) indicates the current directory we are currently in. The second entry with two dots (`..`) represents the parent directory `/dev`. This means we can jump to the parent directory with the following command:
```shell-session
username@htb[/dev/shm]$ cd ..

username@htb[/dev]$
```

We can clear the terminal screen with `clear`. First let's go to `/dev/shm` (idk why):
```shell-session
username@htb[/dev]$ cd shm && clear
```

# Working With Files
## Creating Files And Directories
In Linux using the terminal is fast and efficient allowing you to edit files without needing editors like vim and nano. It's more efficient than a GUI since it allows you to access files with a few commands and allows modifying files selectively using regular expressions (regex) not to mention the power of automation.

```shell-session
AnasSalhen@htb[/htb]$ touch info.txt

AnasSalhen@htb[/htb]$ mkdir Storage
```
In this example we created a file called info.txt and a directory called storage. If you need multiple directories within each other at once use `mkdir -p`:
```shell-session
AnasSalhen@htb[/htb]$ mkdir -p Storage/local/user/documents
```
Now use `tree` to see the structure:
```shell-session
AnasSalhen@htb[/htb]$ tree .

.
├── info.txt
└── Storage
    └── local
        └── user
            └── documents

4 directories, 1 file
```
If you don't want to type the full path use a dot (.) to say that you want to start from the current directory. Example:
```shell-session
AnasSalhen@htb[/htb]$ touch ./Storage/local/user/userinfo.txt

AnasSalhen@htb[/htb]$ tree .

.
├── info.txt
└── Storage
    └── local
        └── user
            ├── documents
            └── userinfo.txt

4 directories, 2 files
```

## Moving/Copying/Deleting Files and Directories
Use `mv`:
```shell-session
# Renames info.txt to information.txt
AnasSalhen@htb[/htb]$ mv info.txt information.txt

AnasSalhen@htb[/htb]$ touch readme.txt

# Moves information.txt and readme.txt to Storage directory
AnasSalhen@htb[/htb]$ mv information.txt readme.txt Storage/

AnasSalhen@htb[/htb]$ tree .

.
└── Storage
    ├── information.txt
    ├── local
    │   └── user
    │       ├── documents
    │       └── userinfo.txt
    └── readme.txt

4 directories, 3 files
```

To copy use `cp`:
```shell-session
# Makes a copy of readme.txt and pastes it in Storage/local/
AnasSalhen@htb[/htb]$ cp Storage/readme.txt Storage/local/

AnasSalhen@htb[/htb]$ tree .

.
└── Storage
    ├── information.txt
    ├── local
    │   ├── readme.txt
    │   └── user
    │       ├── documents
    │       └── userinfo.txt
    └── readme.txt

4 directories, 4 files
```
To delete use `rm`.

# Editing Files
## Nano
There are text editors like Vim and Vi but we will use Nano since it's easier to understand, even of it's less common.
To create a file:
```shell-session
AnasSalhen@htb[/htb]$ nano notes.txt
```
This command opens the Nano editor and creates `notes.txt`.

Nano's interface called `pager` is simple. First we see something like this:
```shell-session
  GNU nano 2.9.3                                    notes.txt                                              

Here we can type everything we want and make our notes.▓


^G Get Help    ^O Write Out   ^W Where Is    ^K Cut Text    ^J Justify     ^C Cur Pos     M-U Undo
^X Exit        ^R Read File   ^\ Replace     ^U Uncut Text  ^T To Spell    ^_ Go To Line  M-E Redo
```

The caret (^) means ctrl, so ctrl+w is for searching. If we search for "we" the cursor (▓) will move to the first match: `Here ▓we can type everything we want and make our notes.` to jump to the next match press ctrl+w then enter: `Here we can type everything ▓we want and make our notes.`.

To view the contents of the file, use `cat`:
```shell-session
AnasSalhen@htb[/htb]$ cat notes.txt

Here we can type everything we want and make our notes.
```

### Important Files
`/etc/passwd` is a file that contains essential info about users such as their usernames, UIDs, GIDs and home directories. It also used to store password hashes but that is now stored in `/etc/shadow` which has stricter permissions.

## VIM
An open-source editor for all kinds of ASCII text like Nano. It's an improved clone of Vi and it focuses on essentials, namely editing text. For tasks beyond that it provides an interface to external programs like grep, awk and sed. This style makes it compact, fast, powerful, flexible, and less error-prone.
It follows the Unix principle, many specialized well-tested programs that are when combined result in a powerful system.
```shell-session
AnasSalhen@htb[/htb]$ vim
```

In contrast to Nano, `Vim` is a modal editor that can distinguish between text and command input. It offers six modes:

| **Mode**  | **Description**                                                                                                                                                                                                                                                 |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Normal`  | In normal mode, all inputs are considered as editor commands. So there is no insertion of the entered characters into the editor buffer, as is the case with most other editors. After starting the editor, we are usually in the normal mode.                  |
| `Insert`  | With a few exceptions, all entered characters are inserted into the buffer.                                                                                                                                                                                     |
| `Visual`  | The visual mode is used to mark a contiguous part of the text, which will be visually highlighted. By positioning the cursor, we change the selected area. The highlighted area can then be edited in various ways, such as deleting, copying, or replacing it. |
| `Command` | It allows us to enter single-line commands at the bottom of the editor. This can be used for sorting, replacing text sections, or deleting them, for example.                                                                                                   |
| `Replace` | In replace mode, the newly entered text will overwrite existing text characters unless there are no more old characters at the current cursor position. Then the newly entered text will be added.                                                              |
| `Ex`      | Emulates the behavior of the text editor [Ex](https://man7.org/linux/man-pages/man1/ex.1p.html), one of the predecessors of `Vim`. Provides a mode where we can execute multiple commands sequentially without returning to Normal mode after each command.     |
When you type "`:`" you go into command mode. Then you can type "`q`" to close Vim.

There is also vimtutor which helps in getting familiar with the editor. To enter tutor from vim use `:Tutor` and from shell use `vimtutor`.

# Finding Files and Directories
### which
`which` can help you know if a program (like Python) exists in the OS by returning the path to the file or link.
```shell-session
AnasSalhen@htb[/htb]$ which python

/usr/bin/python
```

### find
`find` is also a powerful tool that gives you the ability to filter output using options like:

| **Option**            | **Description**                                                                                                                                                                                                                                                           |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-type f`             | Hereby, we define the type of the searched object. In this case, '`f`' stands for '`file`'.                                                                                                                                                                               |
| `-name *.conf`        | With '`-name`', we indicate the name of the file we are looking for. The asterisk (`*`) stands for 'all' files with the '`.conf`' extension.                                                                                                                              |
| `-user root`          | This option filters all files whose owner is the root user.                                                                                                                                                                                                               |
| `-size +20k`          | We can then filter all the located files and specify that we only want to see the files that are larger than 20 KiB.                                                                                                                                                      |
| `-newermt 2020-03-03` | With this option, we set the date. Only files newer than the specified date will be presented.                                                                                                                                                                            |
| `-exec ls -al {} \;`  | This option executes the specified command, using the curly brackets as placeholders for each result. The backslash escapes the ";" because otherwise, the semicolon would terminate the command and not reach the redirection.                                           |
| `2>/dev/null`         | This is a `STDERR` redirection to the '`null device`', which we will come back to in the next section. This redirection ensures that no errors are displayed in the terminal. This redirection isn't an option of the 'find' command. It's aimed for the shell not `find` |

### locate
it's faster than `find` since it works with a local database that contains all info about existing files. We can update it using :`sudo updatedb`. However, compared to `find` it doesn't have as many options.

# File Descriptors
In Linux/Unix File Descriptors (FDs) are maintained by the OS and they act as identifiers for an open file, socket, I/O stream or any other I/O resource. In windows this is called File Handle. 
The first three FDs in Linux are:
- STDIN (0): For input
- STDOUT (1): For output
- STDERR (2): For errors

## Redirection
If we don't want errors resulting from a command to show on the screen we can redirect them to `/dev/null` which is like a blackhole that discards everything which is why we used `2>/dev/null` earlier.
If we want to redirect normal output also to another file we can use `1>` (or just `>` since it assumes `1>`). For example: `find /etc/ -name shadow 2>/dev/null > results.txt` will get rid of errors and redirect output to `results.txt`. We can also redirect errors to a normal file by doing `2> errorsfile.txt`.

When dealing with input we can take input from a file this time using `<`, like:
`cat < inputfile.txt`. It's useful to think about `>` and `<` as directions for data.

`>` overwrites the file if it exists and creates a new file if it doesn't exist. If we want to append (add to content without replacing it) we need to use `>>`.

If we want to type input instead of getting it from a file we can use `<<` with something called End-Of-File **(EOF)**. Combine this with a `>` and you write your input to a file.
![Terminal window with user 'htb-student@nixfund' executing 'cat << EOF > stream.txt' with input 'Hack The Box' and 'EOF'. Then 'cat stream.txt' displays 'Hack The Box'.](https://academy.hackthebox.com/storage/modules/18/find6.png)

### pipes
We can use pipes (|) to redirect output to be the input of another command. In the example below we get the output of the `find` command and use it as input for `grep` to filter results containing `systemd`:
![Terminal window with user 'htb-student@nixfund' executing 'find /etc/ -name *.conf 2>/dev/null | grep systemd'. Output lists systemd configuration files: system.conf, timesyncd.conf, journald.conf, user.conf, logind.conf, resolved.conf.](https://academy.hackthebox.com/storage/modules/18/find7.png)

We can even add another pipe and use `wc` to count the number of outputs:
`AnasSalhen@htb[/htb]$ find /etc/ -name *.conf 2>/dev/null | grep systemd | wc -l`
The output is `6`.
Knowing how to use all of that can be more efficient and save time.

# Filtering Content
## More and Less
To make viewing content easier we can use the pagers `more` and `less` either after a pipe to view a command's result or directly on a file to view it.

`AnasSalhen@htb[/htb]$ cat /etc/passwd | more`
`AnasSalhen@htb[/htb]$ less /etc/passwd`

After closing `less` we notice that the output we were viewing disappears unlike `more`. Also `less` provides more features like scrolling backwards.

## Head and Tail
`head` prints the first 10 lines by default and `tail` returns the last ten.
`AnasSalhen@htb[/htb]$ head /etc/passwd`
`AnasSalhen@htb[/htb]$ tail /etc/passwd`

## Sort
`sort` sorts output, if no given options it does it alphabetically.

We can use `grep` to show results that contain a keyword or results that don't contain it when using the option `-v`.

`tr` can be used to improve readability. i.e. `tr ":" " "` will replace every colon (:) with a space ( ).

`column` is a tool that can improve how data is presented. Using the option `-t` will display data as a table.

`awk` can be used to program the way you want data displayed. For example 
`awk '{print $1, $NF}'` when used after a pipe will display the first and last result of each line of output.

`sed` is a stream editor that uses regex, one of its most common uses is substituting text. For example when we use `sed 's/bin/HTB/g'` after a pipe it replaces every `bin` with `HTB`. `s` at the beginning is the substitute command and `g` means for all matches.

`wc` can be used for counting. The option `-l` means only lines are counted.

# Regular Expressions (RegEx)
It uses special symbols called metacharacters that define the structure of the text when you are searching for something

## Grouping Operators
| **Operators** | **Description**                                                                                                                                                             |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `(a)`         | The round brackets are used to group parts of a regex. Within the brackets, you can define further patterns which should be processed together.                             |
| `[a-z]`       | The square brackets are used to define character classes. Inside the brackets, you can specify a list of characters to search for.                                          |
| `{1,10}`      | The curly brackets are used to define quantifiers. Inside the brackets, you can specify a number or a range that indicates how often a previous pattern should be repeated. |
| `\|`          | Also called the OR operator and shows results when one of the two expressions matches                                                                                       |
| `.*`          | Operates similarly to an AND operator by displaying results only when both expressions are present and match in the specified order                                         |

# Permission Management
Every file and directory has an owner (a user, usually the creator) and is associated with a group. Permissions are defined for the owner, group (users in the same group as the file) and others (everyone else).

## Permission types
- (r - octal 4): read
- (w - octal 2): write
- (x - octal 1): execute
Execute on a directory allows you to access its contents and navigate with `cd`, on a file allows you to execute it. For example, having only execute permission on a directory means you can't list (read) contents with `ls` but if you know the name of the file inside and have the right permissions you can access it.
![Screenshot 2025-04-18 163422](https://github.com/user-attachments/assets/852b65c2-e704-4983-a439-8dd2c430ed13)

1. File type (- = File, d = Directory, l = Link, ... )
2. Permissions of the owner (read, write, execute)
3. Permissions of the group (read, write)
4. Permissions of others (read)
5. Number of hard links
6. User
7. Group
8. File Size
9. Date
## Changing Permissions
Using `chmod` followed by u (owner), g (group), o (others) or a (all users) followed by + or - and lastly the permission we need to change r, w or x followed by file name.
```shell-session
cry0l1t3@htb[/htb]$ ls -l shell

-rwxr-x--x   1 cry0l1t3 htbteam 0 May  4 22:12 shell

cry0l1t3@htb[/htb]$ chmod a+r shell && ls -l shell

-rwxr-xr-x   1 cry0l1t3 htbteam 0 May  4 22:12 shell
```
We can also use octal numbers:
```shell-session
cry0l1t3@htb[/htb]$ chmod 754 shell && ls -l shell

-rwxr-xr--   1 cry0l1t3 htbteam 0 May  4 22:12 shell
```
Explanation:
```shell-session
Binary Notation:                4 2 1  |  4 2 1  |  4 2 1
----------------------------------------------------------
Binary Representation:          1 1 1  |  1 0 1  |  1 0 0
----------------------------------------------------------
Octal Value:                      7    |    5    |    4
----------------------------------------------------------
Permission Representation:      r w x  |  r - x  |  r - -
```

## Changing Owner
`chown` can be used to change the owner and/or the group of the file.
```shell-session
cry0l1t3@htb[/htb]$ chown root:root shell && ls -l shell

-rwxr-xr--   1 root root 0 May  4 22:12 shell
```

## SUID and SGID
### SUID
When a file with Set User ID (SUID) is run it runs with the permissions of the file's owner not the user who is running it which gives normal users the permissions of the owner of that file. A file that has SUID enabled will have "s" instead of "x" in the owner's permissions. For example,
`-rwsr-xr-x`.

### SGID
Set Group ID (SGID) operates like SUID for files, it allows running the program with the group permissions of the file's group (not owner permissions like SUID).
However, for directories it makes all new files/folders created inside it inherit the directory's group, not the creator's group.

There are dangers to using these. If we apply SUID to a program that for example can launch shell from its interface any user running the program could execute a shell as a root.

## Sticky Bit
This feature allows only the file owner to delete and modify their files. Instead of taking away write permissions which won't allow anyone to create or delete anything, we use sticky bits so that everyone can create and delete only their files which helps in situations when a file is shared between multiple users. 
It's denoted by a "t" that comes instead of the "x" in the other's permissions. Capital "T" `drw-rw-r-T` means that others don't have execute permissions. Small "t"  `drw-rw-r-t` means that others have execute permissions.
