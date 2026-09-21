# Running Linux on QEMU

!!! info

    Refer to the main [QEMU](../../toolchain/arc-classic/getting-started-qemu.md) article for information
    on installing QEMU.

## Preface

It's possible to run on QEMU the same Linux images as for nSIM/HAPS. A process of building of Linux
images is completely same.

## Running for ARC HS3x/4x

```shell
qemu-system-arc -M virt -cpu archs -display none -nographic -monitor none -m 2G -kernel vmlinux
```

## Running for ARC HS5x

!!! info

    Linux kernels for ARCv3 are loaded differently in comparison to kernels for ARCv1 and ARCv2.
    Kernels for ARCv3 are always loaded from a physical address `0x0` instead of `0x80000000`.
    A custom `loader` image is generated to distinguish it from a default `vmlinux` (`vmlinux` uses `0x80000000`
    as a starting address in physical and virtual spaces). Thus, it's necessary to run QEMU with
    `-M virt,ram_start=0` parameter.

```shell
qemu-system-arc -M virt,ram_start=0 -cpu hs5x -m 2G -display none -nographic -monitor none -kernel loader
```

## Running for ARC HS6x

```shell
qemu-system-arc64 -M virt,ram_start=0 -cpu hs6x -m 2G -display none -nographic -monitor none -kernel loader
```

## Networking in QEMU Using a User Level Network Interface

### Configuring Network

Firstly, it's necessary to enable `BR2_SYSTEM_DHCP` option in Buildroot configuration.
Just use `BR2_SYSTEM_DHCP=eth0` in your configuration file or use this option in a
configuration menu:

```text
System configuration -> Network interface to configure through DHCP -> eth0
```

You can connect a QEMU machine to a network using a user level network interface by passing
these additional options:

```text
-netdev user,id=net0 -device virtio-net-device,netdev=net0 -device virtio-rng-pci 
```

You can enable port forwarding for a set of ports. Here is an example for
forwarding ports for FTP and SSH:

```text
-netdev user,id=net0,hostfwd=tcp::2021-:21,hostfwd=tcp::2022-:22 -device virtio-net-device,netdev=net0 -device virtio-rng-pci 
```

Here is a full command line for running QEMU with support of networking and forwarding of ports for FTP and SSH:

```text
qemu-system-arc -M virt -cpu archs -m 2G -display none -nographic -monitor none -kernel vmlinux -device virtio-rng-pci \
                -netdev user,id=net0,hostfwd=tcp::2021-:21,hostfwd=tcp::2022-:22 -device virtio-net-device,netdev=net0
```

In boot log output you will see these lines:

```text
Starting network: udhcpc: started, v1.35.0
udhcpc: broadcasting discover
udhcpc: broadcasting select for 10.0.2.15, server 10.0.2.2
udhcpc: lease of 10.0.2.15 obtained from 10.0.2.2, lease time 86400
deleting routers
adding dns 10.0.2.3
```

Use `ifconfig` or `ping` utility to ensure that an Ethernet interface is initialized successfully:

```text
# ifconfig
eth0      Link encap:Ethernet  HWaddr 52:54:00:12:34:56
          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:1180 (1.1 KiB)  TX bytes:689 (689.0 B)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: seq=0 ttl=255 time=15.418 ms
64 bytes from 8.8.8.8: seq=1 ttl=255 time=0.701 ms
64 bytes from 8.8.8.8: seq=2 ttl=255 time=0.558 ms
64 bytes from 8.8.8.8: seq=3 ttl=255 time=1.122 ms
```

If you don't configure Buildroot with `BR2_SYSTEM_DHCP=eth0` option, then
you will to initialize an network device manually after Linux is booted:

```text
# ifconfig eth0 up
# udhcpc
udhcpc: started, v1.35.0
udhcpc: broadcasting discover
udhcpc: broadcasting select for 10.0.2.15, server 10.0.2.2
udhcpc: lease of 10.0.2.15 obtained from 10.0.2.2, lease time 86400
deleting routers
adding dns 10.0.2.3
```

### Using SSH

You can connect to the Linux kernel through SSH using a custom port 2022
(it is forwarded to 22 port inside of QEMU machine). To add SSH server to the
Linux image use `BR2_PACKAGE_OPENSSH=y` and `BR2_PACKAGE_HAVEGED=y` options for Buildroot:

```text
Target packages -> Networking applications -> [*] openssh
                -> Miscellaneous -> [*] haveged
```

Run Linux and create a user `user` and crate a home directory for the user:

```shell
adduser user
mkdir -p /home/user
```

Here is an example for connecting to the Linux using SSH:

```text
$ ssh -p 2022 user@127.0.0.1
user@127.0.0.1's password:
$ pwd
/home/user
```

## Networking in QEMU Using TUN/TAP Network Interface

TUN/TAP network interface allows interacting of the target with your host in both directions.
For example, you can mount host’s NFS directories inside of the target.

Configure TUN/TAP interface on host’s side (choose your own IP address instead of `10.42.0.1`):

```shell
sudo ip tuntap add tap1 mode tap
sudo ip addr add 10.42.0.1/24 dev tap1
sudo ip link set tap1 up
```

Run QEMU

```text
qemu-system-arc -M virt -cpu archs -m 2G -display none -nographic -monitor none -kernel vmlinux \
                -netdev tap,id=net0,ifname=tap1,script=no,downscript=no -device virtio-net-device,netdev=net0
```

Configure a network interface on target's side:

```shell
ifconfig eth0 10.42.0.100
```

After that you can connect to any Linux service without port forwarding. Here is an example of connecting
to the Linux using SSH with TUN/TAP interface configured:

```shell
ssh user@10.42.0.100
```

## Mounting an External Filesystem Image

It's possible to run Linux with an external filesystem image. Firstly,
turn off linking an initial RAM filesystem into Linux kernel (`BR2_TARGET_ROOTFS_INITRAMFS=n`)
and add an option for building Ext2 filesystem image (`BR2_TARGET_ROOTFS_EXT2=y`) in Buildroot:

```text
Filesystem images -> [*] ext2/3/4 root filesystem
                  -> [ ] initial RAM filesystem linked into linux kernel
```

Run the Linux kernel and initialize and external storage:

```text
qemu-system-arc -M virt -cpu archs -m 2G -display none -nographic -monitor none -kernel vmlinux \
                -append "root=/dev/vda ro" -drive file=images/rootfs.ext2,format=raw,id=hd0 \
                -device virtio-blk-device,drive=hd0
```

## Mounting a Shared Folder

If you need shared folder access, add the following arguments to QEMU's command line:

```text
-drive format=vvfat,id=hd0,file=fat:rw:/path/to/directory -device virtio-blk-device,drive=hd0
```

And the on target's side:

```shell
mount /dev/vda1
mount /dev/vda1 /mnt
touch /mnt/test.txt
```

Target will see contents on your directory as if it's a FAT partition on some drive without symbolic links, special attributes, etc.
Target may do changes to the contents of this folder (with read/write permissions above).
Host *must not* do any changes to the folder while target is using this shared folder,
otherwise things will go seriously wrong (QEMU creates FAT table on start and then it's target which manipulates it but not host).

## Mounting NFS Folder

Install and configure NFS on CentOS or Fedora:

```shell
# For Fedora
sudo dnf install nfs-utils

# Enable services and add rules for firewall:
sudo systemctl enable --now rpcbind nfs-server
sudo firewall-cmd --add-service=nfs --permanent
sudo firewall-cmd --reload
```

Install and configure NFS on Ubuntu:

```shell
sudo apt install nfs-kernel-server
sudo systemctl enable --now nfs-server
```

Then you need to configure NFS directory for sharing. For example,
it's `/nfs` directory and the current user owns it. Add this line to
`/etc/exports` (you can find `anonuid` and `anongid` for your user using `id -u`
and `id -g` respectively):

```shell
/nfs *(rw,all_squash,anonuid=1000,anongid=1000,no_subtree_check,insecure)
```

Update the table of exported NFS file systems:

```shell
sudo exportfs -rv
```

Then enable NFS in you Buildroot configuration using configuration
menu (it corresponds to `BR2_PACKAGE_NFS_UTILS=y`):

```text
Target packages -> Filesystem and flash utilities -> [*] nfs-utils
```

Run the Linux image with network interface enabled (use your own path
for `vmlinux` images for HS3x/4x targets or `loader` image for HS5x/6x targets):

```shell
qemu-system-arc -M virt -cpu archs -m 2G -display none -nographic -monitor none \
                -kernel images/vmlinux -device virtio-rng-pci -netdev user,id=net0 \
                -device virtio-net-device,netdev=net0
```

Create a directory for mounting the shared folder in guest system:

```shell
mkdir /nfs
```

Configure the network interface in guest system:

```text
# udhcpc
udhcpc: started, v1.36.1
udhcpc: broadcasting discover
udhcpc: broadcasting select for 10.0.2.15, server 10.0.2.2
udhcpc: lease of 10.0.2.15 obtained from 10.0.2.2, lease time 86400
deleting routers
adding dns 10.0.2.3
```

Mount the shared folder:

```text
# mount -t nfs 10.0.2.2:/nfs /nfs -o nolock
# ls /nfs
arc-gf-linux-gnu-native          debug-root
arc-gf-linux-gnu-with-cross-gdb  main.elf
```
