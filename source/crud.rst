Crud
==================

Crud Operations on enterprise

.. code-block:: go

    import "your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

    db, err := models.NewDB(&models.Options{Url: dbUrl})
    if err != nil {
        panic(err)
    }

    ctx := context.Background()
    account := models.NewAccount(ctx, db)
    account.SetName("name")
    account.SetSurname("surname")
    err = account.Create() // row added to table
    if err != nil {
        log.Fatal(err)
    }
    account.SetSurname("new surname")
    err = account.Update() // row updated on table
    if err != nil {
        log.Fatal(err)
    }

    account.Where(account.IsIDEqual(account.GetID()))
    err = account.Get() // row variables set to account struct
    if err != nil {
        log.Fatal(err)
    }

    err = account.Delete() // delete row from table
    if err != nil {
        log.Fatal(err)
    }
