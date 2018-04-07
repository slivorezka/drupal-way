# Get entity by alias

```php
use Drupal\Core\Url;

// Some entity alias (node, taxonomy_term, user, etc).
$alias = '/about';

// Get URL obj.
$url = Url::fromUri('internal:' . $alias);

// Check exist alias.
if ($url->isRouted()) {
  $params = $url->getRouteParameters();
  $entity_type = key($params);
  // GEt entity.
  $entity = $this->entityTypeManager->getStorage($entity_type)->load($params[$entity_type]);  
  // Return like "node" or "taxonomy_term".
  dsm($entity->getEntityTypeId());
  // Return entity obj.
  dsm($entity);
}
```



