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

#file
##stream editor, filterning and transformating:
$ sed OPTIONS <SCRIPT> <INPUTFILE>
mostly used by just giving scrupt and inputfile

\*Scripts: - 's/wordToBeReplaced/ TheWord' - - - - - -
