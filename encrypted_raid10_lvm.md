install lvm2 package

```shell
# pacman -S lvm2
```

available drive:
sda1 -- mirrored to sdc1
sdb1 -- mirrored to sdd1
sdc1 -- main
sdd1 -- main

create physical volumes

```shell
# pvcreate /dev/sda1 /dev/sdb1 /dev/sdc1 /dev/sdd1
# pvscan
```

create volume group

```shell
# vgcreate my_vg /dev/sda1 /dev/sdb1 /dev/sdc1 /dev/sdd1
# vgscan
```

create raid 10 logical volume

```shell
# lvcreate --type raid10 -l 100%FREE -n lv_r10 my_vg /dev/sdc1 /dev/sda1 /dev/sdd1 /dev/sdb1
# lvscan
```

encrypt the logical volume

```shell
# cryptsetup luksFormat /dev/my_vg/lv_r10
```

open the encrypted logical volume as `crypt_data`

```shell
# cryptsetup luksOpen /dev/my_vg/lv_r10 crypt_data
```

create the mountpoint

```shell
# mkdir /mnt/data
# chown <your-username>: /mnt/data
```

add the key to TPM2

```shell
# systemd-cryptenroll --tpm2-device=auto --tpm2-pcrs=0+7 /dev/my_vg/lv_r10
```

if the encrypted logical volume already opened, close it

```shell
# cryptsetup luksClose /dev/mapper/crypt_data
```

test if the key stored in TPM2 is working or not

```shell
# /usr/lib/systemd/systemd-cryptsetup attach crypt_data /dev/my_vg/lv_r10 - tpm2-device=auto
```

add the following line in `/etc/crypttab`

```
crypt_data /dev/my_vg/lv_r10 - tpm2-device=auto
```

add the following line in `/etc/fstab`

```
/dev/mapper/crypt_data /mnt/data ext4 defaults 0 0
```
