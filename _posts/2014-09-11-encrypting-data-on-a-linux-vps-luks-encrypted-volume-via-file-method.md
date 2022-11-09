---
id: 87
title: 'Encrypting Data on a Linux VPS - LUKS Encrypted Volume via File Method'
date: '2014-09-11T14:25:02-07:00'
author: 'Code Apex'
layout: post
guid: 'http://www.codeapex.com/?p=87'
permalink: /encrypting-data-on-a-linux-vps-luks-encrypted-volume-via-file-method/
categories:
    - Uncategorized
---

## Situation:

You wish to have certain files encrypted on your VPS and you do not want to repartition to accommodate an encrypted volume on a partition or logical volume.

## Objective:

A mountable LUKS encrypted volume based on a file in the filesystem.

## Plan + Execution:

### Install cryptsetup:

If cryptsetup is not installed already, use the appropriate command to install it for your distro.

Debian based:  
`apt-get install cryptsetup`

### Create Encrypted Volume File:

An empty non-sparse file is needed to serve as the encrypted volume. We will create such a file of 1G in size named enc-vol-1G at the root level directory, feel free to substitute your own size, name and location preferences for this file; the same goes for any arbitrary naming used here.

Note we need a non-sparse file meaning the full 1G space has been allocated for the file by the filesystem, so we cannot just use the `touch` command to create the file; our options are either the `fallocate` or `dd` commands. For creating the file `fallocate` is faster but `dd` offers more features, prefilling the allocated space with pseudo-random data using `if=/dev/urandom` for instance.

`fallocate -l 1GB /enc-vol-1G`  
or  
`dd if=/dev/zero of=/enc-vol-1G bs=1M count=1024`

### Transform File into a LUKS Partition with a Filesystem:

Turn the File Into a LUKS Partition:  
`cryptsetup -y luksFormat /enc-vol-1G`

Open the LUKS Partition and Setup Mapping Name:  
`cryptsetup luksOpen /enc-vol-1G encrypted_volume`

The mapping name is arbitrary and in our case we chose “encrypted\_volume”. After running the command a new device is created: `/dev/mapper/encrypted_volume` which we can treat in a similar manner as one would a disk partition.

Create a Filesystem on the LUKS Partition:  
`mke2fs -t ext4 /dev/mapper/encrypted_volume`

Close the LUKS Partition Releasing the Mapping Name and Clearing Key from Kernel Memory:  
`cryptsetup luksClose encrypted_volume`

Create a Mount Point:  
`mkdir /mnt/secret_stuff`

Mount point name and location are arbitrary; customize per your needs.

At this point our objective is met and we have a mountable LUKS encrypted volume based on a file in the filesystem.

### How to use:

Opening the LUKS partition:  
`cryptsetup luksOpen /enc-vol-1G encrypted_volume`

Mounting the Encrypted Volume:  
`mount /dev/mapper/encrypted_volume /mnt/secret_stuff`

Anything you now copy to /mnt/secret\_stuff will be encrypted.

Examples:  
`cp secret_file1.txt /mnt/secret_stuff`

`cp -R secret_dir /mnt/secret_stuff`

Unmounting:  
`umount /mnt/secret_stuff`

Closing the LUKS Partition:  
`cryptsetup luksClose encrypted_volume`

### Notes on Security:

Remember this maxim: Physical Access = Root Access

If someone has physical access to a machine they essentially can get root access.

This is more crucial to note when using a VPS. Not only do the operators of the physical server hosting your VPS have “physical” access to your VPS but so does anyone who hacks the actual machine your VPS is on.

While a LUKS partition is open the decryption key is stored in the kernel memory. A skilled person with root access can look for the key in memory or if they have “physical” access to the VPS they can just take a snapshot running state image of the VPS and pull the key from the snapshot.

Essentially what this means is there is a small chance someone could get the encryption key for the encrypted volume while it is unlocked on the VPS.

With that said a LUKS encrypted volume provides protection in the following scenarios assuming the LUKS partition is not currently open during them:

1. The VPS itself is hacked by an intruder who gains root access. Even though the intruder has root access on the VPS they do not have the key to open the encrypted volume (<small>technically though if no one noticed the intrusion they could potentially leave some hidden process to patiently wait for the encrypted volume to be opened to grab the key then</small>).
2. The physical server hosting the VPS is hacked by an intruder who copies all the data from the physical server offsite before getting found and blocked. This data includes all the data on your VPS’s hard disk (and RAM if an intruder took a snapshot of your running VPS) at the time the data was copied; but since the LUKS encrypted volume was closed at the time and the key not in your VPS’s RAM, the intruder will not be able to access the data in the encrypted volume in their copy of your VPS data.

With that said data stored in a LUKS encrypted volume is more secure than otherwise due to the additional hurdles an intruder would have to navigate to even get a chance to access the data.

## Misc Tips:

### Multiple Passphrases:

A LUKS device has 8 key slots that can be used for passphrases (or key files if one is inclined) to unlock it. Use the luksDump action of the cryptsetup command to to show the header information for the LUKS device and at the bottom will be information on the key slots.

`cryptsetup luksDump /enc-vol-1G`

output snippet with key slot information:

```
<pre class="lang:default decode:true">Key Slot 0: ENABLED
Iterations:             130398
Salt:                   dc 66 3b 57 8b 70 29 d2 ad 36 78 6c 88 01 65 81
98 df 17 7e 16 e6 76 46 d8 53 d7 40 ec a1 11 04
Key material offset:    8
AF stripes:             4000
Key Slot 1: DISABLED
Key Slot 2: DISABLED
Key Slot 3: DISABLED
Key Slot 4: DISABLED
Key Slot 5: DISABLED
Key Slot 6: DISABLED
Key Slot 7: DISABLED
```

Key Slot 0 is currently being used for the passphrase we initially setup our LUKS device with. To add an additional passphrase:

`cryptsetup luksAddKey /enc-vol-1G`

You will be first asked to enter any passphrase that unlocks the volume, then you will be prompted to enter a new additional passphrase that can be used to unlock the volume. The passphrase will use the next available key slot, alternatively to use a specific key slot for the passphrase (ex. using key slot 7): `cryptsetup --key-slot 7 luksAddKey /enc-vol-1G`

To remove a passphrase use the luksRemoveKey action of the cryptsetup command:

`cryptsetup luksRemoveKey /enc-vol-1G`

You will be prompted for the passphrase you want to remove. Alternatively to disable/remove a specific key slot (ex. remove key slot 7): `cryptsetup luksKillSlot /enc-vol-1G 7`
