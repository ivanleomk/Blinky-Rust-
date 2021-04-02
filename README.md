# nucleo-h743zi

This repository was originall forked from the [stm32h7xx-hal](https://github.com/astraw/stm32h7xx-hal) crate. Much of the code is resused but I have adapted it to be able to work with a Nucleo H743ZI board.

[0-clause BSD license](LICENSE-0BSD.txt).

# Installing the Neccessary Libraries

1. Install STM32 Cube Programmer . The installer can be downloaded at

NOTE: if you cannot launch the installer, use the command. This helped me isntall it

```
./SetupSTM32CubeProgrammer-2.7.0.app/Contents/MacOs/SetupSTM32CubeProgrammer-2_7_0_macos
```

My installation path ended up being

```
/Applications/STMicroelectronics/STM32Cube/STM32CubeProgrammer
```

2. Check that the tool chain was accurately installed by going to

```
/Applications/STMicroelectronics/STM32Cube/STM32CubeProgrammer/STM32CubeProgrammer.app/Contents/MacOs/bin/STM32_Programmer_CLI -c port=SWD
```

This should in turn launch the installer, giving you

```
     -------------------------------------------------------------------
                        STM32CubeProgrammer v2.5.0
      -------------------------------------------------------------------


Usage :
STM32_Programmer_CLI.exe [command_1] [Arguments_1][[command_2] [Arguments_2]...]

Generic commands:

 -?, -h, --help         : Show this help
 -c,     --connect      : Establish connection to the device
     <port=<PortName>   : Interface identifier. ex COM1, /dev/ttyS0, usb1,
                          JTAG, SWD...)
    UART port optional parameters:
     [br=<baudrate>]    : Baudrate. ex: 115200, 9600, etc, default 115200
     [P=<parity>]       : Parity bit, value in {NONE,ODD,EVEN}, default EVEN
     [db=<data_bits>]   : Data bit, value in {6, 7, 8} ..., default 8
     [sb=<stop_bits>]   : Stop bit, value in {1, 1.5, 2} ..., default 1
     [fc=<flowControl>] : Flow control
                          Value in {OFF,Hardware,Software} ..., default OFF
                          Not supported for STM32MP
     [noinit=noinit_bit]: Set No Init bits, value in {0,1} ..., default 0
     [console]          : Enter UART console mode
    JTAG/SWD debug port optional parameters:
     [freq=<frequency>] : Frequency in KHz. Default frequencies:
                          4000 SWD 9000 JTAG with STLINKv2
                          ...
```

We can then confirm that your board is connected by running the command

```
/Applications/STMicroelectronics/STM32Cube/STM32CubeProgrammer/STM32CubeProgrammer.app/Contents/MacOs/bin/STM32_Programmer_CLI -c port=SWD
```

In my case, my `NUCLEO-H743ZI` board was connected and I obtained the result of

```
      -------------------------------------------------------------------
                        STM32CubeProgrammer v2.5.0
      -------------------------------------------------------------------

ST-LINK SN  : 0669FF343339415043184950
ST-LINK FW  : V2J37M26
Board       : NUCLEO-H743ZI
Voltage     : 3.26V
SWD freq    : 4000 KHz
Connect mode: Normal
Reset mode  : Software reset
Device ID   : 0x450
Revision ID : Rev Y
Device name : STM32H7xx
Flash size  : 2 MBytes
Device type : MCU
Device CPU  : Cortex-M7
```

Note this installation path down.

## Building and running

Build with:

    cargo build --release

Convert to a .bin file:

    cargo objcopy --release --bin blinky -- -O binary target/thumbv7em-none-eabi/release/blinky.bin

Flash the device:

```
/Applications/STMicroelectronics/STM32Cube/STM32CubeProgrammer/STM32CubeProgrammer.app/Contents/MacOs/bin/STM32_Programmer_CLI -c port=SWD -d ./target/thumbv7em-none-eabi/release/blinky.bin 0x08000000 -s
```

## Virtual Emulation

Run with QEMU. This allows us to run the binary on a QEMU ARM emulation with a m7 cortex ARM chip with some degree of semihosting.

Note : AM currently in the midst of configuring GPIO output for QEMU

```
qemu-system-arm \
 -cpu cortex-m7 \
 -machine mps2-an500 \
 -semihosting-config enable=on,target=native \
 -kernel target/thumbv7em-none-eabi/release/blinky.bin
```
