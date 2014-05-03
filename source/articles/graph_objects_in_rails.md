---
title: Easy Open Graph Objects in Rails
date: 2012-08-24 
---

So I made a [Gem](https://rubygems.org/gems/acts_as_graph_object). Recently I've been working on a couple of Rails apps in which I have needed to add [Open Graph](https://developers.facebook.com/docs/opengraph/) meta tags in order to map objects within my app onto Facebook's social graph. This is done by simply adding `<meta>` tags to your document defining specific properties such as `title`, `image`, `date` etc. Here is an example taken from Facebook's [tutorial](https://developers.facebook.com/docs/opengraph/tutorial/):

```erb
<meta property="fb:app_id" content="[YOUR_APP_ID]" /> 
<meta property="og:type" content="[YOUR_APP_NAMESPACE]:recipe" /> 
<meta property="og:title" content="Stuffed Cookies" /> 
<meta property="og:image" content="http://fbwerks.com:8000/zhen/cookie.jpg" /> 
<meta property="og:description" content="The Turducken of Cookies" /> 
<meta property="og:url" content="http://fbwerks.com:8000/zhen/cookie.html">
```

This is quite a simple example, there are many more options for custom properties and object types, each requiring specific property names, some in the `og` and `fb` namespaces, some in your app's custom namespace. There are also different data types: strings, integers, arrays etc. I played with a few Gems including [meta-tags](https://rubygems.org/gems/meta-tags) and [acts_as_opengraph](https://rubygems.org/gems/acts_as_opengraph), both offer useful helpers to make the job of adding these meta tags easier. After trying different combinations I was still findinig it hard to find a tidy way to map attributes straight from my models to meta tags in my RESTful views.

## Enter acts\_as\_graph\_object
My first venture into the Gem world, the gem is intended to allow such model to view mappings to be super easy.

I started the project with the following goals:

1\. Find a way to provide model URLs using the url_for view/controller helpers.

2\. Map common attribute names to `og` meta tags (e.g. title, description, image etc).

3\. Provide helpers that allow resulting meta tags to be easily added to global layout in a standar Rails REST architecture.
  i.e. for all /show actions, add meta tags to head if object has open graph attributes.

4\. Allow for easy configuration for constants such as `fb:app_id` & `fb:admins` as well as an *app namespace* to be used in `og:type` and any custom attributes.

5\. Automatically handle arrays, i.e. `:cast => ['Tom Cruise', 'Kelly McGillis', 'Val Kilmer']` becomes:

```erb
<meta property="my-app:cast" content="Tom Cruise" />
<meta property="my-app:cast" content="Kelly McGillis" />
<meta property="my-app:cast" content="Val Kilmer" />
```

6\. Keep it unobtrusive! no heavy configuration in models, something simple, e.g.

```ruby
class Movie < ActiveRecord::Base
  acts_as_graph_object :custom => [:director, :writer, :cast]
end
```

This would map all standard properties `title`, `description`, `image`, `app_id` etc along with the custom properties `director`, `writer` & `cast`.

7\. **Write proper tests and documentation!**

## Putting it to use..
So I recently released **v0.0.9** and it's starting to look pretty much there (minus that last point :O).. Here's how to add it to your Rails app:

```bash
gem install acts_as_graph_object
```

Then just add the dependancy to your `Gemfile`.

## Usage

### Configuration

```ruby
# app/config/initializers/acts_as_graph_object.rb
ActsAsGraphObject.configure do |config|
  config.namespace = 'my-app'
  config.app_id = 12345
  config.admins = [1245, 6789]
end
```

#### Default URL Method
In order to use the built in `@model.url` method you need to set the following config option:

```ruby
# app/config/environments/production.rb
routes.default_url_options[:host] = 'my-app.com'
```

#### Add acts\_as\_graph\_object...

```ruby
# app/models/movie.rb
class Movie < ActiveRecord::Base
  acts_as_graph_object :custom => [:director, :writer, :cast]

  def cast
    ...
  end
end
```

#### Outputting meta tags

Use the `graph_object_tags_for(@movie)` helper to output the resulting `<meta>` tags. You can use this in combination with `content_for` to push the results into your `<head>`:

```erb
# app/views/layouts/application.html.erb    
<head>
    <%= yield :meta_tags %>
</head>

# app/views/movies/show.html.erb
<% content_for :meta_tags, graph_object_tags_for(@movie) %>
```

#### Overriding from view
If you want to override a value from the view (for example to use a `url_for` helper):

```erb
# app/views/movies/show.html.erb
<% content_for :meta_tags, graph_object_tags_for(@movie, :url => movie_url(@movie)) %>
```

#### Alternative attribute names (v0.0.7)
You can now use non-default names for the standard Open Graph properties and the Gem will attempt to pick these up. Here are the properties and their alternative names:

```ruby
# standard object properties & alternative names
default_properties = {
  :title           => [:name, :label],
  :type            => [:kind, :group, :class],
  :image           => [:picture, :photo],
  :url             => [:permalink, :link],
  :description     => [:info, :details],
  :site_name       => [:site],
  :latitude        => [:lat],
  :longitude       => [:long],
  :street_address  => [:address],
  :locality        => [:locale, :area],
  :region          => [:province, :territory],
  :postal_code     => [:post_code, :zip_code, :zip],
  :country_name    => [:country],
  :email           => [:email_address],
  :phone_number    => [:phone],
  :fax_number      => [:fax]
}
```
  
**And there you have it!** Check out the code on GitHub, fix/fork/improve! :)

<iframe src="http://ghbtns.com/github-btn.html?user=fredkelly&repo=acts_as_graph_object&type=watch&count=true" allowtransparency="true" frameborder="0" scrolling="0" width="62px" height="20px"></iframe>
<iframe src="http://ghbtns.com/github-btn.html?user=fredkelly&repo=acts_as_graph_object&type=fork&count=true" allowtransparency="true" frameborder="0" scrolling="0" width="62px" height="20px"></iframe>
