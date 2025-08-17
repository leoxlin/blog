# Blog

Personal blog at https://blog.leoxlin.com

## Requirements

- Install [asdf](https://asdf-vm.com/)
- Install libs for ruby
  ```
  sudo apt-get install libz-dev libssl-dev libffi-dev libyaml-dev
  ```
- Install ruby in asdf (.tools-version enforced)
  ```
  asdf install ruby
  ```
## Setup

```
gem install bundler
bundle install
bundle exec jekyll serve
```

## Running the site

```
bundle exec jekyll serve
open http://127.0.0.1:4000/
```
