# knapsack_pro ruby gem

[![Circle CI](https://circleci.com/gh/KnapsackPro/knapsack_pro-ruby.svg)](https://circleci.com/gh/KnapsackPro/knapsack_pro-ruby)
[![Gem Version](https://badge.fury.io/rb/knapsack_pro.svg)](https://rubygems.org/gems/knapsack_pro)
[![Code Climate](https://codeclimate.com/github/KnapsackPro/knapsack_pro-ruby/badges/gpa.svg)](https://codeclimate.com/github/KnapsackPro/knapsack_pro-ruby)
[![Test Coverage](https://codeclimate.com/github/KnapsackPro/knapsack-pro-ruby/badges/coverage.svg)](https://codeclimate.com/github/KnapsackPro/knapsack-pro-ruby/coverage)

Follow us on [Twitter @KnapsackPro](https://twitter.com/KnapsackPro) and give Like on [Facebook KnapsackPro](https://www.facebook.com/KnapsackPro)

Knapsack Pro gem splits tests across CI nodes and makes sure that tests will run comparable time on each node. It uses [KnapsackPro.com API](http://docs.knapsackpro.com). You can learn more at [https://knapsackpro.com](https://knapsackpro.com?utm_source=github&utm_medium=readme&utm_campaign=knapsack_pro-ruby_gem&utm_content=learn_more)

The knapsack_pro gem supports:

* [RSpec](http://rspec.info)
* [Cucumber](https://cucumber.io)
* [Minitest](http://docs.seattlerb.org/minitest/)
* [test-unit](https://github.com/test-unit/test-unit)
* [Spinach](https://github.com/codegram/spinach)
* [Turnip](https://github.com/jnicklas/turnip)

__Would you like to try knapsack_pro gem?__ You can [get an API token here](http://knapsackpro.com?utm_source=github&utm_medium=readme&utm_campaign=knapsack_pro-ruby_gem&utm_content=get_api_token).

## Is knapsack_pro gem free?

* If your __project is open source__ then you can use Knapsack Pro for free. Please let me know via email (arturtrzop@gmail.com) and I will mark your account on KnapsackPro.com as open source.

* If your __project is commercial__ then I'd like to get feedback from you and work closely together to validate if the solution I'm building provide a value for the users. Switching to a paid plan is a good way to validate that and a way to get support from happy users. Maybe you will be the next one who will join and support the project. Thanks!

# How does knapsack_pro work?

## Basics

Basically it will track your branches, commits and for how many CI nodes you are running tests.
Collected data about test time execution will be sent to the API where the test suite split is done.
The next time you run your tests, each CI node will get an appropriate set of test files in order to achieve comparable time execution on each CI node.

## Details

For instance when you run tests with `rake knapsack_pro:rspec`:

* information about all your existing test files are sent to API http://docs.knapsackpro.com/api/v1/#build_distributions_subset_post
* the API returns which files should be executed on a particular CI node (example KNAPSACK_PRO_CI_NODE_INDEX=0)
* if the API server has data about previous test runs then it will use this to return more accurate test split results, otherwise the API returns a simple split based on directory names
* knapsack_pro will run the set of test files which it got from API
* once tests are finished, knapsack_pro will send information about time execution of each file to API http://docs.knapsackpro.com/api/v1/#build_subsets_post so data can be used for future test runs

The knapsack_pro has also [queue mode](#queue-mode) to get an optimal test suite split.

# Requirements

`>= Ruby 2.0.0`

# Table of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Update gem](#update-gem)
- [Installation](#installation)
- [How to set up](#how-to-set-up)
  - [Usage (How to set up 1 of 3)](#usage-how-to-set-up-1-of-3)
    - [Step for RSpec](#step-for-rspec)
    - [Step for Cucumber](#step-for-cucumber)
    - [Step for Minitest](#step-for-minitest)
    - [Step for test-unit](#step-for-test-unit)
    - [Step for Spinach](#step-for-spinach)
    - [Custom configuration](#custom-configuration)
  - [Setup your CI server (How to set up 2 of 3)](#setup-your-ci-server-how-to-set-up-2-of-3)
    - [Set API key token](#set-api-key-token)
    - [Set knapsack_pro command to execute tests](#set-knapsack_pro-command-to-execute-tests)
  - [Repository adapter (How to set up 3 of 3)](#repository-adapter-how-to-set-up-3-of-3)
    - [When you NOT set global variable `KNAPSACK_PRO_REPOSITORY_ADAPTER` (default)](#when-you-not-set-global-variable-knapsack_pro_repository_adapter-default)
    - [When should you set global variable `KNAPSACK_PRO_REPOSITORY_ADAPTER=git` (required when CI provider is not supported)](#when-should-you-set-global-variable-knapsack_pro_repository_adaptergit-required-when-ci-provider-is-not-supported)
- [Queue Mode](#queue-mode)
  - [How does queue mode work?](#how-does-queue-mode-work)
  - [How to use queue mode?](#how-to-use-queue-mode)
  - [Additional info about queue mode](#additional-info-about-queue-mode)
  - [Extra configuration for Queue Mode](#extra-configuration-for-queue-mode)
    - [KNAPSACK_PRO_FIXED_QUEUE_SPLIT (remember queue split on retry CI node)](#knapsack_pro_fixed_queue_split-remember-queue-split-on-retry-ci-node)
    - [KNAPSACK_PRO_MODIFY_DEFAULT_RSPEC_FORMATTERS (hide duplicated summary of pending and failed tests)](#knapsack_pro_modify_default_rspec_formatters-hide-duplicated-summary-of-pending-and-failed-tests)
  - [Supported test runners in queue mode](#supported-test-runners-in-queue-mode)
- [Extra configuration for CI server](#extra-configuration-for-ci-server)
  - [Info about ENV variables](#info-about-env-variables)
    - [KNAPSACK_PRO_FIXED_TEST_SUITE_SPLITE (test suite split based on seed)](#knapsack_pro_fixed_test_suite_splite-test-suite-split-based-on-seed)
    - [Environment variables for debugging gem](#environment-variables-for-debugging-gem)
  - [Passing arguments to rake task](#passing-arguments-to-rake-task)
    - [Passing arguments to rspec](#passing-arguments-to-rspec)
    - [Passing arguments to cucumber](#passing-arguments-to-cucumber)
    - [Passing arguments to minitest](#passing-arguments-to-minitest)
    - [Passing arguments to test-unit](#passing-arguments-to-test-unit)
    - [Passing arguments to spinach](#passing-arguments-to-spinach)
  - [Knapsack Pro binary](#knapsack-pro-binary)
  - [Test file names encryption](#test-file-names-encryption)
    - [How to enable test file names encryption?](#how-to-enable-test-file-names-encryption)
    - [How to debug test file names?](#how-to-debug-test-file-names)
    - [How to enable branch names encryption?](#how-to-enable-branch-names-encryption)
    - [How to debug branch names?](#how-to-debug-branch-names)
  - [Supported CI providers](#supported-ci-providers)
    - [Info for CircleCI users](#info-for-circleci-users)
      - [CircleCI and knapsack_pro Queue Mode](#circleci-and-knapsack_pro-queue-mode)
    - [Info for Travis users](#info-for-travis-users)
    - [Info for semaphoreapp.com users](#info-for-semaphoreappcom-users)
    - [Info for buildkite.com users](#info-for-buildkitecom-users)
    - [Info for Gitlab CI users](#info-for-gitlab-ci-users)
    - [Info for codeship.com users](#info-for-codeshipcom-users)
    - [Info for Heroku CI users](#info-for-heroku-ci-users)
    - [Info for snap-ci.com users](#info-for-snap-cicom-users)
    - [Info for Jenkins users](#info-for-jenkins-users)
- [FAQ](#faq)
  - [Common problems](#common-problems)
    - [Why I see API error commit_hash parameter is required?](#why-i-see-api-error-commit_hash-parameter-is-required)
    - [Why I see `LoadError: cannot load such file -- spec_helper`?](#why-i-see-loaderror-cannot-load-such-file----spec_helper)
    - [Queue Mode problems](#queue-mode-problems)
      - [Why when I use Queue Mode for RSpec then my tests fail?](#why-when-i-use-queue-mode-for-rspec-then-my-tests-fail)
      - [Why when I use Queue Mode for RSpec then FactoryBot/FactoryGirl tests fail?](#why-when-i-use-queue-mode-for-rspec-then-factorybotfactorygirl-tests-fail)
      - [Why I don't see collected time execution data for my build in user dashboard?](#why-i-dont-see-collected-time-execution-data-for-my-build-in-user-dashboard)
      - [Why when I use Queue Mode for RSpec and test fails then I see multiple times info about failed test in RSpec result?](#why-when-i-use-queue-mode-for-rspec-and-test-fails-then-i-see-multiple-times-info-about-failed-test-in-rspec-result)
      - [Why when I use Queue Mode for RSpec then I see multiple times the same pending tests?](#why-when-i-use-queue-mode-for-rspec-then-i-see-multiple-times-the-same-pending-tests)
      - [Does in Queue Mode the RSpec is initialized many times that causes Rails load over and over again?](#does-in-queue-mode-the-rspec-is-initialized-many-times-that-causes-rails-load-over-and-over-again)
      - [Why my tests are executed twice in queue mode? Why CI node runs whole test suite again?](#why-my-tests-are-executed-twice-in-queue-mode-why-ci-node-runs-whole-test-suite-again)
      - [How to fix capybara-screenshot fail with `SystemStackError: stack level too deep` when using Queue Mode for RSpec?](#how-to-fix-capybara-screenshot-fail-with-systemstackerror-stack-level-too-deep-when-using-queue-mode-for-rspec)
      - [Parallel tests Cucumber and RSpec with Cucumber failures exit CI node early leaving fewer CI nodes to finish RSpec Queue.](#parallel-tests-cucumber-and-rspec-with-cucumber-failures-exit-ci-node-early-leaving-fewer-ci-nodes-to-finish-rspec-queue)
  - [General questions](#general-questions)
    - [How to run tests for particular CI node in your development environment](#how-to-run-tests-for-particular-ci-node-in-your-development-environment)
      - [for knapack_pro regular mode](#for-knapack_pro-regular-mode)
      - [for knapsack_pro queue mode](#for-knapsack_pro-queue-mode)
    - [What happens when Knapsack Pro API is not available/not reachable temporarily?](#what-happens-when-knapsack-pro-api-is-not-availablenot-reachable-temporarily)
      - [for knapack_pro regular mode](#for-knapack_pro-regular-mode-1)
      - [for knapsack_pro queue mode](#for-knapsack_pro-queue-mode-1)
    - [How can I change log level?](#how-can-i-change-log-level)
    - [How to split tests based on test level instead of test file level?](#how-to-split-tests-based-on-test-level-instead-of-test-file-level)
      - [A. Create multiple small test files](#a-create-multiple-small-test-files)
      - [B. Use tags to mark set of tests in particular test file](#b-use-tags-to-mark-set-of-tests-in-particular-test-file)
    - [How to make knapsack_pro works for forked repositories of my project?](#how-to-make-knapsack_pro-works-for-forked-repositories-of-my-project)
    - [How to use junit formatter?](#how-to-use-junit-formatter)
      - [How to use junit formatter with knapsack_pro regular mode?](#how-to-use-junit-formatter-with-knapsack_pro-regular-mode)
      - [How to use junit formatter with knapsack_pro queue mode?](#how-to-use-junit-formatter-with-knapsack_pro-queue-mode)
    - [How many API keys I need?](#how-many-api-keys-i-need)
    - [What is optimal order of test commands?](#what-is-optimal-order-of-test-commands)
    - [How to set `before(:suite)` and `after(:suite)` RSpec hooks in Queue Mode (Percy.io example)?](#how-to-set-beforesuite-and-aftersuite-rspec-hooks-in-queue-mode-percyio-example)
    - [How to call `before(:suite)` and `after(:suite)` RSpec hooks only once in Queue Mode?](#how-to-call-beforesuite-and-aftersuite-rspec-hooks-only-once-in-queue-mode)
    - [How to run knapsack_pro with parallel_tests gem?](#how-to-run-knapsack_pro-with-parallel_tests-gem)
    - [How to retry failed tests (flaky tests)?](#how-to-retry-failed-tests-flaky-tests)
    - [How can I run tests from multiple directories?](#how-can-i-run-tests-from-multiple-directories)
    - [Why I don't see all test files being recorded in user dashboard](#why-i-dont-see-all-test-files-being-recorded-in-user-dashboard)
    - [Why when I use 2 different CI providers then not all test files are executed?](#why-when-i-use-2-different-ci-providers-then-not-all-test-files-are-executed)
    - [How to run only RSpec feature tests or non feature tests?](#how-to-run-only-rspec-feature-tests-or-non-feature-tests)
    - [How to use CodeClimate with knapsack_pro?](#how-to-use-codeclimate-with-knapsack_pro)
  - [Questions around data usage and security](#questions-around-data-usage-and-security)
    - [What data is sent to your servers?](#what-data-is-sent-to-your-servers)
    - [How is that data secured?](#how-is-that-data-secured)
    - [Who has access to the data?](#who-has-access-to-the-data)
- [Gem tests](#gem-tests)
  - [Spec](#spec)
- [Contributing](#contributing)
- [Mentions](#mentions)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Update gem

Please check [changelog](CHANGELOG.md) before updating gem. Knapsack Pro follows [semantic versioning](http://semver.org).

## Installation

Add these lines to your application's Gemfile:

```ruby
group :test, :development do
  gem 'knapsack_pro'
end
```

And then execute:

```
$ bundle install
```

If you are not using Rails then add this line at the bottom of `Rakefile`:

```ruby
# Add this only if you are not using Rails.
# If you use Rails then knapsack_pro rake tasks are already loaded
# so there is no need to explicitly load them.
KnapsackPro.load_tasks if defined?(KnapsackPro)
```

__Please check [online installation guide](http://docs.knapsackpro.com/knapsack_pro-ruby/guide/#questions) to get started.__ It will ask you a few questions and generate instruction steps for your project.

_You only need to read the next section if you want to understand optional gem configuration and features._

## How to set up

If you use [VCR](https://github.com/vcr/vcr), [WebMock](https://github.com/bblimke/webmock) or [FakeWeb](https://github.com/chrisk/fakeweb) gems then you need to allow them to make requests to the Knapsack Pro API.

For VCR add Knapsack Pro API subdomain to [ignore hosts](https://www.relishapp.com/vcr/vcr/v/2-9-3/docs/configuration/ignore-request):

```ruby
# spec/spec_helper.rb or wherever your VCR configuration is

require 'vcr'
VCR.configure do |config|
  config.hook_into :webmock # or :fakeweb
  config.ignore_hosts('localhost', '127.0.0.1', '0.0.0.0', 'api.knapsackpro.com')
end

# add below when you hook into webmock
require 'webmock/rspec'
WebMock.disable_net_connect!(:allow => ['api.knapsackpro.com'])

# add below when you use FakeWeb
require 'fakeweb'
FakeWeb.allow_net_connect = %r[^https?://api\.knapsackpro\.com]
```

Ensure you have `require false` in your Gemfile for webmock gem (see below) when VCR is hooked into it. That ensures that the webmock configuration in `spec_helper.rb` (above) is loaded properly.

```ruby
# Gemfile

group :test do
  gem 'vcr'
  gem 'webmock', require: false
  gem 'fakeweb', require: false # example when you use fakeweb
end
```

### Usage (How to set up 1 of 3)

__Tip:__ You can find here an example of a rails app with knapsack_pro already configured.

https://github.com/KnapsackPro/rails-app-with-knapsack_pro

#### Step for RSpec

Add at the beginning of your `spec_helper.rb`:

```ruby
require 'knapsack_pro'

# CUSTOM_CONFIG_GOES_HERE

KnapsackPro::Adapters::RSpecAdapter.bind
```

#### Step for Cucumber

Create file `features/support/knapsack_pro.rb` and add there:

```ruby
require 'knapsack_pro'

# CUSTOM_CONFIG_GOES_HERE

KnapsackPro::Adapters::CucumberAdapter.bind
```

#### Step for Minitest

Add at the beginning of your `test_helper.rb`:

```ruby
require 'knapsack_pro'

# CUSTOM_CONFIG_GOES_HERE

knapsack_pro_adapter = KnapsackPro::Adapters::MinitestAdapter.bind
knapsack_pro_adapter.set_test_helper_path(__FILE__)
```

#### Step for test-unit

Add at the beginning of your `test_helper.rb`:

```ruby
require 'knapsack_pro'

# CUSTOM_CONFIG_GOES_HERE

knapsack_pro_adapter = KnapsackPro::Adapters::TestUnitAdapter.bind
knapsack_pro_adapter.set_test_helper_path(__FILE__)
```

#### Step for Spinach

Create file `features/support/knapsack_pro.rb` and add there:

```ruby
require 'knapsack_pro'

# CUSTOM_CONFIG_GOES_HERE

KnapsackPro::Adapters::SpinachAdapter.bind
```

#### Custom configuration

You can change the default Knapsack Pro configuration for RSpec, Cucumber, Minitest, test-unit or Spinach tests. Here are examples what you can do. Put the configuration below in place of `CUSTOM_CONFIG_GOES_HERE` (in the configuration samples above).

```ruby
# you can use your own logger
require 'logger'
KnapsackPro.logger = Logger.new(STDOUT)
KnapsackPro.logger.level = Logger::DEBUG
```

Debug is default log level and this is recommended. [Read more](#how-can-i-change-log-level).

### Setup your CI server (How to set up 2 of 3)

#### Set API key token

Set one or more tokens depending on how many test suites you run on CI server.

* `KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` - as value set token for rspec test suite. Token can be generated when you sign in to [knapsackpro.com](http://www.knapsackpro.com).
* `KNAPSACK_PRO_TEST_SUITE_TOKEN_CUCUMBER` - token for cucumber test suite.
* `KNAPSACK_PRO_TEST_SUITE_TOKEN_MINITEST` - token for minitest test suite.
* `KNAPSACK_PRO_TEST_SUITE_TOKEN_TEST_UNIT` - token for test-unit test suite.
* `KNAPSACK_PRO_TEST_SUITE_TOKEN_SPINACH` - token for spinach test suite.

__Tip:__ In case you have for instance multiple rspec test suites then prepend each of knapsack_pro command which executes tests with `KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` variable.

#### Set knapsack_pro command to execute tests

On your CI server run this command for the first CI node. Update `KNAPSACK_PRO_CI_NODE_INDEX` for the next one.

    # Step for RSpec
    $ KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:rspec

    # Step for Cucumber
    $ KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:cucumber

    # Step for Minitest
    $ KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:minitest

    # Step for test-unit
    $ KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:test_unit

    # Step for Spinach
    $ KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:spinach

You can add `KNAPSACK_PRO_TEST_FILE_PATTERN` if your tests are not in default directory. For instance:

    # Step for RSpec
    $ KNAPSACK_PRO_TEST_FILE_PATTERN="directory_with_specs/**{,/*/**}/*_spec.rb" KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:rspec

    # Step for Cucumber
    $ KNAPSACK_PRO_TEST_FILE_PATTERN="directory_with_features/**{,/*/**}/*.feature" KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:cucumber

    # Step for Minitest
    $ KNAPSACK_PRO_TEST_FILE_PATTERN="directory_with_tests/**{,/*/**}/*_test.rb" KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:minitest

    # Step for test-unit
    $ KNAPSACK_PRO_TEST_FILE_PATTERN="directory_with_tests/**{,/*/**}/*_test.rb" KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:test_unit

    # Step for Spinach
    $ KNAPSACK_PRO_TEST_FILE_PATTERN="directory_with_features/**{,/*/**}/*.feature" KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:spinach

__Tip:__ If you use one of the supported CI providers then instead of the above steps you should [take a look at this](#supported-ci-providers).

__Tip 2:__ If you use one of unsupported CI providers ([here is list of supported CI providers](#supported-ci-providers)) then you should [set KNAPSACK_PRO_REPOSITORY_ADAPTER=git](#when-should-you-set-global-variable-knapsack_pro_repository_adaptergit-required-when-ci-provider-is-not-supported).

### Repository adapter (How to set up 3 of 3)

#### When you NOT set global variable `KNAPSACK_PRO_REPOSITORY_ADAPTER` (default)

By default `KNAPSACK_PRO_REPOSITORY_ADAPTER` variable has no value so knapsack_pro will try to get info about branch name and commit hash from [supported CI](#supported-ci-providers) (CI providers have branch, commit, project directory stored as environment variables). In case when you use other CI provider like Jenkins then please set below variables on your own.

`KNAPSACK_PRO_BRANCH` - It's branch name. You run tests on this branch.

`KNAPSACK_PRO_COMMIT_HASH` - Commit hash. You run tests for this commit.

You can also use git as repository adapter to determine branch and commit hash, please see below section.

#### When should you set global variable `KNAPSACK_PRO_REPOSITORY_ADAPTER=git` (required when CI provider is not supported)

`KNAPSACK_PRO_REPOSITORY_ADAPTER` - When it has the value `git`, your local version of git on CI server will be used to get the branch name and commit hash. You also need to set `KNAPSACK_PRO_PROJECT_DIR` with the project directory path.

`KNAPSACK_PRO_PROJECT_DIR` - Path to the project on the CI node, for instance `/home/ubuntu/my-app-repository`. It should be the top-level directory of your repository.

## Queue Mode

knapsack_pro has a built-in queue mode designed to determine the optimal test suite split even when there is an unpredictably longer time execution of test files on one node (e.g. by
CI node overload and decrease of performance that may affect how long the tests take on that node, or 
things like external requests done in individual tests).

### How does queue mode work?

On the Knapsack Pro API side, there is test file queue generated for your CI build. Each CI node periodically requests the Knapsack Pro API for test files
that should be executed next. Thanks to that each CI node will finish tests at the same time.

### How to use queue mode?

Please use a separate API token for queue mode from one used already for regular mode.

Use this command to run queue mode:

    bundle exec rake knapsack_pro:queue:rspec

If the above command fails then you may need to explicitly pass an argument to require the `rails_helper` file or `spec_helper` in case you are not doing this in some of your test files:

    bundle exec rake "knapsack_pro:queue:rspec[--require rails_helper]"

Note: when you run queue mode command for the first time it might be slower.
The second build should have a more optimal test suite split.

Please ensure you have explicitly set `RAILS_ENV=test` on your CI nodes.

If you use the capybara-screenshot gem then please [follow this step](#how-to-fix-capybara-screenshot-fail-with-systemstackerror-stack-level-too-deep-when-using-queue-mode-for-rspec).

If you use the rspec_junit_formatter gem then please [follow this step](#how-to-use-junit-formatter-with-knapsack_pro-queue-mode).

If your test suite is very long and the RSpec output is too long for your CI node then you can set log level `KNAPSACK_PRO_LOG_LEVEL=info` to don't show debug messages in RSpec output. [Read more about log level](#how-can-i-change-log-level).

### Additional info about queue mode

* You should use a separate API token for queue mode than for regular mode to avoid problems with test suite split (especially in case you would like to go back to regular mode).
There might be some cached test suite splits for git commits you have run in past for API token you used in queue mode because of the [flag `KNAPSACK_PRO_FIXED_TEST_SUITE_SPLIT=true` for regular mode which is default](#knapsack_pro_fixed_test_suite_splite-test-suite-split-based-on-seed).

* If you are not using one of the [supported CI providers](#supported-ci-providers) then please note that the knapsack_pro gem doesn't have a CI build ID in order to generate a queue for each particular CI build. This may result in two different CI builds taking tests from the same queue when CI builds are running at the same time against the same git commit.

  To avoid this you should specify a unique `KNAPSACK_PRO_CI_NODE_BUILD_ID` environment variable for each CI build. This mean that each CI node that is part of particular CI build should have the same value for `KNAPSACK_PRO_CI_NODE_BUILD_ID`.

* Note that in the Queue Mode by default you cannot retry the failed CI node with exactly the same subset of tests that were run on the CI node in the first place. It's possible in regular mode ([read more](#knapsack_pro_fixed_test_suite_splite-test-suite-split-based-on-seed)). If you want to have similar behavior in Queue Mode you need to explicitly [enable it](#knapsack_pro_fixed_queue_split-remember-queue-split-on-retry-ci-node).

  By default the Queue Mode works this way:

  * If you retry the failed build and your all CI nodes start this new build then there will be a new dynamic test suite split across CI nodes. The reason is that the most of the CI providers schedule a new CI build with a different ID when you retry CI build. They retry all CI nodes again. In that case you don't have to worry with below edge cases because the CI build ID will be different so a new queue will be initialized on Knapsack Pro API side and all retried CI node will connect to that queue.

  Edge cases:

  * Let's say one of the CI nodes failed and you retry just this single CI node while other CI nodes are still running. Let's assume this retried CI node is part of the same CI build ID when you use supported CI provider or `KNAPSACK_PRO_CI_NODE_BUILD_ID` is defined and stays the same. The retried CI node will be connected to the queue consumed by still running CI nodes. You probably would expect the retried CI node to run the tests that were executed there on the first place. To achieve that you need to [enable it](#knapsack_pro_fixed_queue_split-remember-queue-split-on-retry-ci-node).

  * Let's say one of the CI nodes failed and you retry just this single CI node while other CI nodes already finished work. Let's assume this retried CI node is part of the same CI build ID when you use supported CI provider or `KNAPSACK_PRO_CI_NODE_BUILD_ID` is defined and stays the same. The fact is all CI nodes finished work so the queue was consumed.
    * If you retry CI node in first hour since the CI build started for the first time then the retried CI node won't execute tests because the queue was consumed. There is important reason why it works like that. For instance some CI providers like Buildkite allows to start CI node later than the others so sometimes the particular CI node may start work while all other CI nodes finished work. In that case we don't want to run tests on the CI node because queue was already consumed. We don't know whether the CI node is part of the build or it is retried CI node hence the 1 hour lock on initializing a new queue.
    * If you retry CI node after 1 hour since the CI build started for the first time then the retried CI node will initialize a new queue and it will run whole test suite from the queue because there will be no other CI nodes running connected to the queue. The order of tests on retried CI node will be different than on the first run. You probably would expect the retried CI node to run the tests that were executed there on the first place. To achieve that you need to [enable it](#knapsack_pro_fixed_queue_split-remember-queue-split-on-retry-ci-node).

  * When you use unsupported CI provider by knapack_pro gem or you forget to set unique `KNAPSACK_PRO_CI_NODE_BUILD_ID` per CI build then:
    * when you retry single CI node then it will initialize a new queue and it will run whole test suite from the queue because there will be no other CI nodes running connected to the queue. The order of tests on retried CI node will be different than on the first run.
    * when you retry all CI nodes then a new queue will be initialized and all CI nodes will connect to it.

### Extra configuration for Queue Mode

#### KNAPSACK_PRO_FIXED_QUEUE_SPLIT (remember queue split on retry CI node)

* `KNAPSACK_PRO_FIXED_QUEUE_SPLIT=false` (default)

  By default, the fixed queue split is off. It means when you will run tests for the same commit hash and a total number of nodes and for the same branch, and the CI build ID is different with second tests run then the queue will be generated dynamically and CI nodes will fetch from Knapsack Pro API the test files in a dynamic way. This is default because it gives the optimal test suite split for the whole test build across all CI nodes.

* `KNAPSACK_PRO_FIXED_QUEUE_SPLIT=true`

  You can enable fixed queue split in order to remember the test suite split across CI nodes when you used Queue Mode.

  It means when you run test suite or just retry single CI node again for the same commit hash and a total number of nodes and for the same branch
  then you will get exactly the same test suite split as it was when you run the build for the first time.

  Thanks to that when tests on one of your node failed you can retry the node with exactly the same subset of tests that were run on the node in the first place.

  Note when fixed queue split is enabled then you can run tests in a dynamic way only once for particular commit hash and a total number of nodes and for the same branch.

  When Knapsack Pro API server has already information about previous queue split then the information will be used. You will see at the beginning of the knapsack command the log with info that queue name is nil because it was not generated this time. You will get the list of all test files that were executed on the particular CI node in the past.

       [knapsack_pro] {"queue_name"=>nil, "test_files"=>[{"path"=>"spec/foo_spec.rb", "time_execution"=>1.23}]}

  To [reproduce tests executed on CI node](#for-knapsack_pro-queue-mode) in development environment please see FAQ.

#### KNAPSACK_PRO_MODIFY_DEFAULT_RSPEC_FORMATTERS (hide duplicated summary of pending and failed tests)

* `KNAPSACK_PRO_MODIFY_DEFAULT_RSPEC_FORMATTERS=true` (default)

  By default, the knapack_pro will monkey patch [RSpec Formatters](https://www.relishapp.com/rspec/rspec-core/v/2-6/docs/command-line/format-option) in order to
  hide the summary of pending and failed tests after each intermediate run of tests fetched from the work queue on Knapsack Pro API.
  knapack_pro shows summary of all pending and failed tests at the very end when work queue ended. If you use your custom formatter and you have problem with it then you can disable `KNAPSACK_PRO_MODIFY_DEFAULT_RSPEC_FORMATTERS=false` monkey patching.

* `KNAPSACK_PRO_MODIFY_DEFAULT_RSPEC_FORMATTERS=false`

  It causes to show summary of pending and failed tests after each intermediate tests run from the work queue. The summary will grown cumulatively after each intermediate tests run so it means you will see multiple times summary of the same pending/failed tests. It doesn't mean the test files are executed twice. Test files are executed only once. Only summary report grows cumulatively.

### Supported test runners in queue mode

At this moment the queue mode works for:

* RSpec

## Extra configuration for CI server

### Info about ENV variables

By default knapsack_pro gem [supports a few CI providers](#supported-ci-providers) so you don't need to set some environment variables.
In case when you use other CI provider for instance [Jenkins](https://jenkins-ci.org) etc then you need to provide configuration via below environment variables.

`KNAPSACK_PRO_CI_NODE_TOTAL` - total number CI nodes you have.

`KNAPSACK_PRO_CI_NODE_INDEX` - index of current CI node starts from 0. Second CI node should have `KNAPSACK_PRO_CI_NODE_INDEX=1`.

#### KNAPSACK_PRO_FIXED_TEST_SUITE_SPLITE (test suite split based on seed)

Note this is for knapack_pro regular mode only.

* `KNAPSACK_PRO_FIXED_TEST_SUITE_SPLIT=true` (default)

    It means when you run test suite again for the same commit hash and total number of nodes and for the same branch
    then you will get exactly the same test suite split.

    Thanks to that when tests on one of your node failed you can retry the node with exactly the same subset of tests that were run on the node in the first place.

    There is one edge case. When you run tests for the first time and there is no data collected about time execution of your tests then
    we need to collect data to prepare the first test suite split. The second run of your tests will have fixed test suite split.

    To compare if all your CI nodes are running based on the same test suite split seed you can check the value for seed in knapsack logging message
    before your test starts. The message looks like:

        [knapsack_pro] Test suite split seed: 8a606431-02a1-4766-9878-0ea42a07ad21

* `KNAPSACK_PRO_FIXED_TEST_SUITE_SPLIT=false`

    When you disable fixed test suite split then your will get test suite split based on most up to date data about your test suite time execution.
    For instance, when you run tests for the second time for the same commit hash then your will get more optimal test suite split than it was on the first run.

    Don't disable fixed test suite split when:

    * you expect to run the same subset of test suite multiple times for the same node (for instance your would like to retry only single CI node that failed)

        Example of issue:
        * https://github.com/KnapsackPro/knapsack_pro-ruby/issues/15
        * https://github.com/KnapsackPro/knapsack_pro-ruby/issues/12

    * you start your tests not at the same time across your CI nodes. For instance, one of the CI node finished faster than the other CI node started. This would change the seed for the second CI node that started later.

#### Environment variables for debugging gem

`KNAPSACK_PRO_ENDPOINT` - Default value is `http://api.knapsackpro.com` which is endpoint for [Knapsack Pro API](http://docs.knapsackpro.com).

`KNAPSACK_PRO_MODE` - Default value is `production`. When mode is `development` then endpoint is `http://api.knapsackpro.dev:3000`. When mode is `test` then endpoint is `http://api-staging.knapsackpro.com`.

### Passing arguments to rake task

#### Passing arguments to rspec

Knapsack Pro allows you to pass arguments through to rspec. For example if you want to run only specs that have the tag `focus`. If you do this with rspec directly it would look like:

    $ bundle exec rake rspec --tag focus

To do this with Knapsack Pro you simply add your rspec arguments as parameters to the knapsack_pro rake task.

    $ bundle exec rake "knapsack_pro:rspec[--tag focus]"

#### Passing arguments to cucumber

Add arguments to knapsack_pro cucumber task like this:

    $ bundle exec rake "knapsack_pro:cucumber[--name feature]"

#### Passing arguments to minitest

Add arguments to knapsack_pro minitest task like this:

    $ bundle exec rake "knapsack_pro:minitest[--arg_name value]"

For instance to run verbose tests:

    $ bundle exec rake "knapsack_pro:minitest[--verbose]"

#### Passing arguments to test-unit

Add arguments to knapsack_pro test-unit task like this:

    $ bundle exec rake "knapsack_pro:test_unit[--arg_name value]"

For instance to run verbose tests:

    $ bundle exec rake "knapsack_pro:test_unit[--verbose]"

#### Passing arguments to spinach

Add arguments to knapsack_pro spinach task like this:

    $ bundle exec rake "knapsack_pro:spinach[--arg_name value]"

### Knapsack Pro binary

You can install knapsack_pro globally and use binary. For instance:

    $ knapsack_pro rspec "--tag custom_tag_name --profile"
    $ knapsack_pro queue:rspec "--tag custom_tag_name --profile"
    $ knapsack_pro cucumber "--name feature"
    $ knapsack_pro minitest "--verbose --pride"
    $ knapsack_pro test_unit "--verbose"
    $ knapsack_pro spinach "--arg_name value"

This is optional way of using knapsack_pro when you don't want to add it to `Gemfile`.

### Test file names encryption

knapsack_pro gem collects information about you test file names and time execution. Those data are stored on KnapsackPro.com server.
If your test file names or branch names are sensitive data then you can encrypt the names before sending them to KnapsackPro.com API.

By default, encryption is disabled because knapsack_pro can use your test files names to prepare better test suite split when the time execution data are not yet collected on KnapsackPro.com server.
When you will enable test file names encryption then your first test suite split may be less optimal than it could be.

Each test file name is generated with `Digest::SHA2.hexdigest` method and 64 chars salt.

Before you enable test file encryption please ensure you are using fresh API key. You should not use the same API key for encrypted and non encrypted test suite.
You can generate API key for your test suite in [your dashboard](https://knapsackpro.com).

Next step is to generate salt which will be used to encrypt test files or branch names.

    $ bundle exec rake knapsack_pro:salt

Add to your CI server generated environment variable `KNAPSACK_PRO_SALT`.

#### How to enable test file names encryption?

You need to add environment variable `KNAPSACK_PRO_TEST_FILES_ENCRYPTED=true` to your CI server.

#### How to debug test file names?

If you need to check what is the encryption hash for particular test file you can check that with the rake task:

    $ KNAPSACK_PRO_SALT=xxx bundle exec rake knapsack_pro:encrypted_test_file_names[rspec]

You can pass the name of test runner like `rspec`, `minitest`, `test_unit`, `cucumber`, `spinach` as argument to rake task.

#### How to enable branch names encryption?

You need to add environment variable `KNAPSACK_PRO_BRANCH_ENCRYPTED=true` to your CI server.

Note: there are a few branch names that won't be encrypted because we use them as fallback branches on Knapsack Pro API side to determine time execution for test files during split for newly created branches.

* develop
* development
* dev
* master
* staging

#### How to debug branch names?

If you need to check what is the encryption hash for particular branch then use the rake task:

    # show all local branches and respective hashes
    $ KNAPSACK_PRO_SALT=xxx bundle exec rake knapsack_pro:encrypted_branch_names

    # show hash for branch provided as argument to rake task
    $ KNAPSACK_PRO_SALT=xxx bundle exec rake knapsack_pro:encrypted_branch_names[not-encrypted-branch-name]

### Supported CI providers

#### Info for CircleCI users

If you are using circleci.com you can omit `KNAPSACK_PRO_CI_NODE_TOTAL` and `KNAPSACK_PRO_CI_NODE_INDEX`. Knapsack Pro will use `CIRCLE_NODE_TOTAL` and `CIRCLE_NODE_INDEX` provided by CircleCI.

Here is an example for test configuration in your `circleci.yml` file.

```yaml
# CircleCI 1.0

machine:
  environment:
    # Tokens should be set in CircleCI settings to avoid expose tokens in build logs
    # KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC: rspec-token
    # KNAPSACK_PRO_TEST_SUITE_TOKEN_CUCUMBER: cucumber-token
    # KNAPSACK_PRO_TEST_SUITE_TOKEN_MINITEST: minitest-token
    # KNAPSACK_PRO_TEST_SUITE_TOKEN_TEST_UNIT: test-unit-token
    # KNAPSACK_PRO_TEST_SUITE_TOKEN_SPINACH: spinach-token
test:
  override:
    # Step for RSpec
    - bundle exec rake knapsack_pro:rspec:
        parallel: true # Caution: there are 8 spaces indentation!

    # Step for Cucumber
    - bundle exec rake knapsack_pro:cucumber:
        parallel: true # Caution: there are 8 spaces indentation!

    # Step for Minitest
    - bundle exec rake knapsack_pro:minitest:
        parallel: true # Caution: there are 8 spaces indentation!

    # Step for test-unit
    - bundle exec rake knapsack_pro:test_unit:
        parallel: true # Caution: there are 8 spaces indentation!

    # Step for Spinach
    - bundle exec rake knapsack_pro:spinach:
        parallel: true # Caution: there are 8 spaces indentation!
```

Here is another example for CircleCI 2.0 platform.

```YAML
# CircleCI 2.0

# some tests that are not balanced and executed only on first CI node
- run: case $CIRCLE_NODE_INDEX in 0) npm test ;; esac

# auto-balancing CI build time execution to be flat and optimal (as fast as possible).
# Queue Mode does dynamic tests allocation so the previous not balanced run command won't
# create a bottleneck on the CI node
- run:
  name: RSpec via knapsack_pro Queue Mode
  command: |
    # export word is important here!
    export RAILS_ENV=test
    bundle exec rake "knapsack_pro:queue:rspec[--format documentation]"
```

Please remember to add additional containers for your project in CircleCI settings.

##### CircleCI and knapsack_pro Queue Mode

If you use knapack_pro Queue Mode with CircleCI you may want to [collect metadata](https://circleci.com/docs/1.0/test-metadata/#metadata-collection-in-custom-test-steps) like junit xml report about your RSpec test suite.

Here you can read how to configure [junit formatter](#how-to-use-junit-formatter-with-knapsack_pro-queue-mode). Step for CircleCI is to copy the xml report to `$CIRCLE_TEST_REPORTS` directory. Below is full config for your `spec_helper.rb`:

```ruby
# spec_helper.rb or rails_helper.rb

# TODO This must be the same path as value for rspec --out argument
TMP_RSPEC_XML_REPORT = 'tmp/rspec.xml'
# move results to FINAL_RSPEC_XML_REPORT so the results won't accumulate with duplicated xml tags in TMP_RSPEC_XML_REPORT
FINAL_RSPEC_XML_REPORT = 'tmp/rspec_final_results.xml'

KnapsackPro::Hooks::Queue.after_subset_queue do |queue_id, subset_queue_id|
  if File.exist?(TMP_RSPEC_XML_REPORT)
    FileUtils.mv(TMP_RSPEC_XML_REPORT, FINAL_RSPEC_XML_REPORT)
  end
end

# Here is additional configuration to ensure the xml report will be visible by CircleCI
KnapsackPro::Hooks::Queue.after_queue do |queue_id|
  # Metadata collection
  # https://circleci.com/docs/1.0/test-metadata/#metadata-collection-in-custom-test-steps
  if File.exist?(FINAL_RSPEC_XML_REPORT) && ENV['CIRCLE_TEST_REPORTS']
    FileUtils.cp(FINAL_RSPEC_XML_REPORT, "#{ENV['CIRCLE_TEST_REPORTS']}/rspec.xml")
  end
end
```

#### Info for Travis users

You can parallelize your builds across virtual machines with [travis matrix feature](http://docs.travis-ci.com/user/speeding-up-the-build/#Parallelizing-your-builds-across-virtual-machines). Edit `.travis.yml`

```yaml
script:
  # Step for RSpec
  - "bundle exec rake knapsack_pro:rspec"

  # Step for Cucumber
  - "bundle exec rake knapsack_pro:cucumber"

  # Step for Minitest
  - "bundle exec rake knapsack_pro:minitest"

  # Step for test-unit
  - "bundle exec rake knapsack_pro:test_unit"

  # Step for Spinach
  - "bundle exec rake knapsack_pro:spinach"

env:
  global:
    # tokens should be set in travis settings in web interface to avoid expose tokens in build logs
    - KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=rspec-token
    - KNAPSACK_PRO_TEST_SUITE_TOKEN_CUCUMBER=cucumber-token
    - KNAPSACK_PRO_TEST_SUITE_TOKEN_MINITEST=minitest-token
    - KNAPSACK_PRO_TEST_SUITE_TOKEN_TEST_UNIT=test-unit-token
    - KNAPSACK_PRO_TEST_SUITE_TOKEN_SPINACH=spinach-token

    - KNAPSACK_PRO_CI_NODE_TOTAL=2
  matrix:
    - KNAPSACK_PRO_CI_NODE_INDEX=0
    - KNAPSACK_PRO_CI_NODE_INDEX=1
```

Such configuration will generate matrix with 2 following ENV rows:

    KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=rspec-token KNAPSACK_PRO_TEST_SUITE_TOKEN_CUCUMBER=cucumber-token KNAPSACK_PRO_TEST_SUITE_TOKEN_MINITEST=minitest-token KNAPSACK_PRO_TEST_SUITE_TOKEN_TEST_UNIT=test-unit-token KNAPSACK_PRO_TEST_SUITE_TOKEN_SPINACH=spinach-token
    KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=1 KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=rspec-token KNAPSACK_PRO_TEST_SUITE_TOKEN_CUCUMBER=cucumber-token KNAPSACK_PRO_TEST_SUITE_TOKEN_MINITEST=minitest-token KNAPSACK_PRO_TEST_SUITE_TOKEN_TEST_UNIT=test-unit-token KNAPSACK_PRO_TEST_SUITE_TOKEN_SPINACH=spinach-token

More info about global and matrix ENV configuration in [travis docs](https://docs.travis-ci.com/user/customizing-the-build/#Build-Matrix).

#### Info for semaphoreapp.com users

Knapsack Pro supports semaphoreapp ENVs `SEMAPHORE_THREAD_COUNT` and `SEMAPHORE_CURRENT_THREAD`. The only thing you need to do is set up knapsack_pro rspec/cucumber/minitest/test_unit command for as many threads as you need. Here is an example:

    # Thread 1
    ## Step for RSpec
    bundle exec rake knapsack_pro:rspec
    ## Step for Cucumber
    bundle exec rake knapsack_pro:cucumber
    ## Step for Minitest
    bundle exec rake knapsack_pro:minitest
    ## Step for test-unit
    bundle exec rake knapsack_pro:test_unit
    ## Step for Spinach
    bundle exec rake knapsack_pro:spinach

    # Thread 2
    ## Step for RSpec
    bundle exec rake knapsack_pro:rspec
    ## Step for Cucumber
    bundle exec rake knapsack_pro:cucumber
    ## Step for Minitest
    bundle exec rake knapsack_pro:minitest
    ## Step for test-unit
    bundle exec rake knapsack_pro:test_unit
    ## Step for Spinach
    bundle exec rake knapsack_pro:spinach

Tests will be split across threads.

Please remember to set up token like `KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` as global environment.

#### Info for buildkite.com users

Knapsack Pro supports buildkite ENVs `BUILDKITE_PARALLEL_JOB_COUNT` and `BUILDKITE_PARALLEL_JOB`. The only thing you need to do is to configure the parallelism parameter in your build step and run the appropiate command in your build

    # Step for RSpec
    bundle exec rake knapsack_pro:rspec

    # Step for Cucumber
    bundle exec rake knapsack_pro:cucumber

    # Step for Minitest
    bundle exec rake knapsack_pro:minitest

    # Step for test-unit
    bundle exec rake knapsack_pro:test_unit

    # Step for Spinach
    bundle exec rake knapsack_pro:spinach

Please remember to set up token like `KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` as global environment.

Here you can find article [how to set up a new pipeline for your project in Buildkite and configure Knapsack Pro](http://docs.knapsackpro.com/2017/auto-balancing-7-hours-tests-between-100-parallel-jobs-on-ci-buildkite-example) and 2 example repositories for Ruby/Rails projects:

* [Buildkite Rails Parallel Example with Knapsack Pro](https://github.com/KnapsackPro/buildkite-rails-parallel-example-with-knapsack_pro)
* [Buildkite Rails Docker Parallel Example with Knapsack Pro](https://github.com/KnapsackPro/buildkite-rails-docker-parallel-example-with-knapsack_pro)

If you want to use Buildkite retry single agent feature to retry just failed tests on particular agent (CI node) then you should set [`KNAPSACK_PRO_FIXED_QUEUE_SPLIT=true`](#knapsack_pro_fixed_queue_split-remember-queue-split-on-retry-ci-node).

#### Info for Gitlab CI users

Gitlab CI does not provide parallel jobs environment variables so you will have to define `KNAPSACK_PRO_CI_NODE_TOTAL` and `KNAPSACK_PRO_CI_NODE_INDEX` for each parallel job running as part of the same `test` stage. Below is relevant part of `.gitlab-ci.yml` configuration for 2 parallel jobs.

```
# .gitlab-ci.yml
stages:
  - test

variables:
  KNAPSACK_PRO_CI_NODE_TOTAL: 2

# first CI node running in parallel
test_ci_node_0:
  stage: test
  script:
    - export KNAPSACK_PRO_CI_NODE_INDEX=0
    # Cucumber tests in Knapsack Pro Regular Mode (deterministic test suite split)
    - bundle exec rake knapsack_pro:cucumber
    # RSpec tests in Knapsack Pro Queue Mode (dynamic test suite split)
    # It will autobalance bulid because it is executed after Cucumber tests.
    - bundle exec rake knapsack_pro:queue:rspec

# second CI node running in parallel
test_ci_node_1:
  stage: test
  script:
    - export KNAPSACK_PRO_CI_NODE_INDEX=1
    - bundle exec rake knapsack_pro:cucumber
    - bundle exec rake knapsack_pro:queue:rspec
```

Remember to add API tokens like `KNAPSACK_PRO_TEST_SUITE_TOKEN_CUCUMBER` and `KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` to [Secret Variables](https://gitlab.com/help/ci/variables/README.md#secret-variables) in `Gitlab CI Settings -> CI/CD Pipelines -> Secret Variables`.

#### Info for codeship.com users

Codeship does not provide parallel jobs environment variables so you will have to define `KNAPSACK_PRO_CI_NODE_TOTAL` and `KNAPSACK_PRO_CI_NODE_INDEX` for each [parallel test pipeline](https://documentation.codeship.com/basic/builds-and-configuration/parallel-tests/#using-parallel-test-pipelines). Below is an example for 2 parallel test pipelines.

Configure test pipelines (1/2 used)

```
# first CI node running in parallel

# Cucumber tests in Knapsack Pro Regular Mode (deterministic test suite split)
KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:cucumber

# RSpec tests in Knapsack Pro Queue Mode (dynamic test suite split)
# It will autobalance bulid because it is executed after Cucumber tests.
KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=0 bundle exec rake knapsack_pro:queue:rspec
```

Configure test pipelines (2/2 used)

```
# second CI node running in parallel

# Cucumber tests in Knapsack Pro Regular Mode (deterministic test suite split)
KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=1 bundle exec rake knapsack_pro:cucumber

# RSpec tests in Knapsack Pro Queue Mode (dynamic test suite split)
# It will autobalance bulid because it is executed after Cucumber tests.
KNAPSACK_PRO_CI_NODE_TOTAL=2 KNAPSACK_PRO_CI_NODE_INDEX=1 bundle exec rake knapsack_pro:queue:rspec
```

Remember to add API tokens like `KNAPSACK_PRO_TEST_SUITE_TOKEN_CUCUMBER` and `KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` to `Environment` page of your project settings in Codeship.

If you want to use Codeship retry single CI node feature to retry just failed tests on particular CI node then you should set [`KNAPSACK_PRO_FIXED_QUEUE_SPLIT=true`](#knapsack_pro_fixed_queue_split-remember-queue-split-on-retry-ci-node).

#### Info for Heroku CI users

You can parallelize your tests on Heroku CI by configuring `app.json`.

You can set how many parallel dynos with tests you want to run with `quantity` value.
Use `test` key to run knapsack_pro gem.

You need to specify also the environment variable with API token for Knapsack Pro.
For any sensitive environment variables (like Knapsack Pro API token) that you do not want in your `app.json` manifest, you can add them to your pipeline’s Heroku CI settings.

Note the [Heroku CI Parallel Test Runs](https://devcenter.heroku.com/articles/heroku-ci-parallel-test-runs) are in Beta and you may need to ask Heroku support to enabled it for your project.

```
# app.json
{
  "environments": {
    "test": {
      "formation": {
        "test": {
          "quantity": 2
        }
      },
      "addons": [
        "heroku-postgresql"
      ],
      "scripts": {
        "test": "bundle exec rake knapsack_pro:rspec"
      },
      "env": {
        "KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC": "rspec-token"
      }
    }
  }
}
```

You can learn more about [Heroku CI](https://devcenter.heroku.com/articles/heroku-ci).

#### Info for snap-ci.com users

Knapsack Pro supports snap-ci.com ENVs `SNAP_WORKER_TOTAL` and `SNAP_WORKER_INDEX`. The only thing you need to do is to configure number of workers for your project in configuration settings in order to enable parallelism. Next thing is to set below commands to be executed in your stage:

    # Step for RSpec
    bundle exec rake knapsack_pro:rspec

    # Step for Cucumber
    bundle exec rake knapsack_pro:cucumber

    # Step for Minitest
    bundle exec rake knapsack_pro:minitest

    # Step for test-unit
    bundle exec rake knapsack_pro:test_unit

    # Step for Spinach
    bundle exec rake knapsack_pro:spinach

Please remember to set up token like `KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` as global environment.

#### Info for Jenkins users

In order to run parallel jobs with Jenkins you should use Jenkins Pipeline.
You can learn basics about it in the article [Parallelism and Distributed Builds with Jenkins](https://www.cloudbees.com/blog/parallelism-and-distributed-builds-jenkins).

Here is example `Jenkinsfile` working with Jenkins Pipeline.

```
timeout(time: 60, unit: 'MINUTES') {
  node() {
    stage('Checkout') {
      checkout([/* checkout code from git */])

      // determine git commit hash because we need to pass it to knapsack_pro
      COMMIT_HASH = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()

      stash 'source'
    }
  }

  def num_nodes = 4; // define your total number of CI nodes (how many parallel jobs will be executed)
  def nodes = [:]

  for (int i = 0; i < num_nodes; i++) {
    def index = i;
    nodes["ci_node_${i}"] = {
      node() {
        stage('Setup') {
          unstash 'source'
          // other setup steps
        }

        def knapsack_options = """\
            KNAPSACK_PRO_CI_NODE_TOTAL=${num_nodes}\
            KNAPSACK_PRO_CI_NODE_INDEX=${index}\
            KNAPSACK_PRO_COMMIT_HASH=${COMMIT_HASH}\
            KNAPSACK_PRO_BRANCH=${env.BRANCH_NAME}\
        """

        // example how to run cucumber tests in Knapsack Pro Regular Mode
        stage('Run cucumber') {
          sh """${knapsack_options} bundle exec rake knapsack_pro:cucumber"""
        }

        // example how to run rspec tests in Knapsack Pro Queue Mode
        // Queue Mode should be as a last stage so it can autobalance build if tests in regular mode were not perfectly distributed
        stage('Run rspec') {
          sh """KNAPSACK_PRO_CI_NODE_BUILD_ID=${env.BUILD_TAG} ${knapsack_options} bundle exec rake knapsack_pro:queue:rspec"""
        }
      }
    }
  }

  parallel nodes // run CI nodes in parallel
}
```

Remember to set environment variables in Jenkins configuration with your API tokens like `KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` and `KNAPSACK_PRO_TEST_SUITE_TOKEN_CUCUMBER`.
Here is [list of environment variables per test runner](#set-api-key-token).

Above example shows how to run cucumber tests in regular mode and later the rspec tests in queue mode to autobalance build.
If you are going to relay on rspec to autobalance build when cucumber tests were not perfectly distributed you should be aware about [possible edge case if your rspec test suite is very short](#why-my-tests-are-executed-twice-in-queue-mode-why-ci-node-runs-whole-test-suite-again).

## FAQ

### Common problems

#### Why I see API error commit_hash parameter is required?

    ERROR -- : [knapsack_pro] {"errors"=>[{"commit_hash"=>["parameter is required"]}]}

When Knapsack Pro API returns error like above the problem is because you use CI provider not supported by knapack_pro which means
knapack_pro gem cannot determine the git commit hash and branch name. To fix this problem you can do:

* if you have git installed on CI node then you can use it to determine git commit hash and branch name. [See this](#when-should-you-set-global-variable-knapsack_pro_repository_adaptergit-required-when-ci-provider-is-not-supported)
* if you have no git installed on CI node then you should manually set `KNAPSACK_PRO_BRANCH` and `KNAPSACK_PRO_COMMIT_HASH`. For instance this might be useful when you use Jenkins. [See this](#when-you-not-set-global-variable-knapsack_pro_repository_adapter-default)

#### Why I see `LoadError: cannot load such file -- spec_helper`?

When your tests fails with:

    LoadError: cannot load such file -- spec_helper

then problem might be related to the fact you specified complex `KNAPSACK_PRO_TEST_FILE_PATTERN` and knapsack_pro gem cannot detect correct main test directory with spec_helper. You should set `KNAPSACK_PRO_TEST_DIR=spec`. Please [read also example](#how-can-i-run-tests-from-multiple-directories).

#### Queue Mode problems

##### Why when I use Queue Mode for RSpec then my tests fail?

knapsack_pro Queue Mode uses `RSpec::Core::Runner` feature that allows [running specs multiple times with different runner options in the same process](https://relishapp.com/rspec/rspec-core/docs/running-specs-multiple-times-with-different-runner-options-in-the-same-process).
Thanks to that we can run subset of test suite pulled from Knapsack Pro API work queue. This allows dynamic allocation of your tests across CI nodes without reloading whole Ruby/Rails application for each run of test suite subset .

If you have custom things that are not common in how typical RSpec spec looks like then the RSpec feature won't be able to handle it between test suite subset runs.
In that case you need to resolve failed tests in a way that allows RSpec to run the tests. Feel free to [ask me for help](https://knapsackpro.com/contact).

You can learn more about [recent RSpec team changes](https://github.com/KnapsackPro/knapsack_pro-ruby/pull/42) that was backported into knapsack_pro.

##### Why when I use Queue Mode for RSpec then FactoryBot/FactoryGirl tests fail?

You can use [knapsack_pro binary](#knapsack-pro-binary) instead of rake task version to solve problem:

```
# knapsack_pro binary for Queue Mode
$ bundle exec knapsack_pro queue:rspec
```

Other solution is to check if your factories for FactoryBot/FactoryGirl use the same methods as Rake DSL and remove problematic part of the code.

The use of implicit association `task` can cause a problem.

```ruby
# won't work in knapsack_pro Queue Mode
FactoryBot.define do
  factory :assignment do
    task
  end
end
```

Workaround is to replace `task` with explicit association:

```ruby
# this will work in knapack_pro Queue Mode
FactoryBot.define do
  factory :assignment do
    association :task
  end
end
```

##### Why I don't see collected time execution data for my build in user dashboard?

If you go to [user dashboard](https://knapsackpro.com/dashboard) and open `Build metrics` for your API token and you open build for your last git commit you should see there info about collected time execution data from all CI nodes. If you don't see collected time execution data for CI nodes then please ensure:

* you have `Knapsack::Adapters::RspecAdapter.bind` in your `rails_helper.rb` or `spec_helper.rb`
* you explicitly set `RAILS_ENV=test` on your CI nodes (for instance you use Docker then please set `RAILS_ENV`)
* knapsack_pro Queue Mode saves temporary files with collected time execution data in `your_rails_project/tmp/knapsack_pro/queue/`. Please ensure you don't clean `tmp` directory in your tests so knapsack_pro can publish time execution data to Knapsack Pro API server.

##### Why when I use Queue Mode for RSpec and test fails then I see multiple times info about failed test in RSpec result?

The problem may happen when you use old knapsack_pro `< 0.33.0` or if you use custom rspec formatter, or when you set flag [KNAPSACK_PRO_MODIFY_DEFAULT_RSPEC_FORMATTERS=false](#knapsack_pro_modify_default_rspec_formatters-hide-duplicated-summary-of-pending-and-failed-tests).

When you use Queue Mode then knapack_pro does multiple requests to Knapsack Pro API and fetches a few test files to execute.
This means RSpec will remember failed tests so far and it will present them at the end of each executed test subset if flag `KNAPSACK_PRO_MODIFY_DEFAULT_RSPEC_FORMATTERS=false`.
You can see the list of all failed test files at the end of knapack_pro queue mode command.

##### Why when I use Queue Mode for RSpec then I see multiple times the same pending tests?

The problem may happen when you use old knapsack_pro `< 0.33.0` or if you use custom rspec formatter, or when you set flag [KNAPSACK_PRO_MODIFY_DEFAULT_RSPEC_FORMATTERS=false](#knapsack_pro_modify_default_rspec_formatters-hide-duplicated-summary-of-pending-and-failed-tests).

When you use Queue Mode then knapack_pro does multiple requests to Knapsack Pro API and fetches a few test files to execute.
This means RSpec will remember pending tests so far and it will present them at the end of each executed test subset if flag `KNAPSACK_PRO_MODIFY_DEFAULT_RSPEC_FORMATTERS=false`.
You can see the list of all pending test files at the end of knapack_pro queue mode command.

##### Does in Queue Mode the RSpec is initialized many times that causes Rails load over and over again?

No. In Queue Mode the RSpec configuration is updated every time when knapsack_pro gem gets a new set of test files from the Knapsack Pro API and it looks in knapsack_pro output like RSpec was loaded many times but in fact, it loads your project environment only once.

##### Why my tests are executed twice in queue mode? Why CI node runs whole test suite again?

This may happen when you use not supported CI provider by knapack_pro. It's because of missing value of CI build ID. You can set unique `KNAPSACK_PRO_CI_NODE_BUILD_ID` for each CI build. The problem with test suite run again happens when one of your CI node started work later when all other CI nodes already executed whole test suite.
The slow CI node that started work late will initialize a new queue hence the tests executed twice.

To solve this problem you can set `KNAPSACK_PRO_CI_NODE_BUILD_ID` as mentioned above or you can set `KNAPSACK_PRO_FIXED_QUEUE_SPLIT=true`.
Please [read this](#knapsack_pro_fixed_queue_split-remember-queue-split-on-retry-ci-node).

##### How to fix capybara-screenshot fail with `SystemStackError: stack level too deep` when using Queue Mode for RSpec?

Please use fixed version of capybara-screenshot.

```
# Gemfile
group :test do
  gem 'capybara-screenshot', github: 'mattheworiordan/capybara-screenshot', branch: 'master'
end
```

Here is [fix PR](https://github.com/mattheworiordan/capybara-screenshot/pull/205) to official capybara-screenshot repository and the explanation of the problem.

##### Parallel tests Cucumber and RSpec with Cucumber failures exit CI node early leaving fewer CI nodes to finish RSpec Queue.

If you run tests in 2 steps like:

* Step 1. `bundle exec rake knapsack_pro:cucumber` (regular mode)
* Step 2. `bundle exec rake knapsack_pro:queue:rspec` (queue mode)

and your CI provider is configured to fail fast when one of the steps fails then in the case when the first step with Cucumber fails on one of CI nodes then the second step with RSpec in Queue Mode won't start on the CI node that failed fast.

It means the other CI nodes that will run the second step for RSpec in Queue Mode will consume the whole RSpec Queue so your whole CI build will take more than typical CI build when all Cucumber tests are green.

You should configure your CI provider to not fail fast the Cucumber step.

CI providers tips:

* If you use CircleCI 2.0 you can use `when=always` flag. Read more [here](https://discuss.circleci.com/t/parallel-tests-cuc-rspec-w-failures-exit-early-leaving-less-workers-to-finish/18081).

### General questions

#### How to run tests for particular CI node in your development environment

##### for knapack_pro regular mode

In your development environment you can debug tests that were run on the particular CI node.
For instance to run subset of tests for the first CI node with specified seed you can do.

    KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=token \
    KNAPSACK_PRO_REPOSITORY_ADAPTER=git \
    KNAPSACK_PRO_PROJECT_DIR=~/projects/rails-app \
    KNAPSACK_PRO_CI_NODE_TOTAL=2 \
    KNAPSACK_PRO_CI_NODE_INDEX=0 \
    bundle exec rake "knapsack_pro:rspec[--seed 123]"

Above example is for RSpec. You can use respectively rake task name and token environment variable when you want to run tests for minitest, test_unit, cucumber or spinach.
It should work when all CI nodes finished work and sent time execution data to Knapsack Pro API.
You can visit [user dashboard](https://knapsackpro.com/dashboard) to preview particular CI build and ensure time execution data were collected from all CI nodes.
If at least one CI node has not sent time execution data to the Knapsack Pro API then you should check below solution.

Check test runner output on particular CI node you would like to retry in development. You should see at the beginning of rspec command an output that can
be copied and executed in development.

    /Users/ubuntu/.rvm/gems/ruby-2.4.0/gems/rspec-core-3.4.4/exe/rspec spec/foo_spec.rb spec/bar_spec.rb --default-path spec

Command similar to above can be executed in your development this way:

    bundle exec rspec spec/foo_spec.rb spec/bar_spec.rb --default-path spec

If you were running your tests with `--order random` on your CI then you can additionaly pass seed param with proper value in above command (`--seed 123`).

##### for knapsack_pro queue mode

There are a few ways to reproduce tests executed on CI node in your development environment.

* At the end of `knapsack_pro:queue:rspec` results you will find example of command that you can copy and paste to your development machine. It will run all tests executed on the CI node in a single run. I recommend this approach.

* For each intermediate request to Knapsack Pro API queue you will also find example of command to run a subset of tests fetched from API. This might be helpful when you use `--order random` for rspec and you would like to reproduce the tests with the same seed.

* You can also retry tests and record the time execution data for them again for the particular CI node. Note you must be checkout on the same branch and git commit as your CI node was.

  To retry the particular CI node do this on your machine:

      RACK_ENV=test \
      RAILS_ENV=test \
      KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=token \
      KNAPSACK_PRO_REPOSITORY_ADAPTER=git \
      KNAPSACK_PRO_PROJECT_DIR=~/projects/rails-app \
      KNAPSACK_PRO_CI_NODE_TOTAL=2 \
      KNAPSACK_PRO_CI_NODE_INDEX=0 \
      KNAPSACK_PRO_FIXED_QUEUE_SPLIT=true \
      bundle exec rake "knapsack_pro:queue:rspec"

  If you were running your tests with `--order random` on your CI like this:

      bundle exec rake "knapsack_pro:queue:rspec[--order random]"

  Then you can find the seed number visible in rspec output:

      (...)
      Randomized with seed 123

  You can pass the seed in your local environment to reproduce the tests in the same order as they were executed on CI node:

      RACK_ENV=test \
      RAILS_ENV=test \
      KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=token \
      KNAPSACK_PRO_REPOSITORY_ADAPTER=git \
      KNAPSACK_PRO_PROJECT_DIR=~/projects/rails-app \
      KNAPSACK_PRO_CI_NODE_TOTAL=2 \
      KNAPSACK_PRO_CI_NODE_INDEX=0 \
      KNAPSACK_PRO_FIXED_QUEUE_SPLIT=true \
      bundle exec rake "knapsack_pro:queue:rspec[--seed 123]"

#### What happens when Knapsack Pro API is not available/not reachable temporarily?

##### for knapack_pro regular mode

knapack_pro gem will retry requests to Knapsack Pro API multiple times every few seconds til it switch to fallback behavior and it will split test files across CI nodes based on popular test directory names. When knapack_pro starts fallback mode then you will see a warning in the output.

##### for knapsack_pro queue mode

knapack_pro gem will retry requests to Knapsack Pro API multiple times every few seconds till it switches to fallback behavior and it will split test files across CI nodes based on popular test directory names. Note that if one of CI nodes will lose connection to Knapsack Pro API but other not then you may see that some of the test files will be executed on multiple CI nodes. Fallback mode guarantees each of test files is run at least once across CI nodes. Thanks to that we know if the whole test suite is green or not. When knapack_pro starts fallback mode then you will see a warning in the output.

#### How can I change log level?

You can change log level by specifying the `KNAPSACK_PRO_LOG_LEVEL` environment variable.

    KNAPSACK_PRO_LOG_LEVEL=info bundle exec rake knapsack_pro:rspec

Available values are `debug` (default), `info`, `warn`, `error` and `fatal`.

Recommended log levels you can use:

* `debug` is default log level and it is recommended to log details about requests to Knapsack Pro API. Thanks to that you can debug things or ensure everything works. For instance in [user dashboard](https://knapsackpro.com/dashboard) you can find tips referring to debug logs.
* `info` level shows message like how to retry tests in development or info why something works this way or the other (for instance why tests were not executed on the CI node). You can use `info` level when you really don't want to see all debug messages from default log level.

#### How to split tests based on test level instead of test file level?

If you want to split one big test file (test file with long time execution) across multiple CI nodes then you can:

##### A. Create multiple small test files

Create multiple small test files instead of one long running test file with many test cases.
A lot of small test files will give you better test suite split results.

##### B. Use tags to mark set of tests in particular test file

Another way is to use tags to mark subset of tests in particular test file and then split tests based on tags.

This example is for knapack_pro Regular Mode. You can also use knapack_pro Queue Mode with tags.

Here is example of test file with specified tags for describe groups:

```ruby
# spec/features/something_spec.rb
describe 'Feature' do
  describe 'something A', :tagA do
    it {}
    it 'another test' {}
  end

  describe 'something B', :tagB do
    it {}
  end

  describe 'something else' do
    it {}
  end
end
```

You need to create API token per each tag. In this example we need 3 different API tokens.

You need to run below commands for each CI node.

    # run only tests with tagA
    KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=api_key_for_tagA bundle exec rake "knapsack_pro:rspec[--tag tagA]"

    # run only tests with tagB
    KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=api_key_for_tagB bundle exec rake "knapsack_pro:rspec[--tag tagB]"

    # run other tests without tag A & B
    KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=api_key_for_tests_without_tags_A_and_B bundle exec rake "knapsack_pro:rspec[--tag ~tagA --tag ~tagB]"

#### How to make knapsack_pro works for forked repositories of my project?

Imagine one of the scenarios, for this example I use the Travis-CI.

* We don’t want to have secrets like the `KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` in `.travis.yml` in the codebase, because that code is also distributed to clients.
* Adding it as env variables to Travis itself is tricky: It has to work for pull requests from developer’s forks into our main fork; this conflicts with the way Travis handles secrets. We also need a fallback if the token is not provided (when developers do builds within their own fork).

The solution for this problem is to set `KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` as env variables in Travis for our main project.
This won't be accessible on forked repositories so we will run knapsack_pro in fallback mode there.
This way forked repositories have working test suite but without optimal test suite split across CI nodes.

Create the file `bin/knapsack_pro_rspec` with executable chmod in your main project repository.
Below example is for rspec. You can change `$KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC` to `$KNAPSACK_PRO_TEST_SUITE_TOKEN_CUCUMBER` if you use cucumber etc.

```
#!/bin/bash
if [ "$KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC" = "" ]; then
  KNAPSACK_PRO_ENDPOINT=https://api-disabled-for-fork.knapsackpro.com \
    KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=disabled-for-fork \
    bundle exec rake knapsack_pro:rspec # use Regular Mode here always
else
    # Regular Mode
    bundle exec rake knapsack_pro:rspec

    # You can use Queue Mode instead of Regular Mode if you like
    # bundle exec rake knapsack_pro:queue:rspec
fi
```

Now you can use `bin/knapsack_pro_rspec` command instead of `bundle exec rake knapsack_pro:rspec`.
Remember to follow other steps required for your CI provider.

#### How to use junit formatter?

##### How to use junit formatter with knapsack_pro regular mode?

You can use junit formatter for rspec thanks to gem [rspec_junit_formatter](https://github.com/sj26/rspec_junit_formatter).
Here you can find example how to generate `rspec.xml` file with junit format and at the same time show normal documentation format output for RSpec.

    # Regular Mode
    bundle exec rake "knapsack_pro:rspec[--format documentation --format RspecJunitFormatter --out tmp/rspec.xml]"

##### How to use junit formatter with knapsack_pro queue mode?

You can use junit formatter for rspec thanks to gem [rspec_junit_formatter](https://github.com/sj26/rspec_junit_formatter).

    # Queue Mode
    bundle exec rake "knapsack_pro:queue:rspec[--format documentation --format RspecJunitFormatter --out tmp/rspec.xml]"

The xml report will contain all tests executed across intermediate test subset runs based on work queue. You need to add after subset queue hook to rename `rspec.xml` to `rspec_final_results.xml` thanks to that the final results file will contain only single xml tag with all tests executed on the CI node. This is related to the way how queue mode works. Detailed explanation is in the [issue](https://github.com/KnapsackPro/knapsack_pro-ruby/issues/40).

    # spec_helper.rb or rails_helper.rb

    # TODO This must be the same path as value for rspec --out argument
    TMP_RSPEC_XML_REPORT = 'tmp/rspec.xml'
    # move results to FINAL_RSPEC_XML_REPORT so the results won't accumulate with duplicated xml tags in TMP_RSPEC_XML_REPORT
    FINAL_RSPEC_XML_REPORT = 'tmp/rspec_final_results.xml'

    KnapsackPro::Hooks::Queue.after_subset_queue do |queue_id, subset_queue_id|
      if File.exist?(TMP_RSPEC_XML_REPORT)
        FileUtils.mv(TMP_RSPEC_XML_REPORT, FINAL_RSPEC_XML_REPORT)
      end
    end

#### How many API keys I need?

Basically you need as many API keys as you have steps in your build.

Here is example:

* Step 1. API_KEY_A for `bundle exec rake knapsack_pro:cucumber`
* Step 2. API_KEY_B for `bundle exec rake knapsack_pro:rspec`
* Step 3. API_KEY_C for `KNAPSACK_PRO_TEST_FILE_PATTERN="specs/features/*_spec.rb" bundle exec rake knapsack_pro:rspec`
* Step 4. API_KEY_D for `bundle exec rake knapsack_pro:rspec[--tag tagA]`
* Step 5. API_KEY_E for `bundle exec rake knapsack_pro:rspec[--tag ~tagA]`
* Step 6. API_KEY_F for `bundle exec rake knapsack_pro:queue:rspec`

Note:

* If you specified `KNAPSACK_PRO_TEST_FILE_PATTERN` then you run subset of your whole test suite hence you need separate API key because we want to track only tests for this subset.
* If you pass `--tag tagA` or `--tag ~tagA` then you run subset of your whole test suite hence you need separate API key.
* If you use regular or queue mode then you need separate API key for each mode.

#### What is optimal order of test commands?

__Tip 1:__

I recommend to run first the test commands in the regular mode and later the commands in the queue mode.

  * Step 1. `bundle exec rake knapsack_pro:cucumber` (regular mode)
  * Step 2. `bundle exec rake knapsack_pro:queue:rspec` (queue mode)

Thanks to that when for some reason the tests executed for cucumber in regular mode will not be well balanced across CI nodes (for instance when one of CI node has bad performance) then the rspec tests executed later in the queue mode will autobalance your build.

__Tip 2:__

When you have short test suite, for instance in javascript then you could distribute tests this way:

* CI 0
  * Step 1: `npm test`
  * Step 2: `bundle exec rake knapsack_pro:queue:rspec`

* CI 1
  * Step 1: `bundle exec rake knapsack_pro:queue:rspec`

You will run your javascript tests on single CI node and the knapack_pro will auto-balance CI build with Queue Mode. Thanks to that CI build time execution will be flat and optimal (as fast as possible).

#### How to set `before(:suite)` and `after(:suite)` RSpec hooks in Queue Mode (Percy.io example)?

Some tools like [Percy.io](https://percy.io/docs/clients/ruby/capybara-rails) requires to set hooks for RSpec `before(:suite)` and `after(:suite)`.
Knapsack Pro Queue Mode runs subset of test files from the work queue many times. This means the RSpec hooks `before(:suite)` and `after(:suite)` will execute multiple times. If you want to run some code only once before Queue Mode starts work and after it finishes then you should do it this way:

```ruby
# spec_helper.rb or rails_helper.rb

KnapsackPro::Hooks::Queue.before_queue do |queue_id|
  # executes before Queue Mode starts work
  Percy::Capybara.initialize_build
end

KnapsackPro::Hooks::Queue.after_queue do |queue_id|
  # executes after Queue Mode finishes work
  Percy::Capybara.finalize_build
end
```

#### How to call `before(:suite)` and `after(:suite)` RSpec hooks only once in Queue Mode?

Knapsack Pro Queue Mode runs subset of test files from the work queue many times. This means the RSpec hooks `before(:suite)` and `after(:suite)` will be executed multiple times. If you want to run some code only once before Queue Mode starts work and after it finishes then you should do it this way:

```ruby
# spec_helper.rb or rails_helper.rb

KnapsackPro::Hooks::Queue.before_queue do |queue_id|
  # This will be called only once before the tests started on the CI node.
  # It will be run inside of the RSpec before(:suite) block only once.
  # It means you will have access to whatever RSpec provides in the context of the before(:suite) block.
end

KnapsackPro::Hooks::Queue.after_queue do |queue_id|
  # This will be called only once after test suite is completed.
  # Note this hook won't be called inside of RSpec after(:suite) block because
  # we are not able to determine which after(:suite) block will be called as the last one
  # due to the fact the Knapsack Pro Queue Mode allocates tests in dynamic way.
end
```

#### How to run knapsack_pro with parallel_tests gem?

You can run knapsack_pro with [parallel_tests](https://github.com/grosser/parallel_tests) gem to run multiple concurrent knapsack_pro commands per CI node.

Let's consider this example. We have 2 CI node. On each CI node we want to run 2 concurrent knapsack_pro commands by parallel_tests gem (`PARALLEL_TESTS_CONCURRENCY=2`).
This means we would have 4 parallel knapack_pro commands in total across all CI nodes. So from knapsack_pro perspective you will have 4 nodes in total.

Create in your project directory an executable file `bin/parallel_tests`:

```
#!/bin/bash
# This file should be in bin/parallel_tests

# updates CI node total based on parallel_tests concurrency
KNAPSACK_PRO_CI_NODE_TOTAL=$(( $PARALLEL_TESTS_CONCURRENCY * $KNAPSACK_PRO_CI_NODE_TOTAL ))

if [ "$TEST_ENV_NUMBER" == "" ]; then
  PARALLEL_TESTS_CONCURRENCY_INDEX=0
else
  PARALLEL_TESTS_CONCURRENCY_INDEX=$(( $TEST_ENV_NUMBER - 1 ))
fi

KNAPSACK_PRO_CI_NODE_INDEX=$(( $PARALLEL_TESTS_CONCURRENCY_INDEX + ($PARALLEL_TESTS_CONCURRENCY * $KNAPSACK_PRO_CI_NODE_INDEX) ))

# logs info about ENVs to ensure everything works
echo KNAPSACK_PRO_CI_NODE_TOTAL=$KNAPSACK_PRO_CI_NODE_TOTAL KNAPSACK_PRO_CI_NODE_INDEX=$KNAPSACK_PRO_CI_NODE_INDEX PARALLEL_TESTS_CONCURRENCY=$PARALLEL_TESTS_CONCURRENCY

# you can customize your knapsack_pro command here to use regular or queue mode
bundle exec rake knapsack_pro:queue:rspec
```

Now you need to set parallel_tests command per CI node:

* CI node 0 (first CI node):

    ```
    export PARALLEL_TESTS_CONCURRENCY=2; # this must be export
    RAILS_ENV=test \
    KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=xxx \
    KNAPSACK_PRO_CI_NODE_TOTAL=$YOUR_CI_NODE_TOTAL \
    KNAPSACK_PRO_CI_NODE_INDEX=$YOUR_CI_NODE_INDEX \
    bundle exec parallel_test -n $PARALLEL_TESTS_CONCURRENCY -e './bin/parallel_tests'
    ```

* CI node 1 (second CI node):

    ```
    export PARALLEL_TESTS_CONCURRENCY=2; # this must be export
    RAILS_ENV=test \
    KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=xxx \
    KNAPSACK_PRO_CI_NODE_TOTAL=$YOUR_CI_NODE_TOTAL \
    KNAPSACK_PRO_CI_NODE_INDEX=$YOUR_CI_NODE_INDEX \
    bundle exec parallel_test -n $PARALLEL_TESTS_CONCURRENCY -e './bin/parallel_tests'
    ```

Please note you need to update `$YOUR_CI_NODE_TOTAL` and `$YOUR_CI_NODE_INDEX` to the ENVs provided by your CI provider. For instance in case of CircleCI it would be `$CIRCLE_NODE_TOTAL` and `$CIRCLE_NODE_INDEX`. Below is an example for CircleCI configuration:

```
# circle.yml for CircleCI 1.0
# KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=xxx can be set in CircleCI ENV settings
test:
  override:
    - export PARALLEL_TESTS_CONCURRENCY=2; RAILS_ENV=test KNAPSACK_PRO_CI_NODE_TOTAL=$CIRCLE_NODE_TOTAL KNAPSACK_PRO_CI_NODE_INDEX=$CIRCLE_NODE_INDEX bundle exec parallel_test -n $PARALLEL_TESTS_CONCURRENCY -e './bin/parallel_tests':
        parallel: true # Caution: there are 8 spaces indentation!
```

In summary, the `bin/parallel_tests` script will calculate a new values for `KNAPSAKC_PRO_*` environment variables and then run knapsack_pro command with them.
To ensure everything works you can check output for each CI node.

* Expected output for CI node 0 (first CI node):

    ```
    KNAPSACK_PRO_CI_NODE_TOTAL=4 KNAPSACK_PRO_CI_NODE_INDEX=1 PARALLEL_TESTS_CONCURRENCY=2
    KNAPSACK_PRO_CI_NODE_TOTAL=4 KNAPSACK_PRO_CI_NODE_INDEX=0 PARALLEL_TESTS_CONCURRENCY=2
    (tests output here)
    ```

* Expected output for CI node 1 (second CI node):

    ```
    KNAPSACK_PRO_CI_NODE_TOTAL=4 KNAPSACK_PRO_CI_NODE_INDEX=2 PARALLEL_TESTS_CONCURRENCY=2
    KNAPSACK_PRO_CI_NODE_TOTAL=4 KNAPSACK_PRO_CI_NODE_INDEX=3 PARALLEL_TESTS_CONCURRENCY=2
    (tests output here)
    ```

#### How to retry failed tests (flaky tests)?

Flaky (nondeterministic) tests, are tests that exhibit both a passing and a failing result with the same code.

You can use [only failures option in RSpec](https://relishapp.com/rspec/rspec-core/docs/command-line/only-failures) to rerun failed tests.

Please add to your RSpec configuration:

```
RSpec.configure do |c|
  c.example_status_persistence_file_path = "tmp/rspec_examples.txt"
end
```

Then you can execute rspec with only failed tests after main knapsack_pro command finish.

```
# Run knapack_pro in Queue Mode and it will save failed tests in tmp/rspec_examples.txt
bundle exec rake knapsack_pro:queue:rspec

# run only failed tests from tmp/rspec_examples.txt
bundle exec rspec --only-failures
```

#### How can I run tests from multiple directories?

The test file pattern config option supports any glob pattern handled by [`Dir.glob`](http://ruby-doc.org/core-2.4.1/Dir.html#method-c-glob) and can be configured to pull test files from multiple directories. An example of this when using RSpec would be `""{spec,engines/*/spec}/**/*_spec.rb" "`. For complex cases like this, the test directory can't be extracted and must be specified manually using the `KNAPSACK_PRO_TEST_DIR` environment variable:

```
# This is example where you have in engines directory multiple projects
# and each project directory has a spec folder and you would like to run tests for it.
# You want to use the spec_helper from the main spec directory.
#
# Tree:
# * spec
# * engines
#   * project_a
#     * spec
#   * project_b
#     * spec
$ KNAPSACK_PRO_TEST_DIR=spec KNAPSACK_PRO_TEST_FILE_PATTERN="{spec,engines/*/spec}/**/*_spec.rb" bundle exec rake knapsack_pro:queue:rspec
```

`KNAPSACK_PRO_TEST_DIR` will be your default path for rspec so you should put there your `spec_helper.rb`. Please ensure you will require it in your test files this way if something doesn't work:

```ruby
# good
require_relative 'spec_helper'

# bad - won't work
require 'spec_helper'
```

#### Why I don't see all test files being recorded in user dashboard

If you open `Build metrics` for particular API token at [user dashboard](https://knapsackpro.com/dashboard) and you don't see all time execution data recorded for all test files then you should know that knapsack_pro does not track test files with empty content or when the test file contains only pending tests.

The test files with pending tests are executed so you will see it in RSpec output but just not recorded in Knapsack Pro API because there is nothing to record time for.

#### Why when I use 2 different CI providers then not all test files are executed?

Please ensure you use 2 different API token per test suite. If you use 2 CI providers for instance CircleCI and TravisCI at the same time and you run the RSpec test suite then you need to have separate API token for RSpec executed on CircleCI and a separate API token for RSpec test suite executed on the TravisCI.

#### How to run only RSpec feature tests or non feature tests?

**Option 1: RSpec tags**

You can run just feature tests this way. You need to generate a separate API token for it.

```
KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=$API_TOKEN_FOR_FEATURE_TESTS bundle exec rake "knapsack_pro:queue:rspec[--tag type:feature]"
```

If you would like to run only non feature tests then use negation `~type:feature`:

```
KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=$API_TOKEN_FOR_NON_FEATURE_TESTS bundle exec rake "knapsack_pro:queue:rspec[--tag ~type:feature]"
```

Note above examples are for knapack_pro Queue Mode and when you will run tests you may notice that all test files are run by RSpec but only tests specified by tag like `tag type:feature` will be executed. Basically RSpec will just load all files but run just specified tags.

**Option 2: specify directory pattern**

Another approach is to explicitly specify which files should be executed.

Run all specs from multiple directories except `spec/features` directory which is not listed below.
If you would like to run additional directory please add it after comma in `KNAPSACK_PRO_TEST_FILE_PATTERN`.
Ensure the list of directories match your spec directory structure.

```
KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=$API_TOKEN_FOR_NON_FEATURE_TESTS \
KNAPSACK_PRO_TEST_DIR=spec \
KNAPSACK_PRO_TEST_FILE_PATTERN="{spec/*_spec.rb,spec/controllers/**/*_spec.rb,spec/mailers/**/*_spec.rb,spec/models/**/*_spec.rb,spec/presenters/**/*_spec.rb,spec/requests/**/*_spec.rb,spec/routing/**/*_spec.rb,spec/services/**/*_spec.rb,spec/workers/**/*_spec.rb,spec/jobs/**/*_spec.rb}" \
bundle exec rake knapsack_pro:queue:rspec
```

When you would like to run tests only from `spec/features` directory then run:

```
KNAPSACK_PRO_TEST_SUITE_TOKEN_RSPEC=$API_TOKEN_FOR_FEATURE_TESTS \
KNAPSACK_PRO_TEST_DIR=spec \
KNAPSACK_PRO_TEST_FILE_PATTERN="spec/features/**{,/*/**}/*_spec.rb" \
bundle exec rake knapsack_pro:queue:rspec
```

#### How to use CodeClimate with knapsack_pro?

You can check CodeClimate docs about [parallel and multiple test suites](https://docs.codeclimate.com/docs/configuring-test-coverage#section-parallel-and-multiple-test-suites).

### Questions around data usage and security

#### What data is sent to your servers?

The knapsack_pro gem sends branch name, commit hash, CI total node number, CI index node number, the test file paths like `spec/models/user_spec.rb` and the time execution of each test file path as a float.

Here is the [full specification of the API](http://docs.knapsackpro.com/api/v1/) used by knapsack_pro gem.

#### How is that data secured?

The test file paths and/or branch names can be [encrypted](#test-file-names-encryption) on your CI node with a salt and later send to knapsackpro.com API.
You generate the salt locally and __only you__ can decrypt the test file paths or branch names with the salt. Here you can [see how the data are encrypted](lib/knapsack_pro/crypto/digestor.rb).

Connection with knapsackpro.com server is via https.

Regarding payments we use the BraintreePayments.com and they store credit cards and your private information.

#### Who has access to the data?

I’m the only admin so I can preview data in case you need help with debugging some problem etc. I’m not able to decrypt them without knowing the salt.

When you sign in to your user dashboard then you can preview data for recent 100 builds on CI. If the test file paths are encrypted then you only see hashes for test file paths.
You need to [decrypt](#how-to-debug-test-file-names) them locally on your machine to find out what each test file hash is.

## Gem tests

### Spec

To run specs for Knapsack Pro gem type:

    $ bundle exec rspec spec

## Contributing

1. Fork it ( https://github.com/KnapsackPro/knapsack_pro-ruby )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. You can create example tests in related repository with example of [rails application and knapsack_pro gem usage](https://github.com/KnapsackPro/rails-app-with-knapsack_pro).
6. Create a new Pull Request

## Mentions

List of articles where people mentioned Knapsack Pro:

* [Treat your Build Pipeline as a Product](https://medium.com/mydr-engineering/treat-your-build-pipeline-as-a-product-61a1b24ae538).
  * Video - [Your Build Pipeline is a Product](https://www.youtube.com/watch?v=7e8Qk3H6xhg&t=21m14s)
