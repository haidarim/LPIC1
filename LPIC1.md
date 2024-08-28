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

<h4> GNU and Unix Commands (103) </h4>
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

**lspci:** Shows `devices` that are conencted to the computer such as

**lsblk:** Shows all `block-devices` which are those that can read from or write to by blocks of data,

**lshw:** shows information about hardware.

---

#### Loadable Kernel Modules:

Most of the time, Linux-kernel has the needed drivers to communicate with hardwares, so when connecting a new device to the system, the kernel probably knows how to communicate with that device. The Linux-kernel separate derivers into `.ko` files, known as kernel-modules.

In fact, no drivers are loaded until they are needed. therefore these modules are known as loadable kernel modules as well. When connecting a new device to the system, the kernel will detect the device, search for a suitable device driver, and load the corresponding kernel module (`.ko`) into memory. There is no need of restarting either.

These modules are small pieces of code that can be dynamically loaded and unloaded into the kernel at runtime. After all process, the kernel will register the deivice with the appropriate subsystem, making it accessible to user-space application throught device files in the `/dev`. In this way user-space applications can then read from and write to the device using standard system calls such as `read()` and `write`.

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

Boot loader program (e.g. grub) is located in the hard-disk, the program in its turn will boot the kernel based on its configuration. The advantage of use a bootloader program intead of loading the kernel directly by firmaware is that, if there is any issue with the kernel we still can give some commands in boot loader such as checking the kernel, changing the kernel, use older version of the kernel, etc.

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
- Timer units, used to schedule actions to be taken at specified times or intervals
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
- to log information based on more specified time:
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

To get information about a specified target:

```bash
$ sudo systemctl cat graphical.target
```

This command will print out configuration information such as when the target will be run and which other units will be conflicting with this target, i.e., the target's scheduled timelines. There is also information about whether isolation is allowed for the target.

A target can be dependent on another target. For example, target X might need target Y to be in a running state, and target Y, in turn, might need target Z to be running.

**Specific targets belonging to the systemd:** systemd has some specified targets for itself to control the machine such as:

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
| **/**       |   root file system, top-level directory, contains all files needed to boot the OS   |
| **bin**     | system binaries , essential command binaries, commands that being used in terminal is placed here. |
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
| init= | run an specified program instead of the default |
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

- Dependencies: Programs require specified library versions. Managing libraries resolves these dependencies.

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

- To remove all dependencies to a specified package: `sudo apt-get autoremove <thatSpecificPackage>`

- To search after a package: `sudo apt search packageName`

- To upgrade packages: `sudo apt upgrade` 

- To force the upgrade use: `apt dist-upgrade`

- To fix borken packages: `sudo apt insatll -f`



**Package management using dpkg in Debain:** dpkg works with `.deb` files and not repositories. To install a package: `sudo dpkg -i <pathOfTheDebFile>`. Note: `dpkg` is low level and the `apt`is more higher and abstracted one. 

- To list all installed packages in the system: `dpkg -l`
- To purge/ remove a package and its configuration files: `dpkg -P <name>` or by apt: `apt -P <name>`

- To see all related files to a package: `dpkg -L <name>`





### RPM (RedHat Package Manager) and YUM (YellowDog Update Manager) package management (Redhat based systems) (102.5)

**YUM (YellowDog Updater Manager)**
yum is higher level of packet management, used mainly by RedHat based systems. The conf file is located at `/etc/yum.conf` where is all configuration for YUM files. And `/etsc/yum.repos.d` containing repository files. Note: `dnf` is newly nad is working same as yum. 

Commands:
- `yum update <packageName>`: update packages to the latest version.
- `ym install <packageName>`: installs the specified package. 
- `yum remove <packageName>`: removes the specified package. 
- `yum list installed`: lists all installed packages. 


**DNF (Defined YUM):**
The next generation of YUM. `dnf` provides better performance and more featurers while maintaining backward compability with YUM commands. The cocnfiguration files are located at: 
- `/etc/dnf.conf`: AMin conf file for the dnf. 
- `/etc/dnf.repos.d`: directory for repository configuration files as YUM.

Commands: 
- `dnf install <packageNAme>`: install the package. 
- `dnf update`: update all packages. 
- `dnf remove <packageName>`: remove the specified package. 
- `dnf list installed`: lists all installed packages. 


NOTE: THERE ARE OTHER SWITHCHES ALSO THAT CAN BE USED IN FORM OF HIGH
OR LOW LEVEL E.G. 

GENERAL FORM: 
`CMD ACTION -OPTION  

**yumdownloader:** This cmd will downdload `rpm`-files without installing them on the system. Using the command `yumdowwnloader --resolve <packageName>` if the specified pakcage is not available but the RedHat based find some that is the working version on system ask for installation. And by entering `y` to install the package. 

**Zypper:** Likly YUM used mostly by SUSE linux and its sibling openSUSE. This package manager uses `ZYpp` as the package manager engin. There other tools like `YAST` or `Zypper` to communicate with the ZYpp.   

Configuration files for ZYpp is located at the `/etc/zypp/zypp.conf` which is the main configuration file for the Zypper. In the `/etc/zypp/repos.d/` are repository configuration files. 

Commands: 
- `zypper refresh`: refersh the respository metadata. 
- `zypper install <packageName>`: update the specified package. 
- `zypper remove <packageName>`: revoes the psecified package. 
- `zypper update`: updates all packages. 
- `zypper list-updates`: lists packages with available updates. 



**RPM:** Is more low level and more near to the machine. Note not in middle like C, OS ans system calls and assambly.   
general form: 

```sh
rpm Action [OPTIONS] <packageName>
```

ex:

```sh
# Actions like: -i for install (e.g. dpkg -i)
# Options like: -v for verbase i.e. print all process 
rpm -iv packageExmaple
```

**Using rpm to find misconfigured file:**
by using the `-V`switch or even better `-Vv` we can see if a programs or packages configuration files are healthy. Healthy files with have only dash but issued files can have S s and some tokens. 


**General note:**:
Both high level tools like apt or dnf/yum, and low-level tools like dpkg/rpm would have other useful switches. Low level managers can also have `long-tem switches` like --instal instead -i and so on. 



**rmp2cpio:** cpio files are kind of compressed file. rpm2cpio converts rpm files into cpio file. 

Using `rpm2cpio <.rpm file>` will just print out the cpio:ed file. but we can use direction to save the cpio:ed file liek: `rpm2cpio <.rpm> > <.cpio>`

To later do the .cpio file depackage we can use the `cppio -idv < file.cpio` to depacke the .cpio file. 




### Virtualization and Containers 102.6
Virtual machines, commonly known as VMs, simulate physical machines for the guest operating system (OS). Operations like paging, which involves translating virtual memory addresses to physical memory, can be costly. Critical operations that require kernel-mode access are handled by the hypervisor.

To check CPUs and VMs: `less /proc/cpuinfo`and we can grep based on the arch e.g. `vmx`on INTEL like and `svm` for AMD based CPUs.
```sh
less /proc/cpuinfo | grep svm
```

Note: hypervisor of type 1 is kvm, svm, Hyper-V from Linux Kernel version 2.6. And type 2 is virtual box and VMWare. 

```lua
      -------------------------
      | App        | App      |
      -------------------------
      |  Bins/Libs | Bins/Libs|
      -------------------------
      |  guest os  | guest os |
      ------------|------------
            | Hypervisor|
            -------------
            | Host OS   |
            -------------
            | Hardware  |
            -------------
```

**Creating virtual machine:** Most use approach is using virtual box and booting images and most simple one. 

One other method is cloning i.e. having exact same machine as one we clone from to be able run in other VM (like copy?). Then right click on the machine and chose the clone option. There is also an issue with cloning, When cloning we must not have double of Hostname, NIC IP (if not using DHCP), NIC MAC Address and machine-id. When we clone we clone all information. These stuff should be fixed after cloning, delete `/etc/machine-id` and `/var/lib/dbus/machine-id`after these steps run `dbus--uuidgen --ensure`. Even SSH and PGP and Keys stuff should be fixed! HDD UUIDs and any other UUIDs on the system. Note some config files might be empty on templates, they should also be written and modified if templates are used. 

Machine-id: Each machine has a id and unique id and uuid as universal unique id. 

Another approach to make a cpy of some image or guest OS is to make a Open Virtualization Format `OVF` to move machines between and using the hypervisor.  

Some sofisticated way is to create template, it is a cleaner work and make it easer to have some more configurations. 

**Installing additional drivers for VMs on virtual machiine::**
In virtual machine navigate to device -> insert Guest Additional CD image. then the desired drive or tool can be installed. 



**Containers:**
In VMs we needed a complete machine as called virtual machines where a complete OS was needed as the guest OS. 

In modern set up containers used to only use a container engine to run libraries and binaries to reduce the need of a OS, in these containers applications can be run and use bins and libs. 

```lua
        ------------------------------
        | App         | App          |
        ------------------------------
        |Bins/Libs    | Bins/Libs    |
        ------------------------------
            | container Engine | # <----
            --------------------
            |      Host OS     |
            --------------------
            |     Hardware     |
            --------------------
```


**IaaS:**
Back in the days used apporach to host applications, own set up harware, power, ...
later it becames more using virtual machine and giving the hosting responsibility to other organisations. Nowadays it becames more common to use of cloud and run machines (VMs) using some clould infrastructure provider and then the application owner can use services provided by the cloud provider. Using e.g. infrastructure and managing connection using like AWS or GCP is refered as Infrastructure as a Service (IaaS). 

Note: there are other services that can be provided by cloud providers such as SaaS, PaaS that can even provide pre-configured services. 



### Shell and Bash essentials 103.1
Shell is a user-interface to, it is aUnix term and allows user to communicate with system. Bourne Again shell (bash) is a modern and cmmonly used shell in Gnu/Linux systems

There are different versions of bash. to print out which version of bash is used in the system run `echo "$BASH_VERSION"`. 

Bash files mostly have a starting header line as `#!/bin/bash` indicating. More about bash on [Bash](http://www.github.com/haidarim/Bash). 

**Environment Vars:**
It is like what ever variable in programming language, we can initializie them like: 
```sh
MYVAR=010
echo $MYVAR
```
ex: 
```sh
ARG1=Hello && ARG2=WORLD
echo $ARG1 $ARG2
```

Using the `env` command can help to see all important environement variables that are unsderstables by default by the bash. For example the PWD variable is stored already which in turn be used by the `pwd` command to execute. 
We can also create our own runable binaries like .sh or other programs and run them from any where by the shell by first adding the path into the PATH variable. 

ex: `PS1='>>\$: '`

when initializing value or defining paths there should not be spaces because specases are special chars as *, \, ?, ...


Path Variable: `echo $PATH`, it can be temporary set using `PATH=$PATH:/path-to-the -binaries`, but to do it permenantly make changes in the .bashrc or .zshrc file.  


### Process text streams and filters 103.2

Tree Main I/O in Unix: stdin, stdout, stderr. 

**Printing/ Viewing:**
- `cat`: to print information of a file. 
- `grep`: to find a pattern in a specified input tex. 
- `less`: to show information aout a file but havint he options to move e.g. by paging and use commands in a file while reading. 
- `od`: showing the content of the file in octal format. 
ex: to print a file in octal format and saying that want to chars as normal characheters be printed in normal use: `od -t c <fileName>`. 
 or using `od -t -a <fileName>` tells that shows the file and normal character in ascii format like space as `sp`.

- `nl`: to print file's content by having numbers on each line. 

- `head`: shows the first 10 lines in the begining in the file. 

- `tail`: prints last 10 lines of a file.  

Note for noth head an tail can the `-n` switch be used to specify the number of lines. 

- <h5> tail can be used with the `-f` switch to watch online new saved changed on a file.</h5>


**Selecting parts:**
- `split`: to split a file into specified number of filed or number of lines in each part:
1. spliting into X number of files in format of `file00`, ..., `file0X-1`
```sh 
split -d -n X <file>
```

2. spliting a file into different files in format of `out_put_name` + `aa, ab, ac, ...`, ... `za, ab, ...` use: 
```sh
split -l <numberOflines> <fileName> <out_putname>
```

- `cut`: to select and print cuted part of a file. 


**Strings Manipulation:**
- `shuf`: to shufle content in a file. 

- `sort`: to sort content of a file. 
Some useful cases/Ex: 
1. `sort file.txt` to sort the given file alphabetically  in ascending order, if no file given it will read from stdin and then sorts the input. 

2. `sort -r file.txt`: to sort the given file alphabetically in descending order (reverse). 

3. `sort -n file.txt`: to sort the given file numerically (increasing order).

4. Sorting by speciifing a filed: 
```sh
sort -t, -k<x>,<x> file.txt

# -t is used to specify the delimiter (in this case ,)
# -k to indicate the column where <x> is the fileld and until ,<x> column
```

ex.1: if we have the following data as file.txt, where the first column is username and the second field is users' age: 
```txt
Alice,30
Bob,25
Charlie,35
```

to sort the file by users' age (i.e. numerically) in increasing order:
```sh
sort -t, -k2,2n file.txt
#n is used to indicate numerically sort 
```
result: 
```txt
Bob,25
Alice,30
Charlie,35
```

ex.2: if the file.txt contains the following data and we want to sort the file based in users' age and **if the age is equal then sort alphabetically: 
```txt
Alice,30
Bob,25
Alice,25
Bob,30
```
```sh
sort -t, -k2,2n -k1,1 file.txt
```
output: 
```txt
Alice,25
Bob,25
Alice,30
Bob,30
```

- `uniq filename`: to print the unique content in each line in a file, Note, the file must be sorted otherwise the uniq command will not work as desirred.ex. `sort file.txt | uniq`


- `paste`: used to merge lines of fiiles horizontally. 
ex.1 if we have file1.txt containing: 
```txt
1
2
3
```

and file2.txt as following: 
```txt
a
b
c
```
to merge lines in file1.txt in file2.txt: 
```sh
paste file1.txt file2.txt
```

output: 
```txt
1  a
2  b
3  c
```

ex.2 to set custom delimiter in previous example, we want to have the `,`: 
```sh
paste -d, file1.txt file2.txt
```
---

- `tr`: is a versalite tool for translate, delete, squeeze characters. Note this command reads from stdin.  

ex.1: to conver all lowercase charachters in file.txt to uppercase:
```sh
tr 'a-z' 'A-Z' < file.txt
```

ex.2: to replace all ':' characters in file.txt with '.'
```sh
tr ':' '.' < file.txt
```

ex.3:. to replace all occurrences of 'j', 'w' and '4' in file.txt with 'J', 'W' and '8'
```sh
tr 'jw4' 'JW8' < file.txt
```

ex.4: to delete all digits form file.txt
```sh
tr -d '[:digit:]' < file.txt
```
ex.5: to normalize spacing in file.txt, assume that file.txt contains: 
```txt
This           is               a        text       file
```
```sh
tr -s ' ' < file.txt
```
output: 
```sh
This is a text file
```


NOTE: there are several useful options, do tr --help to get more information. 

---

- `sed`: is stream editor command for text processing and is extensively used. general form: 
```sh 
sed OPTION <pattern/script/regex> <inputfile>
``` 
ex.1: find and replace string in a file: 
```sh
sed -i 's/10.0.1.2/10.0.1.10/g' file.txt

# i: indicates in-place process
# s: substitutes
# g: global, i.e. replace all occurrences in each line
# replaces all occurrences of 10.0.1.2 with 10.0.1.10
# 's/old/new/g'
# r: use advanced regex
# n: suppress output, you can use p at the end of your regex, to print the output. 
```
Note: if the `g` charachter is not specified then it will replace the first occurence. 

ex.2: delete lines matching a pattern. Note, it deletes the entire line containing the specified string pattern.  
```sh
sed -i '/password/d' file.txt
```

ex.3: to delete only the specified string pattern and not the entire line: 
```sh
sed -i 's/string//g' file.txt
```

ex.4: to print out specified lines: 
```sh
sed -n '5,10p' file.txt

# subpresses automatic printing of lines, without -n it prints all lines. 

# 'i,jp' indicates number of lines to be printed i.e. [i,j]
```

ex.5: insert a line of text before or after a pattren: 
5.1. to insert before a pattern: 
```sh
sed '/pattern/i\the_text_to_be_inserted' file.txt
# i indicated before the pattern
```

5.2. to insert after a pattern: just like 5.1 but instead of `i` we use `a`. 

ex.6: to replace only on specified line numbers: 
```sh
sed '<lineNumber>s/foo/bar/' file.txt
```

ex.7: delete blank lines: 
```sh
sed  '/^$/d' file.txt
# `^$`: is a regular expression (regex) to match empty lines.  
```

ex.8: multiple editing commands: 
```sh
sed -e 's/foo/bar/g' -e 's/hello/hi/g' file.txt
```

ex.9: back up file before in-place editing: 
```sh
sed -i.bak 's/foo/bar/g' file.txt

# creates a backup with the '.bak' suffix before making changes 
# NOTE: the suffix is not restricted to .bak. 
```

ex.10: using variables (useful in bash scripting): 
```sh
search="foo"
replace="bar"
sed -i "s/$search/$replace/g" file.txt
```

ex.11: using # as delimiters: 
instead of `/` as delimeter inthe script we can use # by doing: 
```sh
sed 's#path/to/old#path/to/new#g' file.txt

# now the old string is 'path/to/old' and new one is 'path/to/new'
# and the delimiters is #
```

---


**Metainfo:**
- `wc`: to print out meta information about a file, things like number of lines, words and charachter (bytes) in a file. 

- hash: there are several commands to make a hash of a file each uses a apecific hash algorithm: 

1. `sha256sum`: uses the sha256 to generate hash. 
2. `sha512sum`: uses the sha512 to generate the hash, longer hash.
3. `md5sum`: outdated, vulnerable, uses md5 algorithm. 


### File Management 103.3 

**Wildcards:** 
wildcards in Linux and Bash are special characters that allows to select multiple files, directories, or strings based on pattern matching rather than specifying exact names. 

|wildcards | name           | description                                             |
-----------|----------------|----------------------------------------------------------
|    *     | Asterisk| Matches any number of characters including none         |
|    ?     | Question Mark| Matches exactly one character                           |
|    []    | Square Brackets| Matches any one of the characters inside the brackets   | 
|    [!abc]| Exclammation Mark| Matches cny characters except a,b or c|
|    [^abc]| Excalamation Mark | Same as [!abc] |
|    {} | Brace Expansion| To generate arbitrary strings by expanding the content inside|
|    ** | Double Asterisks|Matches files/directories recursively |
|    ~| Tilde| Represents the home directory|
|    |||


examples: 
- `ls *.txt`: All files that have the `.txt` extension. 
- `ls file?.txt`: Matches files like file1.txt, fileB.txt, but not fileAB.txt
- `ls | grep [abc]`: Matches files that have the a, b or c in theirs name. 
- `ls | grep  [a-zA-Z0-9]`: Mathch all a to z or A to Z or digits. 
- `echo file{1..9}.txt`: Prints file1.txt ... file9.txt
- `ls **/*.txt`: Lists all `.txt` files in the current directory and all subdirectories. 
- `cd ~`: Change the directory to the user's home directory. 

**Moving file:** use `mv src_path des_path` to move a file. 

**Copy a file**: use the `cp src_path des_path/<optional_newName>` to copy the file. 

**Delete a file:** use the `rm path_to_file` to remove a file, note multiple files can be specified as argumets. To remove a directory use `-r`. 
Note: `rmdir` can also be used to remove an empty directory. 

**Create a file:** use `touch file_name` to careate a file. When creating a file using this command, the new file will have a creation time, if the file already exists the command will update the creation time for that file. We can also give the creation time manually when creating files or even change the creation time for a file: 

- to give creation time when creating a file: `touch -t yyyymmddhhmm.ss file_name`

- to change a file's creation time: `touch "whatever string" file_name`



**Create a directory:** use `mkdir dir_name` to create a directory. 
To create directory and also subdirectories inside it use: `mkdir -p dir1/dir2/dir3`

**To print a file's type:** use `file file_name`. To print more information about a file like charset use the `-i` switch. 

**To copy a file based on size and how much of that file we want:**
For example for copying 1MB of src_file in des_file, Use the following command and switches: 
```sh
dd if=src_file of=des_file count=1024 bs=1024
# if: input file
# of: output file
# count: how many block
# bs: block size, in KB, each block is 1 KB
# so in this particular case we copy 1024 * 1024 KB = 1MB
```

**To find files:** use `find` command. 
- To find based on type:
```sh
find <path_to_search_in> -type d
# -type: indicates the search should be based on the specified type
# d: the type of file should be directory 
``` 

- To find file based on file's name: 
```sh
find <path_to_search_in> -iname <fileName>
```
Note: we can use wildchards as well. 


- To search for files based on size: 
```sh
find <path_to_search_in> -size <size>
# size can be given in following formats: 
# 100c: files with exactly 100 characters
# 100b: files with exactly 100 bytes. 
# +100k: files with size more than 100 kilobytes.
# -20M: fiels with size less than 20 MB
# +2G: files with size more than 2G 
```
- To find files based on access minutes: 
```sh
find <path_to_search_in> -amin time_in_minute_e.g._40
```

- To find files based on status change in minuts: similar to `-amin` but use the `-cmin` switch instead. 

Note: we can use the `+` to say more than and `-` for less than for example: `mmin -60` says those files that have modified time in last hour, less than 60 minutes. 

There are more useful switches that can be used, for more info do `man find` or `find --help`.



Note: multiple siwtches can be used for different commands at once to do some more advanced operation. 


**Compression**
- `gzip` and `gunzip`: to compress and decompress a file to/from `.gz` format. 

- `bzip2` and `bunzip2`: to compress and decompress a file to/from  `.bz2` format.

- `xz` and `unxz`: to compress and decompress a file to/from `.xz` format. 

**Archiving files:** Archiving is useful when we need to make an archive-file containing many other files. The size will not be smaller, rather may be more because of headers. 

- To create a archive-file containing file1, file2 and file3:
```sh
tar cf <nameOfArchiveFile>.tar file1 file2 file3 
# cf: create file
```
This command will make an archive file with the `.tar` extension. 

- To extract an archive-file: 
```sh
tar xf <nameOfTheArchiveFile>
# xf: extract file. 
``` 

**Archiving and compression:** 
- To first make an archive file and then compressing it: 
```sh
tar cfz <nameOfTheFile>.tar.gz file1 file2 file3
```
- To unarchive and unzip: 
```sh
tar xf <fileName>
```

Note: some .deb files also can be unarchived by tar. 


**Using cpio to archive files into a cpio file**
- `cpio -o file > file_name.cpio` to archive and `cpio -id < file_name.cpio` to extract a cpio formated file. 

### Streams, pipes and redirects 103.4

**Strams in Linux:** In Linux world there are three different streams: 
1. stdin: The standard input stream, provides input to a command. 
2. stdout: The standard output stream, output of a command. 
3. stderr: The standard erro stream, error output of a command. 

**Redirections:** using to redirect streams
|Operator|Usage|
---------|-----|
| >      | Redirects `stdout` to a file, creates a file if the file ndoes not exist, overwrites if exists|
|>>| Redirects `stdout` to a file, appends if the ffile exists|
|2>| Similar to > But redirects the `stderr`|
|2>>|Similar to >> But redirects the `stderr`|
|&>| Similar to > But redirects both the `stdout` and the `stderr`|
|&>>|Similar to >> But redirects both the `stdout` and `stderr`|
| < |Redirects the `stdin` from a file |
| < >|Redirects from the `stdin` and sent the `stdout` to it|

**Pipes:** Is used for inter-process communication (IPC). There a re two major types of pipe, Ordinary Pipes and Named Pipes.
The ordinary pipe can make communication for processes within the same process family, and Named Pipes are more powerful and can make communication between different processes belonging to different proccess family (process group). 

To use ordinary pipes in terminal we use the `|` operator between commands that we want to pass output into. for example: 
```sh
ls | wc
# The output of first command will be piped into second command as argument. 
# in this particular case we counts number of files in the current directory
```


**xargs:** This command can run a command. The input or args for the command that be executed by xargs can be passed by pipe:
ex: 
```sh
echo hej | xargs echo 
```


**tee:** Gets args via pipe and similar to redirection, but prints out the redirected stdout put that is now inside the file: 
```sh
ls | tee file
```

### Process Management in Linux 103.5

**Processes:** A process in operating systems represents an active instance of a program, in other word, a process is an instance of a program in execution. The lifecycle of process involves several stages: 
- Creation: When a process being created by system call such as `fork()`.
- Execution: During execution, when a process is actually running and perform tasks. 
- Waiting: A process may be suspended, entering a waiting state for example when waiting for input from user or a resource that is not immediately available. 
- Termination: When a process is terminated i.e. exited. 

In Linux, processes can be executed in either the foreground or the background. Each offering distinct operational characteristics. 

**Execution in the Foreground:** When a command is executed in the terminal without any special modifiers, it runs in the foreground by default. In this mode the terminal is deticated to the process until its completion, during which no other commands can be executed in that terminal session. for example: `ls -R`. 

A foreground process can be interupted by pressing `Ctrl + c`, which sends a `SIGINT` (interrupt signal) to the process. typically resulting the process's termination. 

We can also suspend a foreground process by pressing `Ctrl + z`, which sends a `SIGTSTP` (stop signal). 


**Background Processes:** To execute a command in background, the command should be followed by an ampersand (`&`). This allow the process to run independently of the terminal. 
ex: 
```sh
sleep 10 &
```

Note: both foreground and background jobs when the parent process exists the child processes will also terminate. In most of the time in Linux world zombies will be avoided. 

**Commands related to background and foreground processes:**
- `job`: provides a list of all active background jobs, and displaying their job IDs and statuses. We can use the `-l` also to print out processes' id.  
 
- `fg`: We can use this command to run a background job in the foreground. Running `fg` will target the last job running in background.  
ex.1:
```sh
$ sleep 60 & # sleep for 60s in background 
# it will print out the procees id (PID) that will run in the bg

$ jobs 
[1]  + running    sleep 60

$ fg 
[1]  + running    sleep 60 # now the sleep process runs in fg 
```

Note if we have several jobs in bg and we want to run a specified bg process in fg we can use: `fg %n` where the n is the number in []. 

ex.2: 
```sh
$ sleep 60 & sleep 60 & sleep 60 &
[1] 65339
[2] 65340
[3] 65341

$ jobs
[1]    running    sleep 60
[2]  - running    sleep 60 # <----- 
[3]  + running    sleep 60

$ fg %2

```
 
- `bg`: Moving foreground jobs in the background. Running `bg` will target the last job, using `%n`, as fg we can specify the target. 

ex.1: 
```sh
$ sleep 60                        
^Z
zsh: suspended  sleep 60

$ jobs
[1]  + suspended  sleep 60

$ bg 
[1]  + continued  sleep 60
```
- `disown`: to disown a background process. 

**nohup (No Hang Up):** In Linux, this command is used for running processes that need to continue operating even after the terminal session that started them is closed. It is designed to run a command in such a way that it ignores the `SIGHUP` (hang up) signal. 

The `SIGHUP` is automatically sent to a process when the terminal or session that initiated the process is closed or disconnected. 

The argument to the `nohup` command is another command that will run independent form the terminal session: 
ex: 
```sh
nohup xeyes 
```
We can even run a command in background in commbination with nohup: 
```sh
nohup sleep 60 &
```
This is useeful forexample when conencting to a system using ssh and we want to the job alive even after disconnecting the ssh session. 

By default, `nohup` redirects the stdout and the stderr from the command to a file named `nohup.out` in the directory from which the command was executed. 

**Killing processes:**
We can terminate processes y using the `kill` command. The geral form of this command is as following: 

```sh
kill -<signal> <PID>
```
There are several signals available for different purposes, But Three frequently used are listed as follows: 
|signal number | signal name | description|
---------------|-------------|------------|
|1 | HUP| Informating the process that its controlling terminal is terminated|
|15| TERM| Normal termination request|
|9| KILL| Forcefully kills the process|

There are other commands for same goal: 
- `killall [OPTION] <ProcessName>`: Used to terminate processes by their names. It sends a specified signal to **all processes with a given name**. Example:
```sh
$ killall firefox # will send the SIGTERM i.e. 15 to all firefox processes. 

# Common Options:  
# -s SIGNAL, specify the signal e.g. -s SIGKILL
# -i, prompt for confirmation before killing each process. 
# -v, verbose output, shows which processes are being killed. 
# -r, match processes using regular expressions. 
``` 

- `pkill`: Used to terminate **all processes** with a specific pattern. 
```sh
$ pkill [OPTIONS] <pattern>

# ex: 
# pkill firefo # kills all processes with prefix including firefo
```

**Monitoring Processes:** 
- `ps`: Shows running processes. Each process has a PID and a parent proccess ID (PPID).

```sh 
# To print processes running belonging to the session 
$ ps 

# To print all process runningin the system:
$ ps -aux
```  
- `pgrep`:Is used to search for processe based on specific citeria/pattern and display thier process IDs (PIDs). 
```sh
pgrep [OPTIONS] <pattern>

# ex: 
pgrep firef

# Common Options:
# -u <user>: match processes owned by a specific user. 
# -t <terminal>: match processes attached to a specific terminal.
# -f: match against the full command line (not just the process name). 

# -o: show only the oldest process (with the smallest PID)
# -n: show only the newest process (with the largest PID)
# -d <delimiter>: specify a delimiter to separate muultiple PIDs in the output

# -l: list process names along with PIDs. 
``` 

- `top`: Is a powerful tool for monitoring system performance and processes in real time on Linux and Unix-like systems. It provides a dynamic, real-time view of system resource usage, including CPU, memory, and process information.

```sh
# Useful Shortcuts and commands in `top`
# q: quit and exits the top program. 
# h: display help information. 
# k: kill a process, promts to get a PID to terminate. 
# r: renice a process, promts to get a PID and new priority 
# p: sort by CPU usage
# M: sort by memory usage
# T: sort b ytime
# 1: toggle CPU core display
# d: change the display update interval, prompts to get anew update interval in sec.  
# n: change the number of processes to display, prompts to get the number of processes to be displayed. 
# c: toggle between showing process command lines and just the process names. 
# s: toggle summary area display, show or hide the summary area. 
# u: filter processes by user, prompts to get username. 
# f: add or remove columns, allow to customize which columns are displayed in the process list. 
# x: toggle highlighting of the current sorting column. 
# z: toggle color/mono mode. 

```
- Load average in top: the top command provide load average which privides an indication of how busy the system is by showing the average number of processes that are either in a runnable or unintrruptable state. It consists of three columns which represents the average load over past 1, 5, 15 minutes. For example: 
```sh
1.23, 1.45, 1.67
# 1.23: average load over the last 1 min
# 1.45: average load over the last 5 min
# 1.67: average load over the last 15 min

# for example if the system has 4 core, and the average load is 4.00 it means the system is fully busy and each core has exactly one process to handle.  
``` 
- `free`: prints information about memory and swap. 
- `uptime`: displays information about how long time the system is up, number of users whithin the system, etc. 

- `watch <command>`: used to watching output of a command in realtime. 


#### Terminal Multiplexers: screen & tmux
Both `screen` and `tmux` are terminal multiplexer programs that allows to manage multiple terminal session from a single window or interface. `screen` is older one and is often preinstalled on Linux dirtos, the `tmux` is the new one. 

**Shortcuts for screen:** The following keys can be used after pressing `Ctrl + a` which enters the screen to command mode. 
|Key|Usage|
|---|-----|
|\   | Kill all processes windows and terminate the screen|
|\|| Split current window in two vertical focuses|
|Shift + S| split current windows in two horizontal focuses|
|C| Creates a window in current focus|
|Tab| Go to the next focus|
|D| Detach from window|
|K| Kill current window|
|N| Move to next window|
|P| Move to previous window|

After detaching or just in another terminal we can list out the screen sessions running by usiing `screen ls`. To re-attach to a screen use the `screen -r <screen-id>`. 


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

To move from/to different windows first enter to the command mode and then  just use the number buttons like 0 to go to the window 0, and so on. More about tmux in [tools](./tools.md).



### Modify process execution priorities 103.6
This section is about geting knowledge about jobs priority when creating them, change priority of jobs and other related stuff. 

**nice value in Linux:** The nice value in Linux determines the priority of the process. **The higher the value, the lower the priority, we say the process is nicer than pther process**. The default nice value is 0 on Linux. In top screen and ps output, the nice value of each process is sorted under the `NI` field. In Linux world the nice value can be between the `-20` to `19`. 

To run a command with some specific nice value:
```sh
nice -n <value> <command>
```
Note: to use nice value less than 0, we need the sudo permission. 

We can also change the nice value of a process after running it. For this purpose we can use the `renice` command as described below: 
```sh
renice -n <nice value> <PID>
```
We can also change a process's nice value in top, by pressing the `r` and then it promts to get the process's PID tobe be reniced, and promts again to get the nice value. 

### Searching using regular expressions (Regex) 103.7

Regex is a powerful toll used for pattern matching in text. It allows to define search patterns, which can then be used to find, replace or validate text. It is used in system adminstration, programming, data processing and more. 

**Literal characters:** Literal characters match exactly what they represents, for example, the `cat` will match the string `"cat"` in the text file. 

**Metacharacters:** Metacharacters are symbols with special meaning in regex. Some common metacharacters include: 
|Name|Char| Description|
|----|----|------------|
|Dot|`.`| Matches any single character except a newline|
|Caret|`^`| Matches the start of a string|
|Dollor Sign|`$`| Matches the end of a string|
|Star|`*`| Matches 0 or more repetition of the preceding element|
|Plus|`+`| Matches 1 or more repetitions of the preceding element|
|Question Mark|`?`| Matches 0 or 1 occurence of the preceding element|
|Square Brackets|`[]`| Defines a character class, matching any single character within the brackets|
|Backslach|`\`| Escapes a metacharacter, making it a literal character|

Examples of using metacharacters: 
Given the string `the cat sat on the mat`.

1. Using the regex: `c.t` will match the `cat`. It says words that begins with `c`and ends with `t` and `.` indicates: any char. 

2. Using the `^the`, will match `the`. 

3. Using the `mat.$` will match the `mat.` in the string. 
4. Using the `ca*t` regex, will match words like: `cat`, `caat`, `ct`. 

5. Using the `ca+t` regex, will match words like: `cat`, `caat`, but NOT `ct`. 

6. Using the `colou?r` will match words like: `color`, `colour`, `coloer`. 

7. Using the `[cm]at` will match words like: `cat`, `mat`, `sat`. 

**Character Classes:** allows to define a set of characters to define a pattern: 
- `[abc]`: Matches any one of `a`, `b`, or `c`. 
- `[a-z]`: Matches any lowercase letter. 
- `[^abc]`: Matches any characters except `a`, `b` or `c`. 
- `\d`: Matches any digit, it is equivalent to `[0-9]`
- `\w`: Matches any character (alphanumetric + underscore). 
- `\s`: Matches any whitespace character. 

**Anchors:** Are used to assert position within the text. 
- `^`: Asserts the start of a string.
ex: Given the `Hello World` and using the `^Hello` will match `Hello` fron the string. 

- `$`: Asserts the end of a string. 
ex: Given the `Hello World` and using the `World$` will match `World` in the string. 

- `\b`: Asserts a word boundary. 
ex: Given same string, and using the `\bWorld\b` will match `World` in the string. 
 

- `\B`: Asserts a non-word boundary. 
ex: Having the same string and using the `\Bllo` will match `llo` from the string. 

- `{n}`: Matches exactly `n` occurences. 
ex: Having the `12345` and using the `\d{3}` will match `123`.

- `{n,}`: Matches `n` or more occurence. 

- `{n,m}`: Matches between `n` and `m` occurences. 

**Group:** Groups are used to group multiple characters together and create subpatterns. 
- `(abc)`: Matches the exact string `"abc"`. 
- `(a|b)`: Matches either `a` or `b`. 
- `(ab)+`: Matches abcdef... 
**Capturing groups:** Store the matched text for later use, often with `\1`, `\2`, etc. Refering to the first, second, etc.

### Basic file editing (Vim) 103.8
Vim is a powerful text editor that is an enhanced version of the classic Unix `vi` editor. It is designed to handle both simple and complex text editing tasks focuses on effeciency. 

**Modes in Vim:** There are four crucial modes for effective use: 
1. Normal Mode: Default mode for navigation and text manipulation. To get into normal mode from any other mode press the `Ecc`. Note, to change the current mode to any other mode, you have to change to normal mode first.  
2. Insert Mode: Used for inserting and editing text. To enter into isert mode press the `i` button. 
3. Visual Mdoe: Used for selecting text. To enter in this mode press the `v` button. 
4. Command-Line Mode: For executing commands and saving files. To enter into this mode press the `:` button and then enter the command. 

**Basic Navigation:** Keep in mind, you have to be in normal mode first. 

|Keys| Descrition|
|----|-----------|
|`h`| left|
|`j`| down|
|`k`| up|
|`l`| right|
|`gg`| go to the first line in the file|
|`G`| go to the last line in the file|
|`:`| enter command line mode|
|`0`| move to the beginning of the line|
|`$`| move to the end of the line|

**Search:** IN NORMAL MODE
|Keys| Description|
|----|------------|
|`/pattern`| Search forward for `pattern`|
|`?pattern`| Search backward for `pattern`|
|`n`| Move to the next match|
|`N`| Move to the previous match|

**Text editing and manipulation:**
Insert mode: 
|Keys| Description|
|----|------------|
|`i`| insert before the cursor|
|`I`| insert at the beginning of the line|
|`a`| append after the cursor|
|`A`| append at the end of the line|
|`o`| open a new line below|
|`O`| open a new line above|

Normal mode:. 
|Keys| Description|
|----|------------|
|`x`| delete the character under the cursor|
|`dd`| delete the current line|
|`d<movement>`|delete text based on movement e.g. `d2j` deletes two lines down|
|`yy`| Copy (yank) the current line|
|`P`| paste after the cursor|
|`p`| paste before the cursor|

Text manipulation: 
|Keys| description|
|----|------------|
|`u`| undo the last change|
|`Ctrl+r`| redo the last undone change|
|`.`| repeat the last command|

**Buffer and Window Management:** 
|Keys| Description|
|----|------------|
|`:ls or buffers`| list open buffers|
|`:b <number>`| switch to buffer <number>|
|`:e <filename>`| open a new file|
|`:w`| save the current file|
|`:q`| quit Vim|
|`:wq` or `:x`| save and quit|
|`:qa!`| quit all buffers without saving|
|`:sp <filename>`| split window horizontally|
|`:vsp <filename>`| split window vertically|
|`Ctrl+w <direction>`| navigate between split e.g. `Ctrl+w j` to move down|

**Configuration and Customization:**
|Keys| description|
|----|------------|
|`:set number`| Show line numbers| 
|`:set relativenumber`| Show relative line numbers|
|`:set expandtab`| Use spaces instead of tabs|
|`:set tabstop=4`| Number of spaces for a tab|
|`:set shiftwidth=4`| Number of spaces for indent|
|`:set autoindent`| Automatically indent new lines|
|`:set smartindent`| Enable smart indentation|

**File Management:** 
- `:w <filename>`: write (save) the file with a new name. 
- `:e <filename>`: open a file for editing. 

**Searching and Replacing:** 
- `:%s/old/new/g`: replace all occurence of old with new in the entire file. 
- `:%s/old/new/gc`: replace all occurences with confirmation. 



### Creating Partition (104.1)

**Block Devices:**  Block devices does not lose the data after power off and they are nonvolatile mass storage device and the data inside them can be accessed in any order. Clock devices are like USB and CD-ROMs, we can format these devices to fixed size blocks. To list all blocks devices on the system use the `lsblk` command. We can also use the `ls` command in combination with `grep` for this purpose:  
```sh
ls -l /dev/ | grep '^b' # grep those starting with 'b'
```

**Partitioning:** Based on wich firmware the system use partitioning can be done in different ways:

1. BIOS: partitioning on machines that use BIOS can be done by `fdisk`. However in this machines typically have only 4 partitions, but there are tricks that can be used for more advanced partitioning, for ecample we can define one partition as primary and the rest as the extended partition. Then we can divide the extended partition to several parts. So fdisk for MBR and BIOS.  

2. UEFI: On machine that use this firmware,  we can use the `gdisk` for partitioning. In this architecture the bootloader looks into the GPT. So `gdisk` for GPT and UEFI. The partitioning using GPT is more flexible and can be more advanced. 


**Using fdisk**: 
- To list all partisions: 
```sh
fdisk -l /dev/sda
```

- To get in fdisk and then choose commands: 
```sh
fdisk /dev/sda
# press m to show the commands menu
```

- To create a new partition: Do the previous step and then press the `n` button to give `new partition` command to fdisk. It will ask about the type of partition, the primary is the default one (for creating primary type press 1, for extended press 2). After entering the  type of partition ,it will ask about start sector, we can choose the lowest sector, by pressing enter it will choose the begining. After that, it will ask about the last sector, We can use the size format i.e. `+<size><K/M/G/T/P>`, e.g. +1G. Note, if you choosed extended type, make sure that you have a primary partition, and when it ask about first and last sector just press the enter then the partitioning will be as following:
```sh
|primary|     extended         |
```
 

- To create partitions inside a extended partition: press n to create new partition and then press enter to give lowest sector as first sector and later the size of partition. Note, when you pressing `n`, it would not ask you about type, and shows partiion<n> where n is greater than 4, beacuse using fdisk for BIOS there will be 4 partition. 

- Delete a partition: in fsisk, press `d`, it will ask about number of partition, the default is the last partition. 

- Listing partition types: press `l` in  the fdisk program. it print partition types' number e.g. swap as 82. 
- Changing partition type: press `t` in the fdisk program, enter the partition number. Enter desired type foe example to change to swap type 82. 

- Verifying partition table and writing changes: after changes, verify partition table by pressing `v` in  fdisk program. Then press `w` to wite changes. 

Note: After partitioning we need to format them to make them usable.

**Creating Filesystem and Formatting a Partition:** After partitioning a disk, formatting the partition is necessary. Formatting a filesystem creates a map that stores the location and names of files and directories. This process makes the partition usable and allows for file operations such as moving files between directories, deleting them, and more. In other words, formatting is akin to creating an address or index system. For example, if we format a partition using the FAT (File Allocation Table) format, it will create a table that stores file addresses used by the file system. Whenever the operating system needs to perform an operation on a file within that partition, it will reference this table to locate the file.

some common formats are listed below:

| **File System Format** | **Description** |
|------------------------|-----------------|
| **FAT32 (File Allocation Table 32)** | An older file system that is widely compatible across various operating systems. It supports files up to 4GB in size and partitions up to 8TB. Commonly used in USB drives and memory cards. |
| **NTFS (New Technology File System)** | A file system used by Windows operating systems. It supports large files and partitions, file permissions, encryption, and compression. Suitable for internal drives in Windows systems. |
| **exFAT (Extended File Allocation Table)** | A file system optimized for flash drives. It supports large files and is compatible with both Windows and macOS, making it ideal for external storage devices. |
| **ext4 (Fourth Extended Filesystem)** | A widely used file system for Linux. It supports large volumes and files, journaling, and is known for its stability and performance. Commonly used as the default file system in many Linux distributions. |
| **HFS+ (Hierarchical File System Plus)** | A file system used by older versions of macOS. It supports large files, journaling, and file compression. Replaced by APFS in newer macOS versions but still in use on some older systems. |
| **APFS (Apple File System)** | The modern file system used by macOS, iOS, and other Apple devices. It is optimized for solid-state drives (SSDs) and supports features like encryption, space sharing, and snapshotting. |
| **Btrfs (B-tree File System)** | A modern Linux file system known for its advanced features like snapshotting, checksums, and dynamic disk management. It is designed for fault tolerance, repair, and easy administration. |
| **XFS** | A high-performance file system designed for large-scale storage. It supports large files and directories, journaling, and is often used in enterprise environments. Commonly found in Linux servers. |
| **ZFS (Zettabyte File System)** | A combined file system and logical volume manager designed by Sun Microsystems. It offers high storage capacities, data integrity verification, snapshotting, and more. Commonly used in enterprise and NAS environments. |

**Creating Filesystem:** after creating a partition we need to make a filesystem in the partition and also format the partition, for this purpose we can use the `mkfs` command (and mkswap for swap). 
General form: 
```sh
mkfs -t <format_type> /dev/sda<partition_number>
```

Or we can use binaries in /sbin/mk* to format to a specific foramt: 
```sh
mkfs -t ext3 /dev/sda5

# is the same as 
mkfs.ext3 /dev/sd5
```

### Filesystem Integrity (104.2) 
Filesystem integrity and management are crucial for maintaining the stability and reliability of a Linux system. Filesystem integrity ensures that the data on your disk remains consistent and is not corrupted. Several mechanisms and tools are used to check and maintain filesystem integrity in Linux. 

**i-Node:** In Linux and other Unix-like operating systems, i-nodes (index nodes) are crucial components of the filesystem. They play a significant role in managing and maintaining filesystem integrity. An i-node is a data structure on a filesystem that stores metadata about a file or directory, but not the actual data or file name. Each file and directory on a filesystem is associated with an i-node.

Note: some systems does not have i-node. 

Components of an i-Node: 
1. File type: regular file, directory, symbolic link, etc. 
2. Permissions.
3. Size.
4. Timestamp.
5. Link count: The number of hard link to the file. 
6. Pointer to Data Blocks: Address of the data blocks where the actual file content is stored. 

**Common disk usage commands:**
- `du`: disk usage, will show the disk usage of some particular directory. ex: to get information about disk usage in the current directory: 
```sh
du
```
 
- `df`: disk free, will give data about the entire system, how much is free, how much i-node are free, etc. 
ex:                   
```sh
df

# Common options:
# -h: human readable, 1024
# -H: human readable, 1000
# -T: print types
# -i: print information about i-nodes
```

**Checking file systems:** Checking the filesystem is a critical process to ensure the stability, consistency, and integrity of the data stored on a disk. The purpose of checking a filesystem is to identify and correct errors or inconsistencies that could lead to data corruption, system crashes, or loss of data. 

Assume that we have a corrupted filesystem. The gerenal way to fix this issue we can use the `fsck` command. This command (and its sibliing, placed under `/sbin/*fsck*`) will check the file system to find errors. 

Using the file system check `fsck` to check file system:
```sh
fsck /dev/sda<n> # n is the number of partition to be checked
```

### Mounting and Unmounting filesystems (104.3)
Mounting is a crucial concept in operating systems, especially in Linux, where it plays a key role in managing file systems. When you insert a USB memory stick or any external storage device into a Linux machine, the device is typically mounted to a directory within the filesystem, usually under `/media` (or `/mnt` in older versions of Linux).

Mounting is the process of making a filesystem accessible at a certain point in the directory tree. This allows the operating system and users to interact with the files on the device as if they were part of the main filesystem. 

Mounting a filesystem to a specific partition or directory plays a crucial role in protecting the overall system from potential issues like overwriting or running out of space.

**Filesystem Mounting and Partition Isolation:**
When we mount a filesystem to a specific partition, we're effectively isolating that partition's storage space. This isolation ensures that any data written to that filesystem remains within the confines of the designated partition. 

- **partition Isolation:** Each mounted filesystem is contained within its partition. If you mount a filesystem dedicated to log files on a particular partition (e.g., /var/log), all log data will be written to that partition alone. If the log files grow too large and fill up the partition, the rest of the system remains unaffected. Other partitions, such as /home or /, will not be overwritten or affected by the log files because they reside on separate partitions.

To see all mounted file systems issue the `mount` command. To mount a file system to a partition or directory: 
```sh
mount <fs> <dir>

# Usefull options: 
# -t: to indicate format of file system e.g. $ mount -t ext4 /dev/vda1 /tmp/test, it the type is ommited then it will guess the type. 

# -o <rw>/<user>: to specify access level and/or user priviliges  
# -o loop: to mount an ISO file
```


ex: 
```sh
mount /dev/vda1 /media/newdisk
```
Now all files in `vda1` can be accessed by newdisk directory (hierarchy). 

Assume that `/var/log`got full, we can add new disk and mount the new disk to the `/var/log` to extend the space. Even detach the disk and give logs to others. 

**Unmounting:** To unmount a file system issue the comamnd below: 
```sh
umount <fs> # ex: $ umount /media/newdisk
``` 

after umounting only files that was in the directory before mounting be found. 



**Mounting swap partiions** for swap partitions we dont use mount or umount, instead we use `swapon` or `swapoff`.


**Mounting using UUIDs and /etc/fstab:** 
Using `mount` for mounting will not guarantee that the name of disk will be same in other machines, for example `/dev/vda1` might become `/dev/vdh1`, i.e. the name will not be unique. 

Mounting using UUID (Universally Unique Identifiers) is a relaible way to ensure that the correct filesystem is mounted, regardless of how the device names might change across different machines or even reboots. This method will guarantee that the mounting process is consistent, stable, and independent of the device naming convention that OS uses. 

- UUID: A UUID is a 128-bit number generated to be unique across time and space. Each filesystem has a UUID, which remains constant as long as the filesystem is not reformatted. This UUID is stored in the filesystem's superblock and can be used to reference the filesystem reliably 

Note: super blocks are like several backups, metadata, and there are several backup so that if one of them goes lost the OS can use the other one. Each partition that contains afile system has its own superblock, typically several. To list all super block for a partition first get the device name using `lsbk` and then use the `sudo dumpe2fs /dev/<name> | grep -i superblock`. 


To find UUIDS for filesystems: 
- `lsblk -o +UUID`
- `lsblk -f`
- `blkid /dev/<name>`

To mount with UUID: 
```sh
mount UUID=<id_that_found_bycommands_above> <path:e.g. /media/test>
```
   
**Using labels for mounting:** Using labels instead of UUIDs to identify and mount filesystems can be a convenient alternative, especially if we want to assign more human-readable identifiers to our filesystems. Labels are easier to remember and can be particularly useful in situations where we frequently move disks between systems or want to simplify the mounting process. 

To set a label for a device:
```sh
mkfs.<format> -L <lable> <device>
```

```sh
mount -L <lable_name> <directory>
```

**Using fstab file to mount during the bootup:** 
The `/etc/fstab` file is used by linux to mount and unmount filesystems during the bootup, we can add other filesystems here to do mount operations when system boots up. In the `fstab` file: 
```sh
<filesystem_to_be_mounted>  <where>  <format>   <OPTIONS>
#ex:
# /dev/vda1 /media/test etx4    rw 0 0

# 0: dont need backup 
# 0: dont need fscheck when booting up the system
```

**Systemd mount unit:** we can also use systemd mount unit files to do the mount operations. Mount unit files are stored in `/etc/systemd/system/` or `/lib/systemd/system/`. The filename of a mount unit file corresponds corresponds to the mount file but woth a dot `.mount`at the end, e.g. to create a mount unit for `/mnt/data` create a file named: `/etc/systemd/system/mnt-data.mount`. Then the file should be like the example below: 
```sh
[Unit]
Description=Mount data filesystem

[Mount]
What=/dev/sda1
Where=/mnt/data
Type=ext4
Options=defaults

[Install]
WantedBy=multi-user.target
```

### File Permissions and ownership (104.5)

**Users and Groups in Linux:** In Linux, users and groups are fundamental concepts for managing file permissions and system access. 

- Users: Each user on Linux system has a unique user account identified by a username. Each user also assigned a unique numeric ID called the user ID (UID). For example root user has a UID of 0. We can use the `whoami` to get username of the current user. User information is stored in the `/etc/passwd` file, the format of each line in this file is like the example below: 
```sh
username:password:UID:GID:GECOS:home_directory:shell
# username: the user's login name. 
# password: placeholder for the encrypted password (usually x or * as passwords are stored in /etc/shadow).

# UID: the user's numeric ID
# GID: the user's primary group ID
# GECOS: a field that may contain the user's full name or other information 

# home_directory: path to the user's home directory
# shell: the user's default shell 
```

- Group: Groups are colections of users. They help manage permissions for multiple users at once. Each group is identified by a unique Group ID (GID). Groups are listed in the `/etc/group` file. The format of ecah line in this file is as the example below: 
```sh
groupname:password:GID:user_list

# groupname: the name of the group
# password: placeholder for the group password (rarly used, often x)

# GID: the numeric ID for the group. 
# user_list: a comma-separated list of usernames that are member of this group. 
```

The `id` command will print information about the current user, usUID, groups and GID and other groups the user is memeber in. 


**Permissions:** Each file have a access right, or different access right for users, groups and others. 
- r: read permission
- w: write permission
- x: execute permission

The file permission is sorted in the format of `User,Group,Others`. For example by enterin the `ls -ltrh` in  a direcroty, it will print a complete informations about files in the directory. The intresting part for this section is access right/file permissions for each file. The format of a file permission in general is as following:

```sh
<FileType><UserRights><GroupRights><OtherRights>
# for a file: starting with -
# ex: -rw-rw-r--
# file is a file
# user's permission: rw-
# group's permission: rw-
# other's permission: r--

# for a directory: satrting with d
# ex: drwxrwxr-x 
# file is directory
# user permission: rwx
# group permission: rwx
# other permission: r-x 
```

**Numeric (Octal) Format of Permissions:** In Linux, file and directory permissions can be expressed in a numeric (octal) format, which provides a compact way to represent read, write, and execute permissions. Each digit in this format represents a set of permissions for the user (owner), group, and others.

Numeric values: 
|value| octal|permission|
|-----|------|----------|
| 0   | 000  | --- (no permission)|
| 1   | 001  | --x (execute permission)|
| 2   | 010  | -w- (write permission)|
| 3   | 011  | -wx (write and execute permission)|
| 4   | 100  | r-- (read permission)|
| 5   | 101  | r-x (read and execute permission)|
| 6   | 110  | rw- (read and write permission)|
| 7   | 111  | rwx (read, write and execute permission)|

We can also think about numeric values as: 
- 1: execute permission
- 2: write permission
- 4: read permission

so 7 is 4+2+1 which indicates rwx permision. 

**Changing files' permission:** We can change files' permission either using octal format or the `rwx` format. To do so we can use the `chmod` comamnd, general form: 
```sh
chmod <permission> <file_name>
```

- Using numeric format: UserGroupOther: 
```sh
chmod ugo <filename>
#ex: 
# chmod 755 file
# user: 7, rwx
# group: 5, r-x
# other: 5, r-x
```
Note: for a directory we can use the `-R` switch to recursively change the permission in the directory. 

- Using rwx format, general form: 
```sh
chmod <u/g/o><+/-><r/w/x> file

# ex: chmod g+x file # group get execute permission

# ex: chmod g-w file # write permission being revoked from group  
# ex: chmod u=rwx,g=rw,o= file is same as -rwxrw----
```

**Changing the ownership:** To change ownership of a file we can use the `chown` command: 
```sh
chown <user>:<group> <file_name>
```

Changing group ownership of a file: 
```sh
chgrp <group_name> <file_name>
```


**Creating new user:** To create a new user use the `useradd` command: 
```sh
sudo useradd -<OPTIONS> newUser
# common options: 
# -m: create a home directory for the user
# -s /bin/bash: specify the user's default bash
# -d /path/to/home: specify a different home directory
# -G group1,group2: add user to additional groups
```

**Creating new group:** To create a new group use the `groupadd` command: 
```sh
sudo groupadd <group_name>
```

**Adding a user to a group:** To add a user to a group use the `usermod` command: 
```sh
sudo usermod -aG <group_name> <username>
# ex: sudo usermod -aG  sudo docker
```

**changing the default group:** to change the default group of current user: 
```sh
newgrp <group_name>
```

**SUID (Set User ID):** The SUID bit is a special type of file permission that allows users to execute a file with the permissions of the file owner. When the SUID bit is set on an executable file, the program runs with the privileges of the file's owner, not the user who is running it. This mechanism is commonly used to allow ordinary users to execute commands with elevated privileges (e.g., root) that they wouldn't otherwise have. This permission has the special bit `s` and the special value `4` at the beginging e.g. 4--- (u+s).

To find all files on a system with the SUID bit set: 
```sh
find / -perm -4000 -print
``` 

Changing SUID permission: 
- Using 4 number which indicated SUID bit set: 
```sh
sudo chmod 4<otehr_permissions> <file_name>
```

- Using s character: 
```sh
sudo chmod <u/g><+/->s<others> <file_name>
```

**RUID (Real User ID):** It represents the actual user ID of the persion who started the process. the RUID is used primarily for accounting and logging purpose. It does not typically change during the life time of a process. 

**EUID (Effective User ID)** It is used by the OS to determine the access rights and priviliges of a process. It controls the permission checks when a process tries to read fiels, wite files, execute other programs or access other resources. If a program has the SUID bit set, any user who executes this program will have their euid `temporarily` set to the programs owner, which gives them elevated priviliges during the execution of that program.  

**setuid() in C Programs:** this function isa system call used in C programs to change the user ID of the process, it can be used to frop or elevate priviliges. Examples below describe it better: 

ex.1: If a executable file has the `-rwxr-xr-x` access right (suid bit is not set). and the program owner and group are root, the ruid and euid is same (ruid) before and after execution. The suid(0) will fail. 

ex.2: If a program has the `-rws--x--x` access right and the owner and group of the program is root, when a user with ruid=420 runs the program. The euid will be `0` (root) and the ruid will be 420 just after execution. 


**SGID (Set Group ID):** When the SGID bit is set on an executable file, any user who executes that file will run the process with the group privileges of the file's group owner (GID), instead of the user's default group. This is similar to the SUID bit but applies to group permissions.

Use Case: Useful when you want to allow users to run a program with group privileges that they wouldn't normally have. For example, a program that needs to write to a group-owned directory.

When the SGID bit is set on a directory, any files created inside that directory inherit the group ownership of the directory, not the user's current group.
Use Case: This is particularly useful in shared directories where you want to ensure that all files are accessible to a specific group. For example, in a team working on a shared project, setting SGID on the project directory ensures that all files are group-owned by the team’s group. The SGID bit is represented by the number 2 in the first digit of the octal permission (g+s).

**Sticky Bit:** When the Sticky Bit is set on a directory, it restricts file deletion within that directory. Specifically, only the file’s owner, the directory’s owner, or the root user can delete or rename files in the directory, regardless of the file's permissions. 

Use Case: Commonly used in directories like /tmp, where many users have write access, but you want to prevent users from deleting or renaming each other’s files. This enhances security in shared environments.

Historically, the Sticky Bit was also used on executable files to keep them in swap space for faster execution. However, this usage is mostly obsolete in modern systems. The Sticky Bit is represented by the number 1 in the first digit of the octal permission (+t). 

**Umask:** It is a command and a setting in UNIX-like operating systems that determines the default permissions for newly created files and directories. It essentially defines a "mask" that modifies the default permission settings when files and directories are created, providing a way to control the initial access rights for these new files.

Files typically have a default permission of 666 (rw-rw-rw-), which means read and write for everyone but no execute permissions.Directories typically have a default permission of 777 (rwxrwxrwx), which means read, write, and execute for everyone.

Checking the current umask value by simply typing umask in the terminal:
```sh
$ umask
0022
```

To specify the default permission for files: 
```sh
umask 027
```
This sets the umask to 027, meaning new files will have permissions 640 (rw-r-----) and new directories will have permissions 750 (rwxr-x---).


### Hard Links and Soft Links (symlinks) (104.6)
In UNIX-like operating systems, Hard Links and Soft Links (or Symbolic Links) are two different methods for linking files. Both provide ways to reference files, but they do so in distinct ways, with different behaviors and use cases.

**Hard Links:** A hard link is essentially a direct reference to the data of an existing file. It creates another directory entry for the same file, pointing to the same inode (the data structure that stores file metadata and data locations). It is useful when we need multiple directory entries pointing to the same file data, and we want changes to any of these entries to be reflected across all of them.

Characteristics: 
- Same inode: A hard link points to the same inode as the original file. 
- Data integrity: Share same data as the original one. Changes made to one are reflected in the other. 
- Deletion: Deleting a hard link does not remove the data, the data remains on disk as long as the least hard link or original file still exists.
- Cannot cros file system: Hard link smust reside on the same file system as the original file. 
- No linking to directories: By default, hard links cannot be created to directories.

Creating a hard link: 
```sh
ln <existing_file> <hardlink_file>
```  

**Soft Links (Symbolic Links or Symlinks):** A soft link (or symbolic link) is a file that acts as a pointer or shortcut to another file or directory. Unlike a hard link, it does not share the same inode as the target file.

Characteristics:
- Different Inode: A symlink has its own inode, which contains the path to the target file rather than the data itself.
- Flexible: Symlinks can cross file system boundaries and can link to directories, files, or even non-existent paths.
- Can Be Broken: If the target file is deleted, the symlink remains but becomes a "broken link," pointing to a non-existent location.
- Linking Directories: Unlike hard links, symlinks can be created for directories, making them useful for creating shortcuts to frequently accessed directories.
- Transparent Access: When you access a symlink, the operating system transparently redirects you to the target file or directory.

Creating a soft link: 
```sh
ln <path_to_og_file> <soft_link_file>
```

To unlink a hard or soft link use the unlink command: 
```sh
unlink <file_name>
```

### FHS and finding files on the system (104.7)
We can use the `locate` command to find a file in the system: 
```sh
locate <file_name>
```

we can use the `updatedb` command to refresh the data that being used by locate when searching for a file in the system. The locate command being used mostlu since the find command is slower. 


### Shell: 
```sh
<username>@<hostname><currenr work dir>$
```

Unpriviliged user shell has the token `$`, and the root shell has the `#`. 

**Capturing command line activities, history:**
We can use `script` or `.bash_history` to capture command line activities. They both allow to review what was done in a past session. `.bash_history` allows to see the command been ran, but `script` allows to see both commands and their outputs. 

**.bash_history:** Stored as a simple text file located in `~/.bash_history`, this file cntains a sequential list of commands. It records command generally when the session ends or when the are executed, i.e. not in real-time. Limited customization through environment variables like `HISTSIZE`, `HISTFILE` and `HISTCONTROL`, so that the number of commands stored, ignore duplicate commands and more settings can be customized. This file is used mostly by the `history` command or reverse search `CTRL +r`. In this file, commands containing sentensive information (like password) can be stored which might be a security risk if the history file is accessed by unauthorized users. 

ex: Suppose you previously ran a long rsync command to back up a directory to a remote server. 
```sh
rsync -avz /home/user/data user@remote-server:/backup/data/
```
and later we want to reuse the sam command, for finding it we can use the history commands shown as below: 
```sh
history | grep rsync 
```
or by using the .bash_history file: 
```sh
cat ~/.bash_history | grep rsync
```

**script:** Records everyting printed in the terminal. to start recording a session issue the `script <file_name_to_store>`, and after you are done run the `exit` command to stop recording. 


### Shell Environment Variables (105.1)
Like variables in programming, in shell we have and can define our variables. The variable name can contain `a-z`, `A-Z`, `digits` and `_`, but the name cannot start with digits. 

when assigning a value to a variable in the shell, there must not be any spaces on either side of the = token. If you include spaces, the shell will interpret it incorrectly, resulting in errors or unintended behavior. For example: 
```sh
MY_VAR="Hello World"     # correct
MY_VAR = "Hello World"   # Incorrect
MY_VAR= "Hello World"    # Incorrect
MY_VAR ="Hello World"    # Incorrect
```
Note: variable names in the shell are case-sensitive. This means that MY_VAR, My_Var, and my_var are treated as entirely different variables.

To list system variables we can use the `env` command. This command can also be used to remove, set and run a command in modified environment. 

To run commands with modified environment: 
```sh
env VAR_NAME=value <command_to_run>
```

To list all variables including user defined variables we can use the `set` command. We can also use this command to change the value of a variable.  

Some useful options for the set command: 
| **Switch** | **Description**                                                                 |
|------------|---------------------------------------------------------------------------------|
| `-a`       | Automatically mark variables that are modified or created for export.           |
| `-b`       | Notify the user of background job termination immediately.                      |
| `-e`       | Exit immediately if a command exits with a non-zero status.                     |
| `-f`       | Disable file name generation (globbing) using wildcard characters (`*`, `?`).   |
| `-h`       | Remember the location of commands as they are looked up.                        |
| `-m`       | Job control is enabled; background jobs run in a separate process group.        |
| `-n`       | Read commands but do not execute them (syntax check only).                      |
| `-u`       | Treat unset variables as an error when substituting.                            |
| `-v`       | Print shell input lines as they are read.                                       |
| `-x`       | Print commands and their arguments as they are executed (useful for debugging). |
| `+o`       | Disable the option that was previously enabled using `-o`.                      |
| `-o`       | Enable various shell options (e.g., `set -o vi` for vi-style line editing).     |

To unset a varialbe use the `unset` command with the varialble name. 

To define/set a variable: 
```sh
VAR_NAME=VALUE
```

to print or use the variable, use it with the `$` sign at the begining: 
```sh
echo $VAR_NAME
```

**PATH Variable in Linux:** The PATH variable in Linux is one of the most important environment variables. It specifies a list of directories that the shell searches through when you try to run a command. This allows executing commands and programs without needing to specify their full path.

**Exporting variables:** 
The export command in UNIX-like operating systems is used to set an environment variable so that it is available to child processes spawned by the shell. When we export a variable, it becomes part of the environment that is passed to any subsequently executed programs or scripts. The PATH variable contains a colon-separated list of directories.


```sh
export VAR_NAME=value
```

To add a new path to PATH variable:
```sh
export PATH=$PATH:/path
``` 

**.bashrc file:** This file in Linux is a script that runs every time a new terminal session is started in interactive mode. It is used to configure the user's environment, set up aliases, functions, environment variables and more. This file is stored ini the user's home directory i.e. `~/.bashrc`. 

Using export to set enviaronment variables will save the variable temorary. But when we add a variable in the `.bashrc` file, it will store it permanently. 

**Aliases In Linux:** We can create shortcut or an alternatvive names for a command or a series of commands. Aliases are used to create custom, often shorter, command names for frequently used or complex commands, making it easier to work in the shell. 
```sh
alias <custom_name>=<'command'>
```

ex: 
```sh
alias ll='ls -la'
```

**Source:** The source command in Linux is used to apply changes made in shell configuration (like `.bashrc`). It is also  used to read and execute commands from a file within the current shell session. 
```sh
source <filename>
```

ex: if we have a tst.sh file as following: 
```sh
msg='hello world!'

alias pr='echo $msg'

pr
```

then running source command will prinit out the msg:
```sh
$ source tst.sh 
hello world!
```

**Functions In Shell:** Like programming languages we can define a function and use them in shell. Shell functions are reusable blocks of code in shell scripting that allow user to group multiple commands under a single name for a specific purpose. 

Defining a function: 
```sh
function_name(){
    <commands>
}
```

ex:
```sh 
gb=400

getHighTime(){
	local sum=$((gb+20))
	echo "the high time is: $sum"
}

getHighTime
```
**Types of variables:**
1. Local Variables: These variables are defined within scipts or  function blocks, they are limited to the current shell session or script. These variables do not affect the parent shell or other scripts. ex: 
```sh
my_var="Hello"
echo $my_var
```
In  a function, we can use the `local` keyword to restrict the variable's scope: 
```sh
my_function() {
    local temp_var="Temporary"
    echo $temp_var
}
```
2. Environment variables: These variables are typically used to store configuration settings,path, and other data that multiple programs or scripts may need to access. ex: 
```sh
export PATH="/usr/local/bin:$PATH"
export EDITOR="vim"
```

3. Shell variables: Shell variables are internal variables used by the shell to control its behavior and operations. ex: 
- `PS1`: Defines the primary prompt string (e.g. the command prompt format).
- `HOME`: The current user's home directory. 
- `SHELL`: The path to the current shell binary (e.g. `/bin/bash`).

4. Positional Parameters: These parameters are used to access the argumant passed to a shell script or function. ex: 
```sh


myFunction(){
	echo "the first param is: $1
	the second param is: $2"
}

# entering params in the file
myFunction "Hello" "World"

myFunction $1 $2
``` 
running this bash script using source: 
```sh
source tst.sh "första" "andra"
the first param is: Hello
	the second param is: World
the first param is: första
	the second param is: andra
```



More about Bash Scripting in [Bash](http://www.github.com/haidarim/Bash).
 


**Different types of shell session:**

1. Login shell: Is a shell session that is initiated when we log in  to a system (e.g. ssh or a console login). The shell reads files like `/etc/profile`, `~/.bash_profile`, `~/.bash_login` or `~/.profile` in that order. A login shell session sets up the user's environment, including variables, paths, and other settings which will be inherited by all subsequent subshell and terminal sessions. These files typically contain environment setting, path configurations and other startup script. 

2. Interactive shell: This a shell that is started without logging in, typically by opening a terminal emulator in a graphical user interface (GUI). This shell is typically used for day-to-day command execution and scripting in a terminal windows. The shell reads the `~/.bashrc` file. 

3. Non interactive shell: This shell is started when running a script, command or other non-onoteractive task. The user does not interact with this shell. This shelltypically does not read the `~/.bashrc` file instead other files that have the `#!/bin/bash` header (Shebang line). 

4. Subshell: A child shell session created by the parent shell, usually to execute a command or a group of commands. ex:
```sh
(cd /tmp && ls)
```

When we open a terminal or run a script, the shell spawns a new process. The parent shell process (like opened terminal) can start a new shell (a subshell). The subshell inherits environment variables and the execution environment from the parent. 


### Shell Scripting basics (105.2)

**Commentig in Bash Scripting:**
1. To comment a single line, use the `#`. 
2. To comment multuple lines use the `:<<'EOF' ..... EOF`, ex: 
```sh
list=(e0 Hello e2 e3 e4 e5)

:<<'EOF'
echo ${#list[1]}
# Prints 5
EOF

echo "hej"
```  

**Combining commands:** allows to perform multiple operations in a single line, making your workflows more efficient and flexible. There are several ways to combine commands in the shell, each with different behaviors and use cases.

- Sequential Execution: Using the `;`token we can run multiple commands in sequence, one after the other, regardless whether the previous command was successful or not.
```sh
command1; command2; command3
```

- Conditional Execution (AND): Using the `&&` sign we can chain commands together such that the next command runs only **if the previous command was successful** (i.e. it exited with a status of 0). 
```sh
command1 && command2 && command3
``` 

- Conditional Execution (OR): Using the `||` sign we can run a command **only if the previous command fails** (i.e. it exited with a status different from 0). 
```sh
command1 || command2
```

**Command substitution:** It allows to capture the output of a command and use it as a part of another command or assign it to a variable. 
- Using backticks: 
```sh
result=`command`
```

- Using `$(...)`: 
```sh
result=$(command)
```

**Conditions:** 
```sh
if [condition] 
then 
    do some tasks
elif
    do other task
else 
    do other tasks
fi
```
or 
```sh
if [condition]; then 
    do some task
elif
    do other task
else
    do other task
fi
```

ex: 
```sh
if [ "$1" == "1" ]
then
	echo "ett"
else 
	echo "two"
fi
```

Special Parameters: Special parameters provide additional information about the shell environment and the status of commands, ex:
- `$?`: The exit status of the last command executed.
- `$$`: The process ID of the current shell.
- `$#`: The number of arguments passed to the script or function.
- `$0`: The name of the script or the shell itsefl. 
- `$1, $2, ..., $N`: Positional parametes corresponding to the args passed to the script. 
- `$@`: Expands each args as a separate word, preserving whitespace. 
- `$*`: Expands all args as a single world with all parameters separated by the first character of the `IFS` (Internal Field Separator). 
- `$!`: The PID of the last background command. 
- `$-`: The current options set for the shell. 


Comparsion Operators: 
1. Numeric Comparsions: 
- `-eq`: equal to, ex: `"$1" -eq 10`, if the first parameter equals to 10. 
- `-ne`: not equal to.
- `-lt`: less than. 
- `-le`: less than or equal to. 
- `-gt`: Greater than. 
- `-ge`: Greater than or equal to. 

2. String comparsion: 
- `=`: equal to. 
- `!=`: not equal to. 
- `-z`: true if the string is empty. ex: `if [ -z "$1"]`.

3. File Tests: 
- `-e`: Checks if a file exists. 
- `-f`: Checks if a file is a regular file. 
- `-d`: Checks if a directory exists. 
- `-r`: Checks if a file is readable. 
- `-w`: Checks if a file is writable. 
- `-x`: Checks if a file is executable. 
- `-p`: To print a message when if statement runs. 
- `-t`: To set a time, in sec. 

ex: 
```sh
filename="example.txt"

if [ -f "$filename" ]; then
    echo "'$filename' is a regular file."
else
    echo "'$filename' is not a regular file or does not exist."
fi
```



Examples: We can use special characters in condition:
- `$# -ne`: `$#` number of parameters, `-ne` Not Equal.ex: 
```sh
if [ "$#" -ne 2 ]; then
    # this block runs when the number of args is not exactly 2
    echo "This script requires exactly 2 arguments."
    exit 1
fi
```



**Reading input from user using read command:** The read command in Bash is used to take input from the user. It reads a line of text from standard input (usually the keyboard) and assigns it to one or more variables. 
ex.1:
```sh
echo 'enter input'
read input
echo "your input is: $input" 
``` 
ex.2: 
```sh
#!/bin/bash

if read -t 5 -p "Enter your name (you have 5 seconds): " name; then
    echo "Hello, $name!"
    exit 0
else
    echo "You did not enter your name in time."
    exit 1
fi
```

**Loops:** to execute a block of code multiple times. Bash supports several types of loops: `for`, `while`, and `until`

1. `for` loop: 
- Iterating over a list: 
```sh
list=("m1" "m2" "m3")

for e in ${list[@]}; do
        echo "$e"
done
```

```sh
list=("m1" "m2" "m3")

for e in "${list[*]}"; do
        echo "$e"
done
```

```sh
for fruit in apple orange banana; do
    echo "$fruit"
done
```
${array[@]}: Expands to each element of the array as a separate word. This means that each element is treated individually, which is crucial when we want to handle elements that contain spaces.

`${array[*]}`: Expands to all elements of the array as a single word, with elements joined by the first character of the IFS (Internal Field Separator), which defaults to a space.

- Iterating over a range of numbers:
```sh
for i in {1..5}; do
    echo "Number: $i"
done
```

- Using C-style syntax: 
```sh
for ((i=1; i<=5; i++)); do
    echo "Number: $i"
done
# Note we use two ()
```



2. `while` loop: To executes a block of code as long as a specified condition is true. 
```sh
count=1

while [ $count -le 5 ]; do
    echo "Count: $count"
    ((count++))
done
```

ex: read input data ubtil a condition is met: 
```sh
while true; do
    read -p "enter -q to quit: " input
    if [ "$input" == "q" ]; then
        echo 
        break
    fi
    echo "you entered: $input"
done
```

3. `until` loop: The until loop is similar to the while loop but with the opposite condition, it runs as long as the condition is false: 
```sh
count=1
until [ $count -gt 5]; do
    echo "count: $count"
    ((count++))
done
```

Note: We can use the break and continue statements like other programming languages. 

**Data structures in Bash Scripting:** Bash scripting does not have build-in data structures like lists, queues and stacks, but we can emulate these data structures using arrays and basic operations. 

**List in Bash Scipring:**
```sh
#!/bin/bash

# Define a list (array)
list=("item1" "item2" "item3")

# Access an element (first item)
echo "First item: ${list[0]}"

# Add an element to the list
list+=("item4")

# Iterate over the list
for item in "${list[@]}"; do
    echo "$item"
done
```

**Tricks/syntax in bash on arrays:**

-  When we want to use indexing on an array to access elemnts, we have to use the `${array[i]}` format.  

- Refering to all elements of an array we have to use the `${array[@]}` syntax or `${array[*]}`.

- Slicing operation, To slice an array and set changing the starting position we can use the `:n` syntax, where n is the index of elment to start slicing (inclusive n, i.e. [n,..]). For example `${array[@]:1}` will skip the first elemtn (at index 0) and includes all sunsequent elemnts. Note, to save the slicing operations we have to store it in the array ex: `array=("${array[@]:1}")`. We can also determine the slicing index in the `:n:m` format. For example `${array[@]:1:2}` will slice from first index until the seconnd index (2), i.e. [1,2]. Examples: 

Keep in mind, when we want do operations with variables and lists, we have to use the `$` character. 

```sh
list=("e0" "e1" "e2" "e3" "e4")

list=(${list[@]:1}) # savign changes in the list

for i in ${list[@]}; do
	echo $i 
done

# will print e1, e2, e3, e4
```

```sh
list=("e0" "e1" "e2" "e3" "e4")

list=(${list[@]:1:3})

echo ${list[@]}


# will print e1, e2, e3
```

- Length of an array: To get length of an array we can use the `${#list[@]}` format, using the `#` character. Ex:
```sh
list=(e0 e1 e2 e3 e4 e5)

echo ${#list[@]}
# Prints 6
```

- Length of an array elements: To get the length of an item in an array we can use the `#` char and the index of the element in the array instead if the `@`. Ex:
```sh
list=(e0 Hello e2 e3 e4 e5)

echo ${#list[1]}
# Prints 5
```


- Adding items to a list/array: To add a new item at the end of a list we use the `+=` operators, e.g. `list+="itemN"`, ex:
```sh
list=()

list+=("item1")
list+=("item2")

echo ${list[@]}

# will print: item1 item2
``` 

- Removing items from a list/array: To remove an item from the end of  a list we can use the `${list[@]:0:${#list[@]}-1}`, which means slice from begingin exlisive the last element, Note that we have to store changes. To store the last element before removing it we have to use the `${list[-1]}`, which selects the last element, then we can store it.
 ex:
```sh
list=(e0 e1 e2 e3 e4 e5)

# store the last item 
removed_item=(${list[-1]})
echo $removed_item

# remove the last item 
list=(${list[@]:0:${#list[@]}-1})

echo ${list[@]}

# The output: 
# e5
# e0 e1 e2 e3 e4
```

- Operations and `()`: The `()` is used for list creation, arthimetic operation, sunshells and command grouping. 

Ex: Arthimetic Operations: In Bash, arithmetic operations are usually done inside `(( ... ))`. The `(( ... ))` syntax is used for integer arithmetic. 
```sh
#!/bin/bash

# Arithmetic operation
a=10
b=20
sum=$((a + b))

echo "Sum: $sum"  # Outputs: Sum: 30
```

- `{}` related to array operations: Parameter Expansion: Curly braces {} are used for parameter expansion, where you access or manipulate variables.

Array Operations: {} is used to access specific elements in an array or to perform operations like slicing.
Ex: 
```sh
#!/bin/bash

# Array declaration
array=("apple" "banana" "cherry")

# Accessing the second element
echo "${array[1]}"  # Outputs: banana
```

- `${array[@]}` with and without `" "`:
1. Without Quotes ("${array[@]}" vs ${array[@]}): If we use ${array[@]} without quotes, each element of the array is treated as a separate word but only when not within a quoted context. Ex:

```sh
#!/bin/bash

# Array with spaces in elements
array=("apple" "banana fruit" "cherry")

# Iterate without quotes
for item in ${array[@]}; do
    echo "$item"
done

# Output: 
# apple
# banana
# fruit
# cherry
```

2. With Quotes: When we use "${array[@]}", each array element is treated as a separate argument, even if elements contain spaces. This is generally the safest way to handle arrays, especially when dealing with elements that have spaces. Ex:
```sh
#!/bin/bash

# Array with spaces in elements
array=("apple" "banana fruit" "cherry")

# Iterate with quotes
for item in "${array[@]}"; do
    echo "$item"
done

# Output:
# apple
# banana fruit
# cherry
```

**Examples of basic DSs in Bash:** 
a. Queue in Bash Scripting:
```sh
#!/bin/bash

# Define a queue (array)
queue=()

# Enqueue (add to the end)
queue+=("item1")
queue+=("item2")
queue+=("item3")

# Dequeue (remove from the beginning)
dequeued_item="${queue[0]}"
queue=("${queue[@]:1}")

echo "Dequeued item: $dequeued_item"

# Print remaining queue items
echo "Remaining queue: ${queue[@]}"
```

b. Stack in Bash Scirpting:
```sh
#!/bin/bash

# Define a stack (array)
stack=()

# Push (add to the end)
stack+=("item1")
stack+=("item2")
stack+=("item3")

# Pop (remove from the end)
popped_item="${stack[-1]}"
stack=("${stack[@]:0:${#stack[@]}-1}")

echo "Popped item: $popped_item"

# Print remaining stack items
echo "Remaining stack: ${stack[@]}"
```

**Sending main to the root user:**
In many Linux distributions, system administrators often use email to notify the root user (or other users) about important system events or messages. We can use the mail command to send emails to the root user or any other user on the system. To use the mail command, we need to install a mail utility package. The most common ones are mailutils or bsd-mailx. Here’s how to install them:

- Debian/Ubuntu:
```sh
sudo apt-get update
sudo apt-get install mailutils
```

- CentOS/RHEL:
```sh
sudo yum install mailx
```

- Fedora: 
```sh
sudo dnf install mailx
```

* Basic usage of mail command: 
```sh
echo "This is the body of the email" | mail -s "Subject of the email" root
```

* With a File Attachment: 
```sh
echo "This is the body of the email" | mail -s "Subject of the email" -A /path/to/attachment root
```

* Interactive Mode: After running the command below, we’ll enter an interactive mode where we can type our message. End the message by pressing Ctrl+D.
```sh
mail -s "Subject of the email" root
``

* Checking Mail for the Root User: The root user can check received emails using the mail command:
```sh
mail
```

* Configuring Mail Delivery to External Addresses: If we want the root user’s mail to be forwarded to an external email address (e.g., admin@example.com), we can edit the /root/.forward file:
```sh
echo "admin@example.com" > /root/.forward
```
More about Mailing will be discovered.


**Returned values:** 
In Linux, the concept of "returned values" typically refers to the exit status of a command or a script. This is a numerical value that a command returns when it finishes executing. The exit status indicates whether the command was successful or if an error occurred.

Exit status Basics: 
- `0`: The command executed successfully without any errors. 
- `Non-zero`: Indicates that the command encountered an error. Different commands may return different non-zero values to indicate various types of errors. 

* Using `$?` to check the status of preciding command execution, ex: 
```sh
echo "hej"

echo $? # prints 0 because previous command will be successfully executed

ls non_existent_dir
echo $? # prints non-zero value, because the directory does not exist
```

Other example using if statement: 
```sh
#!/bin/bash

mkdir /some_directory
if [ $? -eq 0 ]; then
    echo "Directory created successfully."
else
    echo "Failed to create directory."
fi
```

* Using `exit` in scripts to exit the script and return a specific exit statusex: 
```sh
#!/bin/bash

if [ "$1" -eq 42 ]; then
    echo "The answer to life, the universe, and everything."
    exit 0
else
    echo "Wrong answer!"
    exit 1
fi
```



**Executign scripts:**
We can execute scripts using `source`, `sh` or changing the the mode of script using `chmod` and then running script by `./script_name`. 


### Linux GUI stack (106.1)
Linux's GUI (Graphical User Interface) stack is a layered architecture that enables users to interact with the operating system visually, using windows, icons, menus, and other graphical elements. The key components in this stack include display servers (like X11 and Wayland), compositors, window managers, and desktop environments.

```lua        
                          --------- 
                          |  User |
                          ---------
                              |
                              |
              ------------------------------------------  
              |                                        |
              |                                        |
              |                                        |                  
    ---------------------              ------------------------------------
    |  Desktop Manager  |              |          Window Manager          |   
    | (GNOME, KDE, ...) |--------------| (OpenBox, i3, dwn, awesome, ...) |
    ---------------------       |      ------------------------------------
                                |
                                |
                         -----------------
                         |Display  Server| # Xorg (X11), WayLand, ... 
                         -----------------
                                |
                                |
                             ---------
                             | Kernel|  # Linux, BSD, ...
                             ---------
                                |
                                |
                       ----------------------
                       | Hardware (GPU, CPU)| # amd64, ARM, PowerPC, ...
                       ----------------------

```

**Component Descriptions:**
1. User: The person interacting with the system through a graphical interface.

2. Desktop Environment (Desktop Manager):
- Examples: GNOME, KDE Plasma, XFCE, LXQt.

- Role: Provides a complete graphical environment, including a window manager, panels, file managers, and system settings. It offers a cohesive user experience with consistent design and integrated applications. The desktop environment often includes or relies on a specific window manager.

3. Window Manager:
- Examples: OpenBox, i3, dwm, awesome, KWin (used in KDE), Mutter (used in GNOME).

- Role: Manages the placement and appearance of windows. It controls how windows are opened, resized, moved, and closed. It also handles keyboard shortcuts, window focus, and decorations (borders, title bars). The window manager communicates with the display server to perform these tasks.
Correction: The window manager doesn't ask the desktop manager to do things; rather, it directly interacts with the display server to manage windows. In some desktop environments, the window manager is tightly integrated with the desktop manager, but in others (like tiling window managers), it can function independently.

4. Display Server:
- Examples: Xorg (X11), Wayland.

- Role: Acts as the middleman between the hardware (especially the GPU) and the graphical applications. It handles input (keyboard, mouse), output (displaying graphics on the screen), and manages the rendering of windows. The display server can also support network transparency, allowing applications to be displayed remotely (especially in the case of X11).

- Difference Between X11 and Wayland: 
X11 (Xorg): An older and more complex system that has been the standard for decades. It requires additional components like compositors and window managers to function efficiently.

Wayland: A newer, more efficient protocol that simplifies the display stack by integrating the compositor and window manager into the display server, offering better performance and security.

5. Kernel:

- Examples: Linux, BSD kernels (like FreeBSD).

- Role: The core part of the operating system that manages hardware resources, including CPU, memory, and I/O devices (like GPUs). It provides a bridge between the hardware and the software running on the system, including the display server.

6. Hardware (GPU, CPU, etc.):
- Examples: amd64 (Intel/AMD processors), ARM (used in many mobile devices), PowerPC.

- Role: The physical components of the computer, including the graphics processing unit (GPU), central processing unit (CPU), and other hardware peripherals. The kernel interacts directly with this hardware to execute instructions and manage resources.

**Summary:**
* User: Interacts with the system.
* Desktop Environment: Provides a complete, cohesive GUI experience.
* Window Manager: Manages the positioning and behavior of windows.
* Display Server: Facilitates communication between the hardware and graphical applications.
* Kernel: Manages hardware resources and system calls.
* Hardware: Executes instructions and performs computations.

<<<<<<<<<<<<<<<<<50 /, 12:00>>>>>>>>>>>>>>>>>

