# Samba

```shell
# pacman -S samba
```

download config

```shell
# curl https://git.samba.org/samba.git/?p=samba.git;a=blob_plain;f=examples/smb.conf.default;hb=HEAD -o /etc/samba/smb.conf
```

## Config

share a directory, 

```
[Share]
comment = Some description
path = <path to directory>
browsable = yes
writeable = yes
create mask = 0700
directory mask = 0700
read only = no
guest ok = no
```

check if there is no error

```
testparm
```

add samba user

```shell
# pdbedit -a -u <your-username>
```

add `mangled names=no` under `[global]` to fix weird names

start the service

```shell
# systemctl enable --now smb.service
# systemctl enable --now nmb.service
```
