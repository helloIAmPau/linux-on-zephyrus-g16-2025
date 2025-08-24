# Linux on Zephyrus G16 (2025)

## Full clone the repo

```
git clone git@github.com:helloIAmPau/linux-on-zephyrus-g16-2025.git
cd linux-on-zephyrus-g16-2025
git submodule update --init
```

## Base system

Ubuntu 25.04

## Add ACPI support for speakers

Compile the device definition

```
iasl gu605c-spi-cs-gpio.asl
```

Create the initcpio image

```
mkdir -p kernel/firmware/acpi
cp ./gu605c-spi-cs-gpio.aml kernel/firmware/acpi
find kernel | cpio -H newc --create > asus-gu605c-acpi.cpio
sudo cp asus-gu605c-acpi.cpio /boot/
```

Update grub to load cpio image

[edit] /etc/default/grub
```
GRUB_EARLY_INITRD_LINUX_CUSTOM="asus-gu605c-acpi.cpio"
```

Update initram and grub

```
sudo update-initramfs -u
sudo update-grub
```

Reboot

## Update kernel to latest stable

Checkout the latest tag

```
cd linux
git checkout 6.16.x
```

Copy the config

```
cp ../kernel-config .config
```

Compile the kernel

```
make menuconfig # if needed
make
make modules
sudo make modules_install
sudo cp arch/x86_64/boot/bzImage /boot/vmlinuz-6.16.x-suffix
sudo update-initramfs -k all -c
sudo update-grub
```

Reboot

## Update to latest stable firmware

Checkout the latest tag

```
cd linux-firmware
git checkout 2025MMDD
```

Build and install deb

```
make deb
sudo apt install ./dist/linux-firmware-upstream_2025MMDD_all.deb
```

Reboot
