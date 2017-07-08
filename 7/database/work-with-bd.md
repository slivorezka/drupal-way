# Work with Database

In the [Database API](https://www.drupal.org/docs/7/api/database-api/database-api-overview "Database API") of Drupal we have the next variant to actions:

```php
// Select.
db_select('table', 't');
// Update.
db_update('table', 't');
// Delete.
db_delete('table', 't');
// Insert.
db_insert('table', 't');
// Merge it is combination of Insert action and Update action.
// You may use ut if you need Insert or Update some value.
db_merge('table');
// Static query.
db_query('SOME SQL WHERE value = :arg', [':arg' => 'value']);
```

## Examples:

### Get node title by node ID.

```php
$title = db_select('node', 'n')
  ->fields('n', array('title'))
  ->condition('n.nid', 123)
  ->execute()
  ->fetchField();
```

### Get all fields of a node by node ID.

```php
$fields = db_select('node', 'n')
  ->fields('n')
  ->condition('n.nid', 123)
  ->execute()
  ->fetchObject();
```

### Get "title" and "type" of all published nodes.

```
$nodes = db_select('node', 'n')
  ->fields('n', array('title', 'type'))
  ->condition('n.status', NODE_PUBLISHED)
  ->execute()
  ->fetchAll();
```



