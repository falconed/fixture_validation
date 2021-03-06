# FixtureValidation

Adds a test that validates all your Rails fixtures.  Tested with Rails
versions 4 and 5.

As your Rails models grow and change over time, your fixtures may 
become outdated without anyone noticing.  In my code I've found 
myself using patterns like this to test validations:
 
```ruby
test 'task must have a name' do
  task = tasks(:one)
  task.name = nil
  refute task.valid?
end
```

However this test will always pass if the task one fixture is already 
invalid for some reason, maybe because a new validation was added but 
the fixture wasn't updated.  So you might rewrite it like this:

```ruby
test 'task must have a name' do
  task = tasks(:one)
  assert task.valid?  ## ensure task one is valid before testing  
  task.name = nil
  refute task.valid?
end
```

With that extra line you can be sure the test will perform as
expected.  But what if you have many tests that use the same fixture?
Furthermore, on a project with hundreds or thousands of fixtures, you
may find yourself repeating this load/validate pattern constantly.

This gem adds a test to your test suite that validates all fixtures at
once.  In the above example, if task :one is invalid the fixture
validation test will fail with output like this:

```
Expected Task fixture 708419058 to be valid.  Errors: Name can't be blank.  Attribute values: {"id"=>708419058, "name"=>nil, "created_at"=>Sat, 12 Nov 2016 17:29:02 UTC +00:00, "updated_at"=>Sat, 12 Nov 2016 17:29:02 UTC +00:00}.
```

All fixture errors are printed when the test runs.  It's not possible
to look up the YAML name of the fixture based on the database entry,
but the attribute values are printed to assist in identifying which
fixture is invalid.

## Installation

Add this line to your application's Gemfile:

```ruby
group :test do
  gem 'fixture_validation'
end
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install fixture_validation

Then call the generator to create the test stub:

```bash
$ rails g fixture_validation 
```

## Usage 

Run your test suite:

    $ rake test
    
Or you can run the fixture validation test on its own, which is useful
if you want to iterate over several errors:

    $ rake test TEST=test/models/fixture_validation_test.rb
    
## Development

After checking out the repo, run `bin/setup` to install
dependencies. Then, run `rake test` to run the tests.

To install this gem onto your local machine, run `bundle exec rake
install`. To release a new version, update the version number in
`version.rb`, and then run `bundle exec rake release`, which will
create a git tag for the version, push git commits and tags, and push
the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at
https://github.com/[USERNAME]/fixture_validation. This project is
intended to be a safe, welcoming space for collaboration, and
contributors are expected to adhere to
the [Contributor Covenant](http://contributor-covenant.org) code of
conduct.


## License

The gem is available as open source under the terms of
the [MIT License](http://opensource.org/licenses/MIT).

