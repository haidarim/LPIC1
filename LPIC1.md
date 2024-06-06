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

Most of the time, Linux-kernel has needed drivers to communicate with hardwares, so when connecting a new device to the system, the kernel probably knows how to communicate with that device. Rather, the Linux-kernel separate derivers into `.ko` files, known as kernel-modules.

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

- ex2: To get the status of services by `systemctl status <e.g. service name>`

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

009 101.3 07:12
#file
##stream editor, filterning and transformating:
$ sed OPTIONS <SCRIPT> <INPUTFILE>
mostly used by just giving scrupt and inputfile

\*Scripts: - 's/wordToBeReplaced/ TheWord' - - - - - -
