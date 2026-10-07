# Installing Eclipse IDE on Linux

## Installing Eclipse IDE

Eclipse IDE for ARC GNU toolchain may be downloaded as `.tar.gz` archive from
the [releases page](https://github.com/foss-for-mips-arc-processors/toolchain/releases).
It contains toolchains, OpenOCD and Eclipse itself.

## Adjusting Rights for `/var/lock`

If you plan to connect to UART port on target board with RxTx plugin controlled
by IDE you need to change permissions of directory `/var/lock` in your system.
Usually by default only users with root access are allowed to write into
this directory, however RxTx tries to write file into this directory, so
unless you are ready to run IDE with sudo, you need to allow write
access to `/var/lock` directory for everyone. Note that if `/var/lock`
is a symbolic link to another directory then you need to change permissions
for this directory as well.

Here is an example of adjusting rights for `/var/lock`:

```text
$ ls -l /var/lock
lrwxrwxrwx 1 root root 9 Jun 10 18:24 /var/lock -> /run/lock
$ ls -ld /run/lock
drwxrwxrwt 3 root root 100 Aug  1 15:12 /run/lock
$ sudo chmod go+w /run/lock 
$ ls -ld /run/lock
drwxrwxrwt 3 root root 100 Aug  1 15:12 /run/lock
```

If it is not possible or not desirable to change permissions for this
directory, then serial port connection must be disabled in Eclipse
debugger configuration window.

## Adding a User to `dialout` Group

Add a user to `dialout` group to allow the user to connect to the
UART device (`/dev/ttyUSBx`):

```shell
$ sudo usermod -a -G dialout `whoami`
```

## Configuring OpenOCD

Follow [Getting OpenOCD](../../platforms/openocd-use.md) guide to configure
OpenOCD.
