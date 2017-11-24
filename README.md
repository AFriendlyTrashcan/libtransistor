# libtransistor
[![Build Status](https://travis-ci.org/reswitched/libtransistor.svg?branch=master)](https://travis-ci.org/reswitched/libtransistor)

Libtransistor is a userland library for the Nintendo Switch. Currently, binaries can only be run under [Mephisto](https://github.com/reswitched/Mephisto). 

## Building

First, clone the repo with

```
git clone --recursive https://github.com/reswitched/libtransistor
```
You will need Python 2 and the python packages listed in [`requirements.txt`](https://github.com/reswitched/libtransistor/blob/master/requirements.txt). You'll also need clang and lld (llvm linker). 

You *should* just be able to run `make`. If that doesn't work, submit an issue (or pull request). This will produce a number of `nro` and `nso` files in `build/test/`. These are binaries for the Nintendo Switch. NRO files are relocatable shared objects suitable for use with [ROhan](https://reswitched.tech/rohan). Both NRO and NSO binaries can be run under Mephisto, but NSO binaries don't get relocated properly at the moment and will crash pretty quickly.

## Running

```
$ ./ctu --load-nro <path-to-libtransistor>/build/test/<binary>.nro
```
For running the `bsd` test, you may want to also pass `--enable-sockets`. Don't pass that for anything you don't want phoning home, though.

## Debugging

Because Mephisto comes with a GDB stub and we produce standard ELF files before converting to NSO/NRO, we can debug libtransistor binaries very easily.

```
$ ./ctu --load-nro <path-to-libtransistor>/build/test/<binary>.nro --enable-gdb
[GdbStub] INFO: Starting GDB server on port 24689...
[GdbStub] INFO: Waiting for gdb to connect...
```
```
$ aarch64-none-elf-gdb 
(gdb) target remote localhost:24689
Remote debugging using localhost:24689
warning: No executable has been specified and target does not support
determining executable automatically.  Try using the "file" command.
0x0000007100000080 in ?? ()
(gdb) add-symbol-file <path-to-libtransistor>/build/test/<binary>.nro.so 0x7100000000
add symbol table from file "<path-to-libtransistor>/build/test/<binary>.nro.so" at
	.text_addr = 0x7100000000
(y or n) y
Reading symbols from <path-to-libtransistor>/build/test/<binary>.nro.so...done.
(gdb) 
```
## Debian/Ubuntu Info
On Debian Sid at least it's necessary to install the packages python-pyelftools and python-lz4. Also it is necessary to install llvm by adding the correct ppa from http://apt.llvm.org/ note that you will also need to install the correct key which can be found further down on the page.
