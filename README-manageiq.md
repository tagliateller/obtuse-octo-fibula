I'm trying to build and run manageiq from source by following theses instructions:

http://ctl.parkerfleming.com/tutorials/manageiq/install-manageiq-on-ubuntu-14-04-on-the-centurylink-cloud/
When I run the following step:

$ git clone https://github.com/ManageIQ/manageiq
$ cd manageiq/vmdb
$ bundle install --without qpid
I get this error:

eugene@3b4d8a92ac5d:~/manageiq/vmdb/manageiq/vmdb$ bundle install --without qpid
Unfortunately, a fatal error has occurred. Please see the Bundler troubleshooting documentation at http://bit.ly/bundler-issues. Thanks!
/home/eugene/manageiq/vmdb/manageiq/lib/Gemfile:1:in `eval_gemfile': Ruby versions less than 2.0 are unsupported! (RuntimeError)
    from /home/eugene/manageiq/vmdb/manageiq/vmdb/Gemfile.global.rb:53:in `eval'
    from /home/eugene/manageiq/vmdb/manageiq/vmdb/Gemfile.global.rb:53:in `include_gemfile'
    from /home/eugene/manageiq/vmdb/manageiq/vmdb/Gemfile:4:in `eval_gemfile'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/dsl.rb:32:in `instance_eval'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/dsl.rb:32:in `eval_gemfile'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/dsl.rb:10:in `evaluate'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/definition.rb:25:in `build'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler.rb:154:in `definition'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/cli/install.rb:77:in `run'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/cli.rb:145:in `install'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/vendor/thor/command.rb:27:in `run'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/vendor/thor/invocation.rb:121:in `invoke_command'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/vendor/thor.rb:363:in `dispatch'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/vendor/thor/base.rb:440:in `start'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/cli.rb:9:in `start'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/bin/bundle:20:in `block in <top (required)>'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/lib/bundler/friendly_errors.rb:5:in `with_friendly_errors'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/gems/bundler-1.7.6/bin/bundle:18:in `<top (required)>'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/bin/bundle:23:in `load'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/bin/bundle:23:in `<main>'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/bin/ruby_executable_hooks:15:in `eval'
    from /home/eugene/.rvm/gems/ruby-1.9.3-p551@global/bin/ruby_executable_hooks:15:in `<main>'
What can I do to overcome this and finish manageiq install?

installin Ruby 2.0.0 worked well. Thanks a lot!

