---
id: 163
title: 'Installing Bodhi Linux on an Acer C720 Chromebook with SeaBIOS as default.'
date: '2014-12-15T10:59:22-08:00'
author: 'Code Apex'
layout: post
guid: 'http://www.codeapex.com/?p=163'
permalink: /installing-bodhi-linux-on-an-acer-c720-chromebook-with-seabios-as-default/
categories:
    - Uncategorized
---

## Situation:

We have an Acer C720 Chromebook and want Chrome OS removed and Bodhi Linux installed on it. Additionally we want to have it boot to Linux by default without having to start SeaBIOS manually by pressing `Ctrl + L` at the white boot splash screen.

## Objective:

Acer C720 Chromebook with Chrome OS removed and Bodhi Linux installed with SeaBIOS booting by default.

## Plan + Execution:

### Outline

The general installation procedure:

1. Enable developer mode to allow access to the superuser shell in Chrome OS.
2. Accessing the superuser shell.
3. Enable legacy boot / SeaBIOS.
4. Set SeaBIOS as default.
5. Prepare the installation media (USB stick).
6. Boot from the installation media and install Bodhi Linux.

### <span class="mw-headline" id="Enabling_Developer_Mode">Enabling Developer Mode</span>

1. Turn on the Chromebook.
2. Press and hold the `Esc + F3 (Refresh)` keys, then press the `Power` button. This enters Recovery Mode.
3. Press `Ctrl + D` (no prompt). It will ask you to confirm, then the system will revert its state and enable Developer Mode.

<div> **Note:** Press `Ctrl + D` (or wait 30 seconds for the beep and boot) at the white boot splash screen to enter Chrome OS.</div>### <span class="mw-headline" id="Accessing_the_superuser_shell">Accessing the superuser shell</span>

Start a terminal session. There are two ways to start a terminal session in Chrome OS (in dev-mode): VT-2 or crosh. For our purposes here it does not matter which way is used.  
  
<span class="collapseomatic " id="id6361a8ca9ce48" tabindex="0" title="Get the command prompt through VT-2">Get the command prompt through VT-2</span>

<div class="collapseomatic_content " id="target-id6361a8ca9ce48">> #### Get the command prompt through VT-2
> 
> One way to get the login prompt is through something called VT-2, or “virtual terminal 2”. This is probably familiar to the Linux hackers out there. You can get to VT-2 by pressing:
> 
> <div>[ Ctrl ] [ Alt ] [ =&gt; ]</div>…where the \[ =&gt; \] key is the right-arrow key just above the number 3 on your keyboard.
> 
> Once you have the login prompt, you should see a set of instructions telling you about command-line access. <span style="color: #ff0000;">By default, you can login as the </span>`chronos`<span style="color: #ff0000;"> user with no password. This includes the ability to do password-less sudo.</span> The instructions on the screen will tell you how you can set a password. They also tell you how to disable screen dimming.
> 
> <div>The instructions do tell you how to get back to the browser, but because it’s so important, I’ll also put it here. Just press:</div>> <div><div>[ Ctrl ] [ Alt ] [ &lt;= ]</div></div>
> 
> <div>…where the [ &lt;= ] key is the left-arrow key just above the number 1 on your keyboard.</div>**SIDE NOTE**: For the technical-minded, you may realize that the top-rows of the keyboard on a Chrome OS device are actually treated by Linux as the keys F1 through F10. Thus, the \[ =&gt; \] key is actually F2 and the \[ &lt;= \] key is actually F1.
> 
> **SIDE NOTE**: If you’re fooling around, you might also notice that kernel messages show up on VT-8.
> 
> - - - - - -

</div><span class="collapseomatic " id="id6361a8ca9ce87" tabindex="0" title="Getting the command prompt through crosh">Getting the command prompt through crosh</span><div class="collapseomatic_content " id="target-id6361a8ca9ce87">#### Getting the command prompt through crosh

<div>Because you booted into developer mode, you also have an alternate way to get a terminal prompt. I’ll mention this as well, since the alternate shell is a little nicer (in the very least, it keeps your screen from dimming on you), even if it is a little harder to get to. To use this alternate way:</div><div>1. Go through the standard Chrome OS login screen (you’ll need to setup a network, etc) and get to the web browser. It’s OK if you login as guest.
2. Press \[ Ctrl \] \[ Alt \] \[ T \] to get the crosh shell.
3. Use the `shell` command to get the shell prompt. NOTE: even if you set a password for the `chronos` user, you won’t need it here (though you still need it for `sudo` access)

</div>Note that entering the shell this way doesn’t give you all the instructions that VT-2 does (like how to set your password). You might want to follow the VT-2 steps once just to get the instructions.

If you want to get back to the browser without killing the shell, you can do it with \[ Alt \] \[ Tab \].

**SIDE NOTE**: You can actually create as many shells as you want. Just hit \[ Ctrl \] \[ Alt \] \[ T \] again and a second shell will be opened. You can \[ Alt \] \[ Tab \] between them.

</div>In the terminal open a bash shell with the `shell` command. Then become superuser with `sudo bash`.

### <span class="mw-headline" id="Enabling_SeaBIOS">Enabling SeaBIOS</span>

This method will allow you to access the pre-installed version of SeaBIOS through the Developer Mode screen in Coreboot.

- Inside your superuser shell enter:

`crossystem dev_boot_usb=1 dev_boot_legacy=1`

- Reboot the machine.

You can now start SeaBIOS by pressing `Ctrl + L` at the white boot splash screen.

### <span class="mw-headline" id="Boot_to_SeaBIOS_by_default">Boot to SeaBIOS by default</span>

To boot SeaBIOS by default, you will need to run [`set_gbb_flags.sh`](https://chromium.googlesource.com/chromiumos/platform/vboot_reference/+/master/scripts/image_signing/set_gbb_flags.sh) in Chrome OS (already included in Chrome OS).

<div> **Note:** It is a good idea to do this and have SeaBIOS boot by default even if you do not mind pressing `Ctrl + L` at boot. The reason being is if you do not set the GBB flags then your system might become corrupted on empty battery, Chrome OS will be forced to recover and you will lose your Linux installation on the internal storage.</div><div> **Warning:** If you do not disable the write protection before setting the GBB flags you endanger wiping out the RW-LEGACY part of the firmware (i.e. SeaBIOS) and your system might not boot (should be recoverable with Chrome OS recovery media). Updated versions of [`set_gbb_flags.sh`](https://chromium.googlesource.com/chromiumos/platform/vboot_reference/+/master/scripts/image_signing/set_gbb_flags.sh) won’t let you set the GBB flags without disabling the write protection.</div>- Disable the hardware write protection by removing screw 7 here: [http://www.chromium.org/\_/rsrc/1381990807648/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook/c720-chromebook-annotated-innards.png ](http://www.chromium.org/_/rsrc/1381990807648/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook/c720-chromebook-annotated-innards.png)Note: Putting the cover back on with just screw 6 in should be enough to boot and complete the rest of the needed steps before putting the write-protect screw back in.

- Boot and start a [superuser shell](https://wiki.archlinux.org/index.php/Chromebook#Accessing_the_superuser_shell "Chromebook") and enter:

`sudo su<br></br>`

- Disable the software write protection.

`flashrom --wp-disable<br></br>`

- Check that write protection is disabled.

`flashrom --wp-status<br></br>`

- Run `set_gbb_flags.sh` with no parameters.

`set_gbb_flags.sh<br></br>`

<div> **Note:** Recent versions of Chrome OS have moved the script to /usr/share/vboot/bin/set_gbb_flags.sh which isn’t in $PATH by default.</div>- Make sure you get the following output.

`GBB_FLAG_DEV_SCREEN_SHORT_DELAY 0x00000001<br></br>GBB_FLAG_FORCE_DEV_SWITCH_ON 0x00000008<br></br>GBB_FLAG_FORCE_DEV_BOOT_LEGACY 0x00000080<br></br>GBB_FLAG_DEFAULT_DEV_BOOT_LEGACY 0x00000400<br></br>`

- Now set SeaBIOS as default.

`# set_gbb_flags.sh 0x489<br></br>`

- Enable back the software write protection.

`flashrom --wp-enable<br></br>`  
Your Chromebook now will boot to SeaBIOS by default, at this point you should re-enable the hardware write protection by reinserting the write protect screw.

### Prepare the Installation Media

Navigate and retrieve the latest Bodhi Linux ISO from here:

[http://sourceforge.net/projects/bodhilinux/files/?source=navbar](http://sourceforge.net/projects/bodhilinux/files/?source=navbar "Bodhi Linux ISO")

As of this writing the current one we want is: [bodhi-3.0.0-chromebook-rc1.iso](http://sourceforge.net/projects/bodhilinux/files/3.0.0-rc1/bodhi-3.0.0-chromebook-rc1.iso/download "bodhi-3.0.0-chromebook-rc1.iso")

Assuming we are preparing the installation media in a Linux environment, change to the directory where you have downloaded the ISO image to and run:  
`dd if=bodhi-c720-chromebook-rc1.iso of=/dev/sdX`  
Where X is replaced with the drive letter of your USB stick. A quick way to find this out is checking the output of the command `dmesg` after inserting the usb drive.

If you prefer a GUI tool or are using OSX/Windows to create the flash drive you can find detailed information on this process [here](https://help.ubuntu.com/community/Installation/FromImgFiles).

### Boot From the Installation Media and Install Bodhi Linux

Since we have set SeaBIOS to boot by default we will not need to press `Ctrl + L` at the white boot splash screen.

A message will appear to press the `ESC` key to select to bring up the boot menu, proceed to do so and select your USB device from the list it provides.

From this point you can follow the [normal Bodhi install instructions](http://wiki.bodhilinux.com/doku.php?id=installation_instructions#iiboot_into_the_live_environment).

---

## Comments

1. r3spo says  
January 19, 2015 at 3:10 am  

    Thanks for the guide, do you think this is working also on an HP14? (not he pavillion, the newest one).  
    Cheers

    - Code Apex says  
    January 19, 2015 at 6:20 pm  

        The HP14 is SeaBIOS as well so I believe it should work though I have not tried it; but do note the location of the write protect screw is different: [HP14 Write Protect Screw](http://s290.photobucket.com/user/bond304/media/IMG_5313_zpsacbb2723.jpg.html)  
        Also note these directions will not work for really old Chromebooks without SeaBIOS though (such as the Acer C710).  
        Regarding the Bodhi part specifically the creator of Bodhi Linux, Jeff Hoogland, has a blog post about picking up a HP14 [here](http://jeffhoogland.blogspot.ca/2014/03/buying-chromebooks-for-their-hardware.html) so I suspect installing Bodhi would work fine.  

