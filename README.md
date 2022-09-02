# The is a demo app for directions and examples of setting up a rails app

## Getting Started

In terminal

```
rails new MyStack -d postgresql -css tailwind
cd MyStack
```

Make sure you're on the latest ruby version and set the .ruby-version file for rbenv.

in Gemfile

```ruby
group :development do
  gem "rubocop"
  gem "rubocop-minitest"
  gem "rubocop-rails"
  gem "foreman"
  gem "htmlbeautifier"
end
```

In terminal

```
bundle
```

Add .rubocop.yml

```
require:
  - rubocop-minitest
  - rubocop-rails
AllCops:
  TargetRubyVersion: 3.0
  NewCops: enable
Layout/LineLength:
  Max: 120
Style/Documentation:
  Enabled: false
Metrics/AbcSize:
  Max: 20
Metrics/MethodLength:
  Max: 16
Lint/MissingSuper:
  Enabled: false
Style/ClassAndModuleChildren:
  Enabled: false
```

Create a root page

```
rails g controller Pages index
```

Set the root route in routes.rb

```ruby
  root "pages#index"
```

Create initial commit and push to github

Then follow each sub-guide

- View Components
- Authentication
