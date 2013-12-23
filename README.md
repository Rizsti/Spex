# Spex 0.4.0

## About Spex

Spex is an asset and template management module for the [ProcessWire CMS/CMF](http://processwire.com/). 

This library was born out of a love of ProcessWire and the yucky feeling the use of a head.inc and foot.inc left in my mouth. The project makes use of [lessphp](http://leafo.net/lessphp/), [less.js](http://lesscss.org/), [jQuery](http://jquery.com/), [modernizr](http://modernizr.com/), [Minify](https://code.google.com/p/minify/) and the ProcessWire [Minify Module](http://modules.processwire.com/modules/minify/) and [MarupCache Module]http://modules.processwire.com/modules/markup-cache/.

* [Information about the author](http://metricmarketing.ca/jonathan-dart)
* [Information about Metric Marketing](http://metricmarketing.ca)
* [The author's blog](http://metricmarketing.ca/blog/author/jonathan-dart)
* [Learn more about ProcessWire](http://processwire.com)

## Installation

These steps assume you have ProcessWire installed.

* Install the [Minify Module](http://modules.processwire.com/modules/minify/)
* Install the [MarkupCache Module](http://modules.processwire.com/modules/markup-cache/)
* Clone this repo in your site's modules directory
* [Install the module](http://modules.processwire.com/install-uninstall/)

## File Struture

Spex expects certain files to exist in your templates directory. You can find some boilerplate code in the example-site directory.

* layouts (directory)
    + _base.php
    + one-column.php
    + ...
* partials (directory)
* _init.php

### layouts/_base.php

This file is responsible for outputting the html, head, body, link (css) and script (js) tags.

This file will have a variable `$layout_body` that has the output from the layout.

### layouts/one-column.php, layouts/two-column.php, etc...

Any file in the layouts directory that is not _base.php is considered a layout. If your site has one column and two column layout you will want to create one-column.php and two-column.php in your layouts directory.

This file will have a variable `$template_output` available that has the output from your template (aka page render).

### _init.php

This file is run before the page render, this is the place to set template variables, set a default layout, and add global css, less and javascript.

## Helpers

### addScript

This function adds a JavaScript file to the page. Spex assumes you want your script tags just before the `</body>` tag. You can add javascript files one at a time like the example code below.

`$spex->addScript('scripts/main.js');`

Or a bunch at once:

`$spex->addScript(array('scripts/main.js', 'scripts/another.js'));`

If the source files are outside of the template directory you'll need to pass a second parameter to addScript like the below:

`$spex->addScript('lib/main.js', $config->urls->siteModules.'Spex/');`

If you want to add scripts that are not on the same server as your PW site, the second parameter should be false:

`$spex->addScript('http://code.jquery.com/jquery-1.10.1.min.js', false);`

### addStyle

Spex can handle .less or .css files. To add one stylesheet to the page at a time, use the below:

`$spex->addStyle('styles/main.less');`

Or add a bunch at once:

`$spex->addStyle(array('styles/main.less', 'styles/home.less'));`

If the source files are outside of the template directory you'll need to pass a second parameter to addStyle like the below:

`$spex->addStyle('lib/main.less', $config->urls->siteModules.'Spex/');`

### addTemplateVar

The template management of Spex means setting a variable in your template doesn't mean it will be available globally. Rather than setting global variables use this function to make a variable available in templates:

`$spex->addTemplateVar('homepage', $pages->get('/'));`

### docReady

If you have some JavaScript in your template, you'll want to wrap it in a docReady() like below.

    <?php $spex->docReady() ?>
        <script>$('#bx-me').bxSlider();</script>
    <?php $spex->docReady() ?>

The JavaScript will be wrapped in a `$(document).ready(function(){...});` and found just before the `</body>` tag. 

docReady can also take a string parameter if you want to just pass it some javascript.

`docReady('console.log("hey there!")');`

### setLayout

To set a layout, call setLayout like the below:

`$spex->setLayout('one-column');`

That would result in Spex using the layout found at `templates/layouts/one-column.php` being used.

### partial

Partials contain reusable fragments of html like a sidebar or toolbar. The convention for partial files is that they are located in a subdirectory of templates called 'partials', i.e. `templates/partials/sidebar.php`. To call the partial sidebar.php you write the below:

`$spex->partial('sidebar');`

You can also pass an associative array to partial() to make additional variables available in the context of the partial, i.e.:

`$spex->partial('sidebar', array('root' => $pages->find('/xyz/')));`

Would make a variable `$root` available in sidebar.php.

Partials can also be cached which might be useful if they pull content from an RSS feed or something like that. 

`$spex->partial('rssFeed', array('url' => '...'), 3600);`

The above would cache the rssFeed for 3600 seconds. The caching is based on the name of the partial unless you pass a fourth parameter of true which would base the caching on the partial name and the URI of the current request.

`$spex->partial('rssFeed', array('url' => '...'), 3600, true);`

### slot / hasSlot

Slots are a way to store a string or value to be used later, perhaps to set a sidebar content or to indicate that a login button should not be shown.

`$spex->slot('sidebar_content', '<p>Sidebar</p>')`

To echo the contents of a slot:

`echo $spex->slot('sidebar_content')`

You can check for the existance of a slot with `hasSlot`:

`if (hasSlot('sidebar_content'))`

There is also a variable $slot available should you prefer to use it directly.

`echo empty($slot['sidebar_content']) ? '' : $slot['sidebar_content'];`

### captureSlot

You can use the captureSlot helper to capture some output, so you don't have to build a string and pass it to slot().

    $spex->captureSlot(); // Starts the capture ?>

    <p>Sidebar</p>

    <?php $spex->captureSlot('sidebar_content'); // Finishes the capture

### addImage

This helper is used to preload an image, it also happens to return whatever is passed to it. 

`$spex->addImage('big-background.png')`

or 

`<div style="background-image: url(<?php echo $spex->addImage('big-background.png') ?>);"></div>`

### includeStyles

This draws out all your `<link>` tags, and is usually only found in the _base layout.

`$spex->includeStyles();`

### includeScripts

This draws out all your `<script>` tags, and is usually only found in the _base layout.

`$spex->includeScripts();`

### includeHeadScripts

This draws any `<script>` tags that should appear in the document head, and is usually only found in the _base layout.

`$spex->includeHeadScripts();`

### includeDocReady

This draws out any JavaScript captured by docRead(), and is usually only found in the _base layout.

`$spex->includeDocReady();`

### lateLoad

This captures html and sets it up to be the last thing loaded on the page. You'd want to use this for third party code like social sharing code or ads.

    lateLoad(); ?>

        <div class="addthis_toolbox addthis_default_style"></div>
        <script type="text/javascript" src="//s7.addthis.com/js/300/addthis_widget.js#pubid=xa-52321d124fb4535d"></script>

    <?php lateLoad();

## Procedural Helpers

Most of the class methods of the Spex class have procedural equivalents. To enable the procedural helpers, in your `templates/_init.php` set:

`$spex->enableProceduralHelpers();`

All the helper functions have the same signature as their equivalents documented above, i.e:

`addScript('scripts/foo.js');`

is equivalent to the below:

`$spex->addScript('scripts/foo.js');`

The list of procedural helpers are below: 

* addScript
* docReady
* addStyle
* includeStyles
* addTemplateVar
* partial
* setLayout
* includeScripts
* includeHeadScripts
* includeDocReady
* addImage
* slot
* hasSlot
* captureSlot
* lateLoad
