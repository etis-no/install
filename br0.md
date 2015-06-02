# Set up bridge
```bash
cat > /etc/sysconfig/network-scripts/ifcfg-br0 <<EOF
DEVICE=br0
TYPE=Bridge
BOOTPROTO=none
ONBOOT=yes
DELAY=0
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
IPV6ADDR_SECONDARIES="2001::db4::1/64"
MTU=9000
EOF

cat > /etc/sysconfig/network-scripts/ifcfg-eth0 <<EOF
TYPE=Ethernet
DEVICE=eth0
ONBOOT=yes
NM_CONTROLLED=no
MTU=9000
EOF

cat > /etc/sysconfig/network-scripts/ifcfg-eth0.256 <<EOF
TYPE=Ethernet
DEVICE=eth0.256
VLAN=yes
ONBOOT=yes
BRIDGE=br0
NM_CONTROLLED=no
MTU=9000
EOF
```