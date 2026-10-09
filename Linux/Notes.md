## Terminal

To increase the font we need to press `ctrl + shift+ (+)`, and to decrease the font we need to press `ctrl+-`

### Clearing the terminal

use the `clear` command and we can also use `ctrl+l` key

### To end the currently running process
use the ctrl + c key

to pause a process or task, use ctrl + z key

- Autocomplete - use the tab key and if u want more references or matching commands, u can press the double tab key and it will show all the potential meanings of your current command

- ### How to close the terminal

  we can use ctrl + shift + t


  ## File Management and Manipulation

  to get the current directory we need to use pwd  - print working directing or i call it present working directory
  to list the directories and files present in the current directory we can use the `ls` command

  ### Other variations of ls command

  example - `ls -l` it will list the contents present in the current directory in the form of list or a table for easy readability

  to get the hidden files as well we can use the all option with this and can use `ls -a ` and we can use it with the list option as well like `ls -al`


  to find the list the files in the more readale format for the users -  we can use the `ls-lh`, readable in the sense that the file size is given in KBs rather than the bytes in the previous command like `ls -l` and the h stands for human readable


  if we want to show the subdirectories as well we can use the command - `ls -lR Desktop/` R stands for recursive here and here we also need to mention the folder where we wanna go recursive form

   change directory - cd command
  if you use `cd ..` we can go to the parent directory

  to go to some specific directory - we can use the command like `cd <directory name`

  and we can also use `/` to specify a directory which is not directly available in the current directory - question is can we just move down the heirarchy or anywhere in the file system, how it usually works and how linux processes this thing



  to navigate back to home directory we can use the `cd ~` command

  > to find the information about particular command or tool we we can use the `whatis` command 



  ## File Commands

  1. How to create a file - `touch` command, example `touch test.txt`
  2. echo command - to print some text in the command line but we can direct this data into some file
  3. redirecting the data to a particular file or the command - used majorly with the eco command, example - `echo "Ujjwal Sharma">test.txt`
  4. to display the content of the file - we can use the `cat` command, it is used to print and concatenate the contents of a file
  5. we can use the cat command to redirect the contents of the file __if the file doesnt exists it will create it and we pss a relative path with cat command__ - example `cat etc/passwrd > pass.txt`


  # File and Directory Permissions and ownership
There are 2 ways to handle permissions in linux
1. Symbolic mode format
2. Octal or binary mode format

you can apply 3 type of permissions you can apply in linux
1. r - > read
2. w -> write
3. x-> execute


how you can see them in the terminal 

you have permissions divided in the block of 3 

like `-rw-r--r--` so you will read it `-rw -r-- r--` the character represents the file type if its a file it will be blank and if its a directory it will start like `drw`
   
Column wise meaning
1. for owner
2. Group permissions
3. For all other users in the system


## CHanging the permissions
`chmod` command is used to change the file mode bits or the permissions of a file

symbolic way to handle this

`chmod u-rwx file.text` __u__ here stands for the current user, to apply permissions to all users and users we can use `chmod go=rwx test.sh`,with this command u stands for current user, g stands for group, o stands for others and a stands for all, and if you dont wanna write all the permissions with euquals to again and again you can use the `+` and `-` symbol to add or remove the permissions like `chmod go-wx` this remove the write and execute permissions from group and others

octal way of handleng permissions

read write and execute permissions are not denoted with binary formats like read is denoted by 4, write by 2, and execute by 1


if i want owner grp and others to have only read permissions i can do, `chmod 444 file.txt` and if you wanna give extra permissions you will just add these values tgether and give the respective number for each entity

> to do anything recursively just use the  -R flag


# File and Directory Ownership

> we will use `chown` command

example `chown root test.sh`


for changing the groups we use the command known as `chgrp` command


example `chgrp root test.sh`


# Grep and Piping

grep prints the lines matching a particular pattern, it also helps you to find the strings and patterns in the file if you are searching for it

> if you are unsure about a tool you can use `man tool/command` name to check how to use it


usage -> `grep "dynamic" /etc/` at the end we specify the location

how to remove the case sensitive search 

use the `-i` flag -> usage `grep -i "dynamic" /etc/`


now we can also pass some data to it and then give it some operations for example -> `cat /etc/pass | grep "Ujjwal" `


| → command → command
> → command → file

# Locate - Finding whiles

it helps us to find the files by name

use of it  ``








