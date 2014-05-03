---
title: Hacking the Nike+ FuelBand API
date: 2012-12-21 
---

This Christmas I was lucky enough to be given a [Nike+ FuelBand](http://www.nike.com/gb/en_gb/lp/nikeplus-fuelband) by [Alice](http://twitter.com/alicebettany).

![My shiney Nike+ FuelBand](https://dl.dropbox.com/u/10170977/fuel_band.jpg)

If you're not familiar with the FuelBand, the basic idea is to track your movement throughout the day, and collect 'Fuel' (Nike's own internal currency). You can compete with your friends to see who can collect the most etc (points can also be collected via other means such as the Nike+ running app for iOS).

![Nike+ Dashboard](https://dl.dropbox.com/u/10170977/nike_plus.png)

## Hacking it!

So having played with the FuelBand for a few hours, I stumbled across this:

<iframe width="500" height="281" src="http://www.youtube.com/embed/YPfBxBs5Zr0?rel=0" frameborder="0" allowfullscreen></iframe>

Based on the findings of Tom Pohl in this video I was able to put together some simple Ruby code (with the aid of [HTTParty](https://github.com/jnunemaker/httparty)):

<script src="https://gist.github.com/d374d9258b0ea501234e.js"></script>

I've also posted the required accompanying files in [this repository](https://github.com/fredkelly/fuel) (also to store any future development).

To get it working you'll need a `config.yml` file with the following:

<script src="https://gist.github.com/08640721d0cf9ccd9546.js"></script>

In order to get hold of these values you'll need to sniff the traffic between the FuelBand app and `api.nike.com`. To do this I used an intercepting Proxy on my Mac (try [Burp Suite](http://www.portswigger.net/burp/)).

Once you've got everything in place you should be good to go. You can pull out **ALL THE DATA :)** as well as some nice summary stuff:

![I can haz data](https://dl.dropbox.com/u/10170977/fuel_stats.png)

As this site currently runs on PHP, I also whipped up the same code in PHP using cURL:

<script src="https://gist.github.com/e425e48f36f34f39c89a.js"></script>

Please feel free to [fork](https://github.com/fredkelly/fuel) and work on this, there are a ton of different method calls that I'm yet to include (see the video ^^).
