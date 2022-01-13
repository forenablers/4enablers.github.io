# Overview

For more details, see https://jekyllrb.com/docs/

## Getting started


* Install all [Prerequisites](https://jekyllrb.com/docs/installation/).
* Install the jekyll and bundler gems

```bash
gem install jekyll bundler
bundle add webrick
```

* Build the site and make it available on a local server.

```bash
bundle exec jekyll serve --incremental
```
	
* Browse to http://localhost:4000

## Install the theme

Search for the [Jekyll theme on RubyGems](https://rubygems.org/search?utf8=%E2%9C%93&query=jekyll-theme)

* Add the theme gem:

```bash
bundle add jekyll-theme-hacker
```

* Update _config.yml file to use the new theme:

```bash
theme: jekyll-theme-minimal
```

* Build and run your site

```bash
bundle install
bundle exec jekyll serve
```