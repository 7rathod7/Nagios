Mode
---------------------------------------------
<pre>
sudo su
</pre>

Packages installation
---------------------------------------------
<pre>
sudo apt install apt-utils wget build-essential apache2 libapache2-mod-php7.3 php7.3-cli php7.3-gd sendmail unzip openssl ca-certificates
</pre>

Creating a new user
---------------------------------------------
<pre>
useradd nagios
groupadd nagcmd
usermod -G nagcmd nagios
usermod -a -G nagcmd www-data
</pre>

Creating a directory for downloading Nagios packages
-----------------------------------------------------
<pre>
mkdir ~/downloads
cd ~/downloads
wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.2.1.tar.gz
wget http://www.nagios-plugins.org/download/nagios-plugins-2.1.2.tar.gz
</pre>

Compiling and installing Nagios
----------------------------------------------------
<pre>
cd ~/downloads
tar zxvf nagios-4.2.1.tar.gz
cd nagios-4.2.1/
./configure --with-nagios-group=nagios --with-command-group=nagcmd
make all
make install
make install-init
make install-config
make install-commandmode
make install-webconf
</pre>

If you get Error
------------------------------------------------
<pre>
root@rathod-jarvis:~/downloads/nagios-4.2.1# make install-webconf
/usr/bin/install -c -m 644 sample-config/httpd.conf /etc/httpd/conf.d/nagios.conf
/usr/bin/install: cannot create regular file '/etc/httpd/conf.d/nagios.conf': No such file or directory
make: *** [Makefile:296: install-webconf] Error 1

/usr/bin/install -c -m 644 sample-config/httpd.conf /etc/apache2/sites-enabled/nagios.conf
ls -l /etc/apache2/sites-enabled/
</pre>

Changing the e-mail address
--------------------------------------------------
<pre>
gedit /usr/local/nagios/etc/objects/contacts.cfg
</pre>

Configuring the Web interface, creating a user
After the execution of the following commands, a password for user nagiosadmin will be required.
--------------------------------------------------
<pre>
htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
a2enmod cgi
service apache2 restart
</pre>

Compiling and installing of Nagios plugins
----------------------------------------------------
<pre>
cd ~/downloads
tar zxvf nagios-plugins-2.1.2.tar.gz
cd nagios-plugins-2.1.2
./configure --with-nagios-user=nagios --with-nagios-group=nagios
make
make install
</pre>

Start Nagios
-----------------------------------------------------
<pre>
ln -s /etc/init.d/nagios /etc/rcS.d/S99nagios
/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
/etc/init.d/nagios start
</pre>

Other
------------------------------------------------------
<pre>
apt install postfix

gedit /etc/postfix/main.cf
/*
    relayhost = [smtp.gmail.com]:587
    smtp_sasl_auth_enable = yes
    smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
    smtp_sasl_security_options = noanonymous
    smtp_tls_CAfile = /etc/postfix/cacert.pem
    smtp_use_tls = yes
*/

gedit /etc/postfix/sasl_passwd
/*
    [smtp.gmail.com]:587 <email>:<password>
*/
Note : make sure to enable less secure app access from google account portal

chmod 600 /etc/postfix/sasl_passwd
postmap /etc/postfix/sasl_passwd
chmod 600 /etc/postfix/sasl_passwd.db
cat /etc/ssl/certs/thawte_Primary_Root_CA.pem | tee -a /etc/postfix/cacert.pem
service postfix reload

echo "Hello World" | mail -s "sending email using terminal" <email>

Note : make appropriate changes in /usr/local/nagios/etc/objects/commands.cfg
</pre>