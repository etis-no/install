# Installation of ZNC

```sh
yum localinstall http://dl.fedoraproject.org/pub/epel/6/x86_64/oidentd-2.0.8-8.el6.x86_64.rpm
yum install epel-release
yum install znc lighttpd
firewall-cmd --permanent --add-port=6697/tcp
firewall-cmd --permanent --add-port=443/tcp
firewall-cmd --permanent --add-port=113/tcp
firewall-cmd --permanent --add-port=80/tcp
chkconfig oidentd on
systemctl enable znc
ln -s ./ /var/lib/znc/.znc
```

# Configuration of ZNC
Update **/var/lib/znc/configs/znc.conf** and **/var/lib/znc/znc.pem**

# Set up lighttpd reverse proxy to make WebUI available over port 443
```sh
tee /etc/lighttpd/lighttpd.conf <<EOF
server.modules	= ("mod_proxy")
server.document-root	= "/var/www/lighttpd/"
server.use-ipv6 = "enable"
$SERVER["socket"] == "[::]:443" {
	server.pid-file 	= "/var/run/lighttpd.pid"
	server.username 	= "lighttpd"
	server.groupname	= "lighttpd"
	ssl.engine      	= "enable"
	ssl.use-sslv2   	= "disable"
	ssl.use-sslv3   	= "disable"
	ssl.cipher-list 	= "ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA"
	ssl.pemfile     	= "/var/lib/znc/znc.pem"
	ssl.ca-file     	= "/var/lib/znc/znc.pem"
	proxy.server    	= ( "" => ( "" => ( "host" => "::1", "port" => 8080 )))
}
server.pid-file  	= "/var/run/lighttpd.pid"
server.username  	= "lighttpd"
server.groupname 	= "lighttpd"
url.redirect     	= ( "^/(.*)" => "https://znc.etis.no/$1" )
EOF
```

# Configure oidentd
```sh
tee /etc/oidentd.conf <<EOF
user "znc" {
	default {
		allow spoof
		allow spoof_all
		allow spoof_privport
		allow random
		allow random_numeric
		allow numeric
		allow hide
	}
}
EOF
```

# Configure ZNC to use ident
```sh
touch ~znc/.oidentd.conf
chmod 644 ~znc/.oidentd.conf
chmod 711 ~znc
tee /var/lib/znc/moddata/identfile/.registry <<EOF
File %7e;%2f;%2e;oidentd%2e;conf
Format global%20;%7b;%20;reply%20;%22;%25;ident%25;%22;%20;%7d;
EOF
chown -R znc:znc ~znc/
```
