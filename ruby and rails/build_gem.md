- add_runtime_dependency and add_development_dependency
  + Runtime dependencies are what gem needs to work (such as rails needing activesupport): core
  + Development dependencies are useful for when someone wants to make modifications to gem (usually contains: rspec, test...)
  + ```gem install --dev name_of_gem``` and RubyGems will grab both sets of dependencies (runtime and development)
  + Understand same as group :development, :test in Rails
  + ```add_runtime_dependency``` and ```add_development_dependency```

- ``` bundle install``` and ``` gem install```
  + RubyGems is akin to a package manager for Ruby. It's a means by which can install self-contained libraries to use in an applications. When use `gem install gemname` means installing that gem to the current machine that we're developing on.
  + `Bundler` is a tool for managing the gems that an application depends on. When create a `Gemfile`, we list the various gems and their versions that application requires. 
This allows to easily ensure that application has the gems it needs when deploy it to a new location - for example, when push to Heroku, `Gemfile` is used to determine all the dependencies of application.
  + Running `bundle install` will take the list of required gems in gemfile and install them if they aren't installed already, just like doing `gem install` for each gem that application needs.
