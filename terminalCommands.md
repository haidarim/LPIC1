- `$ head <file>`: gives som paragraf of head of the file
- `$ pwd`: shows the current directory path
- `sudo su -`: to get all permissions
- `wc -w <filename>` word count, note othe r options are available
- `which <commandname/progName>`: print out the path of executable file og the specified command or program

- `readlink <path of the binary/executable command or program>`: gives the the path of the real binary, e.i. the binary which the specified program/command links to.
  to get the real file (final link to real binary) use `readlink -f <path>`

- `ps`: commands for processes, e.g. list processes, etc.

- `pstree`: shows process's tree

- the argument `--no-pager` will tel the terminal that when printing infor dont use paiging format i.e. jump out fron the

- to print only the houndred bottom lines `-xe`

#file
##stream editor, filterning and transformating:
$ sed OPTIONS <SCRIPT> <INPUTFILE>
mostly used by just giving scrupt and inputfile

\*Scripts: - 's/wordToBeReplaced/ TheWord' - - - - - -
