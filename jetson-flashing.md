This tool makes use of USB mass storage during flashing; therefore,
automount of new external storage device needs to be disabled temporarily
during flashing. On most distributions of Debian-based Linux, you can do this
using the following command:

```bash
systemctl stop udisks2.service
```

Run this script to install the right dependencies:

```bash
sudo tools/l4t_flash_prerequisites.sh # For Debian-based Linux
```

Use Gparted to find the Total Sectors and Sector Size
eg: `512GB Nvme -> num_sectors: 1000215216, sector_size: 512`

```bash
sudo ./tools/kernel_flash/l4t_initrd_flash.sh \
-c ~/Downloads/flash_l4t_nvme.xml \
-S 450GiB \
--external-device nvme0n1 \
jetson-xavier-nx-devkit-emmc \
external
```

lsusb manufacture IDs
For Jetson Nano: 0955:7f21 NVidia Corp
For Jetson Xavier NX: 0955:7e19 NVidia Corp
For Jetson TX2 NX: 0955:7c18 NVidia Corp

To enter recovery mode, you need to connect FC REC and GND using jumpers.
For A203v2 carrier board PIN3:Recovery and PIN4:GND of 14pin(W7 port) pin header

Wiki:https://wiki.seeedstudio.com/reComputer_A203_Flash_System/

Merge custom BSP files
https://files.seeedstudio.com/wiki/A203_V.2/203_jp5.0.2.zip

```bash
lsusb

Bus <bbb> Device <ddd>: ID 0955: <nnnn> Nvidia Corp.

Where:
    <bbb> is any three-digit number
    <ddd> is any three-digit number
    <nnnn> is a four-digit number that represents the type of your Jetson module:

        7023 for Jetson AGX Orin    (P3701-0000 Developer Kit module)
        7223 for Jetson AGX Orin    (P3701-0004 with 32GB)
        7323 for Jetson Orin NX     (P3767-0000 with 16GB)
        7423 for Jetson Orin NX     (P3767-0001 with 8GB)
        7523 for Jetson Orin Nano   (P3767-0003 with 8GB)
        7e19 for Jetson Xavier NX   (P3668-0000 development m)
        7e19 for Jetson Xavier NX   (P3668-0001)
        7019 for Jetson AGX Xavier  (P2888-0001 with 16GB)
        7019 for Jetson AGX Xavier  (P2888-0004 with 32GB)
        7019 for Jetson AGX Xavier  (P2888-0005 with 64GB)
```

|                                  | BOARDID  | BOARDSKU  | TegraID |
| -------------------------------- | -------- | --------- | ------- |
| jetson-agx-xavier-industrial     | 2888     | 0008      | 0x19    |
| jetson-xavier-nx-devkit-tx2-nx   | 3636     | 0001      | 0x18    |
| clara-agx-xavier-devkit          | 3900     | 0000      | 0x19    |
| jetson-xavier-nx-devkit          | 3668     | 0000      | 0x19    |
| jetson-xavier-nx-devkit-emmc     | 3668     | 0001      | 0x19    |
| jetson-nano-devkit               | 3448     | 0000      | 0x21    |
| jetson-nano-devkit-emmc          | 3448     | 0002      | 0x21    |
| jetson-nano-2gb-devkit           | 3448     | 0003      | 0x21    |
| jetson-agx-xavier-devkit (16GB)  | 2888     | 0001      | 0x19    |
| jetson-agx-xavier-devkit (32GB)  | 2888     | 0004      | 0x19    |
| jetson-tx2-devkit                | 3310     | 1000      | 0x18    |
| jetson-tx2-devkit-tx2i           | 3489     | 0000      | 0x18    |
| jetson-tx2-devkit-4gb            | 3489     | 0888      | 0x18    |
| jetson-tx1-devkit                | 2180     | 0000      | 0x21    |
| -------------------------------- | -------- | --------- | ----    |

```bash
sudo ./tools/kernel_flash/l4t_initrd_flash.sh \
--no-flash \
--external-device nvme0n1p1 \
-c ./tools/kernel_flash/flash_l4t_external.xml \
--showlogs \
jetson-xavier-nx-devkit-emmc \
nvme0n1p1
```

Jetson Xavier NX (P3668-0000, for development only)
Jetson Xavier NX (P3668-0001, 8 GB RAM) QSPI_NOR flash_l4t_t194_spi_emmc_p3668.xml  
Jetson Xavier NX 16GB (P3668-0003, 16 GB RAM) QSPI_NOR flash_l4t_t194_spi_emmc_p3668.xml

| Jetson Xavier NX (P3668-0000)               | Development | Jetson Xavier NX reference carrier board (P3509-0000) | jetson-xavier-nx-devkit      | Flashes QSPI-NOR and microSD Card |
| ------------------------------------------- | ----------- | ----------------------------------------------------- | ---------------------------- | --------------------------------- |
| Jetson Xavier NX 8 GB-DRAM (P3668-0001)     | Production  | Jetson Xavier NX reference carrier board (P3509-0000) | jetson-xavier-nx-devkit-emmc | Flashes QSPI-NOR and eMMC         |
| Jetson Xavier NX 16 GB-DRAM (P3668-0003)    |             |                                                       |                              |                                   |
| ------------------------------------------- | ----------- | ----------------------------------------------------- | ------------------------     | --------------------------------- |

## Disable USB auto suspend to avoid recovery reboot errors

sudo -s
echo -1 > /sys/module/usbcore/parameters/autosuspend
