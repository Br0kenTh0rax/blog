---
layout: post
title: Making SecureBoot (UEFI) Friendly Kali Bootable Drive
published: true
---

So I would like to start by saying this was not an easy task as there are not many resources available for this task. When I first started to research this task to be able to boot Kali off a drive while leaving Secure Boot enabled I quickly found that the resources are scarce and not many people have been successful. Most questions/comments lead to people giving up and turning of Secure Boot, however I was determined and on a long night of research I was able to find some pieces that when put together can allow you to install a Secure Bootable Kali. So without further ado lets look at the process.

There are a few key pieces needed to get a bootable device to work with Secure Boot, essentially one of the key components that I found was that the code that is being used to run while using a Secure environment has to be signed by the Microsoft digital key. There happens to be some versions of Grub that are signed by said key and can be used to boot while leaving Secure Boot enabled. In my research I found that there is a version that VladikSS compiled using a version of Grub from a RedHat distro ([https://github.com/ValdikSS/Super-UEFIinSecureBoot-Disk](https://github.com/ValdikSS/Super-UEFIinSecureBoot-Disk)). The second part is having to get Kali to boot from this version of Grub, since Kali wants to install grub on it's own it requires a bit of bullying to get it to work.

Step 1: We begin by collecting the tools and materials needed:
- Flash drive with over 32GB (bigger is better) _**Note I am using a 16GB for tutorial**_
- Second flash drive or bootable device to burn kali installer image on. _**Note I am using a 8GB for tutorial**_
- Super-UEFIinSecureBoot-Disk Minimal([https://github.com/ValdikSS/Super-UEFIinSecureBoot-Disk/releases](https://github.com/ValdikSS/Super-UEFIinSecureBoot-Disk/releases))
- Kali Installer ISO ([https://www.kali.org/get-kali/#kali-bare-metal](https://www.kali.org/get-kali/#kali-bare-metal))
- Etcher ([https://www.balena.io/etcher/](https://www.balena.io/etcher/))
- Rufus ([https://rufus.ie/en/](https://rufus.ie/en/))

Step 2: Extract the files from Super-UEFIinSecureBoot-Disk zip, this will give .img file that will be used in the next step.

Step 3: Install etcher, and then run it to burn the img file to the flash device.
- On the left choose Select from file, and select the "Super-UEFIinSecureBoot-Disk_minimal.img" in the folder you extracted to. 
- Confirm the device in the middle is the device you want to burn the img file to.
- Then click the blue "Flash" button on the right hand side.
![Etcher-Image-Burn](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Etcher.png)

Step 4: Use Rufus to burn kali bootable install on second drive.
- Open Rufus, and on the right find the select box and use that to select the Kali installer iso.
- Confirm that the device is the second drive.
- Change the partition scheme to GPT, and confirm that the target system says UEFI (non CSM).
- Leave the format options to default and click the Start button on the bottom.
![Rufus-Format-Options](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Rufus.png)
- Rufus will ask what mode you want to use, use DD image mode.

Step 5: Insert both devices into the computer.

Step 6: Temporarily disable secure boot on computer, this is necessary to boot the Kali installer flash drive.
- Depending on motherboard you have to get into BIOS/UEFI, typically this can be F10, F2, F12, F1, or DEL keys.
- Reboot computer (or power it on) and press the key from above that gets to the motherboards BIOS/UEFI.
- Once in BIOS/UEFI find the Secure Boot option and disable it.
![Secure-Boot-Option](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Secure Boot.jpg)
- Make sure to apply/save changes before closing BIOS/UEFI.

Step 7: Access the boot menu for the motherboard, this can typically be F11 or F12, though varies per manufacture.
![Boot-Menu-Options](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Boot%20Menu.jpg)
*Note here: Can be a bit hard to distinquish which drive is the Kali installer, if needed you can plug in Kali and boot to it first and then plug in the device with the modified Grub boot.

Step 8: Progress through the Kali installer until you reach the partition section, at this section we have to change a few settings quickly.
- On partition disks we choose Manual.
![manual partition](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Partition1.jpg)
- Then we scroll down until we find out disk with the modified Grub installed, this will have a FAT32 primary with 524 MB and a second free space partition. Select the FREE SPACE partition.
![disk partition screen](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Partition2.jpg)
- Choose "Automatically partition the free space"
![automatically partition](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Partition3.jpg)
- Choose "All files in one partion"
![all files one partition](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Partition4.jpg)
- Finish partition, it will say No EFI partition (We will address this after booting into Kali). For now select "No" and continue.
![Finish partition](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Partition6.jpg)
- On the final partition page, Write changes to disks, click yes and continue.
![Write-changes](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Partition7.jpg)

Step 9: Finish up installation of Kali.
- Will receive an error for installing grub, click continue.
![Failed grub install](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Grub Failed.jpg)
- Click continue on the "installation step failed" page.
![Grub install step failed](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Step Failed.jpg)
- Select the "Continue without boot loader", and then continue.
![Continue without boot loader](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Continue without boot.jpg)
- Finally on "Continue without boot loader", click continue,
![Confirm without boot loader](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/Confirm without boot.jpg)

Step 10: Remove the flash drive with Kali installer. Leave the first flash drive.

Step 11: Re-enable secure boot, done by reversing step 6.

Step 12: Add the device certificate _**This step will need to be done each time this flash drive is connected to a new computer with Secure Boot enabled**_
- With the flash drive connected to the computer, access the boot menu (See step 7) and select the flash drive for boot. This will show a prompt that says "Access Violation" in a message box. 
![UEFI Verification Error](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/UEFI Error.jpg)
- Press OK, then press any key on next page and choose "Enroll cert from disk" menu option. 
![UEFI Press any key](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/UEFI Error 1.jpg)
![UEFI Enroll key from disk](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/UEFI Error 2.jpg)
- Select the drive that you installed grub on.
![UEFI Select Key](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/UEFI Error 3.jpg)
- Select ENROLL_THIS_KEY_IN_MOKMANAGER.cer and confirm certificate enrolling.
![UEFI Enroll This Key](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/UEFI Error 4.jpg)
- On Enroll MOK, click 'continue'.
![Enroll MOK Continue](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/UEFI Error 5.jpg)
- On Enroll Key(s), click 'yes'.
![UEFI Enroll Keys](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/UEFI Error 6.jpg)
- On final screen, choose 'reboot'.
![UEFI MOK Reboot](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/UEFI Error 7.jpg)


Step 13: Temporary changes to Grub bootloader to access Kali install for final steps.
- Access the grub edit menu for either of the current options (Power Off or Reboot) by highlighting one and pressing 'e' on keyboard.
- From the edit menu press 'ctrl' + 'c' on keyboard to open the grub command line.
- In grub command type 'ls' to get list of drives, locate the one that has partitions with msdos in them (in my case it was hd0).
- Type 'ls (hd0,' replacing the 'hd0' part with whichever device yours is but keep the '('. This will give you the UUID of both your grub partition (the fat partition with 8 char UUID) and the Kali install (the ext* partition with long UUID). Write or take a picture of both of these as you'll need them in the next steps.
- Next type 'ls (hd0,5)/boot/', again replacing the 'hd0,5' part with the values you got from the above commands and keeping the parenthesis on both ends. Write or take picture of both the vmlinuz and initrd file names.
![Grub Commands](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/grub commands_LI.jpg)
- Hit 'esc' key on keyboard and we can now edit grub to boot into kali.
- On the grub edit page type the following items and put two spaces before each line. Replace the UUID with your Kali partition UUID and the versions of vmlinuz/initrd with the versions you got from above:
![Grub Changes](https://raw.githubusercontent.com/Br0kenTh0rax/blog/master/_posts/grub temp config_LI.jpg)
	- insmod part_msdos
	- insmod ext2
	- search --no-floppy --fs-uuid --set=root **UUID of your Kali partition**
	- linux /boot/vmlinuz-5.10.0-kali9-amd64 root=UUID=**UUID of your Kali partition** ro  quiet splash
	- initrd /boot/initrd.img-5.10.0-kali9-amd64
- press 'ctrl' + 'x' and your system should start booting into kali where we can make the final changes.

Step 14: Mounting grub partion and adding to fstab.
