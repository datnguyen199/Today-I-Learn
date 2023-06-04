- Use a thread-local variable, which is still a global variable, but visible only within a current thread.

``` ruby
class User < ApplicationRecord
  def self.current_ip
    Thread.current[:current_ip]
  end

  def self.current_ip=(ip)
    Thread.current[:current_ip] = ip
  end
end
```

- adding a unique index on the database level

- Pesimistic locking

``` ruby
def charge_user_for_order(user_id, order_id)
  user = User.find(user_id)
  order = Order.find(order_id)
  ActiveRecord::Base.transaction do
    user.lock! # do not forget about this!
    user.credits -= order.price
    user.save!
    order.paid!
  end
end
```

- Advisory lock: to help prevent concurrent in multiple process, not just threads in same process
Gem: https://github.com/ClosureTree/with_advisory_lock
``` ruby
class S3Worker
  include Sidekiq::Worker

  def perform(*args)
    ActiveRecord::Base.with_advisory_lock("S3Worker-Lock") do
      fetch_file
      modify_file
      upload_file
     end
  end
end
```
Once a lock with S3Worker-Lock is acquired, every process trying to acquire the same lock will need to wait until the existing one is released.

``` ruby
def charge_user_for_order(user_id, order_id)
  ActiveRecord::Base.transaction do
    # wrong if declare outside like this: bcs there is no reloading of the record in the code so we could end up with executing further logic on the outdated records.
    # user = User.find(user_id)
    # order = Order.find(order_id)
    ActiveRecord::Base.with_advisory_lock("charge_user_for_order_#{user_id}_#{order_id}") do
      # need to wrap inside
      user = User.find(user_id)
      order = Order.find(order_id)
      user.credits -= order.price
      user.save!
      order.paid!
    end
  end
end
```

- Redlock: lock for Redis
Gem: https://github.com/leandromoreira/redlock-rb
``` ruby
redlock = Redlock::Client.new(["redis://localhost:6379"])
expiration_time_in_milliseconds = 60_000

first_lock = lock_manager.lock("S3Worker-Lock", expiration_time_in_milliseconds)
# => almost immediately returns a hash containing data about the lock
second_lock = lock_manager.lock("S3Worker-Lock", expiration_time_in_milliseconds)
# => almost immediately returns `false`
```

- Optimistic locking: adding lock_version column to the model’s table
