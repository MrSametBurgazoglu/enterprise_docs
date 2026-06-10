Create Model
==================

For creating a new model on a table, first import the necessary packages:

.. code-block:: go

    import "your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

Single Row Insertion
********************

Use the generated ``New[ModelName]`` function to create a new model instance. Set the fields using the setter methods, and call the ``Create()`` method to insert the row.

.. code-block:: go

    db, err := models.NewDB(&models.Options{Url: dbUrl})
    if err != nil {
        panic(err)
    }

    ctx := context.Background()
    account := models.NewAccount(ctx, db)
    account.SetName("name")
    account.SetSurname("surname")
    
    // Save to the database
    err = account.Create()
    if err != nil {
        log.Fatal(err)
    }

Bulk Insertion
**************

You can insert multiple rows at once using the ``Create`` method on the list struct (e.g., ``AccountList``). This creates a single optimized SQL INSERT query containing all records.

.. code-block:: go

    // Initialize list helper
    accountList := models.NewAccountList(ctx, db)

    // Create individual model instances
    acc1 := models.NewAccount(ctx, db)
    acc1.SetName("Alice")
    acc1.SetSurname("Smith")

    acc2 := models.NewAccount(ctx, db)
    acc2.SetName("Bob")
    acc2.SetSurname("Jones")

    // Insert both in a single query
    err = accountList.Create(acc1, acc2)
    if err != nil {
        log.Fatal(err)
    }