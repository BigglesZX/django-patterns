# Enabling utf8mb4 Support in MySQL

In order to store emojis in MySQL ≥5.5 you need to enable the `utf8mb4` character set for any individual fields that need to support emoji characters. This guide assumes you're changing a single field, but the process is the same for multiple fields – just add more `RunSQL` steps in the migration.

No model changes are necessary, but after creating your field you will need to create a manual migration to run the necessary SQL commands to change the charset. You will need to know 1) the name of the table containing the field you want to change, 2) the name of the field, 3) the current type of the field, e.g. `VARCHAR(1000)`.

```shell
$ django-admin makemigrations --empty --name switch_to_utf8mb4_columns yourappname
```

You can name the migration anything you like.

Open the newly created migration file and edit it so that the `operations` section looks like the following – be sure to substitute the name of your table, the name of the field and the field type:

```python
operations = [
    migrations.RunSQL(
        sql=['ALTER TABLE yourappname_modelname MODIFY fieldname VARCHAR(1000) '
             'CHARSET utf8mb4 COLLATE utf8mb4_unicode_ci'],
        reverse_sql=['ALTER TABLE yourappname_modelname MODIFY fieldname '
                     'VARCHAR(1000)']
    ),
    migrations.RunSQL(
        sql=['ALTER TABLE django_admin_log MODIFY object_repr VARCHAR(200)'
             ' CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL'],
        reverse_sql=['ALTER TABLE django_admin_log MODIFY object_repr '
                     'VARCHAR(200) NOT NULL']
    )
]
```

Note that the second `RunSQL` statement above will modify the Django admin log table so that any log entries that feature the emoji-enabled field will also be able to contain emojis. Otherwise your admin log might break.

You'll then need to edit your database config so that Django uses the right charset when communicating with the database. Edit your `DATABASES` setting so that the `OPTIONS` section looks something like this (your `init_command` may differ):

```python
'OPTIONS': {
    'init_command': "SET sql_mode='STRICT_TRANS_TABLES';"
                    "SET storage_engine=MYISAM",
    'charset': 'utf8mb4',
},
```

Finally, migrate:

```shell
$ django-admin migrate yourappname
```

### Sources

* https://exana.io/community/a-helping-hand/2016/08/23/django-utf8mb4-selected-columns.html
* https://mathiasbynens.be/notes/mysql-utf8mb4
