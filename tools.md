# Tools 

## Tmux: tmux is a terminal multiplexer. It lets to have different windows and has lot of uage for managing tasks/processes. 
To install the tmux on debain: 
```sh
sudo apt install tmux
```

To install on redhat based system: 
```sh
sudo dnf insatll tmux
```
Running tmux: `$ tmux`


**Shortcuts for tmux:** The following keys can be used after pressing `Ctrl+b` which enters th etmux to command mode. 
|Key|Usage|
|---|-----|
|D  |Detach from window|
|%| Split current window in two horizontal focuses|
|"| Split current window in two vertical focuses|

To list all active windows run the `tmux ls` command. 

To re-attach to a window use the `tmux attach -t <session-id>`ex: 
```sh
$ tmux ls
0: 1 windows (created Fri Aug 16 10:31:20 2024)

$ tmux attach -t 0 
```
To move the focuse to another pane first enter to the command mode and then use the arrows.  

To kill a tmux session from outside of the session: 
```sh
$ tmux kill-session -t 0
```

To create a new window inside a tmux window:
1. press the `Ctrl+b`
2. press the `c` button. 

To move from/to different windows first enter to the command mode and then  just use the number buttons like 0 to go to the window 0, and so on.


To change the size of a pane, as usal fisrt enter to the command mode, and then `Ctrl + arrow`

To zoom and give the entire screen to some pane, first enter the command mode, then press the `Ctrl+z`. To move back to the previous mode and having the pane alongside others do the sam process.

 
Tmux is useful specially when we want that our job continue to run even if we close the terminal, by first detaching from the window. 

We can give name to each session in tmux. for example if we create separate session by giving each session then moving between them will be esaier then we ca see the number of session is which session. To this we use `Ctrl+b` and then `,` to rename the session. 


We can remane the window as well, this is useful when we use the `tmux ls` to list all active windows, then having name on windows makes it easier to handle them. To do this enter in command mode and then press the `$`, then enter a name. Then for re-attaching we can enter the name of window instead of number. 

There are other commands in tmux as well which are not like single button. They are like commands we have in Vim `:something`. These commands can be entered after entering the command mode. 

To run same command in all panes within same window, enter into command mode, then enter the `:set synchronize-panes`. By doing the same process we disable the synchronized mode. 

Note: the shorycuts can be configuered and we can have our own keys. 

Scrolling wil not work with the normal scrolling, for this purpose we should first enter in the command mode and then press page up or page down. 

To copy text from some pane we can not do it as we do in normal terminal, for this purpose zoom in a pane and then copy the text. 

Tmux windows can be shared, so that if we connect to same window from other session, we can see changes and do changes in realtime. 

## Docker: 
To list all running containers' ID: 
```sh
docker ps -q
```

To list all images' ID:
```sh
docker images -q
``` 

To stop all containers: 
```sh
docker stop $(docker ps -q)
```

To remive all images: 
```sh
docker rmi -f $(docker images -q)
```




