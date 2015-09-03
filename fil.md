# Installing file server

After creating the container in LXC, edit the settings.

	virsh edit fil

Add the following in `<devices>`

```
<filesystem type='mount' accessmode='passthrough'>
  <source dir='/srv/pub'/>
  <target dir='/srv/pub'/>
</filesystem>
```

Now login to the console and issue the following commands.

```
yum -y install krb5-workstation samba-winbind samba-common samba nscd samba-winbind-clients authconfig openssh-server
authconfig --krb5realm=AD.FYRKAT.NO --enablewinbind --enablewinbindauth --smbsecurity=ads --smbrealm=AD.FYRKAT.NO --winbindjoin=Administrator --enablemkhomedir --smbworkgroup=FYRKAT --enablewinbindusedefaultdomain --winbindtemplateshell=/bin/bash --updateall
chkconfig sshd on
sed -i '/PasswordAuthentication/ s/yes/no/' /etc/ssh/sshd_config
sed -i '/GSSAPIAuthentication/ s/yes/no/' /etc/ssh/sshd_config
sed -i '/PermitRootLogin/ s/yes/no/' /etc/ssh/sshd_config
exec reboot
```