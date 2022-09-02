# View Components

## Gemfile

```
gem "view_component"
```

## Stimulus js

I used this:
https://stackoverflow.com/questions/73223634/how-to-make-viewcomponent-works-with-importmap-rails/73228193#73228193

Keep an eye on this for an official solution:
https://github.com/ViewComponent/view_component/issues/1064

Add to the bottom of the `config.importmap.rb`

```ruby
components_path = Rails.root.join("app/components")
components_path.glob("**/*_controller.js").each do |controller|
  name = controller.relative_path_from(components_path).to_s.remove(/\.js$/)
  pin "components/#{name}", to: name
  #    ^                        ^
  #    |                        |
  # match what                  '- with what `sprockets` expects
  # `eagerLoadControllersFrom`
  # expects
end
```

In `app/javascript/controllers/index.js`

```js
//...
eagerLoadControllersFrom("controllers", application);
// add this additional line
eagerLoadControllersFrom("components", application);
//...
```

In `config/initializers/assets.rb`

```ruby
Rails.application.config.assets.paths << Rails.root.join("app/components")
```

In `app/assets/config/manifest.js`

```ruby
//= link_tree ../../components .js
```

This enables the creation of files such as app/components/widget_controller.js, where the controller identifier matches the data-controller attribute in the component’s HTML template.

After configuring Webpack to load Stimulus controller files from the components directory, add the path to additional_paths in config/webpacker.yml:

additional_paths: ["app/components"]

## Example component generator

```
bin/rails generate component Example title
      invoke  test_unit
      create  test/components/example_component_test.rb
      create  app/components/example_component.rb
      create  app/components/example_component.html.erb
```

## Generating namespaced components

```
bin/rails generate component Sections::Example title content

      create  app/components/sections/example_component.rb
      invoke  test_unit
      create    test/components/sections/example_component_test.rb
      invoke  erb
      create    app/components/sections/example_component.html.erb
```

The content passed to a block is accessed by the `content` accessor in the view.
String content can also be passed to a ViewComponent by calling #with_content:

```ruby
<%= render(ExampleComponent.new(title: "my title").with_content("Hello, World!")) %>
```

## Rendering from controllers

It’s also possible to render ViewComponents in controllers:

## app/controllers/home_controller.rb

```ruby
def show
  render(ExampleComponent.new(title: "My Title"))
end
```

When using turbo frames with turbo-rails, set content_type as text/html:

## app/controllers/home_controller.rb

```ruby
def create
  render(ExampleComponent.new, content_type: "text/html")
end
```

## Rendering ViewComponents to strings inside controller actions

When rendering the same component multiple times for later reuse, use render_in:

```ruby
class PagesController < ApplicationController
  def index
    # Doesn't work: triggers a `AbstractController::DoubleRenderError`
    # @reusable_icon = render IconComponent.new('close')

    # Doesn't work: renders the whole index view as a string
    # @reusable_icon = render_to_string IconComponent.new('close')

    # Works: renders the component as a string
    @reusable_icon = IconComponent.new('close').render_in(view_context)
  end
```
