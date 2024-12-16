Transform DB Table to Enterprise Table
==================

To create your table on enterprise we do recommend to create a package called `db_models`.
For other db fields that we didn't use here you can look to Fields.

The db table name, file name and function name should match. Otherwise enterprise can't recognize your file.
You can create a field by using functions under ``github.com/MrSametBurgazoglu/enterprise/models``

Every field has it own functions. Just like the ``DefaultFunc``. You can set a default value for your field.

``models.Table`` represents a db table. You can add your fields to ``Fields`` variable. To make a primary key use ``SetIDField`` field on table.

An example db table ``your/project/db_models/account.go``

.. code-block:: golang

    package db_models

    import (
	    "github.com/MrSametBurgazoglu/enterprise/models"
	    "github.com/google/uuid"
    )

    func Account() *models.Table {
        idField := models.UUIDField("ID").DefaultFunc(uuid.New)

        tb := &models.Table{
            Fields: []models.FieldI{
                idField,
                models.StringField("Name"),
                models.StringField("Surname"),
                models.UUIDField("DenemeID").SetNillable(),
                models.UintField("Serial").AddSerial(),
            },
        }

        tb.SetTableName(AccountName)
        tb.SetIDField(idField)

        return tb
    }

An example auto-generator table ``your/project/generate/generate.go``
When this executed it will create a new package in current directory called models and put your generated db models in it.

.. code-block:: golang

    package main

    import (
       "github.com/MrSametBurgazoglu/enterprise/generate"
       "your/project/db_models"
    )

    func main() {
        generate.Models(
            db_models.Account(),
        )
    }


``generate.Models`` will create two file for every table. And one for client to be used for all tables.

.. code-block:: text
   :emphasize-lines: 7,10,11

   project/
   ├── migrate/
   │   ├── migrate.go
   ├── generate/
   │   ├── generate.go.py
   └── db_models/ // your table files here
   │    ├── account.go
   └── models/ // generated db files
   │    ├── client.go
   │    ├── account.go
   │    └── account_predicates.go

`account.go` has your Account and AccountList structs. Also hooks are here.

`account_predicates.go` have db predicates to be used by ``Where`` function