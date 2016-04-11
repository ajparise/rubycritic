RubyCritic
==========

[![Gem Version](https://badge.fury.io/rb/rubycritic.svg)](http://badge.fury.io/rb/rubycritic)
[![Build Status](https://travis-ci.org/whitesmith/rubycritic.svg?branch=master)](https://travis-ci.org/whitesmith/rubycritic)
[![Code Climate](https://codeclimate.com/github/whitesmith/rubycritic/badges/gpa.svg)](https://codeclimate.com/github/whitesmith/rubycritic)

<img src="http://i.imgur.com/66HACCD.png" alt="RubyCritic Icon" align="right" />
RubyCritic is a gem that wraps around static analysis gems such as [Reek][1], [Flay][2] and [Flog][3] to provide a quality report of your Ruby code.

This gem provides features such as:

1. An overview of your project:

  ![RubyCritic overview screenshot](http://i.imgur.com/oiE5O3X.png)

2. An index of the project files with their respective number of smells:

  ![RubyCritic code index screenshot](http://i.imgur.com/a0GCn48.png)

3. An index of the smells detected:

  ![RubyCritic smells index screenshot](http://i.imgur.com/PvkFEe3.png)

4. When analysing code like the following:

  ```ruby
  class Dirty
    def awful(x, y)
      if y
        @screen = widgets.map {|w| w.each {|key| key += 3}}
      end
    end
  end
  ```

  It basically turns something like this:

  ![Reek output screenshot](http://i.imgur.com/5G2zPIC.png)

  Into something like this:

  ![RubyCritic file code screenshot](http://i.imgur.com/RNzXewk.png)

5. It uses your source control system (only Git is supported for now) to
  compare your currently uncommitted changes with your last commit.

  **Warning**: If your code is not as you expect it to be after running
  RubyCritic, please check your source control system stash.

Getting Started
---------------

RubyCritic can be installed with the following command:

```bash
$ gem install rubycritic
```

If you'd rather install RubyCritic using Bundler, add this line to your
application's Gemfile:

```ruby
gem "rubycritic", :require => false
```

And then execute:

```bash
$ bundle
```

Usage
-----

Running `rubycritic` with no arguments will analyse all the Ruby files in the
current directory:

```bash
$ rubycritic
```

Alternatively you can pass `rubycritic` a list of files and directories to check:

```bash
$ rubycritic app lib/foo.rb
```

For a full list of the command-line options run:

```bash
$ rubycritic --help
```

| Command flag             | Description                                           |
|--------------------------|-------------------------------------------------------|
| `-v/--version`           | Displays the current version and exits                |
| `-p/--path`              | Sets the output directory (tmp/rubycritic by default) |
| `-s/--minimum-score`     | Set a minimum score                                   |
| `--mode-ci`              | Uses CI mode (faster, but only analyses last commit)  |
| `--deduplicate-symlinks` | De-duplicate symlinks based on their final target     |
| `--suppress-ratings`     | Suppress letter ratings                               |

Analyzer Configuration
-------------------------

* [`Reek`](https://github.com/troessner/reek): `RubyCritic` utilizes `Reek`'s default [configuration loading mechanism](https://github.com/troessner/reek#configuration-file).
  This means that if you have an existing `Reek` configuration file, you can just put this into your
  project root and `RubyCritic` will respect this configuration.
* [`flay`](https://github.com/seattlerb/flay): We use `flay`'s default configuration.
* [`flog`](https://github.com/seattlerb/flog): We use `flog`'s default configuration with a couple of [smaller tweaks](https://github.com/whitesmith/rubycritic/blob/master/lib/rubycritic/analysers/helpers/flog.rb#L5):
    * `all`: Forces `flog` to report scores on all classes and methods. Without this option `flog` will only give results up to a certain threshold.
    * `continue`: Makes it so that `flog` does not abort when a ruby file cannot be parsed.
    * `methods`: Configures `flog` to skip code outside of methods. It prevents `flog` from reporting on the "methods" `private` and `protected`. It also prevents `flog` from reporting on Rails methods like `before_action` and `has_many`.

Alternative Usage Methods
-------------------------

If you're fond of Guard you might like [guard-rubycritic][4]. It automatically analyses your Ruby files as they are modified.

For continuous integration, you can give [Jenkins CI][5] a spin. With it, you can [easily build your own (poor-man's) Code Climate][6]!

Rake Task
---------

You can use RubyCritic as Rake command in its most simple form like this:

```ruby
require "rubycritic/rake_task"

Rubycritic::RakeTask.new
```

A more sophisticated Rake task that would make use of all available configuration options could look like this:

```ruby
RubyCritic::RakeTask.new do |task|
  # Name of RubyCritic task. Defaults to :rubycritic.
  task.name    = 'something_special'

  # Glob pattern to match source files. Defaults to FileList['.'].
  task.paths   = FileList['vendor/**/*.rb']

  # You can pass all the options here in that are shown by "rubycritic -h" except for
  # "-p / --path" since that is set separately. Defaults to ''.
  task.options = '--mode-ci --format json'

  # Defaults to false
  task.verbose = true
end
```

RubyCritic will try to open the generated report with a browser by default. If you don't want this
you can prevent this behaviour by setting the options correspondingly:

```ruby
RubyCritic::RakeTask.new do |task|
  task.options = '--no-browser'
end
```

Compatibility
---------------

RubyCritic is supporting:

* 2.1
* 2.2
* 2.3

Improving RubyCritic
--------------------

RubyCritic doesn't have to remain a second choice to other code quality analysis services. Together, we can improve it and continue to build on the great code metric tools that are available in the Ruby ecosystem.

Arguably, the [better_errors gem][7] only got popular after receiving a [(pretty awesome) Pull Request][8] that changed its page design.

Similarly, Pull Requests that improve the look and feel of the gem, that tweak the calculation of ratings or that fix existing issues will be most welcome. Just commenting on an issue and giving some insight into how something should work will be appreciated. No contribution is too small.

See RubyCritic's [contributing guidelines](CONTRIBUTING.md) about how to proceed.

Contributors / Core Team
---------------

`RubyCritics` initial author was [Guilherme Simões](https://github.com/guilhermesimoes).

The current core team consists of:

* [Nuno Silva](https://github.com/Onumis)
* [Lucas Mazza](https://github.com/lucasmazza)
* [Timo Rößner](https://github.com/troessner)

Credits
-------

![Whitesmith](http://i.imgur.com/Si2l3kd.png)

RubyCritic is maintained and funded by [Whitesmith][9]. Tweet your questions or suggestions to [@Whitesmithco][10].

[1]: https://github.com/troessner/reek
[2]: https://github.com/seattlerb/flay
[3]: https://github.com/seattlerb/flog
[4]: https://github.com/whitesmith/guard-rubycritic
[5]: http://jenkins-ci.org/
[6]: https://github.com/whitesmith/rubycritic/wiki/Building-your-own-Code-Climate
[7]: https://github.com/charliesome/better_errors
[8]: https://github.com/charliesome/better_errors/pull/22
[9]: http://www.whitesmith.co/
[10]: https://twitter.com/Whitesmithco
