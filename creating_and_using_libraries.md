# Creating js and css libraries and adding them to templates.

### Intro
In Drupal 8 the best practice to adding js and css to templates is to create a library and add that library in the template. This allows us to add libraries with granular control instead of loading them all globally. But this means that we need to start handling our files differently.

It’s important as a FED and site builder to know the various ways libraries are used because if something isn’t working properly or needs to be overridden (I want to use the newest flexsider library instead of the older one that came with the module because it has a neat new animation…). Even if they never implement it themselves it’s good to know how it works.

For Details See:

Details for Theme Specific:

https://www.drupal.org/docs/8/theming-drupal-8/adding-stylesheets-css-and-javascript-js-to-a-drupal-8-theme

Details for Module Specific:

https://www.drupal.org/docs/8/creating-custom-modules/adding-stylesheets-css-and-javascript-js-to-a-drupal-8-module

For More on external libraries:

https://www.drupal.org/node/2605130

And commentary on the Libraries module:

https://www.drupal.org/docs/8/modules/libraries-api-8x/using-libraries-api-3x-as-a-module-developer 

### Making and using a library:

Making a library is defining the list of files and dependencies to be loaded by calling it. A library can consist of any number of files (js and/or css) and dependencies, including external libraries. For our site, we had 2 pages that we needed to google fonts on but only those pages. So we created libraries to pull in the external resources and load them just on those pages.

We define our library inside our MYTHEME.libraries.yml.
First is the name of our library. Then the type of file and then where that file is located in relation to the mytheme.libraries.yml file. In this case we are pulling in an external library not storing it on the site. 
```yaml
event-benefit:
  css:
    theme:
      '//fonts.googleapis.com/css?family=Fjalla+One': {type: external, minitied: true}
```

Then we modify our template.twig in this case html--node--184.html.twig to attach the library inside the twig block which overrides the twig block in html.html.twig (read the Twig Blocks Doc for more info):

```twig
{% extends "html.html.twig" %}
 {% block library %}
   {{ attach_library('mytheme/event-benefit') }}
 {% endblock %}
```

After rebuilding the cache the new library is being attached only to node-184.
