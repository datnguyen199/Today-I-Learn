- $LOAD_PATH: references all directories with Ruby source files registered in an array.

- load
  + parse and execute a Ruby file
  + This doesn't mean it's relative to the file where the load is located — it's instead relative to the file that started the Ruby process
  + If it comes across it again, it will load and execute it again, picking any new changes in the file, doesn't matter if had already loaded it earlier.
  + each call to the load method executes the loaded script

``` ruby
# script.rb

puts "Inside script"
load "loaded_script.rb"

method2

puts "Inside script"
load "loaded_script.rb"

a = A.new
a.method1
```

``` ruby
# loaded_script.rb

class A
  def method1
    puts "method1"
  end
end

puts "Inside load script"

def method2
  puts "method 2"
end
```

- autoload
  + This method takes two arguments: a module (it can be either a string or a symbol) and a filename (string), and registers the filename to be loaded the first time that module is accessed.
``` ruby
autoload(:TestModule, "lib/modules/test_module.rb")

# script.rb

puts "Inside script"
autoload :LoadedScript, "./loaded_script.rb"
puts "Inside script"

LoadedScript.new

# loaded_script.rb

puts "Loaded file"

class LoadedScript
end
```
```
~ ruby script.rb

Inside script
Inside script
Loaded file
```

- require
  + The require method also loads the given file, similar load. However, it won't load the file if it's already loaded
  + If the filename neither resolves to an absolute path nor starts with ./ or ../, the file will be searched for in the library directories listed in $LOAD_PATH.
  + When require a gem, really just placing that gem’s lib directory onto $LOAD_PATH.
  + if call `require './test.rb'`: it will only work if run Ruby process in the same directory that test.rb (depends on where run Ruby process)
  + shouldn't start the argument to require with a .

``` ruby
# script.rb

puts "Inside script"
require "./required_script"
puts "Inside script"
require "./required_script"

# required_script.rb
puts "Required file"

runing > ruby script.rb
Inside script
Required file
Inside script
```

- require_relative
  + require_relative always looks for a file that is relative to the current file working on

``` ruby
- require_relative 'path/to/file': Assumes that the relative path between the two Ruby source files will stay the same.
- require 'path/to/file': Assumes that path/to/file is inside one of the directories on the load path ($LOAD_PATH). This generally requires extra setup, since have to add something to the load path.
- require './path/to/file': Assumes that the relative path from the Ruby process's current working directory to file.rb is going to stay the same.
```
