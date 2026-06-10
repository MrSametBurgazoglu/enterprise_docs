Nullable Fields (SetNillable)
=============================

By default, Enterprise generates fields using non-pointer Go types (e.g. ``string``, ``int``, ``bool``). These columns are defined as ``NOT NULL`` in the database schema.

To allow database columns to store ``NULL``, you must declare them as nullable by chaining the ``SetNillable()`` method on the field builder.

Declaring Nullable Fields
*************************

Chaining ``SetNillable()`` changes the generated Go struct type to a **pointer type**, allowing the field to represent a ``nil`` value.

.. code-block:: go

    // db_models/account.go
    func Account() *models.Table {
        return &models.Table{
            Fields: []models.FieldI{
                // Generates as: ID uuid.UUID (NOT NULL)
                models.UUIDField("ID"),

                // Generates as: Name string (NOT NULL)
                models.StringField("Name"),

                // Generates as: Surname *string (NULL)
                models.StringField("Surname").SetNillable(),

                // Generates as: MiddleName *string (NULL) with custom column name
                models.StringField("MiddleName").SetDBName("middle_name").SetNillable(),
            },
        }
    }

Handling Nulls in Code
**********************

When using generated models, nullable fields are accessed using pointers. Getter and setter methods handle pointers appropriately.

Setting Nullable Fields
-----------------------

.. code-block:: go

    account := models.NewAccount(ctx, db)
    
    // Set a non-null value
    surname := "Smith"
    account.SetSurname(&surname)

    // Set to null
    account.SetSurname(nil)

    err = account.Create()

Reading Nullable Fields
-----------------------

To avoid nil pointer dereferences, always check if a nullable field is nil before dereferencing:

.. code-block:: go

    err = account.Get()
    if err == nil {
        if account.GetSurname() != nil {
            fmt.Println("Surname:", *account.GetSurname())
        } else {
            fmt.Println("Surname is NULL")
        }
    }

Null Filters in Predicates
--------------------------

For every nullable field, the generator emits predicate helpers to query for ``IS NULL`` and ``IS NOT NULL`` conditions:

- ``Is[FieldName]Nil()`` maps to ``"column" IS NULL``
- ``Is[FieldName]NotNil()`` maps to ``"column" IS NOT NULL``

.. code-block:: go

    list := models.NewAccountList(ctx, db)
    
    // Find all accounts where Surname is NULL
    list.Where(list.IsSurnameNil())
    err = list.List()