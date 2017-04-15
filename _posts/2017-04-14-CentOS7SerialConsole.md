---
layout:     post
title:      CentOS 7 Serial Console
date:       2017-04-14 04:00:00
summary:    Setting up CentOS 7 + Xen with serial console output throughout the process ( BIOS -> GRUB2 -> Xen -> CentOS 7 )
categories: sysadmin
thumbnail: cogs
tags:
 - CentOS7, Serial, Xen, GRUB2
---

# Adding the serial console to your life
Why set up a serial console? Having a serial console set up is super useful if you have a machine with IPMI that supports SOL (Serial Over Lan). It's also pretty nice if you're debugging boot output on *nix-like os, because you can copy and paste bug reports, stacktraces, etc. when things go wrong (which is what actually got me to sit down and cobble together a working solution from various googling)

#Before you get started
To test which serial port you want to set for all the following stuff, there is a quick test that can be performed in CentOS 7. If you run:
```
/sbin/agetty -L ttyS1 115200 vt100
```
As root/sudo with the other machine attached to the serial port / SOL, you should immediately see a prompt on the serial port when you run that command (`115200` is the speed, `ttyS1` is the serial port, `vt100` is the terminal type (but you shouldn't have to modify that. You may have to modify the speed and serial port until you are able to connect properly and on the port you want)

# BIOS (if available)
If your BIOS supports IPMI enable SOL under the IPMI and/or advanced area of the BIOS config

# GRUB2

Most of these subitems are actually just going to be related to the GRUB2 Config, but at the GRUB2 level, there's only one part that's important here:

```
GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=1 --word=8 --parity=no --stop=1"
GRUB_TERMINAL_OUTPUT="console serial"
```

`GRUB_TERMINAL_OUTPUT` should already exist, but probably says:
```
GRUB_TERMINAL_OUTPUT="console"
```

So add the `serial` portion to that line, and paste in the extra `GRUB_SERIAL_COMMAND`. Depending on your system/SOL/intended use of you may need to alter the speed or unit (unit 1 corresponds to ttyS1), `--word=8 --parity=no --stop=1` are often referred to as `8/n/1` in the serial world.

Update: ~~For some reason this config is not presently accepting input over serial (but normal VGA works as expected...I'll dig into this when I get a chance (or if anyone knows the answer let me know on twitter))~~

Fix: It's an easy fix, just not one that I knew existed. Add this to your /etc/default/grub file:
```
GRUB_TERMINAL_INPUT="console serial"
```

# Xen (skip if you're not using Xen)

Default config probably has a line that looks like 
```
GRUB_CMDLINE_XEN_DEFAULT="cpuinfo console=tty"
```

Change it to
```
GRUB_CMDLINE_XEN_DEFAULT="cpuinfo com1=115200,8n1 console=com1,tty"
```
Again, we're setting up for 115,200 bps, `8/n/1`, with serial and vga consoles active, modify accordingly to fit your particular situation

# CentOS 7
The final piece in the chain: getting the actual OS talking over serial (and still over VGA too). Change:

```
GRUB_CMDLINE_LINUX="crashkernel=auto"
```
 to 
```
GRUB_CMDLINE_LINUX="crashkernel=auto console=ttyS1,115200 console=tty0"
```
`console=tty0` will keep your VGA monitor working, while the `console=ttyS1,115200` portion makes serial work simultaneously.

# My Final GRUB2 Config (with some other xen/centos config stuff in it that isnt relevant here):

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=1 --word=8 --parity=no --stop=1"
GRUB_TERMINAL_OUTPUT="console serial"
GRUB_TERMINAL_INPUT="console serial"
GRUB_CMDLINE_LINUX="crashkernel=auto console=ttyS1,115200 console=tty0"
GRUB_DISABLE_RECOVERY="true"
GRUB_CMDLINE_XEN_DEFAULT="dom0_max_vcpus=4 dom0_vcpus_pin dom0_mem=1536M,max:1536M cpuinfo com1=115200,8n1 console=com1,tty loglvl=all guest_loglvl=all elevator=noop"
GRUB_CMDLINE_LINUX_XEN_REPLACE_DEFAULT="console=hvc0 earlyprintk=xen nomodeset elevator=noop"
```

This was all tested under CentOS 7.3, but should work with any version of CentOS 7.

# Bonus round: connectiong via IPMITool/SOL
After you install ipmitool on your client:
```
ipmitool -I lanplus -H [IP] -U ADMIN -P [PASS] sol activate
```
Obviously replace `[IP]` and `[PASS]` with the relevant bits of data, and you should be able to connect and see/interact with the machine on the IPMI Serial over LAN interface :)

