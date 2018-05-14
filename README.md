# 系统简介
ppu系统内核为riscv-rocket(https://github.com/freechipsproject/rocket-chip).
FPGA上运行主频50Mhz。内核配置有一个32KB ICACHE和一个32KB DCACHE。soc上有一个64KB的SRAM和一个DDR3接口。
# 下载代码
```
    $ git clone https://github.com/fxlantian/FPGAboard.git
    $ cd FPGAboard
    $ git submodule update --init
```
先查看pinconnet目录下的图片连接好电路, 其它pdf文件为参考手册.
# 安装riscv工具链
首先指定RISCV环境变量
```
    $ export RISCV=/path/to/riscv/toolchain/installation
```
构建这个版本的工具
```
    $ cd riscv-tools
    $ git submodule update --init --recursive
    $ export RISCV=/path/to/install/riscv/toolchain
    $ export MAKEFLAGS="$MAKEFLAGS -jN" # Assuming you have N cores on your host system
    $ ./build-rv32ima.sh (using RV32).
```
# 安装ppu软件环境
```
    $ cd ppu-sw
    $ ./ch.sh
```
该软件环境下的apps目录包含已有的外设操作c代码，libs包含外设库文件，ref下为链接脚本，utils包含脚本转换文件。  
文件编译在build目录下进行。
# 程序调试过程
1. 在ppu-sw的build目录中，make相应程序，例如:make helloworld.
2. 链接好板子的电源、jtag和调试串口。
3. 在主机上打开串口调试助手，波特率62500。
3. 在ppu-sw目录下，执行 openocd -f ft2232.cfg
4. 新开一个终端，在ppu-sw目录下，执行 riscv32-unknown-elf-gdb
5. 在gdb内：
```
    $ file build/apps/helloworld/helloworld.elf
    $ target remote localhost:3333
    $ set $pc=0x80000080
    $ load
    $ c
```
程序开始执行，在电脑上在调试命令参看gdb的help。
# FPGA 内存映射
### BootRom
|代码库|链接  |
|:----:|----|
|MarkDown                              |[https://github.com/younghz/Markdown](https://github.com/younghz/Markdown "Markdown")|
|MarkDownCopy                              |[https://github.com/younghz/Markdown](https://github.com/younghz/Markdown "Markdown")|

BootRom

|device   | address     | size |
| ------- | ----------- | ---- |
| bootrom | 0x0000_1000 | 4KB  |

Interrupts

device | address | size
---|---|---
CLINT | 0x0200_0000 | 64KB
PLIC  | 0x0C00_0000 | 64MB

MMIO

device | address | size
---|---|---
uart0   | 0x6000_0000 | 4KB 
gpio    | 0x6000_1000 | 4KB
spi_master0 | 0x6000_2000 | 4KB
timer   | 0x6000_3000 | 4KB
event   | 0x6000_4000 | 4KB
i2c0    | 0x6000_5000 | 4KB
soc_ctrl| 0x6000_6000 | 4KB
uart1   | 0x6000_7000 | 4KB
spi_master1 | 0x6000_8000 | 4KB
i2c1    | 0x6000_9000 | 4KB
pwm     | 0x6000_A000 | 4KB
ahb     | 0x7FFF_C000 | 4KB
SDIO    | 0x7FFF_F000 | 4KB

MEM

device | address | size
---|---|---
DDR3    | 0x8000_0000 | 128MB
IRAM    | 0x8800_0000 | 64KB
BOOTROM | 0x8801_0000 | 512B
