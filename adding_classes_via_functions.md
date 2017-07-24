# Adding Classes:

There are a number ways of dealing with markup either adding it or removing it in Drupal 8. The best practice is to do so through templates and twig. This will take time to learn and transition over to, but will eliminate some of the modules we use to add classes around stuff. Less modules means lighter weight, less configuration and less updates (both function and security).

For the mean time, I've come up with 3 templates for us to be able to add to a project’s THEME_HOOK_preprocess_html(). These will add classes to the <body> tag based on: 1) a text field; 2) a taxonomy reference field; 3) a select list made by us.

These code snippets go into the THEMENAME.theme file. 

## Text field
To the content type a plain text field named: ‘class’ needs to be created. Then the string of classes to be added per node is here. Should a different field be created the code will be need an updated field_NAME.
```php
function THEMENAME_preprocess_html(&$variables) {
  if (!empty($node->field_class)) {
   $field = $node->field_class->value;
   $variables['attributes']['class'][] = $field;
  }
}
```
## Taxonomy Term
This one looks at a taxonomy term and adds that to the body classes. This example the content type has a field Entity Reference called School which pulls in the taxonomy terms from the vocabulary Schools. This chunk will need to be updated with the field_NAME that is chosen for this field on a site by site basis. 

The first line “Use Drupal\taxonomy\Entity\Term;” defines a namespace and goes at the top of the file inside the php tag. The rest of the code goes inside the preprocess_html().
```php
Use Drupal\taxonomy\Entity\Term;

function THEMENAME_preprocess_html(&$variables) {
  if (!empty($node->field_select_class)) {
    $class = Term::load($node->get('field_select_class')->target_id);
    $variables['attributes']['class'][] = $class->getName();
  }
}
```
## Select List
This is a text list field where we enter the values we want selected by the client when they make content. It allows for multiple select or we can configure it like the first example and only have one available. If we only want one class selected remove the loop. As with the others the field name needs to match.
```php
function THEMENAME_preprocess_html(&$variables) {
  if (!empty($node->field_select_class->value)){
    $class = $node->field_select_class;
    foreach ($class as $item) {
      $variables['attributes']['class'][] = $item->value;
    };
  }
}
```
