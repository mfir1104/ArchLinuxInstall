# Secure boot

enable secure boot in setup mode in BIOS menu

install sbctl

```shell
pacman -S sbctl
sbctl status
```

create key

```shell
# sbctl create-keys
```

enroll key

```shell
# sbctl enroll-keys --microsoft
```

if get errors, install `efitools` and manually enroll keys:

```shell
efi-updatevar -f /usr/share/secureboot/keys/db/db.auth db
efi-updatevar -f /usr/share/secureboot/keys/KEK/KEK.auth KEK
efi-updatevar -f /usr/share/secureboot/keys/PK/PK.auth PK
```

verify

```shell
# sbctl verify
```

sign the files in previous prompt

```shell
# sudo sbctl sign -s /boot/EFI/BOOT/BOOTX64.EFI
# sudo sbctl sign -s /boot/EFI/systemd/systemd-bootx64.efi
# sudo sbctl sign -s /boot/vmlinuz-linux
```

make EFISTUB

```shell
# sbctl bundle -s -a /boot/amd-ucode.img -l /usr/share/systemd/bootctl/splash-arch.bmp -c /proc/cmdline /boot/EFI/Linux/linux-linux.efi
# sbctl generate-bundles
```

sign the `linux-linux.efi` file

```shell
# sbctl sign -s /boot/EFI/Linux/linux-linux.efi
```

reboot

# TPM 2.0

add `systemd` and `sd-encrypt` hooks in the initramfs

```
HOOKS=(base udev systemd autodetect keyboard modconf block sd-encrypt filesystems)
```

check TPM support

```shell
cat /sys/class/tpm/tpm0/device/description
cat /sys/class/tpm/tpm0/tpm_version_major
systemd-cryptenroll --tpm2-device=list
```

set up `systemd` and `sd-encrypt` hooks in `/etc/mkinitcpio.conf`

enroll ket in both the TPM and LUKS volume

```shell
# systemd-cryptenroll --tpm2-device=auto --tpm2-pcrs=0,7 /dev/nvme0n1p2
```

To test that the key works, run the following command while the LUKS volume is closed:

```shell
# /usr/lib/systemd/systemd-cryptsetup attach crypt_root /dev/nvme0n1p2 - tpm2-device=auto
```

add `tpm2-device=auto` to `rd.luks.options` in `/boot/loader/entries/arch.conf`

```
options rd.luks.name=<root-partition-UUID>=crypt_root root=/dev/mapper/crypt_root rootflags=subvol=@ rd.luks.options=<root-partition-UUID>=tpm2-device=auto,discard rw quiet
```
