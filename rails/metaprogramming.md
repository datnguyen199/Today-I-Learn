- super and super()
+ super: will auto pass all argurments of parent
+ super(): explicit pass argurments

``` ruby
module ExecutionTimer
  def call
    time = Time.now
    super
  ensure
    result = Time.now - time
    puts "Call executed in #{result} seconds"
  end
end

class Service
  prepend ExecutionTimer

  def call
    sleep(2)
  end
end
```

- define_method: is a (private) method of the object Class, define method for instance to call it
+ can not call define_method directly inside instance method -> must call in class method or use self.class.send(...) in instance method -> create instance method
``` ruby
class MyClass
  define_method(:a_instance_method) do |param1|
    puts "This is instance method of MyClass with param is #{param1}"
  end

  class << self
    define_method(:a_class_method) do |*args|
      puts "This is class method of MyClass with param is #{args[0]}"
    end
  end

  def instance_method1
    self.class.send(:define_method, :method1) do
      puts "I'm a instance method"
    end
  end

  def self.class_method1
    define_method(:instance_method2) do |param|
      puts "I am a instance method with param #{param}"
    end
  end
end

MyClass.class_method1 # will create instance method of MyClass
```

- define_singletone_method: define method for object
``` ruby
a = "abc"
a.define_singleton_method(:hello) { puts "hello" }
a.hello # print 'hello'
```

``` ruby
class MyClass
  def instance_method
    define_singleton_method(:method1) do |*args|
      puts "instance method with params #{args}"
    end
  end
end
```

``` ruby
class Base
  def self.meta_instance_method(&block)
    define_method('instance_method1', block)
  end

  def self.meta_class_method(&block)
    define_singleton_method('class_method1', block)
  end
end

class Child < Base
  meta_instance_method do
    "This is meta_instance_method"
  end

  meta_class_method do
    "This is meta_class_method"
  end

  def method_missing(m)
    self.class.send(:define_method, "missing") do
      "method_missing"
    end
  end
end

puts Child.new.ins
puts Child.cls
```

- method_missing
``` ruby
class User
  def method_missing(method_name, *args)
    attr_name = method_name.to_s[4..]
    instance_variable_set("@#{attr_name}", args[0])
  end

  class << self
    def method_missing(method_name, *args, **options)
      puts method_name
      puts args
      puts options
    end
  end
end

u1 = User.new
u1.set_first_name('first_name')
u1.instance_variables # [:@first_name]
```

``` ruby
def method_missing(method_name, *args, &block)
 if @adapter.respond_to?(method_name)
   @adapter.public_send(method_name, *args)
 else
   super
 end
end
```

``` ruby
module Greeting
  module ClassMethods
    def hello
      puts "class hello"
    end
  end

  def self.included(base)
    base.extend(ClassMethods)
  end

  def hello
    puts "instance hello"
  end
end

class A
  include Greeting
end
```
