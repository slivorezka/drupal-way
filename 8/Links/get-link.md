# Get link

```php
use Drupal\Core\Url;

// Get node edit link as render array.
$link = Link::fromTextAndUrl(t('Edit'), Url::fromRoute('entity.node.edit_form', [
  'node' => 123,
]))->toRenderable();

// Get node edit link as string.
$link = Link::fromTextAndUrl(t('Edit'), Url::fromRoute('entity.node.edit_form', [
  'node' => 123,
]))->toString();
```



