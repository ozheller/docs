# Convert Content Images into Background Images

Here is the code I crafted to make background images from the content images. You will need to paste the code into the files.

First create an image style at /admin/config/media/image-styles. Call it Background Image Style and for the machine name use **'bg_image_style'**. Next go to the display of the image field you wish to change and assign it the Background Image Style. With that in place you can now update your code.

Notice the machine name of the style appears in the preprocess_image. If you use a different machine name you will need to update this value. We can add additional image styles to both files for more specific needs, such as background images that are full width v.s. column width. We can add classes to each so they can be targeted via css. Another note, Height: must be defined or generated somehow because the div is empty and doesn't appear unless forced to.

In your THEMENAME.theme file:
```php
function THEMENAME_preprocess_image(&$variables) {
 if ($variables['style_name'] == 'bg_image_style') {
   $tmp = $variables['uri'];
   $variables['attributes']['bg_url'] = 'url(' . file_create_url($tmp) . ')';
 }
}
```
Then you will need to copy the image.html.twig from classy into theme/templates and paste this code over the existing <img>.

```twig
{% if attributes.bg_url %}

<div style="background-image: {{ attributes.bg_url }}" {{ attributes.addClass(classes).removeAttribute('src', 'width', 'height', 'alt', 'bg_url') }}></div>
{% else %}
   <img{{ attributes.addClass(classes).removeAttribute('bg_url') }}>

{% endif %}
```

​Here is a more robust image.html.twig template with multiple classes for 2 image styles:

```twig
{%
set classes = [
 style_name ? 'image-style-' ~ style_name|clean_class,
   'bg-fill',
   'eq-item',
   'eq-height',
   'eq-row',

]
%}

{% if (attributes.bg_url) and (style_name == 'hero') %}
   <h1>BG HAS VALUE</h1>
<div style="background-image: {{ attributes.bg_url }}"
       {{ attributes.addClass(classes)
       .removeClass('eq-row', 'eq-height', 'bg-fill')
       .removeAttribute('src', 'width', 'height', 'alt', 'bg_url') }}></div>
{% elseif (attributes.bg_url) and (style_name == 'bg_image_style') %}
   <h1>BG HAS VALUE 2</h1>
   <div style="background-image: {{ attributes.bg_url }}"
           {{ attributes.addClass(classes)
           .removeClass('eq-row', 'eq-height', 'eq-item')
           .removeAttribute('src', 'width', 'height', 'alt', 'bg_url') }}></div>
{% else %}
   <img{{ attributes.addClass(classes).removeClass('eq-item', 'eq-height', 'eq-row', 'bg-fill').removeAttribute('bg_url') }}>
{% endif %}
```

​Here we are adding a number of classes and removing them all for the default <img> and only keeping the one for the style we have implemented. In this instance for bg_image_style we are keeping bg-fill. For 'hero' we would keep eq-item class and remove the others. Also we would need to update the preprocessor_image() to accommodate other styles.

If you want to add the height and width of the image style to the images here is an example of extending the code to include those variables.
```twig
{% if attributes.bg_url %}     
    <div style="background-image: {{ attributes.bg_url }}; width:{{ attributes.width }}px; height:{{ attributes.height }}px" {{ attributes.addClass(classes).removeAttribute('src', 'width' , 'height', 'alt', 'bg_url') }}>
    </div>
 {% else %}     
    <img{{ attributes.addClass(classes).removeAttribute('bg_url') }}> 
{% endif %}
```

If you are using paragraphs and slick paragraphs sometime you want an image url to put on the slide as a background image. Here is the code to add to THEMENAME.theme file:

```php
/**
 * Implements theme_preprocess_slick_vanilla() to add background image handling.
 * @param $variables
 */

function THEMENAME_preprocess_slick_vanilla(&$variables) {
  $check = $variables['item']['#paragraph']->bundle();
  if ($check == "your_slide") {
    $bg_image = $variables['item']['#paragraph']->field_bg_image[0];
    if (isset($bg_image)) {
      $url = $bg_image->entity->url();
      $variables['attributes']['bg_url_slide'] = $url;
    }
  }
}
```

This will give you the url as an attribute, to be accessed in a paragraph.twig.php file, from a media entity image to be used as a background image. 

```php
/**
 * Implements template_preprocess_paragraph() to get and return url for
 * background images from media
 * @param $variables
 */

function THEMENAME_preprocess_paragraph(&$variables) {
  if (!empty($variables['elements']['field_bg_image'][0])) {
    $media = $variables['elements']['field_bg_image'][0]['#media'];
    if (isset($media)) {
      $url = $media->field_media_image[0]->entity->url();
      $variables['attributes']['paragraph_url'] = 'url(' . $url . ')';
    }
  }

}
```


