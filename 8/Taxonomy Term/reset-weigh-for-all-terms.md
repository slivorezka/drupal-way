# Reset weigh for all terms

```php
// Get Taxonomy Term service.
$manager = \Drupal::entityTypeManager()->getStorage('taxonomy_term');

// Get thee with full load on entity.
$tree = $manager->loadTree('VOC_NAME', 0, 0, TRUE);

// Set zero weigh for each term.
foreach ($tree as $term) {
  $term->setWeight(0);
  $term->save();
}
```



