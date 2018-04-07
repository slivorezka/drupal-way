# Get link

In Drupal 8 get some link not so easy how you would think.

```php
use Drupal\Core\Link;
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



