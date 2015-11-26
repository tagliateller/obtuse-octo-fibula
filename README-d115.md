Installation D115 - AWS
=======================

Installation LAMP + Test
------------------------

Amazon Linux AMI 2015.03 (HVM), SSD Volume Type - ami-1ecae776
t2.micro
30 GB Storage
Name: d115
my-security-group
my-key-pair

my-security-group: myip inbound setzen

weiteres Vorgehen nach http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-LAMP.html

ssh -i my-key-pair.pem ec2-user@ec2-52-4-193-45.compute-1.amazonaws.com
sudo yum update -y
sudo yum install -y httpd24 php56 mysql55-server php56-mysqlnd
sudo service httpd start -> OK
sudo chkconfig httpd on
[ec2-user@ip-172-31-3-23 ~]$ chkconfig --list httpd
httpd          	0:Aus	1:Aus	2:Ein	3:Ein	4:Ein	5:Ein	6:Aus

inbound-Rule für security group / HTTP definieren, Aufruf Testpage klappt


[ec2-user@ip-172-31-3-23 ~]$ ls -l /var/www
insgesamt 20
drwxr-xr-x 2 root root 4096 18. Mär 20:24 cgi-bin
drwxr-xr-x 3 root root 4096  5. Jul 09:25 error
drwxr-xr-x 2 root root 4096 18. Mär 20:24 html
drwxr-xr-x 3 root root 4096  5. Jul 09:25 icons
drwxr-xr-x 2 root root 4096  5. Jul 09:25 noindex


sudo groupadd www
sudo usermod -a -G www ec2-user

ausloggen, einloggen, gruppen testen

[ec2-user@ip-172-31-3-23 ~]$ groups
ec2-user wheel www

sudo chown -R root:www /var/www
sudo chmod 2775 /var/www
find /var/www -type d -exec sudo chmod 2775 {} +
find /var/www -type f -exec sudo chmod 0664 {} +

echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php

Aufruf http://ec2-52-4-193-45.compute-1.amazonaws.com/phpinfo.php zeigt phpinfo-Seite

D115-Mandant
------------

[ec2-user@ip-172-31-3-23 ~]$ cd /var/www
[ec2-user@ip-172-31-3-23 www]$ ls
cgi-bin  error  html  icons  noindex
[ec2-user@ip-172-31-3-23 www]$ cd html
[ec2-user@ip-172-31-3-23 html]$ ls
phpinfo.php
ssh-keygen -t rsa -C "robert.bloy@itdz-berlin.de"[ec2-user@ip-172-31-3-23 html]$ mkdir -p d115mandant/public
[ec2-user@ip-172-31-3-23 html]$ cp phpinfo.php d115mandant/public/

http://ec2-52-4-193-45.compute-1.amazonaws.com/d115mandant/public/phpinfo.php zeigt gewünscht Info -> OK

D115-Applikation
----------------

sudo yum install git

[ec2-user@ip-172-31-3-23 html]$ ssh-keygen -t rsa -C "robert.bloy@itdz-berlin.de"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ec2-user/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/ec2-user/.ssh/id_rsa.
Your public key has been saved in /home/ec2-user/.ssh/id_rsa.pub.
The key fingerprint is:
79:dd:56:f2:1c:53:b2:47:77:19:30:66:5e:96:cb:d4 robert.bloy@itdz-berlin.de
The key's randomart image is:
+--[ RSA 2048]----+
|             =o+X|
|            + +BE|
|             .=o+|
|         . . . Oo|
|        S . . o o|
|         .   .   |
|                 |
|                 |
|                 |
+-----------------+
[ec2-user@ip-172-31-3-23 html]$ cat ~/.ssh/id_rsa.pub
ssh-rsa XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXx ... XXXX robert.bloy@itdz-berlin.de

Anlage SSH-Key in gitlab ..

[ec2-user@ip-172-31-3-23 html]$ git clone ssh://gitlab@gitlab.berlinonline.net:722/land-intranet/d115mandant.git
Klone nach 'd115mandant'...
remote: Counting objects: 3435, done.
remote: Compressing objects: 100% (2999/2999), done.
remote: Total 3435 (delta 1512), reused 1287 (delta 216)
Empfange Objekte: 100% (3435/3435), 4.85 MiB | 2.54 MiB/s, Fertig.
Löse Unterschiede auf: 100% (1512/1512), Fertig.
Prüfe Konnektivität... Fertig.
[ec2-user@ip-172-31-3-23 html]$ 

[ec2-user@ip-172-31-3-23 html]$ cd d115mandant/
[ec2-user@ip-172-31-3-23 d115mandant]$ ls
bin            composer.json       cron        doc          Makefile      package.json      public       src        vendor
bootstrap.php  composer.lock       data        gulpfile.js  modules       phpmd.rules.xml   README.md    templates
bower.json     config.php.example  DEVELOP.md  less         node_modules  phpunit.xml.dist  routing.php  tests
[ec2-user@ip-172-31-3-23 d115mandant]$ git fetch
[ec2-user@ip-172-31-3-23 d115mandant]$ git checkout openshift
Branch openshift konfiguriert zum Folgen von Remote-Branch openshift von origin.
Gewechselt zu einem neuem Branch 'openshift'
[ec2-user@ip-172-31-3-23 d115mandant]$ 


[ec2-user@ip-172-31-3-23 d115mandant]$ make dev
php -d suhosin.executor.include.whitelist=phar bin/composer.phar update --dev
You are using the deprecated option "dev". Dev packages are installed by default now.
Loading composer repositories with package information
Updating dependencies (including require-dev)         
  - Removing monolog/monolog (1.13.1)
  - Installing monolog/monolog (1.14.0)
    Downloading: 100%         

  - Removing symfony/event-dispatcher (v2.7.0)
  - Installing symfony/event-dispatcher (v2.7.1)
    Downloading: 100%         

  - Installing symfony/filesystem (v2.7.1)
    Downloading: 100%         

  - Installing symfony/config (v2.7.1)
    Downloading: 100%         

  - Installing symfony/dependency-injection (v2.7.1)
    Downloading: 100%         

  - Installing pdepend/pdepend (2.1.0)
    Downloading: 100%         

  - Installing phpmd/phpmd (2.2.3)
    Downloading: 100%         

  - Installing squizlabs/php_codesniffer (2.3.3)
    Downloading: 100%         

  - Installing symfony/yaml (v2.7.1)
    Downloading: 100%         

  - Installing sebastian/version (1.0.6)
    Downloading: 100%         

  - Installing sebastian/recursion-context (1.0.0)
    Downloading: 100%         

  - Installing sebastian/global-state (1.0.0)
    Downloading: 100%         

  - Installing sebastian/exporter (1.2.0)
    Downloading: 100%         

  - Installing sebastian/environment (1.2.2)
    Downloading: 100%         

  - Installing sebastian/diff (1.3.0)
    Downloading: 100%         

  - Installing sebastian/comparator (1.1.1)
    Downloading: 100%         

  - Installing doctrine/instantiator (1.0.5)
    Downloading: 100%         

  - Installing phpunit/php-text-template (1.2.1)
    Downloading: 100%         

  - Installing phpunit/phpunit-mock-objects (2.3.5)
    Downloading: 100%         

  - Installing phpunit/php-timer (1.0.6)
    Downloading: 100%         

  - Installing phpunit/php-file-iterator (1.3.4)
    Downloading: 100%         

  - Installing phpunit/php-token-stream (1.4.3)
    Downloading: 100%         

  - Installing phpunit/php-code-coverage (2.1.7)
    Downloading: 100%         

  - Installing phpunit/phpunit (4.4.5)
    Downloading: 100%         

  - Installing guzzlehttp/promises (1.0.1)
    Downloading: 100%         

  - Installing psr/http-message (1.0)
    Downloading: 100%         

  - Installing guzzlehttp/psr7 (1.1.0)
    Downloading: 100%         

  - Installing guzzlehttp/guzzle (6.0.2)
    Downloading: 100%         

  - Updating bo/clientdldb dev-master (373b24e => 50ddef4)


                                                                                                                                                   
  [RuntimeException]                                                                                                                               
  The .git directory is missing from /var/www/html/d115mandant/vendor/bo/clientdldb, see https://getcomposer.org/commit-deps for more information  
                                                                                                                                                   


update [--prefer-source] [--prefer-dist] [--dry-run] [--dev] [--no-dev] [--lock] [--no-plugins] [--no-custom-installers] [--no-autoloader] [--no-scripts] [--no-progress] [--with-dependencies] [-v|vv|vvv|--verbose] [-o|--optimize-autoloader] [--ignore-platform-reqs] [--prefer-stable] [--prefer-lowest] [packages1] ... [packagesN]


make: *** [dev] Fehler 1
[ec2-user@ip-172-31-3-23 d115mandant]$ 

Auflösung der Runtime-Exception
-------------------------------

[ec2-user@ip-172-31-3-23 d115mandant]$ cd vendor/bo/
[ec2-user@ip-172-31-3-23 bo]$ ls -la
insgesamt 20
drwxrwsr-x  5 ec2-user www 4096  5. Jul 09:54 .
drwxrwsr-x 20 ec2-user www 4096  5. Jul 09:55 ..
drwxrwsr-x  2 ec2-user www 4096  5. Jul 09:54 clientdldb
drwxrwsr-x  2 ec2-user www 4096  5. Jul 09:54 clientzms
drwxrwsr-x  2 ec2-user www 4096  5. Jul 09:54 mellon
[ec2-user@ip-172-31-3-23 bo]$ git clone ssh://gitlab@gitlab.berlinonline.net:722/module/clientdldb.git
Klone nach 'clientdldb'...
remote: Counting objects: 375, done.
remote: Compressing objects: 100% (336/336), done.
remote: Total 375 (delta 180), reused 0 (delta 0)
Empfange Objekte: 100% (375/375), 890.65 KiB | 847.00 KiB/s, Fertig.
Löse Unterschiede auf: 100% (180/180), Fertig.
Prüfe Konnektivität... Fertig.
[ec2-user@ip-172-31-3-23 bo]$ git clone ssh://gitlab@gitlab.berlinonline.net:722/module/clientzms.git
Klone nach 'clientzms'...
remote: Counting objects: 94, done.
remote: Compressing objects: 100% (80/80), done.
remote: Total 94 (delta 34), reused 0 (delta 0)
Empfange Objekte: 100% (94/94), 13.74 KiB | 0 bytes/s, Fertig.
Löse Unterschiede auf: 100% (34/34), Fertig.
Prüfe Konnektivität... Fertig.
[ec2-user@ip-172-31-3-23 bo]$ ssh://gitlab@gitlab.berlinonline.net:722/module/mellon.git
-bash: ssh://gitlab@gitlab.berlinonline.net:722/module/mellon.git: Datei oder Verzeichnis nicht gefunden
[ec2-user@ip-172-31-3-23 bo]$ git clone ssh://gitlab@gitlab.berlinonline.net:722/module/mellon.git
Klone nach 'mellon'...
remote: Counting objects: 83, done.
remote: Compressing objects: 100% (72/72), done.
remote: Total 83 (delta 37), reused 0 (delta 0)
Empfange Objekte: 100% (83/83), 14.15 KiB | 0 bytes/s, Fertig.
Löse Unterschiede auf: 100% (37/37), Fertig.
Prüfe Konnektivität... Fertig.
[ec2-user@ip-172-31-3-23 bo]$ 


Und nochmal:

make dev

[ec2-user@ip-172-31-3-23 d115mandant]$ make dev
php -d suhosin.executor.include.whitelist=phar bin/composer.phar update --dev
You are using the deprecated option "dev". Dev packages are installed by default now.
Loading composer repositories with package information
Updating dependencies (including require-dev)         
  - Updating bo/clientdldb dev-master (373b24e => 50ddef4)
    Checking out 50ddef4587d94f494f58b74b72d32947d05643a8


                                                                                                                                                      
  [RuntimeException]                                                                                                                                  
  Failed to execute git remote set-url composer 'ssh://gitlab@gitlab.berlinonline.net:722/module/clientdldb.git' && git fetch composer && git fetch   
  --tags composer                                                                                                                                     
                                                                                                                                                      
  fatal: No such remote 'composer'                                                                                                                    
                                                                                                                                                      
                                                                                                                                                      


update [--prefer-source] [--prefer-dist] [--dry-run] [--dev] [--no-dev] [--lock] [--no-plugins] [--no-custom-installers] [--no-autoloader] [--no-scripts] [--no-progress] [--with-dependencies] [-v|vv|vvv|--verbose] [-o|--optimize-autoloader] [--ignore-platform-reqs] [--prefer-stable] [--prefer-lowest] [packages1] ... [packagesN]


make: *** [dev] Fehler 1
[ec2-user@ip-172-31-3-23 d115mandant]$ 

Runtime-Exception - wie weiter ??


Neuer Ansatz: clone des Repo wie oben, fetch, checkout openshift --> alles liegt vor
-- mb_string fehlt:

/etc/php.ini

; If enabled, scripts may be written in encodings that are incompatible with
; the scanner.  CP936, Big5, CP949 and Shift_JIS are the examples of such
; encodings.  To use this feature, mbstring extension must be enabled.
; Default: Off
zend.multibyte = On

zzgl.:

[ec2-user@ip-172-31-15-149 html]$ sudo yum install php54-mbstring

die anderen Optionen mb_* in der php.ini wurden auch eingeschaltet, fraglich, ob und was das für einen einfluss hat

endet schließlich mit 

[Sun Jul 05 10:57:45.297725 2015] [:error] [pid 7256] [client 77.3.5.196:33779] PHP Fatal error:  Class 'BO\\Dldb\\TwigExtension' not found in /var/www/html/d115mandant/bootstrap.php on line 43

Neuer Versuch
-------------

Clone des MASTER-Zweigs
make dev

endet mit:

[ec2-user@ip-172-31-15-149 d115mandant]$ make dev
php -d suhosin.executor.include.whitelist=phar bin/composer.phar update --dev
Warning: This development build of composer is over 30 days old. It is recommended to update it by running "bin/composer.phar self-update" to get the latest version.
Loading composer repositories with package information
Updating dependencies (including require-dev)         
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - guzzlehttp/guzzle 6.0.2 requires php >=5.5.0 -> no matching package found.
    - guzzlehttp/guzzle 6.0.1 requires php >=5.5.0 -> no matching package found.
    - guzzlehttp/guzzle 6.0.0 requires php >=5.5.0 -> no matching package found.
    - bo/clientdldb dev-master requires guzzlehttp/guzzle 6.0.* -> satisfiable by guzzlehttp/guzzle[6.0.0, 6.0.1, 6.0.2].
    - Installation request for bo/clientdldb dev-master -> satisfiable by bo/clientdldb[dev-master].

Potential causes:
 - A typo in the package name
 - The package is not available in a stable-enough version according to your minimum-stability setting
   see <https://groups.google.com/d/topic/composer-dev/_g3ASeIFlrc/discussion> for more details.

Read <http://getcomposer.org/doc/articles/troubleshooting.md> for further common problems.
make: *** [dev] Fehler 2


Dann probieren wir es mit make live:

sudo yum install nodejs npm --enablerepo=epel installiert node.js und npm

make live endet in:

[ec2-user@ip-172-31-15-149 d115mandant]$ make live
node_modules/.bin/gulp css
make: node_modules/.bin/gulp: Kommando nicht gefunden
make: *** [css] Fehler 127
[ec2-user@ip-172-31-15-149 d115mandant]$ sudo yum install nodejs npm --enablerepo=epel

Neuer Versuch
-------------

Clone des MASTER-Zweigs
make dev

endet mit:

[ec2-user@ip-172-31-15-149 d115mandant]$ make dev
php -d suhosin.executor.include.whitelist=phar bin/composer.phar update --dev
Warning: This development build of composer is over 30 days old. It is recommended to update it by running "bin/composer.phar self-update" to get the latest version.
Loading composer repositories with package information
Updating dependencies (including require-dev)         
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - guzzlehttp/guzzle 6.0.2 requires php >=5.5.0 -> no matching package found.
    - guzzlehttp/guzzle 6.0.1 requires php >=5.5.0 -> no matching package found.
    - guzzlehttp/guzzle 6.0.0 requires php >=5.5.0 -> no matching package found.
    - bo/clientdldb dev-master requires guzzlehttp/guzzle 6.0.* -> satisfiable by guzzlehttp/guzzle[6.0.0, 6.0.1, 6.0.2].
    - Installation request for bo/clientdldb dev-master -> satisfiable by bo/clientdldb[dev-master].

Potential causes:
 - A typo in the package name
 - The package is not available in a stable-enough version according to your minimum-stability setting
   see <https://groups.google.com/d/topic/composer-dev/_g3ASeIFlrc/discussion> for more details.

Read <http://getcomposer.org/doc/articles/troubleshooting.md> for further common problems.
make: *** [dev] Fehler 2


Dann probieren wir es mit make live:

sudo yum install nodejs npm --enablerepo=epel installiert node.js und npm

make live endet in:

[ec2-user@ip-172-31-15-149 d115mandant]$ make live
node_modules/.bin/gulp css
make: node_modules/.bin/gulp: Kommando nicht gefunden
make: *** [css] Fehler 127
[ec2-user@ip-172-31-15-149 d115mandant]$ sudo yum install nodejs npm --enablerepo=epel

npm install gulp

[ec2-user@ip-172-31-15-149 d115mandant]$ make live
node_modules/.bin/gulp css
[16:26:11] Using gulpfile /var/www/html/d115mandant/gulpfile.js
[16:26:11] Starting 'css'...
[16:26:11] Finished 'css' after 11 ms
node_modules/.bin/gulp js
[16:26:12] Using gulpfile /var/www/html/d115mandant/gulpfile.js
[16:26:12] Starting 'js'...
[16:26:12] Finished 'js' after 26 ms

events.js:72
        throw er; // Unhandled 'error' event
              ^
Error: ENOENT, open '/var/www/html/d115mandant/vendor/jquery/dist/jquery.js'
make: *** [js] Fehler 8
[ec2-user@ip-172-31-15-149 d115mandant]$ ls vendor/jquery/dist/

es scheint also tatsächlich so zu sein, dass einem make live unbedingt ein make dev vorhergehen muss

Ansatz wie bisher, nur mit php55
--------------------------------

neben den bisherigen Schritten muss npm installiert sein
sudo yum install nodejs npm --enablerepo=epel

Läuft durch !!! Startet aber noch nix ...

Aber immer noch: [Wed Jul 08 06:07:34.453835 2015] [:error] [pid 7624] [client 95.119.33.117:52966] PHP Fatal error:  Call to undefined function BO\\Slim\\mb_internal_encoding() in /var/www/html/d115mandant/vendor/bo/slimproject/src/Slim/Bootstrap.php on line 26

sudo yum install php55-mbstring -- danach liefert phpinfo() auch "Zend Multibyte Support provided by mbstring"

Nächster Fehler:

[Wed Jul 08 06:10:17.247165 2015] [:error] [pid 7687] [client 95.119.33.117:52968] PHP Fatal error:  Uncaught exception 'BO\\Dldb\\Exception' with message 'Cannot read /var/www/html/d115mandant/data/locations_de.json' in /var/www/html/d115mandant/vendor/bo/clientdldb/src/Dldb/FileAccess.php:35\nStack trace:\n#0 /var/www/html/d115mandant/bootstrap.php(23): BO\\Dldb\\FileAccess->__construct('/var/www/html/d...', '/var/www/html/d...')\n#1 /var/www/html/d115mandant/public/index.php(3): include('/var/www/html/d...')\n#2 {main}\n  thrown in /var/www/html/d115mandant/vendor/bo/clientdldb/src/Dldb/FileAccess.php on line 35


[ec2-user@ip-172-31-59-81 d115mandant]$ make tests
vendor/bin/phpmd src/ text phpmd.rules.xml
PHP Warning:  date_default_timezone_get(): It is not safe to rely on the system's timezone settings. You are *required* to use the date.timezone setting or the date_default_timezone_set() function. In case you used any of those methods and you are still getting this warning, you most likely misspelled the timezone identifier. We selected the timezone 'UTC' for now, but please set date.timezone to select your timezone. in /var/www/html/d115mandant/vendor/phpmd/phpmd/src/bin/phpmd on line 24

vendor/bin/phpcs --standard=psr2 src/
php -dzend_extension=xdebug.so vendor/bin/phpunit --coverage-html public/_tests/coverage/
Failed loading /usr/lib64/php/5.5/modules/xdebug.so:  /usr/lib64/php/5.5/modules/xdebug.so: cannot open shared object file: No such file or directory
PHPUnit 4.4.5 by Sebastian Bergmann.

Configuration read from /var/www/html/d115mandant/phpunit.xml.dist

The Xdebug extension is not loaded. No code coverage will be generated.

....................R.........

Time: 1,54 seconds, Memory: 69,50Mb

OK, but incomplete, skipped, or risky tests!
Tests: 30, Assertions: 47, Risky: 1.        
[ec2-user@ip-172-31-59-81 d115mandant]$ 

Gelöst mit: 
[ec2-user@ip-172-31-59-81 d115mandant]$ cp ./tests/D115Mandant/fixtures/locations_de.json data/

[Wed Jul 08 06:15:37.638149 2015] [:error] [pid 7690] [client 95.119.33.117:52970] PHP Fatal error:  Uncaught exception 'BO\\Dldb\\Exception' with message 'Cannot read /var/www/html/d115mandant/data/services_de.json' in /var/www/html/d115mandant/vendor/bo/clientdldb/src/Dldb/FileAccess.php:42\nStack trace:\n#0 /var/www/html/d115mandant/bootstrap.php(23): BO\\Dldb\\FileAccess->__construct('/var/www/html/d...', '/var/www/html/d...')\n#1 /var/www/html/d115mandant/public/index.php(3): include('/var/www/html/d...')\n#2 {main}\n  thrown in /var/www/html/d115mandant/vendor/bo/clientdldb/src/Dldb/FileAccess.php on line 42

[ec2-user@ip-172-31-59-81 d115mandant]$ cp ./tests/D115Mandant/fixtures/services_de.json data/

Nun zeigt sich zumindestens die Startseite: http://ec2-54-152-214-233.compute-1.amazonaws.com/d115mandant/public/

Ein Klick auf die vorhandenen Links offenbart jedoch das Fehlen bestimmter Verzeichnisse:

The requested URL /d115mandant/public/standort/ was not found on this server.
The requested URL /d115mandant/public/dienstleistung/ was not found on this server.

Kann aber auch ein Problem bzgl. der Nicht-Erreichbarkeit der REST-API sein. Das wäre das nächste, was angegangen werden muss.

Vorher aber noch ein Test mit make live (nach make dev):

Nach Bereinigung durch BO geht nun auch make live out of the box. Obiges Kopieren ist trotzdem notwendig. Das resultiert dann vermutlich aus dem fehlenden Zugriff auf die REST-API. Das wäre dann der nächste Punkt ...

config.php:

<?php
// @codingStandardsIgnoreFile
 
class App extends \BO\D115Mandant\Application
{
    const APP_PATH = APP_PATH;

    // Uncomment the following lines on debugging
    const SLIM_LOGLEVEL = \Slim\Log::DEBUG;
    const SLIM_DEBUG = TRUE;
    const MONOLOG_LOGLEVEL = \Monolog\Logger::DEBUG;

    // Optimizations
    //
    // const TWIG_CACHE = true; //requires data directory and a manual cleanup on updates

    // Using file data storage
    const REPOSITORY = \BO\D115Mandant\Application::REPOSITORY_FILE;
    public static $data = '/data';
    // For development, it might be helpful to use the test case fixtures as data
    //public static $data = '/tests/D115Mandant/fixtures/';

    // Using Elasticsearch data storage for a better search support (recommended)
    // const REPOSITORY = \BO\D115Mandant\Application::REPOSITORY_ES;
    // const REPOSITORY_ES_INDEX = 'dldb';
    // const REPOSITORY_ES_HOST = 'localhost';
    // const REPOSITORY_ES_PORT = '9200';
    // const REPOSITORY_ES_TRANSPORT = 'Http';

    // Define the URL to the ZMS API
    const ZMS_API_HTTP = 'http://dev.service.berlin.de/terminvereinbarung/api/1/rest/';
    const ZMS_API_USER = "username";
    const ZMS_API_PASS = "password";
    // const ZMS_API_PROXY = "http://proxy:3128";
}
 

Zum Holen der service.json und locations.json gibt es das Script bin/fetchData.sh. Danach müssen die Dateien aber noch in jeweils *_de.json umbenannt werden.

Installation elasticsearch
--------------------------

curl -L -O https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.7.0.zip
Kann gestartet werden, wirft aber Fehler ... ???


Wichtig: Die CRON-Jobs müssen umgesetzt werden, siehe vendor/bin/dldbget

Die hunspell-Verzeichnisse werden mit dem Inhalt aus

curl -L -O http://extensions.openoffice.org/en/download/17633

gefüllt.

Dann klappt auch ein Aufruf von cronjob.hourly, und die locations_de usw. sind danach vorhanden (trotz Fehlermeldung). Der Link der Frontpage zu den Standorten bzw. Dienstleistunge geht aber trotzdem nicht - ggf. ist das ein PHP-Interpreationsproblem.

Ein Schritt weiter - möglicherweise muss noch eine .htaccess-Regel hinzugefügt werden, mit d115mandant/public/index.php explizit aufgerufen klappt es (siehe http://help.slimframework.com/discussions/problems/864-httphostnamedevprojectroute-doesnt-work-but-httphostnamedevprojectindexphproute-does). Nun bescwert sich die Route /standort/ aber über einen fehlenden Index:

Custom Analyzer [default_index] failed to find filter under name [icu_normalizer]

bin/plugin install elasticsearch/elasticsearch-analysis-icu/2.7.0

elasticsearch neu starten, dann ist der o.g. Fehler bei cron/cron.daily weg ...
... und die Standort sind da.

O.g. Lösung bzgl. index.php usw. geht nicht -- muss noch gelöst werden ...

Versuche, elasticsearch als Dämon zu starten:

sudo rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch

sudo vi /etc/yum.repos.d/elasticsearch.repo

[elasticsearch-1.7]
name=Elasticsearch repository for 1.7.x packages
baseurl=http://packages.elastic.co/elasticsearch/1.7/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1

sudo yum -y install elasticsearch

sudo service elasticsearch start
sudo chkconfig --add elasticsearch

sudo /usr/share/elasticsearch/bin/plugin install elasticsearch/elasticsearch-analysis-icu/2.7.0

Damit fehlt aber wieder das hunspell-Dir:

Die hunspell-dirs liegen dann direkt unter /etc/elasticsearch/hunspell usw.

