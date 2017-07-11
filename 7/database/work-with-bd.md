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
// You may use it if you need Insert or Update some value.
db_merge('table');
// Static query.
db_query('SOME SQL WHERE value = :arg', [':arg' => 'value']);
// Static query with range.
db_query_range('SOME SQL WHERE value = :arg', [':arg' => 'value'], 0, 10);
```

## Examples:

### Get node title by node ID

```php
$title = db_select('node', 'n')
  ->fields('n', array('title'))
  ->condition('n.nid', 123)
  ->execute()
  ->fetchField();
```

### Get all fields of a node by node ID

```php
$fields = db_select('node', 'n')
  ->fields('n')
  ->condition('n.nid', 123)
  ->execute()
  ->fetchObject();
```

### Get "title" and "type" of all published nodes

```php
$nodes = db_select('node', 'n')
  ->fields('n', array('title', 'type'))
  ->condition('n.status', NODE_PUBLISHED)
  ->execute()
  ->fetchAll();
```

### Get "title" of all published nodes and sort by date

```php
$nodes = db_select('node', 'n')
  ->fields('n', array('title'))
  ->condition('n.status', NODE_PUBLISHED)
  ->orderBy('n.created', 'DESC')
  ->execute()
  ->fetchAll();
```

### Get a couple fields of all published nodes and sort by date plus group by "uid".

```php
$nodes = db_select('node', 'n')
  ->fields('n', array('nid', 'title', 'status', 'created'))
  ->condition('n.status', NODE_PUBLISHED)
  ->orderBy('n.created', 'DESC')
  ->groupBy('n.uid');
  ->execute()
  ->fetchAll();
```

### Get only 10 node titles

```php
$nodes = db_select('node', 'n')
  ->fields('n', array('title',))
  ->range(0, 10)
  ->execute()
  ->fetchAll();
```

### Use "JOIN"

```php
$query = db_select('node', 'n');
$query->Join('users', 'u', 'n.uid = u.uid');
// Also, you can use "innerJoin", "leftJoin" or "rightJoin".
// $query->innerJoin('users', 'u', 'n.uid = u.uid');
// $query->leftJoin('users', 'u', 'n.uid = u.uid');
// $query->rightJoin('users', 'u', 'n.uid = u.uid');
$query->fields('u', array('name'));
$query->fields('n', array('title'));
$query->condition('u.mail', 'admin@gmail.com');
$result = $query->execute()->fetchAll();
```

### Use "OR"

```php
$nodes = db_select('node', 'n')
  ->fields('n')
  ->condition(
    db_or()
      ->condition('n.title', 'Some node title')
      ->condition('n.nid', 123)
  )
  ->execute()
  ->fetchAll();
```

### Use "IN"

```php
$nodes = db_select('node', 'n')
  ->fields('n')
  ->condition('n.nid', array(123, 456, 789), 'IN')
  ->execute()
  ->fetchAll();
```

### Use "LIKE"

```php
$nodes = db_select('node', 'n')
  ->fields('n')
  ->condition('n.title', '%' . db_like('Some node title') . '%', 'LIKE')
  ->execute()
  ->fetchAll();
```

### Use "BETWEEN"

```php
$nodes = db_select('node', 'n')
  ->fields('n')
  ->condition('n.nid', array(123, 789), 'BETWEEN')
  ->execute()
  ->fetchAll();
```

### Use "NULL"

```php
$data = db_select('table', 't')
  ->fields('t')
  ->isNull('t.field')
  // ->isNotNull('t.field')
  ->execute()
  ->fetchAll();
```

### Use "DISTINCT"

```php
$data = db_select('table', 't')
  ->distinct()
  ->fields('t', array('field'))
  ->execute()
  ->fetchAll();
```

### Use "RANDOM"

```php
$nodes = db_select('node', 'n')
  ->fields('n', array('nid', 'title'))
  ->orderRandom();
  ->execute()
  ->fetchAll();
```

### Use "WHERE"

```php
$nodes = db_select('node', 'n');
$nodes->fields('n', array('nid', 'title', 'status'));
$nodes->condition('n.status', NODE_PUBLISHED);
$nodes->where('YEAR(FROM_UNIXTIME(n.created)) = :created', array(':created' => 2012));
$result = $year->execute()->fetchAll();
```

### Use "TRUNCATE" \(cleaning table\)

```php
db_truncate('table')->execute();
```

### Use "TRANSACTION"

```php
$transaction = db_transaction();

try {
  // Some important SQL query or actions.
}
catch (Exception $e) {
  $transaction->rollback();
  throw $e;
}
```

### Use "NOT EXISTS"

Select all node ID with empty "body" field.

```php
$nodes = db_select('node', 'n')
  ->fields('n', array('nid'))
  ->notExists(
    db_select('field_data_body', 'b')
      ->fields('b', array('entity_id'))
      ->where('b.entity_id = n.nid')
   )
   ->execute()
   ->fetchAll();
```

### Use the same field name on couple tables

Sometime you need do a select with join and both tables can have field like "title". Well Drupal will set separate name for these double fields like "title" and "title\_1". But you can set any name for these fields.

```php
$query = db_select('node', 'n');
$query->Join('table', 't', 'n.uid = t.uid');
$query->fields('n', array('title'));
$query->fields('t', array('title'));
$query->addField('n', 'title', 'node_title');
$query->addField('t', 'title', 'table_title');
$result = $query->execute()->fetchAll();
```

### Use date format in the Query

```php
$query = db_select('node', 'n');
$query->fields('n', array('created'));
$query->addExpression("FROM_UNIXTIME(n.created, '%Y/%m/%d')", 'new_date_format');
$date = $date->execute()->fetchAll();
```

### Count the number of records in the table

```php
$count = db_select('node', 'n')
  ->countQuery()
  ->execute()
  ->fetchField();
```

### Get max value of the field

```php
$query = db_select('node');
$query->addExpression('MAX(nid)');
$max = $query->execute()->fetchField();
```

### Get associative array where key "nid" and value "title"

```php
$nodes = db_select('node', 'n')
  ->fields('n', array('nid', 'title'))
  ->execute()
  ->fetchAllKeyed();
```

### Get two-dimensional associative array

```php
$nodes = db_select('node', 'n')
  ->fields('n', array('nid', 'title'))
  ->execute()
  ->fetchAllAssoc('nid');
```

### Update record

```php
db_update('node')
  ->fields(array('status' => NODE_PUBLISHED))
  ->condition('nid', 123)
  ->execute();
```

### Delete record

```php
db_delete('node')
  ->condition('nid', 123)
  ->execute();
```

### Insert record

```php
db_insert('table')
  ->fields(array('value' => 123))
  ->execute();
```

### Merge record

```php
db_merge('table')
  ->key(array('id' => 123))
  ->insertFields(array(
    'field' => 'some_value',
  ))
  ->updateFields(array(
    'field' => 'some_value',
  ))
  ->execute();
```

### Check table

```php
if (db_table_exists('table')) {
  // Actions ...
}
```

### Static query

```php
$title = db_query('SELECT title FROM {node} WHERE nid = :nid', array(':nid' => 123))->fetchField();
```



