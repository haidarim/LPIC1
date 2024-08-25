- `$ head <file>`: gives som paragraf of head of the file
- `$ pwd`: shows the current directory path
- `sudo su -`: to get all permissions
- `wc -w <filename>` word count, note othe r options are available
- `which <commandname/progName>`: print out the path of executable file og the specified command or program

- `ls -ltrh <filename>`: prints out the access information and the linking path to the og file
- `type <command>`: prints out the information of which type the command is

- `readlink <path of the binary/executable command or program>`: gives the the path of the real binary, e.i. the binary which the specified program/command links to.
  to get the real file (final link to real binary) use `readlink -f <path>`

- `ps`: commands for processes, e.g. list processes, etc.

- `pstree`: shows process's tree

- `wall message` to sen the message to all conencted users
- `mesg` to check whether the system is configured to accept messages sending from others e.g. wall. to change the configuration: `mesg <y or n>`

- `who` to print the sender of message by wall and some other inforamtions

- the argument `--no-pager` will tel the terminal that when printing infor dont use paiging format i.e. jump out fron the

- to print only the houndred bottom lines `-xe`

- to find the location of a file: `locate <name>`

- `file <filename>`: to check what a file is and get inforamtion about a file.

- `readelf`: to check the content of an elf file (linux executable file). 
ex: `$ readelf -Wl <path of the executable elf file>`



**To seach in commadn history:** We can search whithin the command history by pressing the `Ctrl+R`, and then entering which command we are looking for.


**System information:** commands used to get information about the system: 

|Command|Description|
|-------|-----------|
| whoami| Display current username|
| id    | Returns users identity |
| hostname| sets or prints the name of the current hostname|
| uname| information about OS and the hardware|
| pwd| print work directory|
| ifconfig| to assign or to view an address to a network interface nad/or configure network interface parameters|
|ip| to show or manipulate routing, network devices, interfaces and tunnels|
| netstat| network status|
|ss| to investigate sockets|
| ps | process status|
| who| who is logged in |
|env| print environment or sets and executes commands|
|lsblk| list block devices|
|lsusb| list USB devices|
|lsof| list opened files|
|lspci| list PCI devices |


**Specifying a column to be printed:** we can use the `$` and `awk` for this purpose, the `$NF` is the last column: 
ex: To print first and last columns. 
```sh
ls -la | awk '{print $1, $NF}'
``` 



htb;File Descriptors and Redirections
