# Building OpenOCD

## Building from Sources for Linux

Install prerequisites for RHEL/AlmaLinux 8:

```shell
$ sudo dnf install \
      libtool gcc autoconf automake texinfo libusb1 \
      libusb1-devel git make which
```

Install prerequisites for Ubuntu 22.04:

```shell
$ sudo apt install \
      libtool git build-essential autoconf automake \
      texinfo libusb-1.0-0 libusb-1.0-0-dev pkg-config
```

Download OpenOCD sources and checkout the latest release:

```shell
$ git clone -b arc-2026.09 https://github.com/foss-for-mips-arc-processors/openocd
$ cd openocd
```

Configure OpenOCD (use your own `--prefix` path):

```shell
$ ./bootstrap
$ ./configure \
      --enable-ftdi --disable-werror \
      --disable-doxygen-html --prefix=/tools/openocd
```

Also, you can pass `--enable-verbose` and `--enable-verbose-jtag-io` options for development activities.

Build and install:

```shell
$ make
$ make install
```

Configure your environment (use your own installation path):

```shell
$ export PATH=/tools/openocd/bin:$PATH
```

## Building from Sources for Windows

It is possible to use OpenOCD on Windows with FTDI-based debug cables using a
`ftdi` interface and `libusb` driver (further down called `ftdi`/`libusb`). Note,
however that this requires [replacing the original FTDI proprietary drivers](./winusb.md) with
open source ones. This will render Digilent cable unusable by Digilent tools,
like Adept.

Install the same prerequisites like for Linux build (except for `libusb-dev`) and
MinGW cross-compiler:

```shell
$ sudo apt install libtool git-core build-essential autoconf \
      automake texinfo pkg-config wget gcc-mingw-w64
```

Download [libusb sources](http://libusb.info) sources. Configure and build them
with MinGW compiler. It is recommended to build only static `libusb`, so that
OpenOCD will not need this library's `.dll` file to be copied around:

```shell
$ wget https://github.com/libusb/libusb/releases/download/v1.0.26/libusb-1.0.26.tar.bz2
$ tar -xaf libusb-1.0.26.tar.bz2
$ cd libusb-1.0.26
$ ./configure --host=i686-w64-mingw32 --build=x86_64-linux-gnu \
      --prefix=/tools/libusb-mingw --disable-shared --enable-static
$ make
$ make install
```

Download OpenOCD sources:

```shell
$ git clone -b arc-2026.09 https://github.com/foss-for-mips-arc-processors/openocd
$ cd openocd
```

Configure OpenOCD. Consult `configure --help` and generic OpenOCD documentation
for details. This command line is recommended for ARC with `libusb`/`ftdi`:

```shell
$ ./bootstrap
$ PKG_CONFIG_PATH=/tools/libusb-mingw/lib/pkgconfig ./configure \
      --enable-ftdi --host=i686-w64-mingw32 --build=x86_64-linux-gnu \
      --disable-werror --prefix=/tools/openocd-mingw
```

Note that it is required to set `PKG_CONFIG_PATH`, otherwise configure script
will detect host's `libusb` installation, instead of the one cross-compiled for
Windows.

Build and install:

```shell
$ make
$ make install
```

If your application uses libusb and is being linked dynamically (this is by
default), copy `/tools/libusb-mingw/bin/libusb-1.0.dll` to the OpenOCD bin
directory. Copy OpenOCD installation to Window host.

## Building from Sources for macOS

!!! warning

    Ensure that your `PATH` does not contain GNU binutils binaries, otherwise
    linkage will fail.

Follow [README.macOS](https://github.com/openocd-org/openocd/blob/master/README.macOS)
guide and then [README](https://github.com/openocd-org/openocd/blob/master/README)
to install OpenOCD for macOS. Additionally, you have to install `libftdi`
(for Homebrew) or `libftdi1` (for MacPorts) package. Here is a building process
for Apple M1 targets:

```shell
$ git clone -b arc-2026.09 https://github.com/foss-for-mips-arc-processors/openocd
$ cd openocd
$ ./bootstrap
$ CCACHE=none ./configure --enable-ftdi --disable-werror --disable-doxygen-html --prefix=/opt/openocd
$ make
$ make install
```

