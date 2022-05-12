# Podman

install podman

```shell
# pacman -S podman
```

## Enable rootless podman

First, check the value of `kernel.unprivileged_userns_clone` by running: 

```shell
$ sysctl kernel.unprivileged_userns_clone
```

If it is currently set to 0, enable it by setting 1 via sysctl or kernel parameter. 

Set subuid and subgid

```shell
# touch /etc/subuid /etc/subgid
# usermod --add-subuids 100000-165535 --add-subgids 100000-165535 <your-username>
```

check contents of `/etc/subuid` and `/etc/subgid`

```shell
$ bat /etc/subuid
$ bat /etc/subgid
```

Propagate changes to subuid and subgid

```shell
$ podman system migrate
```

## Search images on docker.io

edit `/etc/containers/registries.conf`

```
unqualified-search-registries=["docker.io"]
```
