``` ruby
def measure_time
  start_time = Time.now.to_i
  yield
  end_time = Time.now.to_i - start_time
  puts "#{end_time} seconds"
end

measure_time do
  sleep(2)
end
# 2 seconds
# => nil
```

``` ruby
def print_twice
  yield
  yield
end
print_twice { puts "Hello" }
# "Hello"
# "Hello"
```

``` ruby
def method1(*args, &block)
  puts args.sum
  block.call(6)
end

method1(1, 2, 3) do |param|
  puts "param in call block is #{param}"
end

# 6
# param in call block is 6
```

``` ruby
def yield_example
  puts "block given" if block_given?
end

yield_example
# => nil

yield_example {}
# block given
# => nil
```

``` ruby
my_proc = Proc.new { puts "call my block" }
my_proc.call
```
proc not care about arguments
``` ruby
proc = Proc.new { |x, y| puts "x: #{x} - y: #{y}" }
proc.call
# => nil
```

- return
``` ruby
def some_method
  proc = Proc.new { return :first_name }
  proc.call
  return :last_name # never reach
end

some_method
# => :first_name
```

``` ruby
my_lambda = lambda { |x| puts "I'm lambda #{x}" }
my_lambda2 = ->(x) { puts "hello #{x}" }
my_lambda.call(1)
my_lambda.(1)
```

``` ruby
my_lambda = lambda { |x, y| puts "x: #{x}, y: #{y}" }
my_lambda.call(1)
# => ArgumentError: wrong number of arguments (given 1, expected 2)

my_lambda.call(1, 2)
# => x: 1, y: 2
```

- return
``` ruby
def my_method
  my_lambda = lambda { return 1 }
  my_lambda.call
  2
end

my_method
# => 2
```

``` ruby
def method2
  my_lambda = -> { return 1}
  return my_lambda.call

  2
end

method2
# => 1
```

- Default value proc
``` ruby
my_proc = Proc.new { |x = 1, y = 2| [x, y] }
my_proc.call
# => [1, 2]

my_proc.call(3)
# => [3, 2]
```

``` ruby
my_proc = Proc.new { |x| x + 2}
[1, 2, 3].map(&my_proc)
# => [3, 4, 5]
```

``` ruby
class Bar
  define_method(:initialize) do |*pos_params, **named_params, &block|
    @pos_params = pos_params
    @named_params = named_params
    block.call(self) if block
  end

  define_method(:one_optional_arg) do |arg=nil|
    puts arg
  end

  define_method(:some_args) do |*args|
    puts args.join(', ')
  end

  define_method(:my_name_and_args) do |name, *args|
    puts [name, *args].join(' ')
  end

  define_method(:some_opts) do |**opts|
    puts opts.inspect
  end

  define_method(:named_arg_plus_opts) do |name:, **opts|
    puts({ name: name }.merge(opts).inspect)
  end

  def print
    puts @pos_params.join(', ')
    puts @named_params.inspect
  end
end

foo = Bar.new
foo.one_optional_arg                          # =>
foo.one_optional_arg 'one'                    # => 'one'

foo.some_args                                 # =>
foo.some_args 'Hello', 1, :world              # => Hello, 1, world

# foo.my_name_and_args                        # => ERROR wrong number of arguments (given 0, expected 1+)
foo.my_name_and_args 'David'                  # => David
foo.my_name_and_args 'David', 'was', 'here'   # => David was here

foo.some_opts                                 # => {}
foo.some_opts a: 1, b: 2                      # => {:a=>1, :b=>2}

# foo.named_arg_plus_opts                     # => ERROR missing keyword: :name
foo.named_arg_plus_opts name: 'David'         # => {:name=>"David"}
foo.named_arg_plus_opts name: 'David', role: 'developer'
											  # => {:name=>"David", :role=>"developer"}

BarConstructor.new('The', 'quick', 'brown', 'fox', color: :brown, animal: 'Fox') do |bar|
  bar.print
  # The, quick, brown, fox
  # {:color=>:brown, :animal=>"Fox"}
end
```
