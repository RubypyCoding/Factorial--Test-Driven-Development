

## Test Driven Development

#### Getting Started With RSpec

To understand how RSpec works let’s go over an example step-by-step.

We are going to write a simple application that finds factorial numbers.

The first step:

Initialize Rspec:

```ruby
$ rspec --init
```

Open .rspec file and change it's content to display the tests results in a verbose format:

```ruby
# .rspec

--format documentation
--color
--require spec_helper
```

We'are going to create a test file. It's neccesary to add `_spec.rb` to file.

```ruby
$ touch spec/factorial_spec.rb
```

It is important to create the production file.

```ruby
$ mkdir lib
``` 

```ruby
$ touch lib/factorial.rb
``` 

We gonna to code. Let's begin with test file `factorial_spec.rb`:

```ruby
require 'factorial'
describe Factorial do
  # ...
end
```

This is the initial code for writing your first RSpec test.

You need to require the rspec gem.

Then you need to create a describe block to group all your tests together & to tell RSpec which class you are testing.

Next is the it block:

```ruby
describe Factorial do
  it "does something" do
    # ...
  end
end
```

This is the test name, plus a way to group together all the components of the test itself.

The components are:

- Setup
- Exercise
- Verify

The setup is where you create any objects that you need to create.

It’s the preparation phase.

Then you call the method you want to exercise to get its return value.

Finally, you verify the result with an expectation (RSpec) or assertion (Minitest).

#### RSpec Testing Example

Now if we want to write a factorial method, we have to find out some valid values online or by hand calculation.

Then we use those with our tests.

Like this:

```ruby
describe Factorial do
  it "finds the factorial of 5" do
    calculator = Factorial.new
    expect(calculator.factorial_of(5)).to eq(120)
  end
end
```

When you run this code (like a regular Ruby program) you’ll get this:

```ruby
uninitialized constant Factorial (NameError)
```

This is normal because we don’t have a Factorial class yet.

Let’s create one:

```ruby
class Factorial
end
```

Next error will be:

```ruby
undefined method 'factorial_of'
```

I fix this by creating the factorial_of method:

```ruby
class Factorial
  def factorial_of
  end
end
```

Then run the code again:

```ruby
wrong number of arguments (given 1, expected 0)
```

Another error! But you know what?

That’s a good thing 

Errors are not something to be frustrated with.

They are feedback.

Now:

Add one method argument to the factorial_of method:

```ruby
class Factorial
  def factorial_of(n)
  end
end
```

What you get now is a test failure:

```ruby
expected: 120
     got: nil
```

This is exactly where you want to be at this point!

The next task is to implement the method:

```ruby
class Factorial
  def factorial_of(n)
    (1..n).inject(:*)
  end
end
```

And you’ll get your first passing test:

```ruby
Finished in 0.00315 seconds (files took 0.09083 seconds to load)
1 example, 0 failures
``` 

This is what we call test-driven development (TDD).

You write the tests first, then let the tests guide you on what you need to do next.

#### RSpec Let Method

If you want to write many tests & reuse the same objects you can define these objects with let statements.

It looks like this:

```ruby
describe Factorial do
  let(:calculator) { Factorial.new }
  it "finds the factorial of 5" do  
    expect(calculator.factorial_of(5)).to eq(120)
  end
end
```


Now you can reuse calculator in all your tests under the same describe block.

One thing you should know about let is that it’s “lazy”.

What do I mean by that?

The object won’t be created until the first time you use it.

This can make a difference if creating this object has side-effects, like creating database entries, or writing to a file.

It would be best to avoid these side-effects, but if you can’t do that then use the let! method.

Example:

```ruby
let!(:user) { User.create("rspec@rubyguides.com") }
``` 

The let! method is non-lazy, so the object will be created before any tests are run.

#### How to Use The Subject Method

Another version of let is subject.

The only difference is that you can only have one subject, and it’s meant to be an instance of the main object you are testing.

RSpec already creates a default subject like this:

subject { Factorial.new }

This is called the “implicit subject”.

You can use it like this:

```ruby
describe Factorial do
  it "finds the factorial of 5" do  
    expect(subject.factorial_of(5)).to eq(120)
  end
end
``` 

You can give your subject a name:

```ruby
subject(:calculator) { Factorial.new }
``` 

This behaves the same way as using let, but it enables the use of one-line expectations:

```ruby
it { should be_empty }
```

## How to Run Code Before All Your Tests

RSpec has execution hooks you can use to run something before & after every test, or a whole group of tests.

For example:

```ruby
describe Shop do
  before(:all) { Shop.prepare_database }
  after (:all) { Shop.cleanup_database }
end
```

If you want to run this code for each example (example = test in RSpec) you can use :each instead of :all.

## How To Create Testing Subgroups

If you’re testing different scenarios in your app then it may be helpful to group related tests together.

You can do this using a context block in RSpec.

Here’s an example:

```ruby
describe Course do
  context "when user is logged in" do
    it "displays the course lessons" do
    end
    it "displays the course description" do
    end
  end
  context "when user it NOT logged in" do
    it "redirects to login page" do
    end
    it "it shows a message" do
    end
  end
end
``` 

## How to Temporarily Disable a Test

It’s possible to disable a test for debugging purposes.

All you have to do is to change it to xit for the tests you want to disable.

Example:

```ruby
xit "eats lots of bacon" do
end

``` 

Don’t forget to remove the x when you are done!

** Running Examples By Name
Instead of disabling tests, you can filter the tests you want to run with the -e flag.

Example:

> ruby person.rb -e bacon
This filtering is based on the test name, so the above example will match any test with the word “bacon” on it.


## RSpec Expectations & Matchers

You may remember this example we have been using:

```ruby
expect(calculator.factorial_of(5)).to eq(120)
``` 

But what is this eq(120) part?

Well, 120 is the value we are expecting…

…and eq is what we call a matcher.

Matchers are how RSpec compares the output of your method with your expected value.

In the case of eq, RSpec uses the == operator (read more about Ruby operators).

But there are other matchers you can use.

For example, the be_something matcher:

```ruby
expect(nil).to be_nil
``` 

Where something is a predicate method (like empty?) that is going to be called on the test results.

Other useful matchers:

include (for arrays)
start_with
end_with
match (for regular expression matching)
be_between
have_key / have_value (for hashes)
be_instance_of
respond_to
have_attributes (for testing instance variables)

A matcher that needs special attention is the raise_error matcher.

The reason for that is that to use it you have to wrap your expectation within a block.

Like this:

```ruby
expect{ :x.count }.to raise_error(NoMethodError)
```

The change matcher also works like this:

```ruby
expect{ stock.increment }.to change(stock, :value).by(100)
``` 

RSpec Formatters
The default RSpec output is in the “progress” format.

With this format you see dots (.) representing 1 passing test each, an F for a failed test (expected & actual don’t match), or an E for an error.

But there are alternative formatting options you can use.

Here’s a list:

progress
documentation
json
html
You can enable them with the -f flag:

> ruby factorial.rb -f d

Person
  eats lots of healthy food
  writes many articles
Finished in 0.00154 seconds (files took 0.09898 seconds to load)
2 examples, 0 failures

The documentation format uses your test descriptions to generate the output.

## How to Find Slow Tests

RSpec comes with a very handy option to profile your tests.

Just by passing the --profile flag you’ll be able to see how long each test takes to run & fix the really slow ones.

Here’s an example:

> ruby factorial.rb --profile
Factorial finds the factorial of 5
  0.00043 seconds
