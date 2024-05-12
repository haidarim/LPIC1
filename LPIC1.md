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

- sysfs: system filesystem
  is provided by linux kernel, provides informations about kernel subsystems things like hardware devidces, drivers, etc, i.e. things that is in kernel's memory.
  sysfs is mounted under `/sys`

Note all devices that are at the `block` and `bus` have all connected PCI, USB, serial devices, etc.

Note devices in `sys` is based on their technology but in `/dev` is abstracted.

vid 5 01:32

#file
##stream editor, filterning and transformating:
$ sed OPTIONS <SCRIPT> <INPUTFILE>
mostly used by just giving scrupt and inputfile

\*Scripts: - 's/wordToBeReplaced/ TheWord' - - - - - -
