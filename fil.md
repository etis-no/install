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
yum -y install krb5-workstation samba-winbind samba-common samba nscd samba-winbind-clients authconfig openssh-server rsync nano
authconfig --krb5realm=AD.FYRKAT.NO --enablewinbind --enablewinbindauth --smbsecurity=ads --smbrealm=AD.FYRKAT.NO --winbindjoin=Administrator --enablemkhomedir --smbworkgroup=FYRKAT --enablewinbindusedefaultdomain --winbindtemplateshell=/bin/bash --updateall
chkconfig sshd on
chkconfig smb on
sed -i '/PasswordAuthentication/ s/yes/no/' /etc/ssh/sshd_config
sed -i '/GSSAPIAuthentication/ s/yes/no/' /etc/ssh/sshd_config
sed -i '/PermitRootLogin/ s/yes/no/' /etc/ssh/sshd_config
```

Now create a script to automatically create home directories on Samba log in.

```
mkdir -p /var/lib/samba/scripts/
tee /var/lib/samba/scripts/mkhome <<EOF
#!/bin/sh
if [ ! -e /home/"$1" ]
then
	cp -a /etc/skel /home/"$1"
	chown -hR "$1":domain\ users /home/"$1"
fi
EOF
```

Add the following lines to `/etc/samba/smb.conf`, under the `[global]` section.

```
idmap config * : backend = rid
veto files = /Thumbs.db/.DS_Store/.AppleDB/.AppleDesktop/.AppleDouble/Temporary Items/Network Trash Folder/
hide files = /.*/:2e*/desktop.ini/$RECYCLE.BIN/*.desktop/~$*/
delete veto files = yes
```

Add the following lines to `/etc/samba/smb.conf`, under the `[homes]` section.

```
root preexec = /var/lib/samba/scripts/mkhome %U
```

Finally, reboot.

```
exec reboot
```
