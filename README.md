# uCore for riscv64 : uCore OS Labs on RISCV-64 (privileged spec 1.9)

uCore for riscv64 is a porting of [ucore_os_lab](https://github.com/chyyuu/ucore_os_lab) to RISC-V architecture (privileged spec 1.9). It's built on top of the [`opensbi`](https://github.com/riscv/opensbi), a supervisor execution environment for RISC-V systems.

This uCore port runs on Kendryte K210 chip and QEMU.

**关于移植的更多细节，请看 dosc/ucore_rv64_porting.md **

# Quickstart

## Installing riscv-tools

You may build the toolchain for RV64, or download pre-built toolchain from https://kendryte.com/downloads/

Here are the steps to build the toolchain.  (tested in ubuntu 16.04 x86-64)

### 0. setenv
```bash
$ export RISCV=/path/to/install/riscv/toolchain
$ export PATH=$RISCV/bin:$PATH
```

### 1. build gcc/gdb tools (64bit) 
```bash
$ sudo apt-get install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev
$ git clone https://github.com/riscv/riscv-tools.git
$ cd riscv-tools
$ git submodule update --init --recursive
$ ./build.sh
$ cd ..
```

### 2. build qemu (64bit)

```shell
$ sudo apt install libgtk-3-dev
$ sudo apt install libsdl2-dev
$ cd riscv-tools/riscv-gnu-toolchain/riscv-qemu
$ ./configure --target-list=riscv64-softmmu
$ make
$ cp riscv64-softmmu/qemu-system-riscv64 $RISCV/bin
```

See [Installation Manual](https://github.com/ring00/riscv-tools#the-risc-v-gcc-toolchain-installation-manual) for details.

## Build uCore and OpenSBI

### 1. build ucore (64bit)

To build all projects at once, run the following commands

```bash
$ git clone -b riscv64-priv-1.9 --single-branch https://github.com/z4yx/ucore_os_lab.git
$ cd ucore_os_lab/labcodes_answer/lab8
$ make DEFS=-DRV_PRIV_SPEC_1_9 -j8 bin/kernel
$ riscv64-unknown-elf-objcopy --output-format=binary bin/kernel{,.bin}
```

`labcodes_answer/lab8/bin/kernel.bin` is the uCore binary.

### 2. build OpenSBI

```bash
$ git clone -b ucore-k210-lab --single-branch https://github.com/z4yx/opensbi.git
$ make -j8 PLATFORM=kendryte/k210 CROSS_COMPILE=riscv64-unknown-elf- FW_PAYLOAD_OFFSET=0x200000 FW_PAYLOAD_PATH=<Path to ucore_os_lab>/labcodes_answer/lab8/bin/kernel.bin
```

`build/platform/kendryte/k210/firmware/fw_payload.bin` is the final firmware binary which contains opensbi and uCore.

## Run on K210 chip

Install the kflash program provided by Kendryte.

```bash
$ sudo pip3 install kflash
```

Upload binary to K210 board with the following command

```bash
$ kflash -t build/platform/kendryte/k210/firmware/fw_payload.bin
```

For more information about kflash, run `kflash -h`.

# Labs info

```
lab0: preparing
lab1: boot/protect mode/stack/interrupt
lab2: physical memory management
lab3: virtual memory management
lab4: kernel thread management
lab5: user process management
lab6: scheduling
lab7: mutex/sync
lab8: filesystem
```

# Read the Docs

Detailed documentation can be found in the `docs` directory.

# Maintainers
- Yu Chen: yuchen AT tsinghua.edu.cn
- Yong, Xiang: xyong@tsinghua.edu.cn
- Mao, Junjie: eternal.n08@gmail.com
- Wei Zhang:  zhangwei15 AT mails.tsinghua.edu.cn
- Yuxiang Zhang:  zyx17 AT mails.tsinghua.edu.cn

