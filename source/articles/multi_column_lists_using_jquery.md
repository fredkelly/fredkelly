---
title: Multi Column Lists using jQuery
date: 2010-07-07 
---

Recently, I’ve been working on a project that features a three column ordered list in its main content area. Previously I would have probably built this as three separate `<ol>` tags floated left.

This solution is far from ideal as it throws up all sorts of issues: numbering, positioning, scaling etc. I wanted to be able to achieve the same effect using a single list. After a couple of minutes of Googling I found [this on ALA](http://www.alistapart.com/articles/multicolumnlists/). The solution in this article looks something like this:

```html
<ol>
  <li class="column1">Item #1</li>
  <li class="column1">Item #2</li>
  <li class="column1">Item #3</li>
  <li class="column2 reset">Item #4</li>
  <li class="column2">Item #5</li>
  <li class="column2">Item #6</li>
  <li class="column3 reset">Item #7</li>
  <li class="column3">Item #8</li>
  <li class="column3">Item #9</li>
</ol>
```

This is combined with some dodgy CSS to produce the desired outcome. Not only is this pretty ugly, it’s also unscalable: if I want to add or remove items from the list I have to completely rewrite the CSS – not so good.

## Tidying up with some jQuery.

As a better alternative, I set about porting this same technique into a jQuery plugin. This way all the logic calculations (margins, widths etc.) can be calculated and applied automatically, allowing unlimited expansion.

[The code](http://github.com/fredkelly/multilists) is mega simple, it just loops through the list applying negative margins based on how many columns you want. For example:

```javascript
$(document).ready(function() {
  $('#mylist').multilists({ cols: 3 });
});
```

All done!
