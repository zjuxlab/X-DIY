- [Backup Information](#backup-information)
	- [DRM/KMS](#drmkms)
	- [FBDEV](#fbdev)
- [Step-to-step Procedure from Scratch](#step-to-step-procedure-from-scratch)
	- [1. Get Linux Kernel Source](#1-get-linux-kernel-source)
	- [2. Get and Merge the TMDS Driver](#2-get-and-merge-the-tmds-driver)
		- [2.1. (If you haven't done it) Include the Digilent Dynamic Clock Driver](#21-if-you-havent-done-it-include-the-digilent-dynamic-clock-driver)
	- [3. Setup the Petalinux Project](#3-setup-the-petalinux-project)
	- [4. Configure the Device Tree](#4-configure-the-device-tree)
	- [5. Build and Run](#5-build-and-run)
- [Debugging Tools](#debugging-tools)
	- [modetest](#modetest)
- [Possible Problems](#possible-problems)

---

# Backup Information

## DRM/KMS

[A brief description of Linux DRM/KMS by ST](https://wiki.st.com/stm32mpu/wiki/DRM_KMS_overview):

> The DRM/KMS framework is dedicated to the management of the display, graphic and composition subsystems. With the help of other Linux multimedia frameworks and applications, the DRM/KMS framework is typically used:
> - to compose animated contents taking advantages of the hardware acceleration.
> - to control both display interfaces and external displays including their settings (resolution, frequency, multi-screen...).
> - to display this animated contents on display panels or HDMI outputs.

There're some [slides](https://events.static.linuxfound.org/sites/events/files/slides/brezillon-drm-kms.pdf) that give a deeper insight. To me it is just a pipeline consisting:

 - **Framebuffer**: something manipulates with the GPU/VRAM hardware
 - **Planes**: like display layers
 - **CRTC**: that controls the timing and outputs composited planes
 - **Encoder**: that encodes the raw data by certain protocol
 - **Bridge**: functional blocks inserted in the pipeline (often refers to PHY hardwares?)
 - **Connector**: to the display device
 - **Panel**: an abstraction of the display device

Notice that the DRM/KMS pipeline is somehow abstract, e.g. an *encoder* block contains not only the driver, but also hardware resources that the actual video data is flowing through.

In the example of Zynq, Xilinx provides a module `pl_disp` that provides framebuffer, CRTC and planes. I think it corresponds to the `AXI_VTC` and `VDMA`/`frmbuf_rd` IP cores. The hardware that fits the idea of encoder and connector already exists, but cannot be recognized by the Linux system. So what we gonna do is to implement a driver for them.

## FBDEV

Short for Framebuffer Device. The Framebuffer structure is the predecessor of the DRM/KMS, it's not actively maintained nowadays, or somehow obsolete. The X Windows System we will use later, however, is based on fbdev drivers. It confuses me a lot that the X system seems to run well even through I have only built a DRM driver.

I've found [this](https://www.kernel.org/doc/html/v4.14/gpu/drm-kms.html) in the Linux kernel doc:

> Drivers implementing fbdev emulation with the helpers can call drm_fb_helper_hotplug_changed from this hook to inform the fbdev helper of output changes.

From that I would guess that the DRM subsystem is able to feign itself as a fbdev for the old programs.

# Step-to-step Procedure from Scratch

Environment reference:
> **Petalinux** : v2023.1 \
> **linux-xlnx** : v5.4 (xlnx_rebase_v5.4) \
> **OS** : Ubuntu 20.04 LTS \
> **Hardware Platform** : x86_64

## 1. Get Linux Kernel Source
Clone the repository of Linux kernel for Xilinx SoCs.
```sh
git clone https://github.com/Xilinx/linux-xlnx.git
```

Checkout to desired kernel version, v5.4 in this example.
```sh
git checkout xlnx_rebase_v5.4
```

## 2. Get and Merge the TMDS Driver
Clone the repo of my DRM-TMDS driver:

::github{repo="tsaoo/drm-tmds-pl-drv"}
```sh
git clone https://github.com/tsaoo/drm-tmds-pl-drv.git
cd drm-tmds-pl-drv
```

Checkout to MATCH the kernel version selected before.
```sh
# Check the git page for all available version
git checkout v5.4
```

Copy the source files to the Linux kernel. Replace the ${KERNEL} below with the absolute location of the linux-xlnx kernel root directory.
```sh
cp drivers/gpu/drm/xlnx/* ${KERNEL}/drivers/gpu/drm/xlnx/
```

Edit the Kconfig and the Makefile so that the driver can be included when the kernel is compiled.
```sh
nano ${KERNEL}/drivers/gpu/drm/xlnx/Kconfig

# Add the following lines somewhere:
config DRM_TMDS_PL_DRV
	tristate "DRM TMDS on PL"
	depends on DRM_XLNX
	help
	  DRM driver for TMDS pipeline on PL
```
```sh
nano ${KERNEL}/drivers/gpu/drm/xlnx/Makefile

# Append the following line to the end of the makefile:
obj-$(CONFIG_DRM_TMDS_PL_DRV) += drm-tmds-pl-drv.o
```

### 2.1. (If you haven't done it) Include the Digilent Dynamic Clock Driver
Check the Digilent official repo for their dynamic clock IP core:
::github{repo="Digilent/vivado-library"}

```sh
cp drivers/clk/clk-dglnt-dynclk.c ${KERNEL}/drivers/clk/
```
```sh
nano ${KERNEL}/drivers/clk/Kconfig

# Insert the following lines somewhere:
config CLK_DGLNT_DYNCLK
	tristate on ARCH_ZYNQ || MICROBLAZE
	help
		---help---
		Support for the Digilent AXI Dynamic Clock core for Xilinx FPGAs.
```
```sh
nano ${KERNEL}/drivers/clk/Makefile

# Add the this line to the end:
obj-$(CONFIG_CLK_DGLNT_DYNCLK) += clk-dglnt-dynclk.o
```

## 3. Setup the Petalinux Project
Configure Petalinux project to use the modified local Linux kernel.
```sh
petalinux-config
```
Navigate and configure:
> - Linux Components Selection --->
> 	- linux-kernel --->
> 		- (X) ext-local-src
> 	- External linux-kernel local source settings --->
> 		- External linux-kernel local source path = /.../linux-xlnx_rebase_v5.4
> 		- External linux-kernel license checksum = file:///.../linux-xlnx_rebase_v5.4/LICENSES/preferred/MIT;md5=xxxx

It is (might be) optional to designate the license file and its checksum, but I always do so. The MD5 checksum can be obtained by: `md5sum MIT`.

> Don't forget the "file://" at the head of the license file path

Every Petalinux version has a corresponding linux-xlnx kernel version, and there's a version-checking procedure to prevent mismatch. But you CAN build different kernel though, simply disable the checking step.
```sh
## In early Petalinux version the file or directory may not exist, create them anyway
nano ${PROJECT}/project-spec/meta-user/recipes-kernel/linux/linux-xlnx_%.bbappend

## Append this line:
KERNEL_VERSION_SANITY_SKIP="1"
```

Then configure the kernel building procedure to include the TMDS driver.
```sh
petalinux-config -c kernel
```
Navitage and configure:
> - Devices Drivers --->
>   - <*> Digilent axi_dynclk Driver
>   - Graphics support --->
>     - <*> Xilinx DRM KMS Driver
>       - <*> DRM Driver for TMDS on
>       - <*> Xilinx DRM KMS bridge
>       - <*> Xilinx DRM PL display driver
>     - <*> Xilinx DRM VTC Driver

It is not harmful to turn on everything under the Xilinx DRM KMS subsection, and enable `framebuffer device support` under the Graphics support. But I'm not sure whether they are necessary. If you are using kernel version 4.x or before, it is likely that there's a `Xilinx DRM` option in addition to `Xilinx DRM KMS Driver`. It would suggest you disable the former.

## 4. Configure the Device Tree
Edit the device tree referring to `dts_example.dtsi` in the repo.
```sh
nano ${PROJECT}/project-spec/meta-user/recipes-bsp/device-tree/files/system-user.dtsi
```

## 5. Build and Run
Use `petalinux-build` and `petalinux-package` as normal.

---

# Debugging Tools

## modetest
Provided by `libdrm`. It is used to poll the sectors in the DRM/KMS subsystem. `libdrm` is optional in the Petalinux rootfs.
```sh
petalinx-config -c rootfs
```

Navigate and configure:
> - Filesystem Packages --->
>   - X11 --->
>     - base --->
>       - libdrm --->
> 		  - [*] libdrm
>         - [*] libdrm-tests

I also enabled other stuff in libdrm subsection, just in case.

Check a [blog in Chinese](https://blog.csdn.net/u012839187/article/details/103507833) for more info.

Some frequent uses of mine:
```sh
# To poll every part in the DRM subsystem:
modetest -M xlnx

# To test the connector id 30 and crtc id 28 in 1024x768 size and RG24 format:
modetest -M xlnx -s 30@28:1024x768@RG24
```

# Possible Problems
> I remember Petalinux would throw an error when build the kernel of v4.14. Some sorf of xxx_shipped.l file in the kernel must be edited, but I can't tell which by now. Record it if come across again.