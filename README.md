# Blog

Personal blog at https://blog.leoxlin.com

## Setup

- Install [asdf](https://asdf-vm.com/)
- Install libs for ruby
  ```
  sudo apt-get install libz-dev libssl-dev libffi-dev libyaml-dev
  ```
- Install ruby in asdf (.tools-version enforced)
  ```
  asdf install ruby
  ```
- Install bundler if you havent
  ```
  gem install bundler
  ```
- Install dependencies
  ```
  bundle install
  ```
- Running the site
  ```
  bundle exec jekyll serve
  open http://127.0.0.1:4000/
  ```

## Deploy 

This site is automatically deployed on https://render.com/
