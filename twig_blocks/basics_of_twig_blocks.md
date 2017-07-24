# Twig Blocks and the basics on how to use them.

Twig Blocks are chunks of code that twig identifies as being variable. These are independant of Drupal and different from Drupal Blocks. Once a twig block has been identified in your parent template then it can be overridden in a more specific template without having the full template duplicated with a small change, example html.html.twig overridden by html--node--article.html.twig. 

Some resources:
https://ffwagency.com/digital-strategies-blog/power-extending-twig-templates
http://running-on-drupal8.co.uk/blog/twig-blocks-drupal


This is very useful for dropping libraries into templates so they only load on the pages they are needed. On one site we have two events that get their own webfont but we only want to load the proper font on the proper page.

So in the html.html.twig file I added a twig block in the <head> tag. The twig block was empty but it’s now possible for it to be filled:

Html.html.twig:
```html
<head>
 <head-placeholder token="{{ placeholder_token|raw }}">
 <title>{{ head_title|safe_join(' | ') }}</title>
 <css-placeholder token="{{ placeholder_token|raw }}">
 <js-placeholder token="{{ placeholder_token|raw }}">

 {% block library %}
 {% endblock %}

 <link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
 <link rel="icon" type="image/png" href="/favicon-32x32.png" sizes="32x32">
 <link rel="icon" type="image/png" href="/favicon-16x16.png" sizes="16x16">
 <link rel="manifest" href="/manifest.json">
 <link rel="mask-icon" href="/safari-pinned-tab.svg" color="#787878">
 <meta name="theme-color" content="#ffffff">
</head>
```
Notice that block and endblock are special and needed, ‘library’ is just the identifier of that block. I could have named it ‘fonts’ or ‘type’ or ‘whatever’. I chose library because I may need to add specific js libraries or other libraries, not just fonts. Also, in theory you could place multiples of the same block into the template if you wanted to drop the same thing in multiple places.

Now I identified the nodes that I want to add the libraries to, Node 184 and 185, so the file names are html--node--184.html.twig and html--node--185.html.twig. Note the double hyphens between the elements.

Now that I have the two empty files, I need to add code to tell twig that it should use the twig block in these files instead of what’s in the parent file, html.html.twig. I do that by using extends. The syntax is: {% extends “parent_file_name.twig” %} in this case:
```twig
{% extends "html.html.twig" %}
```
Next I need to let twig know what blocks I’m changing:
```twig
{% block library %}
  {{ attach_library('mytheme/event-benefit') }}
{% endblock %}
```
From the code above you see the same beginning and end for the block that we identified in the parent file, html.html.twig. Then we put what we want to have that block be. Here we are using attach_library() to add our library. This overwrites what was there previously. So, if we had a font that was identified in the parent it would be replaced with our new fonts for those 2 pages.

This just scratches the surface of twig blocks. You are able to pull in the parent block with {{ parent }} to put markup around it. Example: you need to add your font library but you still needed to load the site font. You can do:
```twig
{% block library %}
  {{ parent }}
  {{ attach_library('mytheme/event-benefit') }}
{% endblock %}
```
This loads the parent library items and then pulls in the new library to the end. So you have the original and now your new library loading onto that specific page. There also is an include function, but I’ve not done anything with that yet.

Inside this directory are the files for example on twig blocks.
