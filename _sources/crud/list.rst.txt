List Models
==================

For listing models from a table first import necessary packages

.. code-block:: golang

    import "/your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

Then use ``NewModelList`` function to create new model list struct, use it to list the table.
First use Where to filter and after that use List() function to get rows from table.

.. code-block:: golang

    db, err := models.NewDB(dbUrl)
    if err != nil {
        panic(err)
    }

    ctx := context.Background()
    accountList := models.NewAccountList(ctx, db)

    accountList.Where(accountList.IsNameEqual("samet"))

    err = accountList.List()//rows variables set to account list struct
    if err != nil {
        log.Fatal(err)
    }

    for index, account := range accountList.Items {
        println(index, account.GetSurname())
    }