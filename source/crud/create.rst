Create Model
==================

For creating a new model on table first import necessary packages

.. code-block:: golang

    import "/your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

Then use ``NewModel`` function to create new model struct. After that set variables you want and use ``Create()`` function.

.. code-block:: golang

    db, err := models.NewDB(dbUrl)
    if err != nil {
        panic(err)
    }

    ctx := context.Background()
    account := models.NewAccount(ctx, db)
    account.SetName("name")
    account.SetSurname("surname")
    err = acc.Create()//row added to table
    if err != nil {
        log.Fatal(err)
    }