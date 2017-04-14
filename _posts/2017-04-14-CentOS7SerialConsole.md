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

# BIOS (if available)
If your BIOS supports IPMI enable SOL under the IPMI and/or advanced area of the BIOS config

# GRUB2

# Xen

# CentOS 7

