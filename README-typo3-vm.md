= Installation Typo3 mit Ansible

== Installation Composer

php -r "readfile('https://getcomposer.org/installer');" > composer-setup.php
php -r "if (hash('SHA384', file_get_contents('composer-setup.php')) === 'fd26ce67e3b237fffd5e5544b45b0d92c41a4afe3e3f778e942e43ce6be197b9cdc7c251dcde6e2a52297ea269370680') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); }"
php composer-setup.php
php -r "unlink('composer-setup.php');"

== Vagrant Up

TYPO3_VERSION=master PHP_VERSION=5.5 vagrant up --provision

bash
NOTIFIED: [restart apache] **************************************************** 
failed: [default] => {"failed": true}
msg: Output of config test was:
apache2: Syntax error on line 140 of /etc/apache2/apache2.conf: Syntax error on line 1 of /etc/apache2/mods-enabled/php5.load: Cannot load /usr/lib/apache2/modules/libphp5.so into server: /usr/lib/apache2/modules/libphp5.so: undefined symbol: unixd_config
Action 'configtest' failed.
The Apache error log may have more information.


FATAL: all hosts have already failed -- aborting

NOTIFIED: [restart apache] **************************************************** 
FATAL: no hosts matched or all hosts have already failed -- aborting


FATAL: all hosts have already failed -- aborting

NOTIFIED: [restart apache] **************************************************** 
FATAL: no hosts matched or all hosts have already failed -- aborting


FATAL: all hosts have already failed -- aborting

PLAY RECAP ******************************************************************** 
           to retry, use: --limit @/home/robert/provision.yaml.retry

default                    : ok=17   changed=14   unreachable=0    failed=1   

Ansible failed to complete successfully. Any error output should be
visible above. Please fix these errors and try again.
robert@c3p0:~/workspace/typo3-ci-example$ 

== Manuelle Installation Apache + PHP

vagrant@vagrant-ubuntu-trusty-64:~$ sudo apt-get install apache2 php5 libapache2-mod-php5 mysql-server php5-mysql

OK, nach dem entfernen der debian-spezifischen Tasks läuft das Playbook durch
Ergänzung debian whezzy hatte probleme mit rsync (vagrant !!!)

Fehler bei Ausführung bin/phing

TYPO3 CI example > verify-phpunit:

/vagrant/src/typo3_src-master/typo3/../vendor/autoload.phpPHP Warning:  require(/vagrant/src/typo3_src-master/typo3/../vendor/autoload.php): failed to open stream: No such file or directory in /vagrant/src/typo3_src-master/typo3/cli_dispatch.phpsh on line 26
PHP Fatal error:  require(): Failed opening required '/vagrant/src/typo3_src-master/typo3/../vendor/autoload.php' (include_path='.:/usr/share/php:/usr/share/pear') in /vagrant/src/typo3_src-master/typo3/cli_dispatch.phpsh on line 26
Execution of target "verify-phpunit" failed for the following reason: /vagrant/build.xml:46:44: Task exited with code 255
[phingcall] /vagrant/build.xml:46:44: Task exited with code 255
Execution of target "verify" failed for the following reason: /vagrant/build.xml:33:20: Execution of the target buildfile failed. Aborting.

BUILD FAILED
/vagrant/build.xml:33:20: Execution of the target buildfile failed. Aborting.
Total time: 1.0198 second

Der Path ist falsch

a) korrektur
b) wird das bei composer gebaut ?

TYPO3 CI example > verify-phpunit:

/vagrant/src/typo3_src-master/typo3/../vendor/autoload.phpPHP Fatal error:  Class 'TYPO3\CMS\Backend\Console\Application' not found in /vagrant/src/typo3_src-master/typo3/cli_dispatch.phpsh on line 28
Execution of target "verify-phpunit" failed for the following reason: /vagrant/build.xml:46:44: Task exited with code 255
[phingcall] /vagrant/build.xml:46:44: Task exited with code 255
Execution of target "verify" failed for the following reason: /vagrant/build.xml:33:20: Execution of the target buildfile failed. Aborting.

BUILD FAILED
/vagrant/build.xml:33:20: Execution of the target buildfile failed. Aborting.
Total time: 1.0333 second

vagrant@vagrant-ubuntu-trusty-64:/vagrant$ 

