``` ruby
class CoffeeRoutine
  attr_reader :task

  def initialize(task)
    @task = task
  end

  def time
    0.0
  end
end
```

``` ruby
class GrindCoffee < CoffeeRoutine
  def initialize
    super 'Grinding some coffee!'
  end

  def time
    0.5
  end
end
```

``` ruby
class BoilWater < CoffeeRoutine
  def initialize
    super 'Boiling some water!'
  end

  def time
    4.0
  end
end
```

``` ruby
class AddCoffee < CoffeeRoutine
  def initialize
    super 'Adding in the coffee!'
  end

  def time
    1.0
  end
end
```

``` ruby
class CompositeTasks < CoffeeRoutine
  attr_reader :task, :steps

  def initialize(task)
    @steps = []
  end

  def add_step(step)
    steps << step
  end

  def remove_step(step)
    steps.delete step
  end

  def time_required
    total_time = 0.0
    steps.each { |step| total_time += step.time }
    total_time
  end
end
```

``` ruby
class FrenchPress < CompositeTasks
  def initialize
    super 'Using the FrenchPress to make coffee!!!'
    add_step GrindCoffee.new
    add_step BoilWater.new
    add_step AddCoffee.new
    # ... Omitted actual steps to make coffee from a French press ...
    # ... Imagine PressPlunger class has been defined already ...
    add_step PressPlunger.new
  end
end

class DripMaker < CompositeTasks
  def initialize
    super 'Using the DripMaker to make coffee!!!'
    add_step GrindCoffee.new
    add_step BoilWater
    add_step AddCoffee.new
    # ... Imagine PressStartButton class has been defined already ...
    add_step PressStartButton.new
  end
end
```

``` ruby
frenchpress = FrenchPress.new

# => #<FrenchPress:0x007f88fcf46410
       @task="Using the FrenchPress to make coffee!!!",
       @steps=
         [#<GrindCoffee:0x007f88fcf46370 @step="Grinding some coffee!">,
         #<BoilWater:0x007f88fcf46320 @step="Boiling some water!">]>
         #<AddCoffee:0x007f88fcf46329 @step="Adding in the coffee!">]>
         #<PressPlunger:0x007f88fcf46098 @step="Pressing the plunger down!">]>

dripmaker = DripMaker.new

# => #<DripMaker:0x137t88fcf57109
       @task="Using the DripMaker to make coffee!!!",
       @steps=
         [#<GrindCoffee:0x007f88fcf46370 @step="Grinding some coffee!">,
         #<BoilWater:0x007f88fcf52520 @step="Boiling some water!">]>
         #<AddCoffee:0x007f88fcf46123 @step="Adding in the coffee!">]>
         #<PressStartButton:0x007f88fcf46432 @step="Pushing the start button!">]>
```
