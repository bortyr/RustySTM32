# Rust in STM32

## Build

cargo build --target thumbv7em-none-eabihf

## Verify

cargo readobj --target thumbv7em-none-eabihf --bin f4 -- -file-headers

## Launch in another terminal and leave it open

cd /tmp
openocd -f interface/stlink-v2-1.cfg -f target/stm32f4x.cfg

## In RustySTM32 directory launch gdb

gdb-multiarch -q target/thumbv7em-none-eabihf/debug/f4

## In gdb enter

target extended-remote: 3333

## In gdb enter

load

# References

https://docs.rust-embedded.org/discovery/index.html
https://github.com/stm32-rs/stm32f4xx-hal
