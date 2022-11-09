---
id: 204
title: 'Increasing Linux VPS RAM for Free Using ZRAM - udev rule'
date: '2021-01-12T15:08:10-08:00'
author: 'Code Apex'
layout: post
guid: 'http://www.codeapex.com/?p=204'
permalink: /increasing-linux-vps-ram-for-free-using-zram-udev-rule/
categories:
    - Uncategorized
---

## Situation:

You are reaching or exceeding the RAM limits of your Linux VPS.

## Objective:

An increase in the amount of non-virtualized memory available to your applications on your VPS without resorting to paying for an upgraded VPS.

## Plan + Execution:

Note we want an increase in the amount of fast physical memory available to our applications. Increasing virtualized memory (via an increase in swap partition or swap file sizes) would not meet our objective; though if your VPS is on SSDs then upping swap space may not be a bad option since solid state disks are substantially faster than traditional hard disks (but still not as fast as physical memory).

One way of making more physical memory available to your applications is to be more efficient with the use of the RAM available. We can accomplish this by compressing data stored in memory; specifically we will use [ZRAM](http://en.wikipedia.org/wiki/Zram "ZRAM Wikipedia") to appropriate a percentage of total RAM as compressed block devices. Data stored in the area will be compressed and will take up less memory than if they weren’t.

Realize though that ZRAM will put a little more load on the CPU since it now has to compress/decompress data in that portion. So if you also reaching or exceeding the CPU limits of your VPS you should do testing to see if ZRAM helps your situation or perhaps consider actually upgrading your VPS.

Note some commands listed below may require superuser priviledges. Either

1. use the `su` command to change the login session’s owner to root; or
2. prepend the commands with `sudo` (e.g. `sudo swapon -s`); or
3. login as root directly

### Checking Kernel Support:

In a terminal run this command:  
`modprobe -nv zram`

If the result is a path to zram.ko then your kernel supports zram as a loadable module.

### Checking if ZRAM is Already Enabled:

Some distributions come with ZRAM enabled by default (such as Lubuntu). Before continuing onward we should double check to ensure we are not trying to accomplish something that is already done.  
Check to see if the module is loaded:  
`lsmod | grep zram`

If it is loaded (the result is a line starting with zram) check to see if the compressed block devices are setup and running as available swap devices:  
`swapon -s`

If you see devices listed such as /dev/zram0, /dev/zram1,… then ZRAM is already running and there is nothing you need to do further.

### Creating the udev rule

Enable the module:  
/etc/modules-load.d/zram.conf  
`zram`

Configure the number of /dev/zram nodes you need.  
/etc/modprobe.d/zram.conf  
`options zram num_devices=2`

Create the udev rule as shown in the example.  
/etc/udev/rules.d/99-zram.rules  
`KERNEL=="zram0", ATTR{disksize}="512M" RUN="/sbin/mkswap /dev/zram0", TAG+="systemd"<br></br>KERNEL=="zram1", ATTR{disksize}="512M" RUN="/sbin/mkswap /dev/zram1", TAG+="systemd"`

Add /dev/zram to your fstab.  
/etc/fstab  
`/dev/zram0 none swap sw 0 0<br></br>/dev/zram1 none swap sw 0 0`

As for how large should the zram devices be in total depends on the kind of workload, note the following comment from one of ZRAM’s developers (back when it was known as Compcache): [https://code.google.com/p/compcache/wiki/CompilingAndUsingNew](https://code.google.com/p/compcache/wiki/CompilingAndUsingNew "ZRAM (Compcache) Nitin Gupta Comment")

> <span class="author">Comment by project member [nitingupta910@gmail.com](https://code.google.com/u/nitingupta910@gmail.com/), </span> <span class="date" title="Fri May 7 01:35:52 2010">May 7, 2010</span>
> 
> @Paczesiowa: ramzswap should not be given so much of memory as its just a (virtual) swap device — it can compress only anonymous (say, process stack, heap etc.) memory. There are also other kinds of caches in the system, say filesystem cache which also require lot of memory. So, to have a good balance among all the different caches, ramzswap should not be given nearly 100% of RAM.
> 
> Appropriate amount of ramzswap memory depends on kind of workload. For typical desktop workloads, I found 25% to work just fine (this is the default).

### Reboot:

Reboot.

### Verify:

Verify that the module is loaded:  
`lsmod | grep zram`

If the result is a line starting with zram then it is loaded.

Verify that the compressed block devices are setup and running as available swap devices:  
`swapon -s`

If you see devices listed such as /dev/zram0, /dev/zram1,… then the compressed block devices are enabled as swap devices and the setup of ZRAM is complete.
