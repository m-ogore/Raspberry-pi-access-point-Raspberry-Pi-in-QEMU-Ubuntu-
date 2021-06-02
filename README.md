Raspberry-Pi-in-QEMU-Ubuntu-
QEMU is an open source emulator that can emulate operating systems from all architectures, including ARM, the architecture of the Raspberry Pi. You can even emulate it on an x86 PC

1.Open up a terminal and create a folder for the virtual machine:

~$ mkdir rpi-vm && cd rpi-vm

2.Download the QEMU kernel adapted for Raspberry Pi images
run:

~$ git clone https://github.com/dhruvvyas90/qemu-rpi-kernel.git

3.Download and extract Raspberry Pi OS Lite image to the folder. Link for download is :
https://www.raspberrypi.org/software/operating-systems/#raspberry-pi-os-32-bit

4.The OS images are very small and only really contain space for the operating system itself.  So, let’s add 4GB of space to the image.Run:

~$ dd if=/dev/zero bs=1M count=4096 >> 2020-08-20-raspios-buster-armhf-lite.img

Note: Replace 2020-08-20-raspios-buster-armhf-lite.img with whatever your image is called.

5. Mount the image as a loopback device. Run:
~$ sudo losetup -f -P --show 2020-08-20-raspios-buster-armhf-lite.img

6.Now run gparted /dev/loopX Replace “X” with a number. Right click on the ext4 partition and expand it to all of the remaining space. Click apply and then close.

7. Change the fstab so the OS runs well with the kernel. Let’s mount the ext4 root partition. run:
~$ sudo mount -o rw /dev/loopXp2 /mnt
8.Now, let’s edit the fstab file: Replace all mentions of mmcblk0p with sda and save
~$ sudo nano /mnt/etc/fstab
9. For the image to be compatible with the QEMU VM and kernel, we must also edit another file (Comment out the line and save the file). Run:
~$ sudo nano /etc/ld.so.preload
10.Unmount with the following commands:
~$ sudo umount /mnt
sudo losetup -d /dev/loopX
Now, run the VM with the following command:
~$ qemu-system-arm -M versatilepb -cpu arm1176 -m 256 -kernel qemu-rpi-kernel/kernel-qemu-4.19.50-buster -hda 2020-08-20-raspios-buster-armhf-lite.img -append "dwc_otg.lpm_enable=0 root=/dev/sda2 console=tty1 rootfstype=ext4 elevator=deadline rootwait" -dtb qemu-rpi-kernel/versatile-pb-buster.dtb -no-reboot -serial stdio
