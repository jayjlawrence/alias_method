This one is difficult to reproduce but I have observed it with the following combination:
    a) jruby 1.7.8
    b) bundler 1.5.0.rc.1
    c) warbler 1.40
    d) git refs in my Gemfile

What appears to occur is when bundler monkey patches Gem::Specification the 'alias_method' does results in the newly aliased method pointing to the newly created replacement. i.e. in bundler/lib/bundler/rubygems_ext.rb:
  1) Gem::Specification.loaded_from -> aliased to -> rg_loaded_from
  2) New Gem::Specification.loaded_from defined immediately after
  3) Calling rg_loaded_from actually executes loaded_ from
  4) = stack level too deep

I've made a project that reproduces this issue. Interestingly this example fails in 1.7.6 as well but my real project succeeds in 1.7.6. I have not tried earlier versions.

You may check out https://github.com/jayjlawrence/alias_method and perform the following:
  a) rvm use 1.7.8
  b) bundle install
  c) bundle exec warble war
  d) observe error

I've confirmed on Linux java -version
java version "1.6.0_27"
OpenJDK Runtime Environment (IcedTea6 1.12.6) (6b27-1.12.6-1ubuntu0.12.04.2)
OpenJDK 64-Bit Server VM (build 20.0-b12, mixed mode)

and OS X java -version
java version "1.7.0_25"
Java(TM) SE Runtime Environment (build 1.7.0_25-b15)
Java HotSpot(TM) 64-Bit Server VM (build 23.25-b01, mixed mode)

