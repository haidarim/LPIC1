# LPIC 1: Linux Professional Institute Certification Level 1

<h4> System Architecture (101)</h4>
<ul>
    <li>Determine and configure hardware settings 101.1</li>
    <li>The Boot Process 101.2</li>
</ul>

<h4> Linux installation and Package Management (102)</h4>
<ul>
    <li></li>
    <li></li>
</ul>

<h4> GNU and Unic Commands (103) </h4>
<ul>
    <li></li>
    <li></li>
</ul>

<h4> Devices, Linux Filesystems, Filesystem Hierarchy standard (104)
 </h4>
<ul>
    <li></li>
    <li></li>
</ul>

## System Architecture (101)

### Determine and configure hardware settings 101.1

BIOS (Basic Input Output System) is a Firmware. A Frimware is a software that controlls hardware e.g. keyboard firmware, etc.
The BIOS will boot the system, when power is on the firmware will check up the harddisk and then put the code address into memmory so that cpu knows where it can continue.

**sysfs:** system filesystem `/sys`
Is provided by linux kernel, provides informations about kernel subsystems things like hardware devidces, drivers, etc, i.e. things that is in kernel's memory.
sysfs is mounted under `/sys`

- Note: all devices that are at the `block` and `bus` have all connected PCI, USB, serial devices, etc.
- Note: devices in `sys` is based on their technology but in `/dev` is abstracted.

- Most of time when troubleshooting we dont need to check `sysfs`, instead we going to check `/dev`, i.e. the abstracted form, this is called `udev`.

**udev:** userspace `/dev`
A devide manager for the kernel. It manages hardware devices e.g. harddisks, etc.
when connecting a USB or harddisk, this directory will look like `/dev/sda1`, which indicates that it is a harddisk and it is the first hard dsik "a" and the first partitiion is 1.
If connecting another harddisk it will look like `/dev/sdb1`, "b" and first partion as "1".

- `/dev/block`: memory, harddisk ,etc.
- `/dev/random`: a device which gives random data.
- `/dev/zero`: gives only zero when running it.
- `/dev/tty`: terminals we using to communicate. to `stdout`at the moment the out put is terminal.

- and so on.

**dbus:** D-buss is a buss used for cummunication between applications, messag bus system. In this way processes can speack to each other, in other words, it is a system for IPC.

---

**proc:** directory: `/proc`
Here is where the kernel keeps its settings and properties. This directory includes e.g.

- Network settings: in /proc/sys/net
- irq: interrrupt requests.
- IO-ports: addresses in memory
- dma: Direct memory access.
- Processes (each number in the `/proc` indicates a process's ids known for pid, every thing is file in linux)
- cpuinfo file: info about the cpu
- uptime file: shows how long the system was up (sec)
- meminfo (memory): info about the memory
- ...

* `/proc/sys`:

- `../net`: information about network e.g. all informations and status about ipv4 is in `net/ipv4` and by issueing `$ cat ip_forward` show the number of forwarding ip.

---

#### hardware related commands:

**lsusb:** Shows `USB` devices that are conencted to the system sush as:

- root hub, which is the buss itself, normally shown as Linux Foundation <nr> root hub
- fingerprint device
- camera
- and other connected devices.

**lspsi:** Shows `devices` that are conencted to the computer such as

**lsblk:** Shows all `block-devices` which are those that can read from or write to by blocks of data,

**lshw:** shows information about hardware.

---

#### Loadable Kernel Modules:

Most of the time, Linux-kernel has the needed drivers to communicate with hardwares, so when connecting a new device to the system, the kernel probably knows how to communicate with that device. Rather, the Linux-kernel separate derivers into `.ko` files, known as kernel-modules.

In fact, no drivers are loaded until they are needed. therefore these modules are known as loadable kernel modules as well. When connecting a new device to the system, the kernel will detect the device, search for a suitable device driver, and load the corresponding kernel module (`.ko`) into memory. There is no need of restarting either.

These modules are modules are small pieces of code that can be dynamically loaded and unloaded into the kernel at runtime. After all process, the kernel will register the deivice with the appropriate subsystem, making it accessible to user-space application throught device files in the `/dev`. In this way user-space applications can then read from and write to the device using standard system calls such as `read()` and `write`.

- These modules are located at `/lib/modules`

**Commands related to the kernel-modules:**

- `lsmod`: list all existing modules in the system, and other information related to each module. Note: used by column indicates how many subsystem uses the module right now in the kernel e.g. 0 indicates the specified module is not used by any one.

- `rmmod <name of module>`: to remove a module.

- `modprobe <name of the module>`: similar to `insmod` is used to insert/add a module into kernel. but `modprobe` is more easy to use, the user only need to type the name of module, but using `insmod` the user should give the path of the module.

ex: to add the `iwlwifi` which is the module related to all wifi derivers:

```bash
sudo modprobe iwlwifi
```

- NOTE: sometimes we can not remove a module because they being used by some one, to force the remove we can run:

```bash
sudo rmmod  -f <nameofmodule>
```

- NOTE: the changes will be temporary, but for keeping them permanently add their names into `/etc/modules` and also add their config files into the `/etc/modprobe.d/`

---

### The Boot Process 101.2

When power being streamed into motherbord then the firmware will starts and check hardwares. After firmware performed sanity check of all hardware (PowerOnSelfTest), it will start the bootloader program.

Boot loader program (e.g. grub) is located in hard-disk, thi program in its turn will boot the kernel based on its configuration. The advantage of use a bootloader program intead of loading the kernel directly by firmaware is that, if there is any issue with the kernel we still can give some commands in boot loader such as checking the kernel, changing the kernel, use older version of the kernel, etc.

Kernel loads and prepare the system/ root file system, the kernel itself needs some derivers and therefore uses initramfs/initrd file for this porpuse. The kernel will run the `Init` system which starts programs, services GUI, ...
There are two different init systems:

- SystemVinit(system five init): older version
- systemd: newer version

information about fimware is located in `/sys/firmware`

In Linux almost every thing being logged, even when kernel is loading. In fact during boot the kernle does not log to any file beacause at this time it does not have access for writing to some file, instead the kernel will log to `ring buffer`, which is used to log during the boot by kernel. All logs can be find in `/var/log/`, to check information in ring buffer print the `/var/log/dmesg`. boot logs are in `var/log/boot.log`, etc.

**Commands relatedd to log:**

- `dmesg`: will print all info in ring buffer.
- `journalctl`: prints journals/logs, e.g. `journal -k` to print kernel logs.

---

**More about init systems:**

1. `systemd`: is a daemon (system daemon), a daemon in Linux is a background process, performing tasks normally without user-interaction. systemd are based on units, a unit is an configuration file describing how systemd manages differetn system resources. These units can be of different types depended on which resource they describe. There are 12 types of units including:

- Service units, used to manage system services (daemons)
- Socket units, used to manage sockets
- Device units, used to manage hardware devices recognized by the kernel
- Mount units, used to manage mount points for filesystem
- Timer units, used to schedule actions to be taken at specific times or intervals
- Target units, used to group units and synchronize the systemstate. each target is a combination of services.
- Swap units, used to manage swap space on the system
- Snapshot units, used to capture the current state of the systemd manager.
- Slice units, used to manage resources for processes
- Scope units, used to manage externaly created processes
- Path units, used to manage file system paths
- Automount units, used to manage automount points for the filesystems

These units are typically located in

1. `/etc/systemd/system/`
2. `/run/systemd/system/`
3. `usr/lib/systemd/system`

**Commands related to systemd:** `systemd` uses two important commands; **systemctl** and **journalctl**

- **systemctl**: is used to query or send control command to the system manager, e.g. starting a service, ...
- ex0: to list all units use `systemctl list-units`

- ex1: to list only unit of a special type use `systemctl list-units --type=<typeNameSuchAS:service>`

- ex2: To get the status of a unit by `systemctl status <unitName>`

- ex3: to get information about a unit: `systemctl cat <name of that unit e.g. grafical.target>`

- ex4: to see what is the default target: `systemctl get-default`

- ex5: to see files and the state of uits: `systemctl list-unit-files`

- ex6: to start/start/restart a unit: `systemctl start/stop/restart <nameOfUnit>`

- ex7: to check if some unit is active: `systemctl is-active`

- ex8: to enable/disable a unit: `systemctl enable/disable <name>`

- ex9: to check if the system is running as it was configured: `systemctl is-system-running`

**reloading a service vs deamon-reloading:** When changing the configuration of a service for example to changing its runtime behavior or settings that the service reads dynamically, then we need to reload that service using the command below without restarting:
`systemctl reload <serviceName>`. But when we change the service unit file, i.e. the unit service (everything is file in linux, /etc/systemd/system/serviceName) then we need to do `systemctl daemon-reload` and even restartartin the service is needed after daemon-reloading.

**journalctl:** to log log information:

- to log last 100 lines of log journal in system: `journalctl -xe`
- to log last hour's log: `journalctl -S -1h`
- to log information based on more specific time:
  `journalctl -S "2024-06-06 18:00:00"`
  `journalctl -S "10min ago"`
  `journalctl -S "30sec ago"`

- log only last X lines where X is a number: `journalctl -n X`
- log only logs to the specified X unit, where X is a unit name: `journalctl -u X`
- log only logs related to the specified process id X, where X is a PID: `journal _PID=X`
- log based on priority levels, X is a priority level in rage [0,7]: `journalctl -p X`
  0: Emergency
  1: Alert
  2: Critical
  3: Error
  4: Warning
  5: Notice
  6: Informational
  7: Debug

### Runlevels/boot targets and shutdown and reboot system 101.3

Runlevel is different states in the systems lifecycle.
boot the system ---> kernel ---> load main disk ---> activate other disk ---> root user login ---> multi user ---> network ---> GUI ---> Webserver, dns server ---> shutdown

In Linux systems, the current state can be changed, e.g., to some previous state or different runlevels.

**Targets:** As mentioned in the previous section, each target is a combination of services. Targets can be configured, arranged, scheduled, etc.

To get information about a specific target:

```bash
$ sudo systemctl cat graphical.target
```

This command will print out configuration information such as when the target will be run and which other units will be conflicting with this target, i.e., the target's scheduled timelines. There is also information about whether isolation is allowed for the target.

A target can be dependent on another target. For example, target X might need target Y to be in a running state, and target Y, in turn, might need target Z to be running.

**Specific targets belonging to the systemd:** systemd has some specific targets for itself to control the machine such as:

- `rescue`: is for rescue, no network, file systems are mounted, and only root user (maintanance mode)
- `emergency`: only the root file system and in the read-only mode. No networking and only root (maintanance mode)
- `reboot`
- `halt`: stops all processes, and halts CPU activities.
- `poweroff`: like halt but send a ACPI (Advanced Configuration and Power Interface) shutdown signal.

**Target Isolation:** This is a way for switching between targets, When calling isolate using `systemctl isolate <targetName:X>`, to switch to the target X.

**SysV (systemFive) runlevels:** This is an older version provided as an Init system, this system has usually seven different stages in redhat based systems: 0. Shutdown

1. Single user mode (recovery); also called S or s
2. Multi-user without networking
3. Multi-user with networking
4. to be customized by the admin
5. Multi-user with networking and graphics
6. reboot

in Debain based systems we had stages 0, 1, 2 and 6.

Even in systemd this levels can be used for examle commands such as:

- `runlevel`: to get the current level which the system is running in, if the system did not changed runlevel it will print `N` and a number representing the current running level. if the system have done swiching between levels then it will print the prevois level and the current running level as a number.
- `init <num in range 0 to 6>`Ä: to change the run level, i.e. switch the running target.
- `telinit`: is similar ot init command

**System services scripts:** Are used manage (e.g. starting, stoping, restarting) various of system services (e.g. postgresql, sudo, ssh, ...). this scripts are stored in `/etc/init.d/`.

**Different levels' files:** each run level has its own directory in `/etc/rcX.d` where X is the number of a level. in this directory some files (links) are available that perform each levels tasks, this links are linked actually to files (scripts) existing in the previous explained directory i.e. /etc/initd/

**Safe TearDown:** If we want to stop the system, but we want to save the work before stopping so thhat no data goes lost, we have different commands available for this porpuse:

`shutdown` is a type of halt, power-off or reboot, a better choice for have a safe teardown, then new users dont be accepted to connect the server and connected users can recieve notification about the plan.
format:

```bash
shutdown [options...] [TIME] [WALL]
# args or swithchs
# when the shutdown must happen
# what to write for inform all connected/logged in- user before shutting down
```

ex1: `shutdown now` to shutdown the system imediatly
ex2: `shutdown -r 10 bye bye` to reboot the system in after counting 10 minutes and sendding the message "bye bye" to all connected users.

ex3: `shutdown -c` to cancle the shutdown process

The file `/etc/motd` (message of the day) is used to write som mesage that will be printed for users when they connects by an terminal e.g. ssh.

## Linux installation and Package Management (102)

### Design Hard Disk Layout, Filesystem Hierarchy Standard (FHS) 102.1

**FHS:**

| Directories |                                                                                  |
| ----------- | -------------------------------------------------------------------------------- |
| **bin**     | system binaries ,command binaries, commands that being used in terminal is placed here. |
| **boot**    | used for booting the system, components such as grub,                            |
| **dev**     | all devices                                                                      |
| **etc**     | the most important, contains the configurations                                  |
| **home**    | contaons all the user's home directories                                         |
| **lib**     | shared libraries and kernel modules, shared between programs                     |
| **media**   | Mount point for removable media, such as USB disk                                |
| **mnt**     | Mount point for mounting a filesystem temporary                                  |
| **opt**     | Add-on package installation                                                      |
| **root**    | home directory for the root-user                                                 |
| **sbin**    | Essential system binaries such as systemd, grub-installation ,etc                |
| **srv**     | server directory if there is some server                                         |
| **tmp**     | where temporary files being stored, seenable for other users                     |
| **usr**     | secondary hierarchy                                                              |
| **var**     | variable data e.g. logs, etc                                                     |

**Partitions:**
| /boot | other partitions |

in other partitions section different partitioning is åossible such as `/` the root partition.

For partitioning the disk the most used command is `fdisk`. disk partiions are under `/dev/` such as `/dev/sda` or `/dev/nvm...` depended on the os.

To see how a partition is mounteed then use `mount` command, this will print out information about partitions, fo example it can show that `/dev/nvme0n1p2 on / `, this means that the partition `/dev/nvme0n1p2` is mounted on `/`.

To see the disk partitioning graphically use `gparted`.

**LVM:** Logical Volume Manager is very helpful when there is a need to resize the partitions or install new disks then adding them to the current mount points.

`pv` or physical volume is the physical partions (drive). The LVM makes it possuble to have diffent sized and divide the pv easier in an `vg` (volume group). So instead of using the whole disk as a unpartitioned, we can have a collection of pvs with user defined sizes in a vg.

There are commands for pv as `$ pv` as well as for lvm as `lvm`

**swap partition:** The linux OS have a partion for swap operation, we can change the size of the swap partiion but it should be carfully becuase having large partition for swap can slow down reading and writing as the hard disk operations are not as fast as memmory operations.

the recommneded size for swap partition is between (RAM size + (2 to 4) GB).

**Some partitioning recommendation:** partitioning is depended on the type of computer:

1. Desktop computer: for PCs is good to have one
   | /boot (typically 1GB) | swap (RAM size + (2 to 4) GB) | / |

2. Network workstation: i.e. the biggest for `/home` part since network station users may need much space and to prevent rewriting other partiions
   | /boot (1 GB) | swap (RAM size + (2 to 4) GB) | / (~50GB)| /home (the remainin of size) |

3. Server: the `/var` should be enaugh large since the logs may take much place, and other parts such as `/opt`. When it comes to server partitions should be seperated or using more advanced storage such as RAID. RAID is a data storage virtualization technology that combines multiple physical disk drive components into one or more logical units. There are many types of RAID, one of them for use for backup like fail over scenario, by having two disks that contains same information so if the connected disk fails we dont lose the data and information. RAID can be software or hardware level.

| /boot | / | /var | /opt |

**Other concepts and commands:**

- `free`: To see the status of the memory use the `free` command. For see the information in human readable format use `free -h`
- `swapon`: To print out information about swap partition
- `top`: To check the status of the system, CPU, memory, processes, etc.

### Install a boot manager 102.2

This part is important to know for troubleshooting for example when the linux not booting, when the gnu linux not loads.

All boot loaders are or have some parts in the MBR (Master Boot Record). MBR is very small (only 512 bytes) and contains informations about the boot loader. The grub boot loader has bigger size and therefire some parts of it is placed in MBR, which loads the rest of the grub (which resists in other place on the disk).

The boot loader is responsible to find the linux kernel and other related components such as ramfs. The boot loader can also do chain loading as well, it means when we have multi OS then first boot loader for OS gives the control to the boot loader of OS 2.

There are different versions of grub, grub1 (or grub version 0.98 also called and called grub legacy) and grub2. The used version is grub2.

**GRUB legacy:**
This is usually installed in `/boot/grub`. The main configoration of the grub is placed in `/boot/grub/menu.list` (in redhat) or in `/boot/grub/grub.conf`.

`grub.conf` or `menu.list` has two different sections as follows:

1. general:

- color: foreground color and background color for normal and active item.
- default: which boot menu is the default.
- fallback: which boot menu shpuöd be replaced for use if the default one fails.
- hiddenmenu: hide the menu options.
- splashimage: show this image in the background.
- timeout: wait the amout if the time and then start the default.
- password: security set, ask the password (md5)
- savedefault

2. boot options:

- title: defines the section name e.g. fedora, ubuntu so show a menu item called e.g. fedora
- root: which partition is adderessed for booting the system.
- kernel: which kernel on that partition.
- initrd: which initrd in that partition.
- rootnoverify: define a non-linux root partition (if the set up is set for chaining)
- chainloader: got to another chain, another file will act as stage 1 loader, used for booting windows systsems

To install the grub1:

```bash
grub-install /dev/sda # note not sda1, we install on the whole disk

```

**GRUB2:** Used in linux and the modern version.
This is installed in `/boot/grub/` or `/boot/grub2`. Under UEFI it is placed in `/boot/efi/EFI/ditroName/`. The configuration file of grub2 is called `grub.cfg`. it is similar to grub legacy:

- menuentry: defines a new menuentry
- set root: define the rrot where the /boot is located.
- linux ,linux16: defines the location of the linux kernel on BIOS system.
- linuxefi: defines the linux kernel on UEFI systems.
- initrd: defines the initramfs image for BIOS systems.
- initrdefi: defines the initramfs image for the UEFI systems.

Commands for GRUB2:
The installation is same as the grub legacy, i.e. `grub-install /dev/sda`. after changing the grub file we need to issue the command: `grub2-mkconfig -o /boot/grub/grub.cfg` or `grub-mkconfig > /boot/grub/grub.cfg`, this command will read the configuration files from `/etc/grub.d/` and `/etc/default/grub` and then creates the `grub.cfg` file based on readed files.

Remmeber that configurations are located under `etc` så we can change the grub configuration in `/etc/default/grub`, so we can change the configuration in this file and then using the command `grub-mkconfig > /boot/grub/grub.cfg` save changes and rebbot the system.

**sending parameter to the kernel:** we can send parameters to the kernel in grub command line mode.
| options for sending parameters| |
|-------------------------------|---|
| console= | set the console |
| debug | start in debug mode|
| init= | run an specific program instead of the default |
| initrd= | use the specified initrd |
| ro | Mount the root read only |
| rw | Mount the root file systemfor read and write |
| root= | use the specified path as the root filesystem |
| selinux| disable selinux on boot |
| single, S, 1, Single | Boot in single user mode for trouble shooting (SysV) |
| systemd.unit= | Boot in the specified systemd target |



### Manage Shared Libraries 102.3 
Shared libraries in Linux refer to dynamically linked libraries that programs can use during execution. Instead of each program containing all the code it needs to run (static linking), programs in Linux can depend on external shared libraries, which are separate files containing compiled code that multiple programs can use simultaneously.


Managing shared libraries in Linux is necessary to ensure:

- Compatibility: Different versions may exist, each with improvements or fixes. Managing libraries ensures programs use the correct version.

- Dependencies: Programs require specific library versions. Managing libraries resolves these dependencies.

- Security: Libraries can have vulnerabilities needing patches. Managing libraries includes applying security updates.

- Performance: Updating or optimizing libraries can improve program performance.

- Stability: Proper management prevents duplication and maintains system stability during updates or changes.


Linux dynimic librares have the `.so` extension and in general look like `libName.so.versionNr` and are located at places like `/lib*/` and `/usr/lib*/`, in Windows OS they are called `DLIs`. 
- `/lib/`: contains 32-bit libraries (for 32-bit based computer)
- `/lib64/`: conatins 64-bit libraries (for 64-bit based computer)  

**system libraies and usr libraies:** libraries used by the system are stored/located in `/lib*/` and user/softwares libraries are placed iin `/usr/lib*/`


**How dynamic libraries being found by OS:**
The OS will serach files in order to find a shared library that is needed for a program, the steps taken by OS is described as follows: 

1. The OS will check the LD_LIBRARY_PATH which is a environment variable. The OS will check if the required library is there or not. 

2. The OS will look at the PATH, where the program is running.

3. The Os will check the `/etc/ld.so.conf`

4. The OS will look at the `/lib/`, `/lib64/`, `/usr/lib/`, `/usr/lib64/`. 


**Using linker to run executable in linux:**
There is an other approach to run an executable file in linux using linker. 
The linker in linux is `ld-linux`, which not be executed by calling in whatever directory. to use it we have to enter the complete path for it. The argument given to it is the path of the binary file we want to execute.  
This approach is used by hackers to run programms which have not execute-permission. 
ex running ls using ld-linux: 

```sh
$ /usr/lib64/ld-linux-x86-64.so.2 /usr/bin/ls

```




**Commands:**
- To chech what libraries are needed for a program: `ldd <path of binaries>`, ex: `ldd /usr/bin/ls` if a program is liniked statically then the `ldd` will tell it. 


- `ldconfig`: will read all configurations and also create a cache of all libraries. 
- `ldconfig -p`: to print all libraries inside the cache. 


### Linux Software Repositories (102.4)
In Linux world a repository (also known as repo) is a storage location from which software packages can be retrived and installed. Repositories can be either official, third-party or personal package archives (PPAs). 

**Components of Repositories:**
- Package Files: These are the actual software files, e.g. `.deb` for debain based and `.rpm` for REd Hat-based systems. 

- Metadata: information about the packages, such as description, dependencies and version number. 

**Repositories' configuration:**
Repositories are configured in system files, for example in Debain-based systems repositories' informations are stored in files within `/etc/apt/source.list` and `/etc/apt/source.list.d`

The `/etc/apt/sources.list` file is the primary repository configuration file and can be modified during system updates/upgrades, which might make it harder to manage, especially in collaborative environments. The `/etc/apt/sources.list.d/` directory, however, provides a more modular approach. You can create individual files, such as example.list, in this directory and add repository entries there. The APT package manager will read and use all repository entries from both /etc/apt/sources.list and the files in /etc/apt/sources.list.d/, making it easier to manage repositories separately and collaboratively.


 

**Package File Cache:**
This cahce-file is used by package managers like APT to store downloaded package files and related metadata. When you install software, the package manager checks this cache first; if the package is already cached, it uses the cached version to install the software, rather than downloading it again from the repository. This improves efficiency by saving download time and bandwidth. In Debain-based systems the cache file is `pkgcache.bin` and is located at `/var/cache/apt/`.

When running `sudo apt update` in Debain-based systems or `sudo dnf update` in Red Hat-based systems, the package manager fetches the latest package lisst from the repositories and updates the local metadata stored in the cache. 

There is also a directory located in `/var/cache/apt/`, called `archives` where you can find many packages that can be installed, this is useful for eaxmple when installing asoftware so package manager may have the software's package already in the archive and dont need to download it.  


**Package-management using apt in Debain:**
- To install package: `$ sudo apt-get install <softwareName>`
- To simulate the insatllation of a package (i.e. not installing but just simulating whatwould be done by the package manger): `sudo apt-get -s <softwareName>`

- To just download a package without installing it: `sudo apt-get install --download-only <packageName>`

- To download a package without its dependency or other related files: `sudo apt-get  download <packageName>` 

- To remove a package: `sudo apt-get remove <packageName>`
NOTE: When removing a package, the package manager will only remove the package, not its dependencies. 

- To remove all unneccecary pakcages/dependencies: `sudo apt-get autoremove`

- To remove all dependencies to a specific package: `sudo apt-get autoremove <thatSpecificPackage>`

- To search after a package: `sudo apt search packageName`

- To upgrade packages: `sudo apt upgrade` 

- To force the upgrade use: `apt dist-upgrade`

- To fix borken packages: `sudo apt insatll -f`



**Package management using dpkg in Debain:** dpkg works with `.deb` files and not repositories. To install a package: `sudo dpkg -i <pathOfTheDebFile>`.

- To list all installed packages in the system: `dpkg -l`
- To purge/ remove a package and its configuration files: `dpkg -P <name>` or by apt: `apt -P <name>`

- To see all related files to a package: `dpkg -L <name>`





### RPM and YUM package management (Redhat based systems) (102.5)

102.5/1 start


#file
##stream editor, filterning and transformating:
$ sed OPTIONS <SCRIPT> <INPUTFILE>
mostly used by just giving scrupt and inputfile

\*Scripts: - 's/wordToBeReplaced/ TheWord' - - - - - -
