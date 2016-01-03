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

... oder zunächst mal als binäre Installation ?? gem install taskkjuggler

# Apache als Rev. Proxy installieren


# Neuinstallation auf ec2

sudo yum -y update

gem install taskjuggler

# per Playbook

- Problem: yum update geht nicht via ansible (1.9.4)
Test mit ad hoc:

```console
[ec2-user@ip-172-31-53-132 ansible-taskjuggler]$ ansible servers -i tj3-inventory -m yum -a "name=* state=latest" -u azureuser --sudo
```
hängt auch - versuche es mit direktem Aufruf von yum

```console
[ec2-user@ip-172-31-53-132 ansible-taskjuggler]$ ansible servers -i tj3-inventory -a "yum -y update" -u azureuser --sudo -vvvv
```

das schlägt unmittelbar fehl, da es Paketkonflikte gibt. Erneuter Versuch nun mit einer frischen Maschine ...

```console
[ec2-user@ip-172-31-53-132 ansible-taskjuggler]$ ansible servers -i tj3-inventory -a "yum -y update" -u azureuser --sudo -vvvv
```

OK - läuft so durch

```console
[ec2-user@ip-172-31-53-132 ansible-taskjuggler]$ ansible servers -i tj3-inventory -m yum -a "name=python-pycurl state=installed" -u azureuser --sudo -vvvv
```
OK

```console
[ec2-user@ip-172-31-53-132 ansible-taskjuggler]$ ansible servers -i tj3-inventory -m rpm_key -a "key=https://jenkins-ci.org/redhat/jenkins-ci.org.key state=present validate_certs=no" -u azureuser --sudo -vvvv
```

OK

usw. - mal wieder komplettes Playbook laufen lassen ...

## AWS

TASK: [Update-center Jenkins] ************************************************* 
<ec2-54-172-35-193.compute-1.amazonaws.com> ESTABLISH CONNECTION FOR USER: ec2-user
<ec2-54-172-35-193.compute-1.amazonaws.com> REMOTE_MODULE command cat /opt/jenkins/updates_jenkins.json | sed '1d;$d' | curl -X POST -H 'Accept: application/json' -d  http://localhost:8080/updateCenter/byId/default/postBack #USE_SHELL
<ec2-54-172-35-193.compute-1.amazonaws.com> EXEC ssh -C -tt -vvv -o ControlMaster=auto -o ControlPersist=60s -o ControlPath="/home/robert/.ansible/cp/ansible-ssh-%h-%p-%r" -o StrictHostKeyChecking=no -o Port=22 -o IdentityFile="/home/robert/workspace/cloud/aws-amazon/my-key-pair-eu-west-1" -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o User=ec2-user -o ConnectTimeout=10 ec2-54-172-35-193.compute-1.amazonaws.com /bin/sh -c 'mkdir -p $HOME/.ansible/tmp/ansible-tmp-1451822840.79-258312809451463 && chmod a+rx $HOME/.ansible/tmp/ansible-tmp-1451822840.79-258312809451463 && echo $HOME/.ansible/tmp/ansible-tmp-1451822840.79-258312809451463'
<ec2-54-172-35-193.compute-1.amazonaws.com> PUT /tmp/tmpZRAHhE TO /home/ec2-user/.ansible/tmp/ansible-tmp-1451822840.79-258312809451463/command
<ec2-54-172-35-193.compute-1.amazonaws.com> EXEC ssh -C -tt -vvv -o ControlMaster=auto -o ControlPersist=60s -o ControlPath="/home/robert/.ansible/cp/ansible-ssh-%h-%p-%r" -o StrictHostKeyChecking=no -o Port=22 -o IdentityFile="/home/robert/workspace/cloud/aws-amazon/my-key-pair-eu-west-1" -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o User=ec2-user -o ConnectTimeout=10 ec2-54-172-35-193.compute-1.amazonaws.com /bin/sh -c 'sudo -k && sudo -H -S -p "[sudo via ansible, key=sstlcoacdqrmgmzyizvsxbrevrurtsdl] password: " -u root /bin/sh -c '"'"'echo BECOME-SUCCESS-sstlcoacdqrmgmzyizvsxbrevrurtsdl; LANG=en_US.UTF-8 LC_CTYPE=en_US.UTF-8 /usr/bin/python /home/ec2-user/.ansible/tmp/ansible-tmp-1451822840.79-258312809451463/command; rm -rf /home/ec2-user/.ansible/tmp/ansible-tmp-1451822840.79-258312809451463/ >/dev/null 2>&1'"'"''
changed: [tj3-master] => {"changed": true, "cmd": "cat /opt/jenkins/updates_jenkins.json | sed '1d;$d' | curl -X POST -H 'Accept: application/json' -d @- http://localhost:8080/updateCenter/byId/default/postBack", "delta": "0:00:01.328056", "end": "2016-01-03 12:07:34.605206", "rc": 0, "start": "2016-01-03 12:07:33.277150", "stderr": "  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current\n                                 Dload  Upload   Total   Spent    Left  Speed\n\r  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0\r  0  927k    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0\r100  941k  100 14592  100  927k  11057   702k  0:00:01  0:00:01 --:--:--  703k", "stdout": "\n\n\n\n    \n    <!DOCTYPE html><html><head resURL=\"/static/ec892dce\">\n    \n\n    <title>Jenkins [Jenkins]</title><link rel=\"stylesheet\" type=\"text/css\" href=\"/static/ec892dce/css/style.css\" /><link rel=\"stylesheet\" type=\"text/css\" href=\"/static/ec892dce/css/color.css\" /><link rel=\"stylesheet\" type=\"text/css\" href=\"/static/ec892dce/css/responsive-grid.css\" /><link rel=\"shortcut icon\" type=\"image/vnd.microsoft.icon\" href=\"/static/ec892dce/favicon.ico\" /><link color=\"black\" rel=\"mask-icon\" href=\"/images/mask-icon.svg\" /><script>var isRunAsTest=false; var rootURL=\"\"; var resURL=\"/static/ec892dce\";</script><script src=\"/static/ec892dce/scripts/prototype.js\" type=\"text/javascript\"></script><script src=\"/static/ec892dce/scripts/behavior.js\" type=\"text/javascript\"></script><script src='/adjuncts/ec892dce/org/kohsuke/stapler/bind.js' type='text/javascript'></script><script src=\"/static/ec892dce/scripts/yui/yahoo/yahoo-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/dom/dom-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/event/event-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/animation/animation-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/dragdrop/dragdrop-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/container/container-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/connection/connection-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/datasource/datasource-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/autocomplete/autocomplete-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/menu/menu-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/element/element-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/button/button-min.js\"></script><script src=\"/static/ec892dce/scripts/yui/storage/storage-min.js\"></script><script src=\"/static/ec892dce/scripts/hudson-behavior.js\" type=\"text/javascript\"></script><script src=\"/static/ec892dce/scripts/sortable.js\" type=\"text/javascript\"></script><script>crumb.init(\"\", \"\");</script><link rel=\"stylesheet\" type=\"text/css\" href=\"/static/ec892dce/scripts/yui/container/assets/container.css\" /><link rel=\"stylesheet\" type=\"text/css\" href=\"/static/ec892dce/scripts/yui/assets/skins/sam/skin.css\" /><link rel=\"stylesheet\" type=\"text/css\" href=\"/static/ec892dce/scripts/yui/container/assets/skins/sam/container.css\" /><link rel=\"stylesheet\" type=\"text/css\" href=\"/static/ec892dce/scripts/yui/button/assets/skins/sam/button.css\" /><link rel=\"stylesheet\" type=\"text/css\" href=\"/static/ec892dce/scripts/yui/menu/assets/skins/sam/menu.css\" /><link title=\"Jenkins\" rel=\"search\" type=\"application/opensearchdescription+xml\" href=\"/opensearch.xml\" /><meta name=\"ROBOTS\" content=\"INDEX,NOFOLLOW\" /><script src=\"/static/ec892dce/scripts/yui/cookie/cookie-min.js\"></script></head><body data-model-type=\"hudson.model.Hudson\" id=\"jenkins\" data-version=\"jenkins-1.643\" class=\"yui-skin-sam jenkins-1.643\"><a href=\"#skip2content\" class=\"skiplink\">Skip to content</a><div id=\"page-head\"><div id=\"header\"><div class=\"logo\"><a id=\"jenkins-home-link\" href=\"/\"><img id=\"jenkins-head-icon\" alt=\"title\" src=\"/static/ec892dce/images/headshot.png\" /><img id=\"jenkins-name-icon\" height=\"34\" alt=\"title\" width=\"139\" src=\"/static/ec892dce/images/title.png\" /></a></div><div class=\"login\"></div><div class=\"searchbox hidden-xs\"><form style=\"position:relative;\" name=\"search\" action=\"/search/\" class=\"no-json\" method=\"get\"><div id=\"search-box-minWidth\"></div><div id=\"search-box-sizer\"></div><div id=\"searchform\"><input id=\"search-box\" placeholder=\"search\" name=\"q\" class=\"has-default-text\" /> <a href=\"http://wiki.jenkins-ci.org/display/JENKINS/Search+Box\"><img style=\"width: 16px; height: 16px; \" class=\"icon-help icon-sm\" src=\"/static/ec892dce/images/16x16/help.png\" /></a><div id=\"search-box-completion\"></div><script>createSearchBox(\"/search/\");</script></div></form></div></div><div id=\"breadcrumbBar\"><tr id=\"top-nav\"><td id=\"left-top-nav\" colspan=\"2\"><link rel='stylesheet' href='/adjuncts/ec892dce/lib/layout/breadcrumbs.css' type='text/css' /><script src='/adjuncts/ec892dce/lib/layout/breadcrumbs.js' type='text/javascript'></script><div class=\"top-sticker noedge\"><div class=\"top-sticker-inner\"><div id=\"right-top-nav\"></div><ul id=\"breadcrumbs\"><li class=\"item\"><a class=\"model-link inside\" href=\"/\">Jenkins</a></li><li class=\"children\" href=\"/\"></li></ul><div id=\"breadcrumb-menu-target\"></div></div></div></td></tr></div></div><div id=\"page-body\" class=\"clear\"><div id=\"side-panel\"><div class=\"task\"><a class=\"task-icon-link\" href=\"http://jenkins-ci.org/\"><img style=\"width: 24px; height: 24px; width: 24px; height: 24px; margin: 2px;\" class=\"icon-next icon-md\" src=\"/static/ec892dce/images/24x24/next.png\" /></a> <a class=\"task-link\" href=\"http://jenkins-ci.org/\">Jenkins project</a></div><div class=\"task\"><a class=\"task-icon-link\" href=\"http://issues.jenkins-ci.org/\"><img style=\"width: 24px; height: 24px; width: 24px; height: 24px; margin: 2px;\" class=\"icon-gear2 icon-md\" src=\"/static/ec892dce/images/24x24/gear2.png\" /></a> <a class=\"task-link\" href=\"http://issues.jenkins-ci.org/\">Bug tracker</a></div><div class=\"task\"><a class=\"task-icon-link\" href=\"http://jenkins-ci.org/content/mailing-lists\"><img style=\"width: 24px; height: 24px; width: 24px; height: 24px; margin: 2px;\" class=\"icon-search icon-md\" src=\"/static/ec892dce/images/24x24/search.png\" /></a> <a class=\"task-link\" href=\"http://jenkins-ci.org/content/mailing-lists\">Mailing Lists</a></div><div class=\"task\"><a class=\"task-icon-link\" href=\"https://twitter.com/jenkinsci\"><img style=\"width: 24px; height: 24px; width: 24px; height: 24px; margin: 2px;\" class=\"icon-user icon-md\" src=\"/static/ec892dce/images/24x24/user.png\" /></a> <a class=\"task-link\" href=\"https://twitter.com/jenkinsci\">Twitter: @jenkinsci</a></div></div><div id=\"main-panel\"><a name=\"skip2content\"></a><h1 style=\"text-align: center\"><img height=\"179\" width=\"154\" src=\"/static/ec892dce/images/rage.png\" /><span style=\"font-size:50px\"> Oops!</span></h1><div id=\"error-description\"><p>A problem occurred while processing the request.\n        Please check <a href=\"https://issues.jenkins-ci.org/\">our bug tracker</a> to see if a similar problem has already been reported.\n        If it is already reported, please vote and put a comment on it to let us gauge the impact of the problem.\n        If you think this is a new issue, please file a new issue.\n        When you file an issue, make sure to add the entire stack trace, along with the version of Jenkins and relevant plugins.\n        <a href=\"http://jenkins-ci.org/content/mailing-lists\">The users list</a> might be also useful in understanding what has happened.</p><h2>Stack trace</h2><pre style=\"margin:2em; clear:both\">javax.servlet.ServletException: org.acegisecurity.AccessDeniedException: browser-based download disabled\n\tat org.kohsuke.stapler.Stapler.tryInvoke(Stapler.java:796)\n\tat org.kohsuke.stapler.Stapler.invoke(Stapler.java:876)\n\tat org.kohsuke.stapler.MetaClass$6.doDispatch(MetaClass.java:249)\n\tat org.kohsuke.stapler.NameBasedDispatcher.dispatch(NameBasedDispatcher.java:53)\n\tat org.kohsuke.stapler.Stapler.tryInvoke(Stapler.java:746)\n\tat org.kohsuke.stapler.Stapler.invoke(Stapler.java:876)\n\tat org.kohsuke.stapler.MetaClass$4.doDispatch(MetaClass.java:211)\n\tat org.kohsuke.stapler.NameBasedDispatcher.dispatch(NameBasedDispatcher.java:53)\n\tat org.kohsuke.stapler.Stapler.tryInvoke(Stapler.java:746)\n\tat org.kohsuke.stapler.Stapler.invoke(Stapler.java:876)\n\tat org.kohsuke.stapler.Stapler.invoke(Stapler.java:649)\n\tat org.kohsuke.stapler.Stapler.service(Stapler.java:238)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:848)\n\tat org.eclipse.jetty.servlet.ServletHolder.handle(ServletHolder.java:686)\n\tat org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1494)\n\tat hudson.util.PluginServletFilter$1.doFilter(PluginServletFilter.java:132)\n\tat hudson.util.PluginServletFilter.doFilter(PluginServletFilter.java:123)\n\tat org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1482)\n\tat hudson.security.csrf.CrumbFilter.doFilter(CrumbFilter.java:49)\n\tat org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1482)\n\tat hudson.security.ChainedServletFilter$1.doFilter(ChainedServletFilter.java:84)\n\tat hudson.security.ChainedServletFilter.doFilter(ChainedServletFilter.java:76)\n\tat hudson.security.HudsonFilter.doFilter(HudsonFilter.java:171)\n\tat org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1482)\n\tat org.kohsuke.stapler.compression.CompressionFilter.doFilter(CompressionFilter.java:49)\n\tat org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1482)\n\tat hudson.util.CharacterEncodingFilter.doFilter(CharacterEncodingFilter.java:81)\n\tat org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1482)\n\tat org.kohsuke.stapler.DiagnosticThreadNameFilter.doFilter(DiagnosticThreadNameFilter.java:30)\n\tat org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1474)\n\tat org.eclipse.jetty.servlet.ServletHandler.doHandle(ServletHandler.java:499)\n\tat org.eclipse.jetty.server.handler.ScopedHandler.handle(ScopedHandler.java:137)\n\tat org.eclipse.jetty.security.SecurityHandler.handle(SecurityHandler.java:533)\n\tat org.eclipse.jetty.server.session.SessionHandler.doHandle(SessionHandler.java:231)\n\tat org.eclipse.jetty.server.handler.ContextHandler.doHandle(ContextHandler.java:1086)\n\tat org.eclipse.jetty.servlet.ServletHandler.doScope(ServletHandler.java:428)\n\tat org.eclipse.jetty.server.session.SessionHandler.doScope(SessionHandler.java:193)\n\tat org.eclipse.jetty.server.handler.ContextHandler.doScope(ContextHandler.java:1020)\n\tat org.eclipse.jetty.server.handler.ScopedHandler.handle(ScopedHandler.java:135)\n\tat org.eclipse.jetty.server.handler.HandlerWrapper.handle(HandlerWrapper.java:116)\n\tat org.eclipse.jetty.server.Server.handle(Server.java:370)\n\tat org.eclipse.jetty.server.AbstractHttpConnection.handleRequest(AbstractHttpConnection.java:489)\n\tat org.eclipse.jetty.server.AbstractHttpConnection.headerComplete(AbstractHttpConnection.java:949)\n\tat org.eclipse.jetty.server.AbstractHttpConnection$RequestHandler.headerComplete(AbstractHttpConnection.java:1011)\n\tat org.eclipse.jetty.http.HttpParser.parseNext(HttpParser.java:651)\n\tat org.eclipse.jetty.http.HttpParser.parseAvailable(HttpParser.java:235)\n\tat org.eclipse.jetty.server.AsyncHttpConnection.handle(AsyncHttpConnection.java:82)\n\tat org.eclipse.jetty.io.nio.SelectChannelEndPoint.handle(SelectChannelEndPoint.java:668)\n\tat org.eclipse.jetty.io.nio.SelectChannelEndPoint$1.run(SelectChannelEndPoint.java:52)\n\tat winstone.BoundedExecutorService$1.run(BoundedExecutorService.java:77)\n\tat java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)\n\tat java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)\n\tat java.lang.Thread.run(Thread.java:745)\nCaused by: org.acegisecurity.AccessDeniedException: browser-based download disabled\n\tat jenkins.model.DownloadSettings.checkPostBackAccess(DownloadSettings.java:85)\n\tat hudson.model.UpdateSite.doPostBack(UpdateSite.java:180)\n\tat sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)\n\tat sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)\n\tat sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)\n\tat java.lang.reflect.Method.invoke(Method.java:606)\n\tat org.kohsuke.stapler.Function$InstanceFunction.invoke(Function.java:298)\n\tat org.kohsuke.stapler.Function.bindAndInvoke(Function.java:161)\n\tat org.kohsuke.stapler.Function.bindAndInvokeAndServeResponse(Function.java:96)\n\tat org.kohsuke.stapler.MetaClass$1.doDispatch(MetaClass.java:121)\n\tat org.kohsuke.stapler.NameBasedDispatcher.dispatch(NameBasedDispatcher.java:53)\n\tat org.kohsuke.stapler.Stapler.tryInvoke(Stapler.java:746)\n\t... 52 more\n</pre></div></div></div><footer><div class=\"container-fluid\"><div class=\"row\"><div class=\"col-md-6\" id=\"footer\"></div><div class=\"col-md-18\"><span class=\"page_generated\">Page generated: Jan 3, 2016 12:07:34 PM</span><span class=\"rest_api\"><a href=\"api/\">REST API</a></span><span class=\"jenkins_ver\"><a href=\"http://jenkins-ci.org/\">Jenkins ver. 1.643</a></span><div id=\"l10n-dialog\" class=\"dialog\"></div><div id=\"l10n-footer\" style=\"display:none; float:left\"><a href=\"#\" onclick=\"return showTranslationDialog();\"><img src=\"/static/ec892dce/plugin/translation/flags.png\" />\n      Help us localize this page\n    </a></div><script>var footer = document.getElementById('l10n-footer');\n    var f = document.getElementById('footer');\n    f.insertBefore(footer,f.firstChild);\n    footer.style.display=\"block\";\n\n    var translation={}; \n    translation.bundles = \"lGvAtpmMaaCdkgRzENmgk0SmsXEvKCIvBwyuM7+3oClHBVoeGtWeBYB4brJ5a9U7EU88+ZSVQmc9vYsJUHKS77itKYCDrtxvvkbZL3rapTg/zlqG2EeYlPCzQMwtStglorzBuDeTFCSOcWLOlPvREaUcf9odrFhK8w/cvnXrLq15J6zQsIADGygN6CBRqDQJT7b1TcJY5+IZg+DCjd3DW1NSulujNmrPQebAzZDXwbhx3+UXXvRP2H4yU7ydlAdjOY7R8E8LcdIuHc3VSlPMwHyQw2h4BTltAf02HKq6+Gp0/mxn8N4XrG4nnjksMHETrAddvzt1kilTWB/S6gnMC/bMRTmN9o5Y4F6ufrPM4cmjBb394csopxmg8GQFhkGubWq2RVOdCNVmDDBBh4emAzxrvVO6bXCGxxrLhLY6VIuVInH7J+FZ6tS1FG6Z+Rni\";\n    translation.detectedLocale = \"\";\n\n    function showTranslationDialog() {\n      if(!translation.launchDialog)\n        loadScript(\"/static/ec892dce/plugin/translation/dialog.js\");\n      else\n        translation.launchDialog();\n      return false; \n    }</script><script>Behaviour.addLoadEvent(function() {\n        loadScript(\"http://usage.jenkins-ci.org/usage-stats.js?Dp7IFjlql8+aV6Lae2jvke7Mg+AQ6Cc2nPj+HtzSFWO8CgElNM9PdKEanDRg6kOufU+RzNMI/sBI/0m5mF3CQ/I5LtyjaPc/d9hsgFESBvxrOmjwkdBjoV95sINMVJiPf4W3hkZV68wQawJGF3WvurA7t7KNts+aL4KxHnxIp7/sEP8ajozNfei8L17PlDylqvg2RaJZQhF9hZ9TDEXlxS+nkQ0vG+D84V+vH61vZQXFi3OuAQgln2CYr7uip0fP6vEL63i24/VKKc3oOnS/6PORaFv88nnd0zIZeaIUrg51S6w9P3Ii77kUz25GC2/jbGR+K8udAr9+bLsH4Idx7Bemterccaz6eyYNdA+nfZyuoT+zZg/IX7gg3wrVhe5GtbaJagBvu3ZTuF++r1HeF0FWJbKJGn/yCfvhuhyu35AErJRMTzUrnBmZdvr7gQ9Y/P2Ae3cYxAZJCcSgGYkEwUQBhoKc2yTWI3ZZIm9ODrlBF+gaPZ/QacsovjyQtpb1TzTPSi7h7KTKhPwFO0W3Ptqk1jXAl0aGRWJ4zbXpPYfh6Cc7bz1//I2s0xxY0ZEM/ZrKLjtv6PVBkgIROMm5FXuMAYTI01ilLvm3uvqnTSmjZEGBRaHV3yO+ntpuCyRA2xzBEYOyMXmlHNOYWb/jJfFLldfmSwCHppDasIQoNRIUW20l5pzzor+AsDLY8CIsX8PtgS/ycuJRDDwD2ABdI6LTWbT3omNmZSw+49vVm5nmFeDGtOT0jicBFt6D5WxxO82iHrwubEhAq0KCps4IcFqoW2HrreTCqrZOw2e/OGBdo8udN+fRQyMECJQESX/u3k/fGB+77Ml8AkfROxUECA==\");\n      });</script></div></div></div></footer></body></html>", "warnings": []}
ERROR: change handler (Restart Jenkins) is not defined
[robert@Marions-Arbeits-Laeppi ansible-taskjuggler]$ 

Change Handler ist nicht definiert, siehe https://github.com/ICTO/ansible-jenkins





