# Device Tree

- [Device Tree](#device-tree)
  - [List of Argots](#list-of-argots)
  - [Introduction](#introduction)
  - [Get a DTS to analyze](#get-a-dts-to-analyze)
  - [Format](#format)


## List of Argots
 - **DTS**: Device Tree Source
 - **DTC**: Device Tree Complier
 - **DTB**: Device Tree Blob

## Introduction

A device tree contains vital information of peripherals, like the base address of an Memory-Mapped I/O device. It is built by hardware supplier, usually in ANSII .dts/.dtsi format, then compiled into binary .dtb file by DTC. During boot sequence, the kernel reads the DTB and configures its peripheral controllers.


## Get a DTS to analyze

Under /image/linux/ of any built Petalinux project, you may run:

        dtc -I dtb -O dts -o system.dts system.dtb

To decompile the DTB into DTS.

## Format

We begin with an example of a USB communication between Petalinux kernel on ZYNQ SoC and a USB hard drive. The USB part of the DTS looks like:

```dts
/dts-v1/;

/ {
    #address-cells = <0x01>;
    #size-cells = <0x01>;
    compatible = "xlnx,zynq-7000";

    ...

    axi {
        u-boot,dm-pre-reloc;
        compatible = "simple-bus";
        #address-cells = <0x01>;
        #size-cells = <0x01>;
        interrupt-parent = <0x04>;
        ranges;
        phandle = <0x15>;

        ...

        usb@e0002000 {
            compatible = "xlnx,zynq-usb-2.20a\0chipidea,usb2";
            status = "okay";
            clocks = <0x01 0x1c>;
            interrupt-parent = <0x04>;
            interrupts = <0x00 0x15 0x04>;
            reg = <0xe0002000 0x1000>;
            phy_type = "ulpi";
            usb-reset = <0x09 0x2e 0x00>;
            dr_mode = "host";
            usb-phy = <0x0a>;
            phandle = <0x33>;
        };
    };

    usb_phy@0 {
        compatible = "ulpi-phy";
        #phy-cells = <0x00>;
        reg = <0xe0002000 0x1000>;
        view-port = <0x170>;
        drv-vbus;
        phandle = <0x0a>;
    };

    ...

    __symbols__ {
        usb0 = "/axi/usb@e0002000";
        ...
    }
};
```

A device tree is literally built in tree structure, and the basic element is a **node**. A node comprises a **label**, some **properties** and a **name** in the format of strings@base_address. Together they are held up like:

```
<label>:<name> {
    <property 1>
    <property 2>
    ...
    <property n>
}
```

Labels are optional, automatically generated DTS like the one above may not use it. Instead it defines an alias or symbol. After defining the symbol *usb0*, you may refer to this node as *&usb0*, then edit its properties or append some **after the root node ends**:
```dts
\{
    usb@e0002000 {
        compatible = "xlnx,zynq-usb-2.20a\0chipidea,usb2"
    };

    __symbols__ {
        usb0 = "/axi/usb@e0002000";
    };
};
&usb0 {
    dt_mode = "host";
};
```

 In addition to define symbols, it's equivalent to use a label when declaring the usb@e0002000 node, like:
```dts
usb0:usb@e0002000 {
    ...
}
```

