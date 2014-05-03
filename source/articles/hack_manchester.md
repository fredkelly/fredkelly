---
title: Hack Manchester 2012
date: 2012-10-28 
---
[Hack Manchester](http://hackmanchester.com) is a 24 hour coding event held at the [Museum of Science & Industry (MOSI)](http://www.mosi.org.uk) in the heart of Manchester.

![Getting setup at Hack Manchester](https://dl.dropbox.com/u/10170977/IMG_0936.jpg)

The event gave myself and [@dannyprout](http://twitter.com/dannyprout) the perfect opportunity to develop an idea we'd been batting around for a while. The plan was to create an app that would hook into GitHub and allow users to get valuable peer reviews on their coding style etc. The idea sprouted from seeing how Facebook's own [Phabricator](http://phabricator.org) project allows developers to 'ok' each other's code before pushing live.

We build the app using a basic Rails/PostgreSQL backend. I mainly worked on setting up the oauth connection with Github and building out the design and basic functionality. Meanwhile Dan concentrated on the mamouth task of parsing code into 'Snippets' (individual functions/methods) which would be easily digestible for reivewing.

Dispite hitting a serious sleep deprivation wall around 5am, we managed to build a pretty decent working prototype:

![Brogram, the early prototype](https://dl.dropbox.com/u/10170977/brogram.png)

## Introducing Brogram!

So I've had the domain <http://brogr.am/> for a while since finding [this hilarious Quora article](http://www.quora.com/Brogramming/How-does-a-programmer-become-a-brogrammer) and kinda becoming a self-proclaimed Brogrammer.

As you can see the UI is dead simple, you login via GitHub, pick from a list of you repositories and hit *submit*. From there, our application parses each of the files contained within your repo and creates 'Snippets' each containing independent logical blocks (well, that's the idea). From there users can review each of these codes earning themselves credit, which they can then in turn use to get their code reviewed. The end product gives you an overview of your project and which areas need most attention etc.

## Future expansion

Although we managed to get a basic working version running. We still have a ton to do:

- Push everything live to Heroku
- Improve the parsing, add other common languages
- ML elements: learn which users give the best reviews for language X, i.e. work who has pushed most code in C or Java, and rate their reviews for those languages accordingly.
- Better sharing of snippets, short urls etc.
- More review options, per-line commenting, flags, highlighting etc.
- …

## Wrap up…
Overall I'm pretty pleased with what we managed to achieve (in about 12 hours - Dan tapped out around 5am!). I think the idea has quite a bit of scope and would definitely like to take it a bit further and implement a couple of the ideas above.

**Happy Hacking :)**
