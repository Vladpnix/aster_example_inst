```
sudo apt -y install git vim curl wget libnewt-dev libssl-dev libncurses5-dev subversion libsqlite3-dev build-essential libjansson-dev libxml2-dev uuid-dev
```
```
cd /usr/src/
wget https://downloads.asterisk.org/pub/telephony/asterisk/asterisk-20-current.tar.gz
tar xvf asterisk-20-current.tar.gz
```
##### To download the MP3 decoder library into the source tree, do the following command.
```
cd asterisk-20*/
contrib/scripts/get_mp3_source.sh
```
##### Run the command below to verify that all dependencies have been met:
```
sudo contrib/scripts/install_prereq install
```
##### Step 3 – Build and Install Asterisk
```
./configure
make menuselect

1. Under Add-ons, select chan_ooh323 and format_mp3
2. Choose audio packets in the manner shown on Core Sound Packages.
3. On Music On Hold, choose the below modules.
4. Go to Extra Sound, and choose Packages as shown.
5. On Applications, enable app_macro.
Click Save&Exit when finished. Keep in mind that you can add more modules as necessary for your installation. Next, we construct Asterisk by running the command below.

make
sudo make install
```
##### Install the documentation as indicated. (OPTIONAL)
```
sudo make progdocs
sudo make samples
sudo make config
sudo ldconfig
```
##### Step 4 – Start Asterisk Service and Create Asterisk User
```
sudo groupadd asterisk
sudo useradd -r -d /var/lib/asterisk -g asterisk asterisk
sudo usermod -aG audio,dialout asterisk
sudo chown -R asterisk:asterisk /etc/asterisk
sudo chown -R asterisk:asterisk /var/{lib,log,spool}/asterisk
sudo chown -R asterisk:asterisk /usr/lib/asterisk
```
##### Make an asterisk the default user by editing the files listed below.
```
$ sudo vim /etc/default/asterisk
AST_USER="asterisk"
AST_GROUP="asterisk"

$ sudo vim /etc/asterisk/asterisk.conf
runuser = asterisk ; The user to run as.
rungroup = asterisk ; The group to run as
```
##### Restart and enable Asterisk.
```
sudo systemctl restart asterisk
sudo systemctl enable asterisk
```
##### Check your connection to Asterisk CLI.
```
sudo asterisk -rvv
```
##### If you have an active firewall, you must allow the following ports:
```
sudo ufw allow proto tcp from any to any port 5060,5061
```

##### Step 5 – Install FreePBX on Debian 12
Install MariaDB database server/client
```
sudo apt -y install mariadb-server mariadb-client
sudo systemctl start mariadb
sudo systemctl enable mariadb

$ sudo mysql_secure_installation 
Switch to unix_socket authentication [Y/n] y
Change the root password? [Y/n] n
Remove anonymous users? [Y/n] y
Remove test database and access to it? [Y/n] y
Reload privilege tables now? [Y/n] y
```
##### Install Node.JS
```
sudo apt install curl dirmngr apt-transport-https lsb-release ca-certificates
sudo curl -sL https://deb.nodesource.com/setup_14.x | sudo bash
sudo apt update
sudo apt -y install gcc g++ make
sudo apt -y install nodejs
```
##### Install Apache2 Web Server
```
sudo apt -y install apache2
sudo cp /etc/apache2/apache2.conf /etc/apache2/apache2.conf_orig
sudo sed -i 's/^\(User\|Group\).*/\1 asterisk/' /etc/apache2/apache2.conf
sudo sed -i 's/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf
```
##### Remove the default index.html page.
```
sudo rm -f /var/www/html/index.html
sudo unlink  /etc/apache2/sites-enabled/000-default.conf
```
##### Install and configure PHP
```
sudo apt install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2
```
##### Add the Sury PPA repository, which contains the most recent PHP packages.
```
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/sury-php.list
```
##### Add the repository’s GPG key.
```
wget -qO - https://packages.sury.org/php/apt.gpg | sudo apt-key add -
```
##### If not in use, remove any current PHP versions that are installed.
```
sudo apt remove php*
```
##### Install all necessary extensions and PHP 7.4 packages.
```
sudo apt install php7.4-{mysql,cli,common,imap,ldap,xml,fpm,curl,mbstring,zip,gd,gettext,xml,json}
```
##### Install the Apache PHP module as well.
```
sudo apt install libapache2-mod-php7.4
```
##### Limit the size of your PHP files.
```
sudo sed -i 's/\(^upload_max_filesize = \).*/\120M/' /etc/php/7.4/apache2/php.ini
sudo sed -i 's/\(^upload_max_filesize = \).*/\120M/' /etc/php/7.4/cli/php.ini
```
##### Change the memory restriction as well.
```
sudo sed -i 's/\(^memory_limit = \).*/\1256M/' /etc/php/7.4/apache2/php.ini
```
##### Install FreePBX on Debian 12
```
sudo apt install sox mpg123 lame ffmpeg sqlite3 git unixodbc dirmngr postfix  odbc-mariadb pkg-config libicu-dev
```
##### Set up ODBC:
```
sudo tee /etc/odbcinst.ini<<EOF
[MySQL]
Description = ODBC for MySQL (MariaDB)
Driver = /usr/lib/x86_64-linux-gnu/odbc/libmaodbc.so
FileUsage = 1
EOF
```
```
sudo tee /etc/odbc.ini<<EOF
[MySQL-asteriskcdrdb]
Description = MySQL connection to 'asteriskcdrdb' database
Driver = MySQL
Server = localhost
Database = asteriskcdrdb
Port = 3306
Socket = /var/run/mysqld/mysqld.sock
Option = 3
EOF
```
##### Download FreePBX
```
wget http://mirror.freepbx.org/modules/packages/freepbx/freepbx-16.0-latest.tgz
```
```
tar -xvzf freepbx-16.0-latest.tgz

cd freepbx
sudo systemctl stop asterisk
sudo ./start_asterisk start

./install -n
```
##### Install each module of Freepbx by running the commands below.
```
sudo fwconsole ma disablerepo commercial
sudo fwconsole ma installall
sudo fwconsole ma delete firewall
sudo fwconsole reload
sudo fwconsole restart

sudo a2enmod rewrite
sudo systemctl restart apache2
```
