Retrieve Model
==================

For getting a new model on table first import necessary packages

.. code-block:: golang

    import "/your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

Then use ``New"Model"`` function to create new model struct, use it to get the model you want.
First use Where to filter and after that use ``Get()`` function to get row from table.

.. attention::

  Get() can only be used to get single row from table. If more than one it will get first row.

.. code-block:: golang

    db, err := models.NewDB(dbUrl)
    if err != nil {
        panic(err)
    }

    ctx := context.Background()
    account := models.NewAccount(ctx, db)

    account.Where(account.IsIDEqual(uuid.New()))

    err = account.Get()//row variables set to account struct
    if err != nil {
        log.Fatal(err)
    }