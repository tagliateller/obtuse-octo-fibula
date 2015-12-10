# Installation

```console
[ec2-user@ip-172-31-27-36 ~]$ gem install rake mail rspec term-ansicolor rcov
Fetching: rake-10.4.2.gem (100%)
Successfully installed rake-10.4.2
Parsing documentation for rake-10.4.2
Installing ri documentation for rake-10.4.2
Done installing documentation for rake after 1 seconds
Fetching: mime-types-2.99.gem (100%)
Successfully installed mime-types-2.99
Fetching: mail-2.6.3.gem (100%)
Successfully installed mail-2.6.3
Parsing documentation for mime-types-2.99
Installing ri documentation for mime-types-2.99
Parsing documentation for mail-2.6.3
Installing ri documentation for mail-2.6.3
Done installing documentation for mime-types, mail after 16 seconds
Fetching: rspec-support-3.4.1.gem (100%)
Successfully installed rspec-support-3.4.1
Fetching: rspec-core-3.4.1.gem (100%)
Successfully installed rspec-core-3.4.1
Fetching: diff-lcs-1.2.5.gem (100%)
Successfully installed diff-lcs-1.2.5
Fetching: rspec-expectations-3.4.0.gem (100%)
Successfully installed rspec-expectations-3.4.0
Fetching: rspec-mocks-3.4.0.gem (100%)
Successfully installed rspec-mocks-3.4.0
Fetching: rspec-3.4.0.gem (100%)
Successfully installed rspec-3.4.0
Parsing documentation for rspec-support-3.4.1
Installing ri documentation for rspec-support-3.4.1
Parsing documentation for rspec-core-3.4.1
Installing ri documentation for rspec-core-3.4.1
Parsing documentation for diff-lcs-1.2.5
Installing ri documentation for diff-lcs-1.2.5
Parsing documentation for rspec-expectations-3.4.0
Installing ri documentation for rspec-expectations-3.4.0
Parsing documentation for rspec-mocks-3.4.0
Installing ri documentation for rspec-mocks-3.4.0
Parsing documentation for rspec-3.4.0
Installing ri documentation for rspec-3.4.0
Done installing documentation for rspec-support, rspec-core, diff-lcs, rspec-expectations, rspec-mocks, rspec after 6 seconds
Fetching: tins-1.8.1.gem (100%)
Successfully installed tins-1.8.1
Fetching: term-ansicolor-1.3.2.gem (100%)
Successfully installed term-ansicolor-1.3.2
Parsing documentation for tins-1.8.1
Installing ri documentation for tins-1.8.1
Parsing documentation for term-ansicolor-1.3.2
Installing ri documentation for term-ansicolor-1.3.2
Done installing documentation for tins, term-ansicolor after 1 seconds
Fetching: rcov-1.0.0.gem (100%)
Building native extensions.  This could take a while...
ERROR:  Error installing rcov:
	ERROR: Failed to build gem native extension.

    /usr/bin/ruby2.0 extconf.rb
mkmf.rb can't find header files for ruby at /usr/share/ruby/include/ruby.h


Gem files will remain installed in /home/ec2-user/.gem/ruby/2.0/gems/rcov-1.0.0 for inspection.
Results logged to /home/ec2-user/.gem/ruby/2.0/gems/rcov-1.0.0/ext/rcovrt/gem_make.out
11 gems installed
```

Ok, so geht es nicht ...

```console
sudo yum -y install ruby-devel gcc
```

Lesen hilft - rcov ist nicht zwingend notwendig (code coverage)

sudo yum -y install git


$ git clone https://github.com/tagliateller/TaskJuggler.git

[ec2-user@ip-172-31-27-36 ~]$ cd TaskJuggler/
[ec2-user@ip-172-31-27-36 TaskJuggler]$ rake gem

```
project acso "Accounting Software"  2002-01-16 +4m {
}

resource boss "Paul Henry Bullock" {
  email "phb@crappysoftware.com"
  rate 480
}
resource dev "Developers" {
  managers boss
  resource dev1 "Paul Smith" {
    email "paul@crappysoftware.com"
    rate 350.0
  }
  resource dev2 "Sébastien Bono" {
    email "SBono@crappysoftware.com"
  }
  resource dev3 "Klaus Müller" {
    email "Klaus.Mueller@crappysoftware.com"
    leaves annual 2002-02-01 - 2002-02-05
  }
}
resource misc "The Others" {
  managers boss
  resource test "Peter Murphy" {
    email "murphy@crappysoftware.com"
    limits { dailymax 6.4h }
    rate 310.0
  }
  resource doc "Dim Sung" {
    email "sung@crappysoftware.com"
    rate 300.0
    leaves annual 2002-03-11 - 2002-03-16
  }
}

task AcSo "Accounting Software" {
  task spec "Specification" {
  }
  task software "Software Development" {
  }
  task test "Software testing" {
  }
  task manual "Manual" {
    journalentry 2002-02-28 "User manual completed" {
      author boss
      summary "The doc writers did a really great job to finish on time."
    }
  }
  task deliveries "Milestones" {
  }
}
```

tj3d -w

tj3client add test.tjp

--> Ergebnis - Statuspage ist (ungeschützt) sichtbar

# Jenkins installieren

* Jenkins wird von git angetriggert und holt die geänderten Projektdateien ab
* Jenkins lädt den tj3d neu

sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins

[ec2-user@ip-172-31-27-36 symmetrical-couscous]$ sudo service jenkins start
Starting Jenkins                                           [  OK  ]
[ec2-user@ip-172-31-27-36 symmetrical-couscous]$ 


GIT-Plugin installieren

GIT-Repo in Job einbinden

--> OK

Shell-Tasks (Stop, Start, Add) anlegen
Problem: im Path nicht verfügbar, keine Rechte zur Ausführung
--> TaskJuggler so installieren, dass jeder Nutzer diesen aufrufen kann

# Apache als Rev. Proxy installieren



