# LPIC 1

<h2>Linux Professional Institute Certification Level 1:</h2>

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

* NOTE: the changes will be temporary, but for keeping them permanently add their names into `/etc/modules` and also add their config files into the `/etc/modprobe.d/`


### The Boot Process 101.2

#file
##stream editor, filterning and transformating:
$ sed OPTIONS <SCRIPT> <INPUTFILE>
mostly used by just giving scrupt and inputfile

\*Scripts: - 's/wordToBeReplaced/ TheWord' - - - - - -
