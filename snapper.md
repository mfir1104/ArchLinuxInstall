# Install snapper

```shell
# pacman -S snapper
```

create new configuration

```shell
# umount /.snapshots
# rm -r /.snapshotsb
# snapper -c root create-config /
# btrfs subvolume delete /.snapshots
# mkdir /.snapshots
# mount -a
# chmod 750 /.snapshots
```

edit `/etc/snapper/configs/root`

```
ALLOW_USERS="<your-usename>"

TIMELINE_MIN_AGE="1800"
TIMELINE_LIMIT_HOURLY="5"
TIMELINE_LIMIT_DAILY="7"
TIMELINE_LIMIT_WEEKLY="0"
TIMELINE_LIMIT_MONTHLY="0"
TIMELINE_LIMIT_YEARLY="0"
```

enable `snapper-timeline.timer` and `snapper-cleanup.timer`

```shell
# systemctl enable snapper-timeline.timer
# systemctl enable snapper-cleanup.timer
```
