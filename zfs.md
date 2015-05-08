# Install ZFS on Linux
```sh
yum localinstall --nogpgcheck https://download.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
yum localinstall --nogpgcheck http://archive.zfsonlinux.org/epel/zfs-release.el7.noarch.rpm
yum install kernel-devel zfs
yum update && reboot
```

# Create a ZFS pool
```sh
export TANK=tank
```

```sh
zpool create -o ashift=12 $TANK /dev/disk/by-id/ata-Crucial_CT1024MX200SSD1_15030E696BB7
zfs set mountpoint=/mnt/$TANK $TANK
zfs create -o mountpoint=/srv/esx $TANK/esx
zfs create -o mountpoint=/srv/lxc $TANK/lxc
zfs create -o mountpoint=/srv/pub $TANK/pub
zfs set acltype=posixacl $TANK/lxc
zfs set dedup=on $TANK/esx
zfs set dedup=on $TANK/lxc
```
