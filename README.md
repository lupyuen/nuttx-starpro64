# Apache NuttX RTOS on StarPro64 RISC-V SBC (ESWIN EIC7700X)

Work in progress...

```bash
git clone https://github.com/lupyuen2/wip-nuttx nuttx --branch starpro64
git clone https://github.com/lupyuen2/wip-nuttx-apps apps --branch starpro64
cd nuttx
tools/configure.sh milkv_duos:nsh

## Build the NuttX Kernel and Apps
make -j
make -j export
pushd ../apps
./tools/mkimport.sh -z -x ../nuttx/nuttx-export-*.tar.gz
make -j import
popd

## Generate Initial RAM Disk
## Prepare a Padding with 64 KB of zeroes
## Append Padding and Initial RAM Disk to NuttX Kernel
genromfs -f initrd -d ../apps/bin -V "NuttXBootVol"
head -c 65536 /dev/zero >/tmp/nuttx.pad
cat nuttx.bin /tmp/nuttx.pad initrd \
  >Image

## Copy NuttX Image to TFTP Server
scp Image tftpserver:/tftpboot/Image-starpro64
ssh tftpserver ls -l /tftpboot/Image-starpro64

## In U-Boot: Boot NuttX over TFTP
## setenv tftp_server 192.168.31.10 ; dhcp ${kernel_addr_r} ${tftp_server}:Image-starpro64 ; tftpboot ${fdt_addr_r} ${tftp_server}:jh7110-star64-pine64.dtb ; fdt addr ${fdt_addr_r} ; booti ${kernel_addr_r} - ${fdt_addr_r}
```

NuttX boots a tiny bit on StarPro64 yay!

```text
Boot HART Debug Triggers  : 4 triggers
Boot HART MIDELEG         : 0x0000000000002666
Boot HART MEDELEG         : 0x0000000000f0b509

Hardware Feature[7C1]: 0x4000
Hardware Feature[7C2]: 0x80
Hardware Feature[7C3]: 0x104095c1be241
Hardware Feature[7C4]: 0x1d3ff
ll

U-Boot 2024.01-gaa36f0b4 (Jan 23 2025 - 02:49:59 +0000)

CPU:   rv64imafdc_zba_zbb
Model: ESWIN EIC7700 EVB
DRAM:  32 GiB (effective 16 GiB)
llCore:  143 devices, 31 uclasses, devicetree: separate
Warning: Device tree includes old 'u-boot,dm-' tags: please fix by 2023.07!
MMC:   sdhci@50450000: 0, sd@50460000: 1
Loading Environment from SPIFlash... SF: Detected w25q128fw with page size 256 Bytes, erase size 4 KiB, total 16 MiB
OK
[display_init]Eswin UBOOT DRM driver version: v1.0.1
In:    serial,usbkbd
Out:   vidconsole,serial
Err:   vidconsole,serial
Success to initialize SPI flash at spi@51800000
Bootspi flash write protection enabled
Get board info from flash
ERROR: There is no valid hardware board information!!!
Cpu volatge need boost above 1.6 Ghz!
sdhci_transfer_data: Transfer data timeout
Low power features will not be supported!
Net:   eth0: ethernet@50400000
Working FDT set to ed4ecb90
starting USB...
Bus usb1@50490000: Register 2000140 NbrPorts 2
Starting the controller
USB XHCI 1.10
scanning bus usb1@50490000 for devices... 2 USB Device(s) found
       scanning usb for storage devices... 0 Storage Device(s) found
No SATA device found!
Autoboot in 5 seconds
ethernet@50400000 Waiting for PHY auto negotiation to complete........ done
BOOTP broadcast 1
BOOTP broadcast 2
*** Unhandled DHCP Option in OFFER/ACK: 43
*** Unhandled DHCP Option in OFFER/ACK: 43
DHCP client bound to address 192.168.31.194 (815 ms)
Using ethernet@50400000 device
TFTP from server 192.168.31.10; our IP address is 192.168.31.194
Filename 'Image-starpro64'.
Load address: 0x84000000
Loading: #################################################################
         #################################################################
         #################################################################
         ############################################################
         1.2 MiB/s
done
Bytes transferred = 3738129 (390a11 hex)
Using ethernet@50400000 device
TFTP from server 192.168.31.10; our IP address is 192.168.31.194
Filename 'jh7110-star64-pine64.dtb'.
Load address: 0x88000000
Loading: ####
         1.2 MiB/s
done
Bytes transferred = 50235 (c43b hex)
Working FDT set to 88000000
Moving Image from 0x84000000 to 0x80200000, end=80407000
## Flattened Device Tree blob at 88000000
   Booting using the fdt blob at 0x88000000
Working FDT set to 88000000
   Using Device Tree in place at 0000000088000000, end 000000008800f43a
Working FDT set to 88000000

Starting kernel ...

123Hello NuttX!
1      
```