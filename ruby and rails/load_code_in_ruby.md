- $LOAD_PATH: references all directories with Ruby source files registered in an array.

- load
  + parse and execute a Ruby file
  + This doesn't mean it's relative to the file where the load is located — it's instead relative to the file that started the Ruby process
  + If it comes across it again, it will load and execute it again, picking any new changes in the file, doesn't matter if you had already loaded it earlier.
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
