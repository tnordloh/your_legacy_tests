# YourLegacyTests

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'your_legacy_tests'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install your_legacy_tests

## Usage

This gem should be used by people who have inherited a legacy application which lacks any tests, but has been running in production for a while.  It uses Sumo Logic to find the most frequently used urls in an application, and then generates tests for those urls.

### prerequisites

Currently, this gem requires an active account with sumologic, and that you read your logfiles into sumologic.  It then uses their api to aggregate the results, and create tests, based on frequency.

After reading in log files with Sumo, you can run the sumo_sum script.  It will prompt you to fill in the ~/.sumo_creds file, with your credentials, if you haven't already.  Otherwise, it should parse your sumo account, and return a list of tests to your command line, which you can pipe into a test file of your choise.

All of the tests are initially set to 'skip', so that you can enable them one at a time. Start at the first test, which is the most-visited link, and try to run it.  It may need to have some prerequisites filled in; for example, perhaps it requires that the user be logged in, which may require you to create a relevant fixture, and ensure that a login url is called first.


#### EXAMPLES
Fair warning; these examples assume no real knowledge on building tests, other than the ability to run the 'rake test' command.  This is mostly written as the reference I wish I had access to, when I tried to figure out how to test my legacy application, so experts may want to just skim the examples, when my lecture mode kicks in.
#### Example 1:  Base url test

```ruby
test "visit /" do
  skip
  #this url was visited 1647 times
  get '/'
  assert_response :success
end
```

After uncommenting the 'skip', and running 'rake test', you might see something like:
  1) Failure:
  UserStoriesTest#test_visit_/ [/my_app/test/integration/user_stories_test.rb:96]:
  Expected response to be a <success>, but was <302>

You can then go to the production site, and take a look for yourself, to see if this visit always results in a redirect, and modify the test accordingly.  Also, you can look at the content of the page, and modify this test to be aware of that content, perhaps by adding an 'assert_content :index', or whatever is appropriate.  And you might decide that the name itself is misleading, and decide to change that.

After the test is cleaned up, it may look more like this:

```ruby
test "visit home url, ensure it redirects to login" do
  skip
  #this url was visited 1647 times
  get '/'
  assert_response :redirect 
  assert_template :index
end
```

Once you have this one test perfected, you will have the most visited part of the site tested, and you can move to the next test.  

#### Example 2: url requiring a login
Wow, that was easy, wasn't it?  Unfortunately, building tests on a legacy application is rarely a simple matter, and you may have to go prospecting deep into the application to make a test work.  Just remember, we are leveraging logfiles to make sure that the tests we write give us the most bang for the buck possible.

Say this test, as generated, doesn't work:
```ruby
test "visit /user/profile/:id" do
  skip
  #this url was visited 424 times
  get '/user/profile/:id'
  assert_response :success
end
```

after taking a look in app/views/user/profile.erb, looking at ./app/controllers/user, examining the model, and logging in to the website to browse this url, you determine that you need the following things to make this test work:
1. The ability to login before running this test
2. To treat this like a 'real' application, you want to visit the login page.

This is the toughest part of starting to write tests, so it's good to break it down.  Also, these tests are all meant to be thrown away, so I like them to be able to stand on their own.

Step one actually r


## Development

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/tnordloh/your_legacy_tests. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](contributor-covenant.org) code of conduct.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

