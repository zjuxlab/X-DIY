-
# USB Configuration

##  Device Tree

- [USB Configuration](#usb-configuration)
  - [Device Tree](#device-tree)
    - [Edit](#edit)
    - [Explanation](#explanation)


### Edit

```sh
gedit /project-spec/meta-user/recipes-bsp/device-tree/files/system-user.dtsi
```

Add:
```dts
/include/ "system-conf.dtsi"
/ {
    usb_phy0:usb_phy@0{
        compatible = "ulpi-phy";
        #phy-cells = <0>;
        reg = <0xe0002000 0x1000>;
        view-port = <0x0170>;
        drv-vbus;
    };
};

&usb0{
    compatible = "xlnx,zynq-usb-2.20a", "chipidea, usb2";
    clocks = <&clkc 28>;
    dr_mode = "host";
    interrupt-parent = <&intc>;
    interrupts = <0 21 4>;
    reg = <0xe0002000 0x1000>;
    usb-phy = <&usb_phy0>;
};
```
Notice that the usb_phy0 node locates on the first layer beneath the root, while the &usb0 is outside the root node.

Then build the kernel.

```sh
petalinux-build
```

### Explanation

The built DTS (decompiled from system.dtb) is like:
```dts
/{
    axi {
        //ZYNQ USB Controller at 0xE0002000, refer to UG585 Table 4-6.
        usb@e0002000 {
            //USB2.0 controller built by Xilinx, v2.20a. Also compatible of chipidea usb2.
            compatible = "xlnx,zynq-usb-2.20a\0chipidea,usb2";

            //Enabled.
            status = "okay";

            //Clock is provided by device with phandle 0x01.
            // Use the 29th(0x1c+1) clock signal of the clock source.
            clocks = <0x01 0x1c>;

            //Interrupt of this device is handled by the Zynq GIC, phandle 0x04.
            interrupt-parent = <0x04>;
            interrupts = <0x00 0x15 0x04>;

            //Address space starts at 0xe0002000, the size is 0x1000.
            reg = <0xe0002000 0x1000>;

            //Accept PHY of ULPI protocol.
            phy_type = "ulpi";

            //
            usb-reset = <0x09 0x2e 0x00>;

            //Runs as a USB host.
            dr_mode = "host";

            //USB PHY's phandle is 0x0a.
            usb-phy = <0x0a>;

            //This controller's phandle is 0x33.
            phandle = <0x33>;
        };
    };
};

usb_phy@0 {
    //Define the PHY type. A ULPI PHY driver will be linked.
    compatible = "ulpi-phy";

    //Cells number of PHY specifier (0 means one?).
    #phy-cells = <0x00>;

    //Redirect to the USB controller?
    reg = <0xe0002000 0x1000>;

    //Visit 0xe0002170 to access the PHY registers.
    view-port = <0x170>;

    //Enable voltage output.
    drv-vbus;

    //Register phandle.
    phandle = <0x0a>;
};
```

The above-mentioned information is provided by the hardware manufacture (Xilinx in this case) to the kernel. THen it knows where to find the PHY, what kind of driver should be linked, etc.