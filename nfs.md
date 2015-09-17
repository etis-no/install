# Installation
These commands will enable NFS on an existing ZFS filesystem.

```sh
yum install nfs-utils
systemctl enable nfs-server.service
firewall-cmd --permanent --add-port=111/tcp
firewall-cmd --permanent --add-port=54302/tcp
firewall-cmd --permanent --add-port=20048/tcp
firewall-cmd --permanent --add-port=2049/tcp
firewall-cmd --permanent --add-port=46666/tcp
firewall-cmd --permanent --add-port=42955/tcp
firewall-cmd --permanent --add-port=875/tcp
zfs set sharenfs="rw=esx.fyrkat.no,no_root_squash" ssd/esx
zfs set sharenfs="rw=esx.fyrkat.no,no_root_squash" ufisa
zfs set sharenfs="ro=*,all_squash" tank/pub
reboot
```
