# Set ZFS flags
```sh
zfs set acltype=posixacl tank/lxc
```

# Install LXC
```sh
yum install libvirt virt-install
systemctl enable libvirtd.service
systemctl start libvirtd.service
virsh net-undefine default
virsh net-destroy default
virsh net-define --file /dev/stdin <<EOF
<network ipv6="yes">
<name>default</name>
<bridge name="br0"/>
<forward mode="bridge"/>
</network>
EOF
virsh net-autostart default
virsh net-dumpxml default
virsh net-start default
```

# Installation of a CentOS 7 container
Set environment variables

```sh
export CONTAINER=mylxc
export VIRSH_DEFAULT_CONNECT_URI=lxc:///
```

```sh
mkdir /srv/lxc/$CONTAINER/etc/yum.repos.d/ -p
sed s/'$releasever'/7/g /etc/yum.repos.d/CentOS-Base.repo > /srv/lxc/$CONTAINER/etc/yum.repos.d/CentOS-Base.repo
yum install epel-release dhclient firewalld hostname initscripts iptables kernel-tools less openssh-clients passwd policycoreutils rootfiles sudo tar vim yum --installroot=/srv/lxc/$CONTAINER/ --nogpgcheck -y
echo "pts/0" >>/srv/lxc/$CONTAINER/etc/securetty
echo "$CONTAINER.etis.no" >/srv/lxc/$CONTAINER/etc/hostname
cat >/srv/lxc/$CONTAINER/etc/sysconfig/network << "EOF"
NETWORKING=yes
HOSTNAME=$CONTAINER.etis.no
EOF
cat >/srv/lxc/$CONTAINER/etc/sysconfig/network-scripts/ifcfg-eth0 << EOF
DEVICE=eth0
BOOTPROTO=none
ONBOOT=yes
NM_CONTROLLED=no
DEFROUTE=no
PEERDNS=no
PEERROUTES=no
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=yes
EOF
```

Create the container from the Linux installation
```sh
virt-install --connect lxc:/// --name $CONTAINER --ram 512 --vcpu 1 --filesystem /srv/lxc/$CONTAINER/,/ --noautoconsole
```
