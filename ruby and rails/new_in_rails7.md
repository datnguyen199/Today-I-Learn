- Rails 7.1 adds ActiveJob.perform_all_later to enqueue multiple jobs at once
- Just like Active Record bulk methods, perform_all_later doesn't run any callbacks
``` ruby
welcome_email_jobs = users.map do |user|
  WelcomeEmailJob.new(user)
end

ActiveJob.perform_all_later(welcome_email_jobs)
```

``` ruby
class Thing < ApplicationRecord
  # ...
  self.ignored_columns = ["old_column"]
  # ...
end

- This stops the rest of application being able to reference this column but not remove it from database
- After that, can test and remove it from database later
```

- insert_all, upsert_all will skip callback and validate

- rails 7.1 normalizes
https://www.bigbinary.com/blog/rails-7-1-adds-activerecord-base-normalizes

``` ruby
class User < ApplicationRecord
  normalizes :user_name, with:
    -> user_name { user_name.parameterize.underscore }

  normalizes :email, with: -> { _1.strip.downcase }

  normalizes :profile_image, with:
    -> profile_image {
      profile_image.present? ? URI.parse(profile_image).to_s :
      "https://source.boringavatars.com/beam" },
    apply_to_nil: true
end
```

- #sole and #find_sole_by
``` ruby
class Product
    validates :price, presence: true
end

Product.where(["price = %?", price]).sole
#=> ActiveRecord::RecordNotFound Exception     (if no Product with the given price)
#=> #<Product ...>                             (if one Product with the given price)
#=> ActiveRecord::SoleRecordExceeded Exception (if more than one Product with the given price)

Product.find_sole_by(price: price)
#=> ActiveRecord::RecordNotFound Exception     (if no Product with the given price)
#=> #<Product ...>                             (if one Product with the given price)
#=> ActiveRecord::SoleRecordExceeded Exception (if more than one Product with the given price)
```

- Company.first.products.maximum(:price) -> get max price

- in_order_of
```ruby
ChatRoom.in_order_of(:status, ['user_initiated', 'customer_service_window'])
```

- previously_persisted?
``` ruby
previous_user = User.find_by_name('John Doe')
previous_user.destroy!
previous_user.previously_persisted? # returns true
```
- excluding
``` ruby
=> comments = Comment.excluding(current_user.comments)
=> "SELECT \"comments\".* FROM \"comments\" WHERE \"comments\".\"id\" NOT IN (1, 2)"

alias without
=> users = User.without(current_user)
=> "SELECT \"users\".* FROM \"users\" WHERE \"users\".\"id\" != 1"
```
