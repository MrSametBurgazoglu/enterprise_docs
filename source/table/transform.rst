Transform DB Table to Enterprise Table
======================================

Enterprise utilizes a schema-first, code-generation approach. You define your database tables as Go code, run the code generator, and use the fully type-safe generated models in your business logic.

This page explains the directory layout, how to write table definitions, and how to trigger the code generation process.

Recommended Project Layout
**************************

We recommend organizing your project with a dedicated `db_models` package (where you write the schemas) and a `models` package (where the generated code will reside):

.. code-block:: text

   project/
   ├── db_models/           # Hand-written schema definitions
   │   ├── account.go
   │   └── group.go
   ├── generate/            # Code-generation driver script
   │   └── generate.go
   ├── migrate/             # Migration runner
   │   └── migrate.go
   └── models/              # Auto-generated code (created by generator)
       ├── client.go
       ├── account.go
       └── account_predicates.go

Defining a Table Schema
***********************

A table schema is defined as a function that returns a ``*models.Table``. 

- Use functions from the ``github.com/MrSametBurgazoglu/enterprise/models`` package to construct fields.
- Set the table name using ``tb.SetTableName("name")``.
- Define the primary key using ``tb.SetIDField(field)``.
- Add single-column or composite indexes using ``tb.AddIndex(name, columns...)``.

Example: ``db_models/account.go``
----------------------------------

.. code-block:: go

    package db_models

    import (
        "github.com/MrSametBurgazoglu/enterprise/models"
        "github.com/google/uuid"
    )

    // Account defines the "account" table schema
    func Account() *models.Table {
        // Define fields using builders
        idField := models.UUIDField("ID").DefaultFunc(uuid.New)

        tb := &models.Table{
            Fields: []models.FieldI{
                idField,
                models.StringField("Name"),
                models.StringField("Surname"),
                models.UUIDField("DenemeID").SetNillable(),
                models.UintField("Serial").AddSerial(),
            },
            Relations: []*models.Relation{
                // Many-to-One relation
                models.ManyToOne("Deneme", "id", "deneme_id"),
            },
        }

        // Configure table name and primary key
        tb.SetTableName("account")
        tb.SetIDField(idField)

        // Define a composite index on Name and Surname columns
        tb.AddIndex("account_name_surname_idx", "Name", "Surname")

        return tb
    }

Writing the Generator Script
****************************

Create a simple `main` package inside `generate/generate.go` to invoke the Enterprise generator:

.. code-block:: go

    package main

    import (
        "github.com/MrSametBurgazoglu/enterprise/generate"
        "your/project/db_models"
    )

    func main() {
        // Run code generation for the defined tables
        generate.Models(
            db_models.Account(),
        )
    }

Generating the Code
*******************

Run the generator script using the standard Go toolchain:

.. code-block:: bash

   go run generate/generate.go

Once executed, Enterprise will:
1. Create the `models` directory if it does not exist.
2. Generate `models/client.go` containing connection setup, logger configuration, and transactions wrapper.
3. Generate `models/[table_name].go` containing the main entity struct, getters, setters, lifecycle hooks, and List/Create/Update/Delete operations.
4. Generate `models/[table_name]_predicates.go` containing type-safe query builders and logical filters for every field (e.g. `IsNameEqual`, `IsSerialGreater`).
5. Run `gofmt` over the generated directory to format the output files.