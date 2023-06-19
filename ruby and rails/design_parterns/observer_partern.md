``` ruby
class Subject
  def initialize
    @observers = []
  end

  def add_observer(observer)
    @observers << observer
  end

  def delete_observer(observer)
    @observers.delete(observer)
  end

  def notify_observers
    @observers.each do |observer|
      observer.update(self)
    end
  end
end

class Payroll
  def update(changed_employee)
    puts("Cut a new check for #{changed_employee.name}!")
    puts("His salary is now #{changed_employee.salary}!")
  end
end

class TaxMan
  def update(changed_employee)
    puts("Send #{changed_employee.name} a new tax bill!")
  end
end

# inheritance
class Employee < Subject
  attr_reader :name, :address
  attr_reader :salary

  def initialize(name, title, salary)
    super()
    @name   = name
    @title  = title
    @salary = salary
  end

  def salary=(new_salary)
    @salary = new_salary
    notify_observers
  end
end

employee1 = Employee.new('employee1', 'owner', 10000)
employee1.add_observer(Payroll.new)
employee1.salary = 20000

output>
Cut a new check for employee1!
His salary is now 20000!
=> 20000
```

``` ruby
use module

module Subject
  def initialize
    @observers=[]
  end

  def add_observer(observer)
    @observers << observer
  end

  def delete_observer(observer)
    @observers.delete(observer)
  end

  def notify_observers
    @observers.each do |observer|
      observer.update(self)
    end
  end
end

class Employee
  include Subject

  attr_reader :name, :address
  attr_reader :salary

  def initialize( name, title, salary)
    super() # still need to call super() to call initialize of module
    @name = name
    @title = title
    @salary = salary
  end

  def salary=(new_salary)
    @salary = new_salary
    notify_observers
  end
end
```

``` ruby
use prebuilt Observable module of Ruby standard library

require 'observer'

class Employee
  include Observable

  attr_reader :name, :address
  attr_reader :salary

  def initialize(name, title, salary)
    @name = name
    @title = title
    @salary = salary
  end

  def salary=(new_salary)
    @salary = new_salary
    changed
    notify_observers(self)
  end
end
```

``` ruby
block version

module Subject
  def initialize
    @observers=[]
  end

  def add_observer(&observer)
    @observers << observer
  end

  def delete_observer(observer)
    @observers.delete(observer)
  end

  def notify_observers
    @observers.each do |observer|
      observer.call(self)
    end
  end
end

e = Employee.new('employee', 'admin', 3000)
e.add_observer do |e|
  puts("Cut a new check for #{e.name}!")
  puts("His salary is now #{e.salary}!")
end
e.salary = 5000

==============================
# Don't inform the observers just yet
fred.salary = 1000000
fred.title = 'Vice President of Sales'
# Now inform the observers!
fred.changes_complete
```

``` ruby
class EmployeeObserver < ActiveRecord::Observer
  def after_create(employee)
    # New employee record created
  end

  def after_update(employee)
    # Employee record updated
  end

  def after_destroy(employee)
    # Employee record deleted
  end
end
```
