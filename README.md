# H3_ALLWINNER
HOW TO BUILD UBOOT AND LINUX FOR H3 ALLWINER CPU
1 Introduction to Linux Mainline
Linux kernel distributions have several lines. The Mainline is maintained by Linus. Other variants and distributions are maintained by various groups and organizations. All the variants and distributions are submitted to Linux and will be merged to the Mainline. The latest mainline of U-boot and Linux kernel already has support for H3/H5 SoC. FriendlyElec customized the latest mainline u-boot and Linux kernel and made that u-boot and kernel work for all FriendlyElec's H5/H3/H2+ boards.
Note: all the operations and instructions listed in this topic only apply to Linux-4.14.y. Don't apply them to H5-Linux-3.10/H3-Linux-3.4 .

2 Features
Sunxi-mainline-kernel-4.14-features

3 How to Compile Linux-4.14 BSP
3.1 How to Compile Linux-4.14 BSP for H3/H2+
3.1.1 Install Cross Compiler
Visit here download link and enter the toolchain directory to download the cross compiler:arm-cortexa9-linux-gnueabihf-4.9.3.tar.xz and extract it:

$ mkdir -p /opt/FriendlyARM/toolchain
$ tar xf arm-cortexa9-linux-gnueabihf-4.9.3.tar.xz -C /opt/FriendlyARM/toolchain/
Add the compiler's path to the "PATH" variable by appending the following lines in the ~/.bashrc file:

$ export PATH=/opt/FriendlyARM/toolchain/4.9.3/bin:$PATH
$ export GCC_COLORS=auto
Run the ~/.bashrc script to make the changes in effect immediately in your working shell. Attention: there is a space after ".":

$ . ~/.bashrc
This is a 64-bit compiler and it cannot run on a 32-bit Linux. You can check whether or not your compiler is setup correctly by running the following commands:

$ arm-linux-gcc -v
gcc version 4.9.3 (ctng-1.21.0-229g-FA)
3.1.2 Compile U-boot
Download the U-boot source code and enter the master-h3 branch:

$ git clone https://github.com/friendlyarm/u-boot.git -b sunxi-v2017.x --depth 1
Compile U-boot:

$ apt-get install swig python-dev python3-dev
$ make nanopi_h3_defconfig ARCH=arm CROSS_COMPILE=arm-linux-
$ make ARCH=arm CROSS_COMPILE=arm-linux-
Although this configuration file was originally made for the NanoPi M1 Plus it works for other H3 based boards too. After compilation is done successfully a "u-boot-sunxi-with-spl.bin" file will be generated.
Update the u-boot in your installation SD card in a PC host:

$ dd if=u-boot-sunxi-with-spl.bin of=/dev/sdX bs=1024 seek=8
$ sync && eject /dev/sdX
Please replace "/dev/sdx" with your TF card's device name recognized by your system.
The "sync" command writes data to your TF card. The "eject" command ejects your TF card safely.
When your system boots from an SD card you can use the "scp" command to copy the "u-boot-sunxi-with-spl.bin" file to your board first and then use the "dd" command to update the U-boot in the SD card:

$ scp u-boot-sunxi-with-spl.bin root@192.168.1.230:/root/
$ dd if=/root/u-boot-sunxi-with-spl.bin of=/dev/mmcblk0 bs=1024 seek=8

If your board has eMMC flash and boots from eMMC you can use the "scp" command to copy the "u-boot-sunxi-with-spl.bin" file to your board first and then use the "dd" command to update the U-boot in eMMC:

$ scp u-boot-sunxi-with-spl.bin root@192.168.1.230:/root/
$ dd if=/root/u-boot-sunxi-with-spl.bin of=/dev/mmcblk0 bs=1024 seek=8
The boot device's name recognized by system under NanoPi H3/H2+ is always "/dev/mmcblk0".

3.1.3 Compile and Update Linux Kernel
Download Linux Kernel Source Code:

$ git clone https://github.com/friendlyarm/linux.git -b sunxi-4.14.y --depth 1
Compile Linux Kernel:

$ cd linux
$ touch .scmversion
$ make sunxi_defconfig ARCH=arm CROSS_COMPILE=arm-linux-
$ make zImage dtbs ARCH=arm CROSS_COMPILE=arm-linux-
If your compilation is successful a zImage will be generated under "arch/arm/boot/" and a dtb file will be generated under "arch/arm/boot/dts/".

If your SD card's boot partition is mounted at "/media/SD/boot/" you can update its zImage and dtb files by running the following commands:

$ cp arch/arm/boot/zImage /media/SD/boot/
$ cp arch/arm/boot/dts/sun8i-*-nanopi-*.dtb /media/SD/boot/
Compile and Update Modules:

$ cd linux
$ make modules ARCH=arm CROSS_COMPILE=arm-linux-
If your SD card's rootfs partition is mounted at "/media/SD/rootfs/" you can update its modules by running the following commands:

$ cd linux
$ make modules_install INSTALL_MOD_PATH=/media/SD/rootfs/ ARCH=arm CROSS_COMPILE=arm-linux-
3.2 How to Compile Mainline BSP for H5
3.2.1 Install Cross Compiler
Visit here download link and enter the toolchain directory to download the cross compiler gcc-linaro-6.3.1-2017.02-x86_64_aarch64-linux-gnu.tar.xz and extract it:

$ mkdir -p /opt/FriendlyARM/toolchain
$ tar xf gcc-linaro-6.3.1-2017.02-x86_64_aarch64-linux-gnu.tar.xz -C /opt/FriendlyARM/toolchain/
Add the compiler's path to the "PATH" variable by appending the following lines in the ~/.bashrc file:

$ export PATH=/opt/FriendlyARM/toolchain/gcc-linaro-6.3.1-2017.02-x86_64_aarch64-linux-gnu/bin:$PATH
$ export GCC_COLORS=auto
Run the ~/.bashrc script to make the changes in effect immediately in your working shell. Attention: there is a space after ".":

$ . ~/.bashrc
You can check whether or not your compiler is setup correctly by running the following commands:

$ aarch64-linux-gnu-gcc -v
gcc version 6.3.1 20170109 (Linaro GCC 6.3-2017.02)
3.2.2 Compile U-boot
Download the U-boot source code and enter the master-h3 branch:

$ git clone https://github.com/friendlyarm/u-boot.git -b sunxi-v2017.x --depth 1
Compile U-boot:

$ apt-get install swig python-dev python3-dev
$ make nanopi_h5_defconfig CROSS_COMPILE=aarch64-linux-gnu-
$ make CROSS_COMPILE=aarch64-linux-gnu-
The "nanopi_h5_defconfig" works for all existing FriendlyElec's H5 based boards.

Update U-boot on SD Card:

$ dd if=spl/sunxi-spl.bin of=/dev/sdX bs=1024 seek=8
$ dd if=u-boot.itb of=/dev/sdX bs=1024 seek=40
Note: you need to replace "/dev/sdx" with the device name in your system.

3.2.3 Compile Linux Kernel
Download Linux Kernel Source Code:

$ git clone https://github.com/friendlyarm/linux.git -b sunxi-4.14.y --depth 1
Compile and Update Linux Kernel:

$ cd linux
$ touch .scmversion
$ make sunxi_arm64_defconfig ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu-
$ make Image dtbs ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu-
If your compilation is successful an Image will be generated under "arch/arm64/boot/" and a dtb file will be generated under "arch/arm64/boot/dts/allwinner/".

If your SD card's boot partition is mounted at "/media/SD/boot/" you can update its Image and dtb files by running the following commands:

$ cp arch/arm64/boot/Image /media/SD/boot/
$ cp arch/arm64/boot/dts/allwinner/sun50i-h5-nanopi*.dtb /media/SD/boot/
Compile and Update Modules:

$ cd linux
$ make modules ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu-
If your SD card's rootfs partition is mounted at "/media/SD/rootfs/" you can update its modules by running the following commands:

$ cd linux
$ make modules_install INSTALL_MOD_PATH=/media/SD/rootfs/ ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu-
