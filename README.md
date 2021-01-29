# Rust in STM32

# Install step by step

Assuming ubuntu 20.04 LTS or 20.10

```sh
sudo apt-get install gdb-multiarch minicom openocd
```

## udev rules

These rules let you use USB devices like the F4 and the Serial module without root privilege, i.e. sudo.

Create file

```sh
touch /etc/udev/rules.d/99-openocd.rules
sudo gedit /etc/udev/rules.d/99-openocd.rules
```

Write in the file:

```sh
# STM32F3DISCOVERY rev A/B - ST-LINK/V2
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", MODE:="0666"

# STM32F3DISCOVERY rev C+ - ST-LINK/V2-1
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="374b", MODE:="0666"
```

Then reload the udev rules with:

```sh
sudo udevadm control --reload-rules
```

## Verify installation of tools

Connect the F4 to your computer using an USB cable. Be sure to connect the cable to the "USB ST-LINK" port, the mini USB port in the center of the edge of the board.

The F4 should now appear as a USB device (file) in /dev/bus/usb

```sh
lsusb | grep -i stm
Bus 003 Device 004: ID 0483:374b STMicroelectronics ST-LINK/V2.1
    ^^^        ^^^
```

The F4 got connected to the bus #3 and got enumerated as the device #4. This means the file /dev/bus/usb/003/004 is the F4. Let's check its permissions:

```sh
ls -l /dev/bus/usb/003/004
crw-rw-rw- 1 root root 189, 20 Sep 13 00:00 /dev/bus/usb/003/004
```

Should be crw-rw-rw- . Otherwise check udev permissions by realoding the files:

```sh
sudo udevadm control --reload-rules
```
If it still the same then try to procced with permissions you have. I manged to make it work with crw-rw-rw-+ .

## Check OpenOCD connection

First, connect the F4 to your computer using an USB cable. Connect the cable to the USB port in the center of edge of the board, the one that's labeled "USB ST-LINK".

Two red LEDs should turn on right after connecting the USB cable to the board.

Next, run this command:

```sh
cd /tmp
openocd -f interface/stlink-v2-1.cfg -f target/stm32f4x.cfg
```

You should see something like this:

```sh
pen On-Chip Debugger 0.10.0
Licensed under GNU GPL v2
For bug reports, read
        http://openocd.org/doc/doxygen/bugs.html
Info : auto-selecting first available session transport "hla_swd". To override use 'transport select <transport>'.
adapter speed: 1000 kHz
adapter_nsrst_delay: 100
Info : The selected transport took over low-level target control. The results might differ compared to plain JTAG/SWD
none separate
Info : Unable to match requested speed 1000 kHz, using 950 kHz
Info : Unable to match requested speed 1000 kHz, using 950 kHz
Info : clock speed 950 kHz
Info : STLINK v2 JTAG v27 API v2 SWIM v15 VID 0x0483 PID 0x374B
Info : using stlink api v2
Info : Target voltage: 2.915608
Info : stm32f3x.cpu: hardware has 6 breakpoints, 4 watchpoints
```

openocd will block the terminal. That's fine.

Also, one of the red LEDs, the one closest to the USB port, should start oscillating between red light and green light.

That's it! It works. You can now close/kill openocd.

## Get this project files

```sh
git clone https://github.com/bortyr/RustySTM32
```

Go to this directory

```sh
cd RustySTM32
```

## Build

```sh
cargo build --target thumbv7em-none-eabihf
```

## Verify

```sh
cargo readobj --target thumbv7em-none-eabihf --bin f4 -- -file-headers
```

## Launch in another terminal and leave it open

```sh
cd /tmp
openocd -f interface/stlink-v2-1.cfg -f target/stm32f4x.cfg
```

## In RustySTM32 directory launch gdb

```sh
gdb-multiarch -q target/thumbv7em-none-eabihf/debug/f4
```

## In gdb enter

```sh
target extended-remote: 3333
```

## In gdb enter

```sh
load
```

# References

https://docs.rust-embedded.org/discovery/index.html
https://github.com/stm32-rs/stm32f4xx-hal
