# Ruby package management <!-- omit in toc -->

## Table of contents <!-- omit in toc -->

- [Installing Ruby](#installing-ruby)
- [Installing gems](#installing-gems)
  - [Installing gems with Bundler](#installing-gems-with-bundler)
  - [Caching Bundler gems with GitLab CI](#caching-bundler-gems-with-gitlab-ci)
  - [List installed gems](#list-installed-gems)
  - [Uninstalling old gem versions](#uninstalling-old-gem-versions)
  - [Cross-platform Gemfile](#cross-platform-gemfile)
  - [Fixing dependency errors](#fixing-dependency-errors)
  - [Update RubyGems manually](#update-rubygems-manually)
- [Jekyll](#jekyll)
  - [Local server](#local-server)
  - [Check if Jekyll installed properly](#check-if-jekyll-installed-properly)
  - [GitHub Pages](#github-pages)
- [References](#references)

## Installing Ruby

First, check if Ruby is already installed and its version:

```sh
ruby --version
```

Download and install Ruby using instructions from the [official website](https://www.ruby-lang.org/en/).

- Use [RubyInstaller](https://rubyinstaller.org/) on Windows
- Use apt on Debian/Ubuntu (*may not be the latest version*):

  ```sh
  sudo apt-get install ruby-full
  ```

## Installing gems

Check the RubyGems environment:

```sh
gem environment
```

On Linux, install RubyGems locally, not using `sudo`. Installing using `sudo` can interfere with the system-wide installation and configurations.

```sh
gem install --user-install <gemname>
```

Add the user install directory to the `PATH` environment variable. Alternatively, change the `GEM_HOME` and `GEM_PATH` variables so that gems can be installed without `sudo` or `--user-install`, by adding the following to `~/.bash_profile` (or equivalent file):

```sh
export GEM_HOME=$HOME/.gem
export GEM_PATH=$GEM_HOME
export PATH="$GEM_PATH/bin:$PATH"
```

### Installing gems with Bundler

To install gems from a Gemfile, install [Bundler](https://bundler.io):

```sh
gem install bundler
```

Bundler installs gems in the same path as `gem install`.

To globally set Bundler to install gems in a custom path:

```sh
bundle config set path <path>
```

Install gems using Bundler:

```sh
bundle install
```

### Caching Bundler gems with GitLab CI

```yml
job:
  image: ruby:latest
  variables:
    BUNDLE_PATH: .gem
  cache:
    key: bundler-cache
    paths:
      - $BUNDLE_PATH
  before_script:
    - bundle install
  script:
    # [...]
```

### List installed gems

To see the list of installed gems:

```sh
gem list
```

### Uninstalling old gem versions

To check if there are any older versions of gems installed:

```sh
gem cleanup --dryrun
```

To uninstall them, omit `--dryrun` from the command.

### Cross-platform Gemfile

If a project has both the Gemfile and Gemfile.lock checked in, and it is used cross-platform, and some dependencies only apply to a particular platform, specify them in the Gemfile like this...

```ruby
gem "execjs"
gem "therubyracer", :platforms => :ruby # only install in 'ruby' platform
```

Doing the above will ensure Gemfile.lock does not get regenerated every time.

The following is an example Gemfile for a Jekyll site, which requires two additional libraries on Windows:

```ruby
source "https://rubygems.org"

gem "minimal-mistakes-jekyll"

platforms :mswin, :mingw, :x64_mingw do
    gem "wdm", ">= 0.1.0"
    gem "tzinfo-data"
end
```

### Fixing dependency errors

The following dependency errors can occur if gems are installed using `sudo`:

```text
An error occurred while installing eventmachine (1.2.7), and Bundler cannot continue.
Make sure that `gem install eventmachine -v '1.2.7' --source 'https://rubygems.org/'` succeeds before
bundling.
```

```text
[...]
package configuration for openssl is not found
```

```text
*** extconf.rb failed ***
Could not create Makefile due to some reason, probably lack of
necessary libraries and/or headers.  Check the mkmf.log file for more
details.  You may need configuration options.
```

To fix this, either uninstall all `sudo` gems and reinstall them locally, or do the following:

```sh
sudo apt-get install ruby-full ruby-eventmachine
gem install bundler eventmachine
bundle install
```

### [Update RubyGems manually](https://github.com/jekyll/jekyll/issues/7463)

[Don't use this if Ruby is installed using a package manager](https://github.com/rubygems/rubygems/issues/3831), as it will handle updates automatically.

```sh
gem update --system
```

## Jekyll

### Local server

```sh
bundle exec jekyll serve
```

View the site at `http://localhost:4000/`.

### [Check if Jekyll installed properly](https://jekyllrb.com/docs/installation/windows/)

```sh
jekyll -v
```

- [Jekyll on Windows](https://jekyllrb.com/docs/installation/windows/)
- [Jekyll documentation](https://jekyllrb.com/docs/)

### [GitHub Pages](https://pages.github.com/)

- [GitHub Pages Basics](https://docs.github.com/en/pages/getting-started-with-github-pages)
- [Using Jekyll as a static site generator with GitHub Pages](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll)
- [Setting up your GitHub Pages site locally with Jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll)
- [Installation - Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/docs/installation/)
- [Jekyll Remote Theme](https://github.com/benbalter/jekyll-remote-theme)

## References

- <https://bundler.io/v2.2/man/bundle-install.1.html>
- <https://bundler.io/v2.2/man/bundle-config.1.html>
- <https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Ruby.gitlab-ci.yml>
- <https://www.heatware.net/ruby-rails/solved-installing-mysql-gem-extconf-rb-failed-error/>
- <https://github.com/tzinfo/tzinfo/wiki/Resolving-TZInfo::DataSourceNotFound-Errors>
- <https://bundler.io/v2.2/man/gemfile.5.html#PLATFORMS>
- [does using ":platforms =>" in your gemfile work?](https://stackoverflow.com/a/8460294/4573584)
- ['sudo gem install' or 'gem install' and gem locations](https://stackoverflow.com/q/2119064/4573584)
- [How to set Ruby GEM_HOME and GEM_PATH](https://unix.stackexchange.com/a/210012/287341)
