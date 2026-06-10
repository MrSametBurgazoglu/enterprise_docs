Refresh Model
==================

For refreshing a model's state, first import the necessary packages:

.. code-block:: go

    import "your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

Refreshing Fields
*****************

The ``Refresh()`` function re-scans the model's fields from the database based on its current primary key. This is useful when database triggers, defaults, or concurrent operations change the database values, and you need to resynchronize your in-memory Go struct.

.. code-block:: go

    db, err := models.NewDB(&models.Options{Url: dbUrl})
    if err != nil {
        panic(err)
    }

    ctx := context.Background()
    account := models.NewAccount(ctx, db)
    account.SetID(targetID)

    // Sync in-memory model fields with the latest database state
    err = account.Refresh()
    if err != nil {
        log.Fatal(err)
    }

    fmt.Println("Refreshed Surname:", account.GetSurname())