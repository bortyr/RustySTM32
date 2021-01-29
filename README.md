# Rust in STM32

# Install step by step

Assuming ubuntu 20.04 LTS or 20.10

```sh
sudo apt-get install gdb-multiarch minicom openocd
```

## udev rules

These rules let you use USB devices like the F4 and the Serial module without root privilege, i.e. sudo.

Create these two files in /etc/udev/rules.d with the contents shown below.

```sh
touch /etc/udev/rules.d/99-ftdi.rules
sudo gedit /etc/udev/rules.d/99-ftdi.rules
```

Write this in the file

```sh
ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", MODE:="0666"
```

Create another file

```sh
touch /etc/udev/rules.d/99-openocd.rules
sudo gedit /etc/udev/rules.d/99-openocd.rules
```

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
