## Table of Content

* [Introduction](#Introduction)
* [Processor Features](#Processor-Features)
* [Differences to the Original MSP430 Processors](#Differences-to-the-Original-MSP430-Processors)
* [Implementation Results](#Implementation-Results)
* [Quick Start](#Quick-Start)
* [Contact](#Contact)
* [Disclaimer, Proprietary and Legal Notice](#Disclaimer-Proprietary-and-Legal-Notice)


## Introduction

Welcome to the __NEO430 Processor__ project!

You need a small but still powerful, customizable and microcontroller-like
processor system for your next FPGA project? Then the NEO430 is the right
choice for you.

This processor is based on the Texas Instruments MSP430(TM) ISA and provides full
compatibility with the original instruction set. The NEO430 is not an MSP430
clone – it is more like a complete new implementation from the bottom up. The
processor features a very small outline, already implementing standard
features like a timer, a watchdog, UART, TWI and SPI serial interfaces, general
purpose IO ports, an internal bootloader and of course internal memory for
program code and data. All of the implemented peripheral modules are optional – so if you
do not need them, you can exclude them from synthesis to reduce the size
of the system. Any additional modules, which make a more customized system,
can be connected via a Wishbone-compatible bus interface or directly implemented within the
processor. By this, you can built a system, that perfectly fits your needs.

It is up to you to use the NEO430 as stand-alone, configurable and extensible
microcontroller, or to use it as controller within a more complex SoC design.

The high-level software development is based on the free TI msp430-gcc
compiler tool chain. You can either use Windows (PowerShell or Linux Subsystem) or
Linux as build environment for your applications – the project supports both worlds.

This project is intended to work "out of the box". Just synthesize the test
setup from this project, upload it to your FPGA board of choice (the NEO430 uses
a FPGA vendor-independent VHDL description) and start exploring the capabilities of
the NEO430 processor. Application program generation works by executing a single "make"
command. Jump to the "Let’s Get It Started" chapter in the NEO430 documentary, which provides
a lot of guides and tutorials to make your first NEO430 setup run: ![NEO430 Datasheet](https://raw.githubusercontent.com/stnolting/neo430/master/doc/figures/PDF_32.png)[NEO430 Datasheet](https://raw.githubusercontent.com/stnolting/neo430/master/doc/NEO430.pdf "NEO430 Datasheet from GitHub")


## Processor Features

![NEO430 Overview](https://raw.githubusercontent.com/stnolting/neo430/master/doc/figures/neo430_arch.png)

(optional modules are marked using dashed lines in the figure above)


- 16-bit open source soft-core microcontroller-like processor system
- Code-efficient CISC-like instruction capabilities
- Full support of the original MSP430 instruction set architecture
- Tool chain based on free TI msp430-gcc compiler
- Application compilation scripts for Windows Powershell / Windows Subsystem for Linux / native Linux
- Software requirements (regardless of platform):
  - [TI `msp430-gcc` compiler](http://software-dl.ti.com/msp430/msp430_public_sw/mcu/msp430/MSPGCC/latest/index_FDS.html "TI `msp430-gcc` compiler")
  - native C compiler (`GCC`)
  - `GNU Make`
- Completely described in behavioral, platform-independent VHDL
- Fully synchronous design, no latches, no gated clocks
- Very small outline and high operating frequency
- Internal DMEN (RAM, for data) and IMEM (RAM or ROM, for code), configurable sizes
- One external interrupt line
- Customizable processor hardware configuration:
  - Optional multiplier/divider unit (MULDIV)
  - Optional high-precision timer (TIMER)
  - Optional universal asynchronous receiver and transmitter (UART)
  - Optional serial peripheral interface (SPI)
  - Optional I2C-compatible two wire serial interface (TWI) supporting clock stretching
  - Optional general purpose parallel IO port (GPIO), 16 inputs & 16 outputs, with pin-change interrupt
  - Optional 32-bit Wishbone bus interface adapter (WB32) - including bridges to Avalon(TM) bus and AXI4-Lite(TM)
  - Optional watchdog timer (WDT)
  - Optional cyclic redundancy check unit (CRC16/32)
  - Optional custom functions unit (CFU) for user-defined processor extensions
  - Optional 4 channel PWM controller with 1 to 8 bit resolution (PWM)
  - Optional internal bootloader (2kB ROM) with serial user console and automatic boot from external SPI EEPROM


## Differences to the Original MSP430 Processors

- Completely different processor modules with different functionality
- Up to 48kB instruction memory and 12kB data memory
- Specific memory map – provided NEO430 linker script and compilation script required
- Custom binary executable format
- No default support of CPU's DADD instruction (btu can be enabled in package)
- Just 4 CPU interrupt channels
- Single clock domain for complete processor
- Different numbers of instruction execution cycles
- Only one power-down (sleep) mode
- Wishbone-compatible interface to attach custom IP
- Internal bootloader with text interface (via UART serial port)


## Implementation Results

Mapping results generated for HW version 0x0303. The full (default) hardware configuration includes
all optional processor modules (excluding the CFU and DADD instruction), an IMEM size of 4kB and a DMEM size of 2kB.
The minimal configuration only includes the CPU and the GPIO module. Results generated with Xilinx Vivado 2017.3,
Intel Quartus Prime Lite 17.1 and Lattice Radiant 1.0 (Synplify)

| __Xilinx Artix-7 (XC7A35TICSG324-1L)__  | LUTs       | FFs        | BRAMs    | DSPs   | f_max*  |
|:----------------------------------------|:----------:|:----------:|:--------:|:------:|:-------:|
| Full (default) configuration:           | 941 (4.5%) | 960 (2.3%) | 2.5 (5%) | 0 (0%) | 100 MHz |
| Minimal configuration (CPU + GPIO):     | 768 (3.6%) | 288 (0.7%) | 1   (2%) | 0 (0%) | 100 MHz |

| __Intel Cyclone IV (EP4CE22F17C6)__  | LUTs      | FFs      | Memory bits  | DSPs   | f_max     |
|:-------------------------------------|:---------:|:--------:|:------------:|:------:|:---------:|
| Full (default) configuration:        | 1603 (7%) | 928 (4%) | 65792 (11%)  | 0 (0%) | 119.6 MHz |
| Minimal configuration (CPU + GPIO):  | 607  (3%) | 230 (1%) | 49408  (8%)  | 0 (0%) | 119.6 MHz |

| __Lattice iCE40 UltraPlus (iCE40UP5K-SG48I)__  | LUTs       | FFs        | EBRs     | DSPs   | SRAMs  | f_max* |
|:-----------------------------------------------|:----------:|:----------:|:--------:|:------:|:------:|:------:|
| Full (default) configuration:                  | 2833 (54%) | 1131 (21%) | 16 (53%) | 0 (0%) | 0 (0%) | 20 MHz |
| Minimal configuration (CPU + GPIO):            | 1464 (28%) | 498   (9%) | 12 (40%) | 0 (0%) | 0 (0%) | 20 MHz |

*) Constrained


### Device Utilization by Entity

The following table shows the required resources for each module of the NEO430 processor system. Note that the provided
numbers only represent a coarse overview as logic elements might be merged and optimized beyond module boundaries.

Mapping results generated for HW version 0x0303. The full (default) hardware configuration includes all optional
processor modules (excluding the CFU and DADD instruction), an IMEM size of 4kB and a DMEM size of 2kB. Results were generated
using Intel Quartus Prime Lite 17.1.

| __Intel Cyclone IV (EP4CE22F17C6)__ | LUTs | FFs | Memory Bits | DSPs |
|:------------------------------------|:----:|:---:|:------------|:----:|
| Bootloader Memory (Boot ROM, 2kB)   | 2    | 1   | 16384       | 0    |
| Central Processing Unit (CPU)       | 547  | 196 | 256         | 0    |
| Checksum Unit (CRC)                 | 111  | 94  | 0           | 0    |
| Custom Functions Unit (CFU)         | -    | -   | -           | -    |
| Data Memory (DMEM, 2kB)             | 5    | 1   | 16384       | 0    |
| IO Port Unit (GPIO)                 | 53   | 45  | 0           | 0    |
| Instruction Memory (IMEM, 4kB)      | 6    | 1   | 32768       | 0    |
| Multiplier & Divider (MULDIV)       | 186  | 131 | 0           | 0    |
| Pulse-Width Modulation Unit (PWM)   | 80   | 67  | 0           | 0    |
| Serial Peripheral Interface (SPI)   | 57   | 43  | 0           | 0    |
| System Info Memory (SYSCONFIG)      | 16   | 14  | 0           | 0    |
| High-Precision Timer (TIMER)        | 66   | 55  | 0           | 0    |
| Two Wire Interface (TWI)            | 82   | 41  | 0           | 0    |
| Universal Asynchronous Rx/Tx (UART) | 129  | 89  | 0           | 0    |
| Wishbone Interface (WB32)           | 130  | 117 | 0           | 0    |
| Watchdog TImer (WDT)                | 51   | 34  | 0           | 0    |


## Quick Start

 * At first, make sure to get the most recent version of this project from GitHub:
 
    ~~~
    git clone https://github.com/stnolting/neo430.git
    ~~~

 * Next, install the `MSP430-GCC` compiler toolchain from the TI homepage (select the "compiler only" package according to your system OS):

   https://software-dl.ti.com/msp430/msp430_public_sw/mcu/msp430/MSPGCC/latest/index_FDS.html

 * Make sure `GNU Make` and a native C compiler (`GCC`) are installed (double check for the newest versions)

 * Follow the instructions from the "Let's Get It Started" section of the NEO430 documentary:

   ![NEO430 Datasheet](http://raw.githubusercontent.com/stnolting/neo430/master/doc/figures/PDF_32.png)[NEO430 Datasheet](https://raw.githubusercontent.com/stnolting/neo430/master/doc/NEO430.pdf "NEO430 Datasheet from GitHub")

 * The NEO430 documentary will guide you to create a simple test setup, which serves as "hello world" FPGA demo: 

![NEO430 Test Example Setup](https://raw.githubusercontent.com/stnolting/neo430/master/doc/figures/test_setup.jpg)

 * The NEO430 project also includes some example programs from which you can start your own application:

   https://github.com/stnolting/neo430/tree/master/sw/example

 * Have fun! =)


## Contact

If you have any questions, bug reports, ideas or if you are facing problems with the NEO430, feel free to drop me a line.
I'm always happy to hear what cool projects you are realizing with this core :smiley:

  stnolting@gmail.com


## Citation

If you are using the NEO430 for some kind of publication, please cite it as follows:

> S. Nolting, "The NEO430 Processor", github.com/stnolting/neo430


## Disclaimer, Proprietary and Legal Notice

This is a hobby project released under the LGPL-3.0 license. No copyright infringement intended.

This project is not affiliated with or endorsed by the Open Source Initiative (https://www.oshwa.org / https://opensource.org).

This source file may be used and distributed without restriction provided that this copyright
statement is not removed from the file and that any derivative work contains the original
copyright notice and the associated disclaimer.

This source file is free software; you can redistribute it and/or modify it under the terms of
the GNU Lesser General Public License as published by the Free Software Foundation, either
version 3 of the License, or (at your option) any later version.

This source is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY;
without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
See the GNU Lesser General Public License for more details.

You should have received a copy of the GNU Lesser General Public License along with this
source; if not, download it from https://www.gnu.org/licenses/lgpl-3.0.en.html


"MSP430" is a trademark of Texas Instruments Corporation.

"Windows" is a trademark of Microsoft Corporation.

"Virtex", "Artix" and "Vivado" are trademarks of Xilinx Inc.

"Cyclone", "Quartus" and "Avalon Bus" are trademarks of Intel Corporation.

"iCE40 UltraPlus", "Lattice Radiant" and "Lattice Diamond" are trademarks of Lattice Semiconductor Corporation.

"AXI", "AXI4" and "AXI4-Lite" are trademarks of Arm Holdings plc.



![Open Source Hardware Logo https://www.oshwa.org](https://raw.githubusercontent.com/stnolting/neo430/master/doc/figures/oshw_logo.png)


Made with :beer: in Hannover, Germany.
