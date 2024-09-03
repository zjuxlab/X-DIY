# Digilent HDMI IP

- [Digilent HDMI IP](#digilent-hdmi-ip)
- [Rollback the Linux-Xlnx Kernel](#rollback-the-linux-xlnx-kernel)
  - [Download or Clone](#download-or-clone)
  - [Fix](#fix)
- [Configure Petalinux (2023.1)](#configure-petalinux-20231)
  - [Use Local Kernel](#use-local-kernel)
  - [Disable Sanitary Check](#disable-sanitary-check)
  - [Add Digilent Drivers as Custom Modules](#add-digilent-drivers-as-custom-modules)
- [Edit the Driver](#edit-the-driver)
- [Device Tree and Final Build](#device-tree-and-final-build)

Digilent (not sure what they are, seems to be some sort of partner of Xilinx) provided a RGB to DVI Soft IP Core, containing a dynamic clock generator and a rgb2dvi converter. I've used them to display HDMI colorbars fully baesd on PL, it runs fine. But since the Linux graphics system is considerably complex (AXI stream, VDMA, DRM stuffs), it needs drivers from Digilent.

However the last update of those drivers happened in 2017. After that when the [Linux kernel](https://www.kernel.org/) upgraded to v5.0 they removed drmP.h which the Diligent drivers rely on. Xilinx also demolished the /driver/gpu/drm/xilinx folder of [linux-xlnx](https://github.com/Xilinx/linux-xlnx), where the drivers should be put. There's literally no answer from Digilent, they seems to have done absolutely nothing. In there [own version of kernel](https://github.com/Digilent/linux-digilent) there's also no trace of the drivers.

Consequently, in the year of 2024, to build the Zynq board compatible with HDMI interface, the options are:

- Use Xilinx official IP Core [HDMI 1.4/2.0 Transmitter Subsystem](https://docs.amd.com/r/en-US/ug1449-multimedia/HDMI-Transmitter-Subsystem) alternatively. However it's not free, neither can you purchase online like Amazon.

- Use off-chip PHY like ADI ADV7511. Xilinx implement this solution on their evalution boards. ADI provides HDL reference designs, drivers and even a custom kernel on their [wiki](https://wiki.analog.com/resources/fpga/xilinx/kc705/adv7511#downloads). But I haven't seen on the Internet that someone has built a custom board with that and it worked.

- Rollback the kernel used by Petalinux so that the ancient Digilent IP cores and drivers still work. Some tinkers on both the driver and the kernel are necessary, but it's not as horrible as I thought to amend the kernel, so it's farily worthy.

# Rollback the Linux-Xlnx Kernel

## Download or Clone
v4.9 (2019)

## Fix
linux/driver/gpu/drm/drm_file.h

'extern'thing

# Configure Petalinux (2023.1)

## Use Local Kernel
path, license md5

## Disable Sanitary Check
/project-spec/meta-user/recipe-kernel/linux/.config

## Add Digilent Drivers as Custom Modules
create module

copy files

petalinux-config -c kernel

petalinux-config -c rootfs

# Edit the Driver
replace obsolute functions

# Device Tree and Final Build
check the forum about the device tree.