---
id: 56
title: 'Increasing Linux VPS RAM for Free Using ZRAM - rc script'
date: '2014-07-31T11:03:29-07:00'
author: 'Code Apex'
layout: post
guid: 'http://www.codeapex.com/?p=56'
permalink: /increasing-linux-vps-ram-for-free-zram-rcscript/
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

### Creating the Startup Script:

If it does not already exist create the file **/etc/init.d/zram** with the following contents:

```
<pre class="lang:sh decode:true" title="zram">#!/bin/bash
### BEGIN INIT INFO
# Provides: zram
# Required-Start:
# Required-Stop:
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: Increased Performance In Linux With zRam (Virtual Swap Compressed in RAM)
# Description: Adapted from systemd scripts at https://github.com/mystilleef/FedoraZram
# Included as part of antix-goodies package by anticapitalista <antiX@operamail.com>
# This script was written by tradetaxfree and is found at http://crunchbanglinux.org/forums/topic/15344/zram-a-good-idea/
# Copy this script (as root) from /usr/local/bin to /etc/init.d and then #update-rc.d zram defaults
# After booting verify the module is loaded with: lsmod | grep zram
### END INIT INFO

start() {
    # get the number of CPUs
    num_cpus=$(grep -c processor /proc/cpuinfo)
    # if something goes wrong, assume we have 1
    [ "$num_cpus" != 0 ] || num_cpus=1

    # set decremented number of CPUs
    last_cpu=$((num_cpus - 1))
    
    #default Factor % = 90 change this value here or create /etc/default/zram
    FACTOR=25
    #& put the above single line in /etc/default/zram with the value you want
	[ -f /etc/default/zram ] && source /etc/default/zram || true
	factor=$FACTOR # percentage

    # get the amount of memory in the machine
    memtotal=$(grep MemTotal /proc/meminfo | awk ' { print $2 } ')
    mem_by_cpu=$(($memtotal/$num_cpus*$factor/100*1024))

    # load dependency modules
    # attempt load using syntax for kernels 3.4 onwards falling back to syntax for kernels 3.1 - 3.3
    if ! modprobe zram num_devices=$num_cpus && ! modprobe zram zram_num_devices=$num_cpus; then
      echo -e "Your Kernel needs to be compiled with ZRAM support:" \
      "\n\nDevice Drivers --> Staging Drivers --> Compressed RAM block device support (M)" \
      "\nDevice Drivers --> Staging Drivers --> Dynamic compression of swap pages and clean pagecache pages (*)" \
      "\n\nThe Liquorix Kernel (http://liquorix.net) has ZRAM support built in."
      exit 1
    fi
    echo "zram devices probed successfully"
    
    # initialize the devices
    for i in $(seq 0 $last_cpu); do
    echo $mem_by_cpu > /sys/block/zram$i/disksize
    # Creating swap filesystems
    mkswap /dev/zram$i
    # Switch the swaps on
    swapon -p 100 /dev/zram$i
    done
}

stop() {
    # get the number of CPUs
    num_cpus=$(grep -c processor /proc/cpuinfo)

    # set decremented number of CPUs
    last_cpu=$((num_cpus - 1))

    # Switching off swap
    for i in $(seq 0 $last_cpu); do
    if [ "$(grep /dev/zram$i /proc/swaps)" != "" ]; then
    swapoff /dev/zram$i
    sleep 1
    fi
    done

    sleep 1
    rmmod zram
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    restart)
        stop
        sleep 3
        start
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        RETVAL=1
esac
exit $RETVAL
```

The original source of this script can be found here:  
[http://crunchbanglinux.org/forums/topic/15344/zram-a-good-idea/](http://crunchbanglinux.org/forums/topic/15344/zram-a-good-idea/ "zram init script")

The creator also notes that the latest version of the script can be found here: [http://dl.dropbox.com/u/96561917/zram.zip](http://dl.dropbox.com/u/96561917/zram.zip "Latest Version of zram Script")

We do make some slight changes to the script.

The if statement for the module loading has been corrected to not exit if it successfully loads using syntax for kernels 3.4 and onward.

Additionally take note of the change we make to FACTOR in the script from the default 90 to 25. We implement this change due to the following comment from one of ZRAM’s developers (back when it was known as Compcache): [https://code.google.com/p/compcache/wiki/CompilingAndUsingNew](https://code.google.com/p/compcache/wiki/CompilingAndUsingNew "ZRAM (Compcache) Nitin Gupta Comment")

> <span class="author">Comment by project member [nitingupta910@gmail.com](https://code.google.com/u/nitingupta910@gmail.com/), </span> <span class="date" title="Fri May  7 01:35:52 2010">May 7, 2010</span>
> 
> @Paczesiowa: ramzswap should not be given so much of memory as its just a (virtual) swap device — it can compress only anonymous (say, process stack, heap etc.) memory. There are also other kinds of caches in the system, say filesystem cache which also require lot of memory. So, to have a good balance among all the different caches, ramzswap should not be given nearly 100% of RAM.
> 
> Appropriate amount of ramzswap memory depends on kind of workload. For typical desktop workloads, I found 25% to work just fine (this is the default).

### Enabling the Startup Script:

Enable execute permissions on the startup script:  
`chmod +x /etc/init.d/zram`

Add the startup script to the default run level:  
`update-rc.d zram defaults`

### Verify:

Now either run the script manually:  
`/etc/init.d/zram start`  
or reboot (preferred since this will test if the script auto executes on boot).

Verify that the module is loaded:  
`lsmod | grep zram`

If the result is a line starting with zram then it is loaded.

Verify that the compressed block devices are setup and running as available swap devices:  
`swapon -s`

If you see devices listed such as /dev/zram0, /dev/zram1,… then the compressed block devices are enabled as swap devices and the setup of ZRAM is complete.
