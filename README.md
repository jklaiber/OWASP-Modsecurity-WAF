# OWASP-Modsecurity-WAF
OWASP Modsecurity Web Application Firewall on Ubuntu 12.04

## Installation Modsecurity
update and install build-tools and dependencies
```
$ sudo apt-get update && sudo apt-get upgrade
$ sudo apt-get install gcc make libxml2 libxml2-dev liblua5.1 apache2-prefork-dev
```
enable mod_unique_id
```
$ sudo a2enmod unique_id
$ sudo service apache2 restart
```
compile and install in the /tmp/ folder
```
$ cd /tmp/
$ wget https://www.modsecurity.org/tarball/2.9.0/modsecurity-2.9.0.tar.gz
$ tar xvzf modsecurity-2.9.0.tar.gz
$ cd modsecurity-2.9.0/
$ sudo ./configure
$ sudo make
$ sudo make install
```
create .conf file in /etc/apache2/mods-available/
```
$ sudo nano /etc/apache2/mods-available/security2.conf
```
add the following lines into the file
```
<IfModule security2_module>
# Default Debian dir for modsecurity's persistent data
SecDataDir /var/cache/modsecurity

# Include all the *.conf files in /etc/modsecurity.
# Keeping your local configuration in that directory
# will allow for an easy upgrade of THIS file and
# make your life easier
Include /etc/modsecurity/*.conf
</IfModule>
```
create .load file in /etc/apache2/mods-available/
```
$ sudo nano /etc/apache2/mods-available/security2.load
```
add the following lines into the file
```
# Depends: unique_id
LoadFile /usr/lib/x86_64-linux-gnu/libxml2.so.2
LoadModule security2_module /usr/lib/apache2/modules/mod_security2.so
```
copy the recommended configuration files
```
$ sudo mkdir /etc/modsecurity/
$ sudo cp modsecurity.conf-recommended unicode.mapping /etc/modsecurity/
$ sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
```
load module
```
$ sudo a2enmod security2
$ sudo service apache2 restart
```
check if module is loaded
```
$ sudo apachectl -M | grep security
```
## Installation OWASP Core Rule Set
install the Open Web Application Security Project (OWASP) Core Rule Set
```
$ wget https://github.com/SpiderLabs/owasp-modsecurity-crs/archive/v3.0/master.zip
$ unzip master.zip
$ sudo cp -r owasp-modsecurity-crs-3.0-master/* /etc/modsecurity/
$ sudo cp /etc/modsecurity/crs_setup.conf.example /etc/modsecurity/crs_setup.conf
```
add the rules to Apache2
```
$ sudo nano /etc/apache2/mods-available/security2.conf
```
add the following line before `</IfModule>`
```
Include "/etc/modsecurity/rules/*.conf"
```
reload Apache2
```
$ sudo service apache2 restart
```
