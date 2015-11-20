wget http://download.opensuse.org/repositories/isv:ownCloud:community/Debian_7.0/Release.key
apt-key add - < Release.key  

Keys installieren

sudo apt-get install debian-keyring debian-archive-keyring
sudo apt-key update

sudo apt-get update

echo 'deb http://download.opensuse.org/repositories/isv:/ownCloud:/community/Debian_7.0/ /' >> /etc/apt/sources.list.d/owncloud.list 
-- geht so nicht, Datei manuell anlegen aber schon

sudo apt-get install owncloud

Löschen der config/config.php ermöglicht die Installation zum 2. Mal

