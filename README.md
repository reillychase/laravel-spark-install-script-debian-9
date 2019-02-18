# Laravel Spark Install Script [Debian 9]
My first time installing Laravel Spark took several days. I hope these tips will make it easier for you.

## Install Virtualmin, setup website etc
wget http://software.virtualmin.com/gpl/scripts/install.sh

chmod +x install.sh

./install.sh

Enable Apache SSL when creating the server


## Install PHP 7.3
apt update

apt upgrade -y

apt -y install lsb-release apt-transport-https ca-certificates 

wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg

echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/php7.3.list

apt -y install php7.3

php -v

apt install php7.3-cli php7.3-fpm php7.3-json php7.3-pdo php7.3-mysql php7.3-zip php7.3-gd  php7.3-mbstring php7.3-curl php7.3-xml 
php7.3-bcmath php7.3-json

apt install libapache2-mod-php7.3


## Install Composer
apt install curl php-cli php-mbstring git unzip

curl -sS https://getcomposer.org/installer -o composer-setup.php

php composer-setup.php --install-dir=/usr/bin --filename=composer


## Spark Installer
composer global require laravel/installer

## Create PATH
echo 'export PATH="$PATH:/home/captifi/spark-installer"' >> /etc/profile.d/spark.sh

echo 'export PATH="$PATH:~/.config/composer/vendor/laravel/installer/"' >> /etc/profile.d/laravel.sh

echo 'export PATH="$PATH:$HOME/.config/composer/vendor/laravel/installer/"' >> ~/.bashrc

source ~/.bashrc

exit

## Clean up
apt autoremove

## Install git
apt-get install git

## Install Spark Installer
cd /home/captifi

git clone https://github.com/laravel/spark-installer.git

cd spark-installer

composer install

## Install NPM and NodeJS
curl -sL https://deb.nodesource.com/setup_10.x > setup_10.x 

chmod +x setup_10.x

./setup_10.x

apt install nodejs

apt install build-essential libssl-dev

## Create Spark Project
cd /home/captifi

spark new captifi-spark-team-billing --team-billing

## Change Virtualmin public_html path
Under Website options - change to /home/captifi/captifi-spark-team-billing/public

## Fix NPM
cd captifi-spark-team-billing

npm audit fix --force

## Fix Apache and PHP Version
a2dismod php7.0

a2enmod php7.3

service apache2 restart

Then go to Website Options in Virtualmin and change from FCGI to mod_php

## Fix permissions
chown captifi:captifi * -R

chown captifi:captifi /home/captifi/captifi-spark-team-billing/.[^.]*

chmod -R 775 storage

chmod -R 775 bootstrap/cache

## Link Storage directory
php artisan storage:link

## Setup admin/"developer" account
Add your e-mail address to the $developers property in the App\Providers\SparkServiceProvider.

## Install Adminer
Then create a database and username/pass

## Fix Adminer login as root
mysql -u root

DROP USER 'root'@'localhost';

CREATE USER 'root'@'%' IDENTIFIED BY 'your-password';

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;

FLUSH PRIVILEGES;

## Fix a Laravel/Spark DB issue
cd app/Providers

nano AppServiceProvider.php

Under boot() section, add:

Schema::defaultStringLength(191);

Also add this to top:

use Illuminate\Support\Facades\Schema;

## Migrate database
nano .env and setup db name, user, pass

php artisan migrate
