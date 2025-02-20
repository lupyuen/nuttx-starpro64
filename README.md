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

# Output Log

NuttX boots a tiny bit on StarPro64 yay!

```text
sdhci_transfer_data: Transfer data timeout
Low power features will not be supported!
Net:   eth0: ethernet@50400000
Working FDT set to ed4ecb90
starting USB...
Bus usb1@50490000: Register 2000140 NbrPorts 2
Starting the controller
USB XHCI 1.10
scanning bus usb1@50490000 for devices... pll config ok
die_num:0,die_ordinal:0
Firmware version:1.4;disable ECC
PHY0 training process:100%
PHY1 training process:100%
DDR type:LPDDR5;Size:32GB,Data Rate:6400MT/s
DDR self test OK

OpenSBI v1.5
   ____                    _____ ____ _____
  / __ \                  / ____|  _ \_   _|
 | |  | |_ __   ___ _ __ | (___ | |_) || |
 | |  | | '_ \ / _ \ '_ \ \___ \|  _ < | |
 | |__| | |_) |  __/ | | |____) | |_) || |_
  \____/| .__/ \___|_| |_|_____/|____/_____|
        | |
        |_|

Platform Name             : ESWIN EIC7700 EVB
Platform Features         : medeleg
Platform HART Count       : 4
Platform IPI Device       : aclint-mswi
Platform Timer Device     : aclint-mtimer @ 1000000Hz
Platform Console Device   : uart8250
Platform HSM Device       : ---
Platform PMU Device       : ---
Platform Reboot Device    : eswin_eic770x_reset
Platform Shutdown Device  : eswin_eic770x_reset
Platform Suspend Device   : ---
Platform CPPC Device      : ---
Firmware Base             : 0x80000000
Firmware Size             : 357 KB
Firmware RW Offset        : 0x40000
Firmware RW Size          : 101 KB
Firmware Heap Offset      : 0x4f000
Firmware Heap Size        : 41 KB (total), 2 KB (reserved), 11 KB (used), 27 KB (free)
Firmware Scratch Size     : 4096 B (total), 416 B (used), 3680 B (free)
Runtime SBI Version       : 2.0

Domain0 Name              : root
Domain0 Boot HART         : 1
Domain0 HARTs             : 0*,1*,2*,3*
Domain0 Region00          : 0x0000000002000000-0x000000000200ffff M: (I,R,W) S/U: ()
Domain0 Region01          : 0x0000000080000000-0x000000008007ffff M: (R,W) S/U: ()
Domain0 Region02          : 0x000000c000000000-0x000000d000000000 M: () S/U: (R,W)
Domain0 Region03          : 0x0000001000000000-0x0000008000000000 M: () S/U: ()
Domain0 Region04          : 0x0000000000000000-0xffffffffffffffff M: () S/U: (R,W,X)
Domain0 Next Address      : 0x0000000080200000
Domain0 Next Arg1         : 0x00000000f8000000
Domain0 Next Mode         : S-mode
Domain0 SysReset          : yes
Domain0 SysSuspend        : yes

Boot HART ID              : 1
Boot HART Domain          : root
Boot HART Priv Version    : v1.11
Boot HART Base ISA        : rv64imafdchx
Boot HART ISA Extensions  : sscofpmf,zihpm,sdtrig
Boot HART PMP Count       : 8
Boot HART PMP Granularity : 12 bits
Boot HART PMP Address Bits: 39
Boot HART MHPM Info       : 4 (0x00000078)
Boot HART Debug Triggers  : 4 triggers
Boot HART MIDELEG         : 0x0000000000002666
Boot HART MEDELEG         : 0x0000000000f0b509

Hardware Feature[7C1]: 0x4000
Hardware Feature[7C2]: 0x80
Hardware Feature[7C3]: 0x104095c1be241
Hardware Feature[7C4]: 0x1d3ff


U-Boot 2024.01-gaa36f0b4 (Jan 23 2025 - 02:49:59 +0000)

CPU:   rv64imafdc_zba_zbb
Model: ESWIN EIC7700 EVB
DRAM:  32 GiB (effective 16 GiB)
Core:  143 devices, 31 uclasses, devicetree: separate
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
ethernet@50400000 Waiting for PHY auto negotiation to complete..... done
BOOTP broadcast 1
BOOTP broadcast 2
*** Unhandled DHCP Option in OFFER/ACK: 43
*** Unhandled DHCP Option in OFFER/ACK: 43
DHCP client bound to address 192.168.31.194 (713 ms)
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
Bytes transferred = 3738121 (390a09 hex)
Using ethernet@50400000 device
TFTP from server 192.168.31.10; our IP address is 192.168.31.194
Filename 'jh7110-star64-pine64.dtb'.
Load address: 0x88000000
Loading: ####
         1.1 MiB/s
done
Bytes transferred = 50235 (c43b hex)
Working FDT set to 88000000
Moving Image from 0x84000000 to 0x80200000, end=84200000
## Flattened Device Tree blob at 88000000
   Booting using the fdt blob at 0x88000000
Working FDT set to 88000000
   Using Device Tree in place at 0000000088000000, end 000000008800f43a
Working FDT set to 88000000

Starting kernel ...

123Hello NuttX!
00ABCnx_start: Entry
uart_register: Registering /dev/console
uart_register: Registering /dev/ttyS0
group_setupidlefiles: ERROR: Failed to open stdin: -22
dump_assert_info: Current Version: NuttX  12.4.0 c04002cfd8 Feb 20 2025 18:23:25 risc-v
dump_assert_info: Assertion failed group_setupidlefiles(): at file: init/nx_start.c:736 task: Idle_Task process: Kernel 0x80201240
up_dump_register: EPC: 00000000802148ce
up_dump_register: A0: 0000000080402920 A1: 00000000000002e0 A2: 000000008021bda8 A3: 0000000000000000
up_dump_register: A4: 0000000000000002 A5: 0000000000000007 A6: 0000000000000009 A7: 0000000000000000
up_dump_register: T0: 00000000802000c0 T1: 0000000000000007 T2: 0000000000000000 T3: 0000000080407af0
up_dump_register: T4: 0000000080407ae8 T5: 0000000000000000 T6: 00000000ed4ec178
up_dump_register: S0: 0000000000000000 S1: 0000000080400e78 S2: 0000000080402b78 S3: 0000000000000000
up_dump_register: S4: 000000008021bda8 S5: 000000008021bd60 S6: 0000000080402b78 S7: 0000000000000004
up_dump_register: S8: 0000000080402b68 S9: 00000000000002e0 S10: 0000000000000000 S11: 0000000000000000
up_dump_register: SP: 0000000080407a10 FP: 0000000000000000 TP: 0000000080400e78 RA: 00000000802148ce
dump_stackinfo: User Stack:
dump_stackinfo:   base: 0x80407010
dump_stackinfo:   size: 00003056
dump_stackinfo:     sp: 0x80407a10
stack_dump: 0x804079d0: 0000000000000009 0000000080223c08 0000000080228638 0000000000000000 0000000000000000 0000000080400e78 0000000080407a10 0000000080214ac2
stack_dump: 0x80407a10: 0000000080201240 0000000000003232 0000000000000000 000000008020dc2e 0000000080400e78 0000000080402920 000000008021bda8 000000008021bd60
stack_dump: 0x80407a50: 00000000000002e0 000000587474754e 0000000000000000 00000000fd7333b8 fffffffffffffff3 0000000000000001 0000000080402b78 2e32310080401030
stack_dump: 0x80407a90: 0000000000302e34 0000000080218482 3230303430630e78 6265462038646663 3532303220303220 323a33323a383120 0000000080210035 000000008021a118
stack_dump: 0x80407ad0: 73697200802088b2 0000000000762d63 ffffffffffffffea 0000000080208b16 0000000080402b78 0000000000000000 0000000000000000 0000000000000000
stack_dump: 0x80407b10: 00000000fd5782d6 0000000000000000 0000000000000000 00000000fd7333b8 fffffffffffffff3 0000000000000001 0000000080402b78 0000000080401030
stack_dump: 0x80407b50: 0000000080400e78 00000000802074c6 0000000080400e78 00000000802013b8 0000000080600000 0000000001400000 0000000080447c00 000000008021bab0
stack_dump: 0x80407b90: 0000000080447c00 0000000080407c00 0000000000000000 000000008020076e 0000000080410609 0000000080200940 0000000000000000 2d00000000000000
stack_dump: 0x80407bd0: 0000000000000000 0000000000000003 00000000fd5782d6 00000000fd757408 0000000000000000 00000000802000a8 0000000000000000 0000000000000000
dump_tasks:    PID GROUP PRI POLICY   TYPE    NPX STATE   EVENT      SIGMASK          STACKBASE  STACKSIZE      USED   FILLED    COMMAND
dump_tasks:   ----   --- --- -------- ------- --- ------- ---------- ---------------- 0x80400610      2048       216    10.5%    irq
dump_task:       0     0   0 FIFO     Kthread -   Running            0000000000000000 0x80407010      3056      1648    53.9%    Idle_Task
```
