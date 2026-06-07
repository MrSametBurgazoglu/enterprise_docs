Field Default Values
====================

Enterprise supports both **static** and **dynamic** default values. Default values can be set in-memory at model instantiation and mapped to database-level default clauses (e.g. ``DEFAULT 10``) during schema generation.

Static Default Values
*********************

Static defaults are fixed, constant values defined directly in the schema using the ``Default(v)`` method.

.. code-block:: go

    // Boolean default
    models.BoolField("IsActive").Default(true)

    // Integer default
    models.IntField("Count").Default(42)

    // String default
    models.StringField("Role").Default("user")

Dynamic Default Values
**********************

Dynamic defaults are evaluated at runtime when a new model instance is created. This is crucial for timestamps, UUIDs, or cryptographically random values. Declare them using the ``DefaultFunc(f)`` method.

.. code-block:: go

    // Generate UUIDs automatically using google/uuid
    models.UUIDField("ID").DefaultFunc(uuid.New)

    // Populate created timestamps automatically using standard time package
    models.TimeField("CreatedAt").DefaultFunc(time.Now)

    // Custom random generator function
    models.IntField("RandomKey").DefaultFunc(func() int {
        return rand.Intn(10000)
    })

How Default Values are Applied
******************************

1. **In-Memory Instantiation**:
   When you initialize a model using ``models.New[ModelName](ctx, db)``, Enterprise automatically calls an internal ``SetDefaults()`` method. This executes your default functions and assigns the values to the struct fields.
2. **Database Schema Mapping**:
   When generating migrations, Enterprise maps your default values to PostgreSQL default expressions.
   - Functions like ``uuid.New`` are mapped to ``gen_random_uuid()``.
   - Functions like ``time.Now`` are mapped to ``now()``.
   - Static values are mapped to literal expressions (e.g. ``DEFAULT 'user'``).

Example Schema configuration
----------------------------

.. code-block:: go

    func Account() *models.Table {
        idField := models.UUIDField("ID").DefaultFunc(uuid.New)

        return &models.Table{
            Fields: []models.FieldI{
                idField,
                models.StringField("Role").Default("user"),
                models.TimeField("CreatedAt").DefaultFunc(time.Now),
            },
        }
    }

Calling `NewAccount` automatically executes the generators:

.. code-block:: go

    account := models.NewAccount(ctx, db)
    fmt.Println(account.GetID())        // Evaluated uuid.New()
    fmt.Println(account.GetRole())      // "user"
    fmt.Println(account.GetCreatedAt()) // Evaluated time.Now()