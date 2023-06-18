- module inside class
``` ruby
class Person
  def handshake
    :sloppy
  end

  def mind_contents
    :spam
  end

  module Proper
    def handshake
      :firm
    end
  end

  module Clever
    def mind_contents
      :theories
    end
  end
end

class Professor < Person
  include Proper
  include Clever

  # ...
end
```

- apply in Rails app
``` ruby
# /app/models/product/shopify.rb
class Product
  module Shopify
    extend ActiveSupport::Concern

    def shopify_id
      '#shopify_id'
    end

    # implement other code here
  end
end

# /app/models/product.rb
class Product < ApplicationRecord
  include Shopify
end
```

- When define
``` ruby
  module A
    module B
    end
  end
```
If module A doesn't exist, Ruby will create it first, then define module B. If module A exists, then it will re-open it and define module B inside it.

- difference between
``` ruby
module A
  module B
  end
end

# and

module A::B
```
`module A::B`
  + It assumes that the outer module, i.e. A is already defined. If it's not, Ruby will throw a NameError: uninitialized constant A  error. So use this syntax only if you're sure that module A exists
- access constant
``` ruby
module A
  Z = 10

  module B
    puts Z
  end
end

# output
10

============================================================

module A
  Z = 10
end

# somewhere else, after the above module is defined
module A
  module B
    puts Z
  end
end

# output
10

=============================================================

module A
  Z = 10
end

module A::B
  puts Z
end

# output
uninitialized constant A::B::Z (NameError)

==============================================================

# must use like this
module A
  Z = 10
end

module A::B
  puts A::Z
end

# output
10
```

``` ruby
module A
  PI = 3.14

  module B
    def get_pi = PI  # method shorthand for one-liner methods
  end
end

class C
  include A::B
end

assert_equal 3.14, C.new.get_pi  # Passes!
```

- But if use :: to define A::B without nesting it inside A, a NameError exception will be raised.
``` ruby
module A
  PI = 3.14
end

module A::B
  def get_pi = PI
end

class C
  include A::B
end

assert_raises(NameError) { C.new.get_pi }  # uninitialized constant A::B::PI (NameError)
```
