---
id: 4
title: 'Restore an Acer C720 Chromebook to stock after installing Linux on it'
date: '2014-07-25T18:42:23-07:00'
author: 'Code Apex'
layout: post
guid: 'https://www.codeapex.com/?p=4'
permalink: /restore-an-acer-c720-chromebook-to-stock-after-installing-linux-on-it/
categories:
    - Uncategorized
---

## Situation:

Following a guide on the internet such as this one [here](http://www.codeapex.com/installing-bodhi-linux-on-an-acer-c720-chromebook-with-seabios-as-default/ "Installing Bodhi Linux on an Acer C720 Chromebook with SeaBIOS as default.") for getting Linux installed on an Acer C720 Chromebook, summarized you:

1. Switched the Chromebook to Developer Mode
2. Enabled SeaBIOS
3. Made SeaBIOS default on boot.
4. Installed Linux

## Objective:

Restore the Acer C720 Chromebook back to factory stock.

## Plan + Execution:

First realize there are essentially two things we want back at stock:

1. A fresh Chrome OS installed.
2. The default BIOS and its stock settings.

### Restoring Chrome OS

1. Find your version of your Chromebook by holding Ctrl+D on boot to get to the recovery screen. You will need it when creating the recovery USB flash drive by entering it when it asks for it in the creation script.
2. Create a recovery USB flash drive for Chrome OS, follow the directions in step 2 here for the OS you are creating the recovery USB flash drive in: [https://support.google.com/chromebook/answer/1080595?hl=en](https://support.google.com/chromebook/answer/1080595?hl=en "Create a recovery USB flash drive") or see directions for Linux and Windows below:  
    <span class="collapseomatic " id="id6361a8ca93898" tabindex="0" title="Directions for creating the recovery USB flash drive in Linux">Directions for creating the recovery USB flash drive in Linux</span><div class="collapseomatic_content " id="target-id6361a8ca93898">
    1. > Click this link to download the Recovery Tool: [https://dl.google.com/dl/edgedl/chromeos/recovery/linux\_recovery.sh](https://dl.google.com/dl/edgedl/chromeos/recovery/linux_recovery.sh)
    2. > Modify the script permissions to allow execution with the following command: `$ sudo chmod 755 linux_recovery.sh`
    3. > Run the script with root privileges with the following command: `$ sudo bash linux_recovery.sh`
    4. > Follow the prompts from the tool to complete building the operating system image.
    
    </div>  
    <span class="collapseomatic " id="id6361a8ca938f5" tabindex="0" title="Directions for creating the recovery USB flash drive in Windows">Directions for creating the recovery USB flash drive in Windows</span><div class="collapseomatic_content " id="target-id6361a8ca938f5">
    1. > Click this link to download the Recovery Tool: <https://dl.google.com/dl/chromeos/recovery/chromeosimagecreatorV2.exe> If you’re a network administrator for your school, business, or organization, click this link to download the Recovery Tool: <https://dl.google.com/dl/chromeos/recovery/chromeosimagecreator.exe>
    2. > Run the tool and follow the instructions that appear on your screen.
    3. > After you recover your Chromebook, you ***must*** format your USB flash drive or SD card using the Recovery Tool. If you don’t format your USB flash drive or SD card, you won’t be able to use all the storage space on your external device. Additionally, your USB flash drive or SD card may not be recognizable by Windows. [See instructions for reformatting your USB flash drive or SD card](https://support.google.com/chromebook/answer/2554370).
    
    </div>
3. Reinstall Chrome OS, step 3 here [https://support.google.com/chromebook/answer/1080595?hl=en](https://support.google.com/chromebook/answer/1080595?hl=en "Create a recovery USB flash drive") or see directions below:  
    <span class="collapseomatic " id="id6361a8ca93916" tabindex="0" title="Reinstall Chrome OS, step 3">Reinstall Chrome OS, step 3</span><div class="collapseomatic_content " id="target-id6361a8ca93916">
    1. > Start your Chromebook.
    2. > When the “Chrome OS is missing or damaged” screen appears, insert the USB flash drive or SD card you created into the USB port or SD card slot on your Chrome device. Note if the Chromebook is not booting to Recovery mode by default, hold down the <span style="color: #990000;">ESC</span> and <span style="color: #990000;">Refresh</span> (F3) keys and press the <span style="color: #990000;">Power</span> button to get into Recovery mode.
    3. > Wait for the Chromebook to boot up from the flash drive.
    4. > Follow the instructions that appear on the screen.
    5. > On successful installation of the Chrome operating system, you will be prompted to remove the USB flash drive or SD card.
    6. > Remove the USB flash drive or SD card when prompted, and your Chromebook will automatically restart.
    
    </div>

### Restore the default BIOS with stock settings

1. Remove the write-protect screw inside the Chromebook. Recall from when you did this to set SeaBIOS as default originally, it is screw 7 here: [http://www.chromium.org/\_/rsrc/1381990807648/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook/c720-chromebook-annotated-innards.png ](http://www.chromium.org/_/rsrc/1381990807648/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook/c720-chromebook-annotated-innards.png)Note: Putting the cover back on with just screw 6 in should be enough to boot and complete the rest of the needed steps before putting the write-protect screw back in.
2. Switch the Chromebook to Developer mode: First hold down the <span style="color: #990000;">ESC</span> and <span style="color: #990000;">Refresh</span> (F3) keys and press the <span style="color: #990000;">Power</span> button to power on the Chromebook. Then at the Recovery screen press <span style="color: #990000;">Ctrl-D</span>. It will ask you to confirm, then reboot into dev-mode.On boot you will see a warning screen and will need to press <span style="color: #990000;">Ctrl-D</span> or wait 30 seconds to continue booting.
3. Start a terminal session. There are two ways to start a terminal session in Chrome OS (in dev-mode): VT-2 or crosh. For our purposes here it does not matter which way is used.  
    <span class="collapseomatic " id="id6361a8ca93932" tabindex="0" title="Get the command prompt through VT-2">Get the command prompt through VT-2</span><div class="collapseomatic_content " id="target-id6361a8ca93932">> #### Get the command prompt through VT-2
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
    
    </div>  
    <span class="collapseomatic " id="id6361a8ca93961" tabindex="0" title="Getting the command prompt through crosh">Getting the command prompt through crosh</span><div class="collapseomatic_content " id="target-id6361a8ca93961">#### Getting the command prompt through crosh
    
    <div>Because you booted into developer mode, you also have an alternate way to get a terminal prompt. I’ll mention this as well, since the alternate shell is a little nicer (in the very least, it keeps your screen from dimming on you), even if it is a little harder to get to. To use this alternate way:</div><div>
    1. Go through the standard Chrome OS login screen (you’ll need to setup a network, etc) and get to the web browser. It’s OK if you login as guest.
    2. Press \[ Ctrl \] \[ Alt \] \[ T \] to get the crosh shell.
    3. Use the `shell` command to get the shell prompt. NOTE: even if you set a password for the `chronos` user, you won’t need it here (though you still need it for `sudo` access)
    
    </div>Note that entering the shell this way doesn’t give you all the instructions that VT-2 does (like how to set your password). You might want to follow the VT-2 steps once just to get the instructions.
    
    If you want to get back to the browser without killing the shell, you can do it with \[ Alt \] \[ Tab \].
    
    **SIDE NOTE**: You can actually create as many shells as you want. Just hit \[ Ctrl \] \[ Alt \] \[ T \] again and a second shell will be opened. You can \[ Alt \] \[ Tab \] between them.
    
    </div>
4. In the terminal open a bash shell with the `shell` command. Then become superuser with `sudo bash`.
5. Run these commands: 
    1. `crossystem dev_boot_usb=1 dev_boot_legacy=1 disable_dev_request=1`
    2. `flashrom --wp-disable`
    3. `set_gbb_flags.sh 0×000<br></br>`(note: if set\_gbb\_flags.sh is not found then try this full path to script: /usr/share/vboot/bin/set\_gbb\_flags.sh 0x000)
    4. `flashrom --wp-enable`
    5. `chromeos-firmwareupdate --sb_extract /tmp`
    6. `flashrom -w /tmp/bios.bin -i RW_LEGACY`
6. Shutdown the Chromebook and replace the write-protect screw. Put the backcover back on with all the screws.
7. The Chromebook should now be returned to stock.

---

## Comments

1. magnus says  
July 4, 2015 at 2:46 pm  

    Per John Lewis’ script, I completely flashed a custom rom such that my chromebook doesn’t get me to the recovery screen after pressing ctrl+D; my SSD is also dedicated entirely to my linux installation. Any ideas on how to get ChromeOs back?  

    - Code Apex says  
    July 7, 2015 at 11:56 am  

        Assuming you already have the made the recovery usb drive and are just having trouble getting into Recovery mode to restore from the usb drive (Restoring Chrome OS step 3 above): Hold down the ESC and Refresh (F3) keys and press the Power button to get into Recovery mode.

2. jesseflb says  
July 8, 2015 at 12:55 am  

    still booting to my Linux installation which is Eos i have already made a recovery drive but when i insert it and power on with the escape and f3 keys held down only the seabios come up then my linux installation ..no chrome os recovery….is there any other work around to this? i am actually removing my linux installation cause i have an issue with my wireless not working anymore just started one day like that ive sen some people with the same problem and there’s no permanent fix only temporary……but switching back solves it and everything workss as new again.

    - jesseflb says  
    July 8, 2015 at 12:57 am  

        btw, i am using an acer c720…… i greatly aprreciate any help you can offer

        - Code Apex says  
	July 14, 2015 at 10:06 am  

            Try booting normally but tap the space bar as its booting. On my C720 right before seabios it briefly shows a screen that says OS verification is off and says to press space to enable but it only shows for 1-2 seconds before jumping to seabios.

3. Brady Osborne says  
August 11, 2015 at 6:02 pm  

    I think my Chromebook is completely shot. I don’t see the recovery mode option at all, although I can boot into Linux. But how do I run these commands since they aren’t available in my distro?

    - Code Apex says  
    August 17, 2015 at 9:40 pm  

        Where are you getting stuck and are you using a C720?  
        If you have the usb recovery drive setup and are trying to get the Chromebook to recover from it:  
        You need to get to the recovery screen and press ctrl+d to start the recovery with the usb drive plugged in.  
        If you are having trouble getting to the recovery screen try the following:  
        1) Press and hold the Esc + Refresh keyboard icon keys.  
        2) Press the Power button, then let go.  
        3) Continue holding the Esc + Refresh keyboard icon keys until the “Chrome OS is missing or damaged” message appears, then let go.

        Some Chromebooks use a special button to enter recovery mode. Refer to your Chromebook’s user manual to find the location of the button.

        If the above does not work try tapping the space bar while booting to re-enable OS verification.  
        (On my C720 right before seabios it briefly shows a screen that says OS verification is off and says to press space to enable but it only shows for 1-2 seconds before jumping to seabios).  
        When OS verification is enabled then you should be able to enter recovery mode as instructed above.

    - Phil says  
    February 2, 2016 at 5:43 am  

        Hi, i know this was a long time ago but did ever get this sorted? im in the same position as you were. I have seabios as default and running ubuntu 14.04 … I cannot get to recovery at all on boot, not even sure its still there. always just boot to seabios menu screen then into ubuntu

        did you find a way to get your c720 back to chromeOS ?

        any guides would be helpful

        Thanks

4. Chris Taylor says  
November 26, 2015 at 4:47 am  

    Hi,  
    So, I’m in a similar position to the above; I had a linux distort on the SSD of the Acer c720, now I want to get back back to having ChromeOS installed. I have a recovery drive, and this is where I am at:

    1. Boot into recovery mode and install – done.

    2. ChromeOS verifies the media and eventually arrives at the “System Recovery Complete” screen – done.

    On the restart, regardless of whether or not I hit the space bar to re-enable OS verification, it is ignored and the Seagate BIOS screen tries to boot up from SSD (it is unclear what it tries to boot as the text flashes past) then the screen goes black.

    Have you got any suggestions please?

    Thanks,
    Chris.

5. JD says  
January 4, 2016 at 6:45 am  

    Install Ubuntu Server on my C720 almost 2 yrs ago. Been happy until the SSD died.
    Never changed the BIOS, except to enable USB boot.
    I have the recovery screen, but every attempt fails at the validation/verification step (around 99%) regardless of how the flash storage was made (Linux script, dd, or Chome browser from Windows). Tried 5 different storage devices – SDHC, USB, USB, USB, SDHC … avoided SanDisk.

    Never undid the write-protect screw, until the keyboard needed to be replaced. That’s what started all of this – swapping a broken keyboard out. Put the write protect screw back before attempting any boot. Basically, that screw was only missing when power was removed.

    Completely stuck here. Empty SSD, Recovery media seems to be created fine, but never works.
    Any leads before I chuck chuck it?

    - Code Apex says  
    January 19, 2016 at 10:37 am  

        For the verification error try using the latest version of the chromebook recovery app to create the media:  
        <https://chrome.google.com/webstore/detail/chromebook-recovery-utili/jndclpdbaamdhonoechobihbbiimdgai>  
        Earlier versions had a bug where it would fail on verification:  
        <https://code.google.com/p/chromium/issues/detail?id=352442>

        Also is the replacement SSD a SATA 42mm M.2 / NGFF not larger than 128GB? I heard there are issues if trying to put anything larger than 128GB into the C720. Check here to make sure the replacement SSD is compatible with the C720:  
        <http://www.mydigitaldiscount.com/m.2-ngff-ssd-compatibility-list.html>

6. peter says  
February 11, 2016 at 8:13 am  

    Same issue here. on an ACER Chromebook 15, CB5-571
    I flashed SeaBIOS, WIndows 10 immediately boots.
    I do have a very recent Backup of ChromeOS on an DD-Card, but didn’t manage to geet a Backup of the Original BIOS.

    There;s NO WAY I can get into BIOS Recovery, I think because the Seabios has completely overwritten the original IOS (and therefore, not showing any Chrome Recovery/Developer options.

    1. I can’t seem to find an original ChromeOS/Coreboot Acer CB5-571 BIOS
    IS someone able to post an original BIOS-version?

    2. How to flash this original BIOS to completely replace the SeaBIOS from within Windows?

    I’ve been reading and searching a lot, founs some info with Linux commands using Flashrom, maybe someone knows another way of going back to original ChromeOS?

7. Jacob Meza says  
March 15, 2016 at 12:55 pm  

    I have a toshiba chromebook 2. I replaced the bios and wrote over the entire hard drive with linux.

    The recovery usb process is freezing when I get to ‘syncing your preferences’.

    Any help?

8. ShinyCanoe says  
May 24, 2016 at 3:18 pm  

    I got this to work on an Acer C720. What I did was install Chromium OS on the machine, then followed the steps to “Restore the default BIOS with stock settings.” Once the bios was restored I could use the repair usb to reinstall factory chrome is. Chromium OS is the open source equivalent to ChromeOS and it can be flashed to a USB stick. As long as you can still boot from a USB with Chromium on it, you can follow this tutorial and put your computer back good as new. Instructions for installing Chromium can be found here: <http://arnoldthebat.co.uk/wordpress/chromium-os/>

9. jimmy says  
September 10, 2016 at 4:26 pm  

    how do i backup bios onto sd card and restore it at a later point ?

10. sanjay says  
September 11, 2016 at 11:49 am  

    Hi ShinyCanoe,
    I was able to start Chromium OS from SD card, I can see Chromium OS login. what steps you followed to “Restore default BIOS with stock settings…”

11. Ando says  
October 20, 2016 at 6:43 am  

    Thanks for the guide! Worked perfectly 🙂

12. Barry says  
January 6, 2017 at 11:20 pm  

    Thanks for this detailed guide.
    Worked perfectly on my C720.

13. tuxnet says  
August 14, 2017 at 11:04 am  

    Thank you.

    Work like a charm.
