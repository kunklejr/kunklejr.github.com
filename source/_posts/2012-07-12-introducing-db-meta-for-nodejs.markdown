---
layout: post
title: Introducing db-meta for NodeJS
comments: true
tags: nodejs
---
If you're using NodeJS and are in need of schema metadata from your relational database, you're in luck. [db-meta](https://github.com/nearinfinity/node-db-meta) provides an API to retrieve basic schema metadata from MySQL, PostgreSQL, and SQLite databases. 

``` js
var dbmeta = require('db-meta');

dbmeta('pg', { database: 'test' }, function(meta) {
  meta.getVersion(function(err, version) {
    console.log('postgres version is ' + version);
  });

  meta.getTables(function(tables) {
    tables.forEach(function(table) {
      console.log('table name is ' + table.getName());
    });
  });

  meta.getColumns('tablename', function(columns) {
    columns.forEach(function(column) {
      console.log('column name is ' + column.getName());
      console.log('column data type is ' + column.getDataType());
      console.log('column is nullable? ' + column.isNullable());
      console.log('column max length is ' + column.getMaxLength());
    });
  });
});
```

If you're interested in learning more, check out the [db-meta Github page](https://github.com/nearinfinity/node-db-meta) for additional information and API documentation.
