## opensips-cp
# Ubuntu 22
```
curl https://apt.opensips.org/opensips-org.gpg -o /usr/share/keyrings/opensips-org.gpg
echo "deb [signed-by=/usr/share/keyrings/opensips-org.gpg] https://apt.opensips.org jammy 3.5-releases" >/etc/apt/sources.list.d/opensips.list 
echo "deb [signed-by=/usr/share/keyrings/opensips-org.gpg] https://apt.opensips.org jammy cli-nightly" >/etc/apt/sources.list.d/opensips-cli.list

apt update
apt-get install apache2 libapache2-mod-php php-curl php php-mysql php-gd php-pear php-cli php-apcu git sngrep htop mariadb-server m4

apt install opensips opensips-cli opensips-mysql-module 

opensips-cli -> database create
cd /var/www/html/
git clone -b 9.3.5 https://github.com/OpenSIPS/opensips-cp.git
chown -R www-data:www-data /var/www/html/opensips-cp/
cd opensips-cp/
mysql -Dopensips -p < config/db_schema.mysql
```
### Cron
```cp config/tools/system/smonitor/opensips_stats_cron /etc/cron.d/```
###  add to /etc/apache2/sites-enabled/000-default.conf  before ending Virthost
```
<Directory /var/www/html/opensips-cp/web>
		Options Indexes FollowSymLinks MultiViews
		AllowOverride None
		Require all granted
	</Directory>
	<Directory /var/www/html/opensips-cp>
		Options Indexes FollowSymLinks MultiViews
		AllowOverride None
		Require all denied
	</Directory>
	Alias /cp /var/www/html/opensips-cp/web

	<DirectoryMatch "/var/www/html/opensips-cp/web/tools/.*/.*/(template|custom_actions|lib)/">
		Require all denied
	</DirectoryMatch>
  ```
And
```
systemctl restart apache2.service
systemctl restart cron.service
```

### OPTIONAL CONF 
```
/usr/sbin/osipsconfig

cp opensips_residential_* opensips.cfg
chmod 644 opensips.cfg
```
### IF NEEDED
```
apt install opensips-http-modules opensips-dialplan-module opensips-auth-modules rtpproxy
```
### change
```
modparam("rtpproxy", "rtpproxy_sock", "unix:/var/run/rtpproxy/rtpproxy.sock")  -> in opensips .conf
usermod -aG rtpproxy opensips 

systemctl restart rtpproxy
systemctl restart opensips
```
### Change network interface in opensips
```
socket=udp:eth0:5060   # CUSTOMIZE ME
```
### Change httpd for better security 
```
modparam("httpd", "ip", "127.0.0.1")
```

###  change 
```
modparam("mi_fifo", "fifo_name", "/run/opensips/opensips_fifo")

opensips-cli -x mi which
```




