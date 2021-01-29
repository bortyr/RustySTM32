# Rust in STM32

openocd -f interface/stlink-v2-1.cfg -f target/stm32f4x.cfg

gdb-multiarch -q target/thumbv7em-none-eabihf/debug/f4
target extended-remote: 3333

cargo build --target thumbv7em-none-eabihf

cargo readobj --target thumbv7em-none-eabihf --bin f4 -- -file-headers
