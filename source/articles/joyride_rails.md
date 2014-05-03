---
title: Joyriding Rails
date: 2014-01-29
---

The latest incarnation of Zurb's Foundation, includes a neat feature called ["Joyride"](http://foundation.zurb.com/docs/components/joyride.html) which makes it easy to provide users with a tour of your interface:

![Joyride in Action](http:///dl.dropboxusercontent.com/u/10170977/joyride_example.png)

In order to configure each tooltip, Foundation uses `<ul>` or `<ol>` marked up with the `data-joyride` attribute:

```html
<!-- At the bottom of your page but inside of the body tag -->
<ol class="joyride-list" data-joyride>
  <li data-id="firstStop" data-text="Next" data-options="tip_location: top">
    <p>Hello and welcome to the Joyride documentation page.</p>
  </li>
  <li data-id="numero1" data-class="custom so-awesome" data-text="Next">
    <h4>Stop #1</h4>
    <p>You can control all the details for you tour stop. Any valid HTML will work inside of Joyride.</p>
  </li>
  <li data-id="numero2" data-button="Next" data-options="tip_location:top;tip_animation:fade">
    <h4>Stop #2</h4>
    <p>Get the details right by styling Joyride with a custom stylesheet!</p>
  </li>
  <li data-button="End">
    <h4>Stop #3</h4>
    <p>It works as a modal too!</p>
  </li>
</ol>
```

Adding all this code in a Rails project can make your views get messy pretty quickly, not to mention being a major PITA.

The following helper (HAML based) takes an array of hashes and renders out the neccesary HTML:

```ruby
def joyride_list(stops = [])
  return if stops.empty?
  haml_tag(:ol, class: "joyride-list", :"data-joyride" => true) do
    stops.each do |stop|
      options = {}
      if stop.has_key?(:options)
        options[:"data-options"] = stop[:options].map{|k,v| "#{k}:#{v};"}.join
      end
      %w(id class button text).each do |key|
        options[:"data-#{key}"] = stop[key.to_sym] if stop.has_key?(key.to_sym)
      end
      haml_tag(:li, options) do
        haml_tag(:h4, stop[:title]) if stop.has_key?(:title)
        haml_tag(:p, stop[:description]) if stop.has_key?(:description)
      end
    end
  end
end
```
We can then setup our array (e.g. in the controller):

```ruby
@stops = [
	{ id: "my_element_id", title: "My First Stop", description: "Something really interesting here..", options: { tip_location: "bottom" } }
	# ...
]
```

And render them using `joyride_list(@stops)`.

:)
 	  