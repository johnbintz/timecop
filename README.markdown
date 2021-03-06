# timecop

- Source[http://github.com/jtrupiano/timecop]
- Documentation[http://johntrupiano.rubyforge.org/timecop]

## DESCRIPTION

A gem providing "time travel" and "time freezing" capabilities, making it dead simple to test time-dependent code.  It provides a unified method to mock Time.now, Date.today, and DateTime.now in a single call.

## INSTALL

  gem install timecop

## FEATURES

- Freeze time to a specific point.
- Travel back to a specific point in time, but allow time to continue moving forward from there.
- No dependencies, can be used with _any_ ruby project
- Timecop api allows arguments to be passed into #freeze and #travel as one of the following:
  - Time instance
  - DateTime instance
  - Date instance
  - individual arguments (year, month, day, hour, minute, second)
  - a single integer argument that is interpreted as an offset in seconds from Time.now
- Nested calls to Timecop#travel and Timecop#freeze are supported -- each block will maintain its interpretation of now.
- Works with regular Ruby projects, and Ruby on Rails projects

## USAGE

Run a time-sensitive test

```ruby
joe = User.find(1)
joe.purchase_home()
assert !joe.mortgage_due?
# move ahead a month and assert that the mortgage is due
Timecop.freeze(Date.today + 30) do
  assert joe.mortgage_due?
end
```

Set the time for the test environment of a rails app -- this is particularly
helpful if your whole application is time-sensitive.  It allows you to build
your test data at a single point in time, and to move in/out of that time as
appropriate (within your tests)
  
in config/environments/test.rb

```ruby
config.after_initialize do
  # Set Time.now to September 1, 2008 10:05:00 AM (at this instant), but allow it to move forward
  t = Time.local(2008, 9, 1, 10, 5, 0)
  Timecop.travel(t)
end
```

### The difference between Timecop.freeze and Timecop.travel

freeze is used to statically mock the concept of now. As your program executes,
Time.now will not change unless you make subsequent calls into the Timecop API.
travel, on the other hand, computes an offset between what we currently think
Time.now is (recall that we support nested traveling) and the time passed in.
It uses this offset to simulate the passage of time.  To demonstrate, consider
the following code snippets:

```ruby
new_time = Time.local(2008, 9, 1, 12, 0, 0)
Timecop.freeze(new_time)
sleep(10)
new_time == Time.now # ==> true

Timecop.return # "turn off" Timecop
Timecop.travel(new_time)
sleep(10)
new_time == Time.now # ==> false
```

## REFERENCES

* {0.3.4 release}[http://blog.smartlogicsolutions.com/2009/12/07/timecop-0-3-4-released/]
* {0.3.0 release}[http://blog.smartlogicsolutions.com/2009/09/20/timecop-0-3-0-released/]
* {0.2.0 release}[http://blog.smartlogicsolutions.com/2008/12/24/timecop-2-released-freeze-and-rebase-time-ruby/]
* {0.1.0 release}[http://blog.smartlogicsolutions.com/2008/11/19/timecop-freeze-time-in-ruby-for-better-testing/]

## Contribute

timecop is maintained by [travisjeffery](http://github.com/travisjeffery), who
you can also hit up on [Twitter](http://twitter.com/travisjeffery).

Here's the most direct way to get your work merged into the project.

- Fork the project
- Clone down your fork
- Create a feature branch
- Hack away and add tests, not necessarily in that order
- Make sure everything still passes by running tests
- If necessary, rebase your commits into logical chunks without errors
- Push the branch up to your fork
- Send a pull request for your branch

