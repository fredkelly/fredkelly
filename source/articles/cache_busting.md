---
title: Super Simple Cache Busting
date: 2010-11-02 
---

If you’re not familiar with **cache busting** it is basically the idea of preventing browsers from caching your site’s CSS/JS. This is normally achieved by assigning a meaningless query string to your `<link>` tags:

```html
<link rel="stylesheet" type="text/css" href="mystyle.css?v=12345" />
```

By adding a random string, i.e. `12345` the browser is tricked into downloading a fresh copy of the file each time the page loads instead caching it.

Whilst this works, it means that the file is downloaded EVERY single time the user visits the site, removing any possible performance gained from caching.

So, instead of re-downloading the file every time the page loads why not just load it every time the file is modified? This PHP snippet lets you do just that:

```php
<?php
  /*
   * Simple helper for linking to CSS/JS
   * Uses the files last modified date
   * to create a 'cache-busting' URL.
   */
    function link_file($path)
    {
        if (file_exists($path))
        {
            // get the file's timestamp
            $time = filemtime($path);
            // return a 'cache-busting' URL
            return $path . '?v=' . $time;
        }
    }
?>
```

Using the [filemtime()](http://php.net/manual/en/function.filemtime.php) function, this code grabs the UNIX timestamp of the file’s last modified time, and appends it as a cache-busting query-string. This can then be put to use using something like this:

```html
<link rel="stylesheet" type="text/css" href="<?=link_file('mystyle.css')?>" />
```

**No more wasted downloading!**
