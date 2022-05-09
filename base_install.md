# Base install

sync system clock

```shell
timedatectl set-ntp true
```

create partitions

```shell
gdisk /dev/nvme0n1
```

512 MiB for EFI partition and the rest for root

encrypt root partition

```shell
# cryptsetup --cipher aes-xts-plain64 --hash sha512 --use random luksFormat /dev/nvme0n1p2
# cryptsetup luksOpen /dev/nvme0n1p2 crypt_root
```

format the partitions

```shell
mkfs.vfat -F32 -n EFI /dev/nvme0n1p1
mkfs.btrfs -L ArchOS /dev/mapper/crypt_root
```

create subvolumes

```shell
mount /dev/mapper/crypt_root /mnt
btrfs sub create /mnt/@
btrfs sub create /mnt/@home
btrfs sub create /mnt/@abs
btrfs sub create /mnt/@tmp
btrfs sub create /mnt/@srv
btrfs sub create /mnt/@snapshots
btrfs sub create /mnt/@btrfs
btrfs sub create /mnt/@log
btrfs sub create /mnt/@cache
umount /mnt
```

mount the subvolumes and EFI partition

```shell
mount -o noatime,space_cache=v2,ssd,discard=async,subvol=@ /dev/mapper/crypt_root /mnt
mkdir -p /mnt/{boot,home,.snapshots,btrfs,var/abs,var/tmp,var/log,var/cache,srv}
mount /dev/nvme0n1p1 /mnt/boot
mount -o noatime,space_cache=v2,ssd,discard=async,subvol=@home /dev/mapper/crypt_root /mnt/home
mount -o noatime,space_cache=v2,ssd,discard=async,subvol=@snapshots /dev/mapper/crypt_root /mnt/.snapshots
mount -o noatime,space_cache=v2,ssd,discard=async,subvol=@btrfs /dev/mapper/crypt_root /mnt/btrfs
mount -o noatime,space_cache=v2,ssd,discard=async,subvol=@abs /dev/mapper/crypt_root /mnt/var/abs
mount -o noatime,space_cache=v2,ssd,discard=async,subvol=@tmp /dev/mapper/crypt_root /mnt/var/tmp
mount -o noatime,space_cache=v2,ssd,discard=async,subvol=@log /dev/mapper/crypt_root /mnt/var/log
mount -o noatime,space_cache=v2,ssd,discard=async,subvol=@cache /dev/mapper/crypt_root /mnt/var/cache
mount -o noatime,space_cache=v2,ssd,discard=async,subvol=@srv /dev/mapper/crypt_root /mnt/srv
```

edit `/etc/pacman.conf`

```
# Misc options
UseSyslog
Color
#NoProgressBar
CheckSpace
VerbosePkgLists
ParallelDownloads = 10
```

update pacman mirrorlist

```shell
reflector --save /etc/pacman.d/mirrorlist --protocol https --sort rate --thread 4
```

install basic system

```shell
pacstrap /mnt linux linux-headers linux-firmware base base-devel btrfs-progs amd-ucode git neovim zsh zsh-completions
```

generate the fstab

```shell
genfstab -U /mnt > /mnt/etc/fstab
```

copy zsh config

```shell
cp /etc/zsh/zprofile /mnt/root/.zprofile
cp /etc/zsh/zshrc /mnt/root/.zshrc
```

copy pacman config

```shell
cp /etc/pacman.conf /mnt/etc/pacman.conf
```

copy pacman mirrorlist

```shell
cp /etc/pacman.d/mirrorlist /mnt/etc/pacman.d/mirrorlist
```

chroot into system

```shell
arch-chroot /mnt /bin/zsh
```

set root password

```shell
passwd
```

change default shell to zsh

```shell
chsh -s /bin/zsh
```

create user

```shell
useradd -m -G wheel -s /bin/zsh <your-username>
passwd <your-username>
```

edit sudoers file

```shell
EDITOR=nvim visudo
```

uncomment this line `%wheel ALL=(ALL) ALL`

set host name

```shell
echo <your-hostname> > /etc/hostname
```

set locale

```shell
echo 'en_US.UTF-8 UTF-8' >> /etc/locale.gen
locale-gen
echo "LANG=\"en_US.UTF-8\"" > /etc/locale.conf
```

set timezone

```shell
timedatectl list-timezones
timedatectl set-timezone <your-timezone>
hwclock --systohc
```

define hosts in `/etc/hosts`:

```
127.0.0.1 localhost
::1 localhost
127.0.1.1 <your-hostname>.localdomain <your-hostname>
```

configure initramfs, `/etc/mkinitcpio.conf`

edit `MODULES=(btrfs amdgpu)`

edit `HOOKS=(base udev autodetect keyboard modconf block filesystems)`

recreate initramfs

```shell
mkinitcpio -p linux
```

install essential packages

```shell
pacman -S networkmanager dialog wpa_supplicant xdg-utils xdg-user-dirs
```

enable network manager service

```shell
systemctl enable NetworkManager
```

install pipewire

```shell
pacman -S pipewire pipewire-alsa pipewire-pulse
```

install reflector

```shell
pacman -S reflector
```

edit `/etc/xdg/reflector/reflector.conf`

```
--save /etc/pacman.d/mirrorlist
--protocol https
--latest 100
--sort score
```

enable `reflector.service` and `reflector.timer`

```shell
systemctl enable --now reflector.service
systemctl enable --now reflector.timer
```

install systemd-boot

```shell
bootctl --path=/boot install
```

append UUID of root partitoin

```shell
echo $(blkid -s UUID -o value /dev/nvme0n1p2) >> /boot/loader/entries/arch.conf
```

edit `/boot/loader/entries/arch.conf`

```
title Arch Linux
linux /vmlinuz-linux
initrd /amd-ucode.img
initrd /initramfs-linux.img
options rd.luks.name=<root-partition-UUID>=crypt_root root=/dev/mapper/crypt_root rootflags=subvol=@ rd.luks.options=<root-partition-UUID>=discard rw quiet
```

edit `/boot/loader/loader.conf`

```
default arch.conf
editor no
timeout 10
```

exit chroot, unmount partitions and reboot

```shell
exit
umount -a
reboot
```
