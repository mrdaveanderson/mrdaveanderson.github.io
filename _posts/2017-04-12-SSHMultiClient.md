---
layout:     post
title:      SSHMultiClient (Updated)
date:       2016-08-29 01:01:01
summary:    SSHMultiClient is a tool for quickly executing the same command to a large number of machines quickly and easily.
categories: SSHMultiClient
thumbnail: cogs
tags:
 - SSHMultiClient
---

# What is SSHMultiClient?

SSHMultiClient is a GUI wrapper for a platform/distro's standard ssh binary (currently tested against macOS 10.11.6's ssh binary) that lets you run the same command on arbitrarily selected groups of machines for admin / quick response to situations. 
Most of the admin tools I've seen are about scripting specific things away, which is fine for giant corps, but a lot of us need just reactionary tools where say new $frighteningPatch or $configChange needs to be made, and this is designed to help people in those situations.
Specifically that person was me, but I've since talked to a fair number of people who are interested in such a tool.


![Image](/images/SSHMultiClient-v0.5.png)


## Features

 * Create configs for all managed machines
 * Select N of those machines (via ctrl/cmd clicking N of them)
 * Run the same command on these selected machines
 * Only requires ssh, nohup, and bash on remote machine
 * Support SU if required, initial connections to selected machines via ssh keys
 * SU passwords stored encrypted with master UI password entered at launch
 * View all the results in one place
 * When all machines report complete, a final combined log is written locally
 * Commands are run on remote machines in a relatively safe way:
   * Commands are written to bash script on remote host (in .sshmcTasks dir)
   * Commands are executed in a nohup and also logged on remote host
   * If connection dies, task will complete and logs will be on remote machine


## Near Future (before general availability on github)

 * UI Still needs some work
 * Figure out OSS license to use (either 3clause BSD, or the one clang uses)
 * Cleanup more screen output when using su
 * A bit more testing. 
   * Try to ensure su password cant accidentally be printed to screen
   * I can't make any guarantees it will work for all people in all situations
   * I want to ensure any horrific bugs die first


## Future (stuff to do after general availability)
 
 * Machine groups? Not sure, but might be interesting
 * Generic templates feature which would:
   * Drag and drop common tasks
   * Combine with metadata in machine description files to chose actions
     * i.e. for update task:
       * on a debian machine the "update" task would use apt-get 
       * on a redhat machine the "update" task would use yum


## Update
I'm still working on this, but between injury and illness, it's been a rough last 6 months, most of the time I spend at the computer had to be spent on my day job, or other more important tasks

On, the plus side, I've been testing it, and making some improvements while working. Will probably be at least a month or two until I'm happy with it enough to throw it up on github. If you'd like to test or just show interest though, hit me up on twitter (@mrdaveanderson).
