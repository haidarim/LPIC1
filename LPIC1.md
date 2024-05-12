# LPIC 1

Linux Professional Institute Certification Level 1:

- System Architecture (101)
- Linux installation and Package Management (102)
- GNU and Unic Commands (103)
- Devices, Linux Filesystems, Filesystem Hierarchy standard (104)

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

#### hardware related commands:

**lsusb:** Shows `USB` devices that are conencted to the system sush as:

- root hub, which is the buss itself, normally shown as Linux Foundation <nr> root hub
- fingerprint device
- camera
- and other connected devices.

**lspsi:** Shows `devices` that are conencted to the computer such as

**lsblk:** Shows all `block-devices` which are those that can read from or write to by blocks of data,

**lshw:** shows information about hardware.

#### Loadable Kernel Modules:

Linux has almost needed drivers for hardwares so when connecting a new device to the system Linux probably knows how to communicate with the newly connected device, it means that the Linux-kernel most of the time has the needed driver.

part 6 3/3 07:05

#file
##stream editor, filterning and transformating:
$ sed OPTIONS <SCRIPT> <INPUTFILE>
mostly used by just giving scrupt and inputfile

\*Scripts: - 's/wordToBeReplaced/ TheWord' - - - - - -
