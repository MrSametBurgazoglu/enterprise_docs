Update Model
==================

For updating models, first import the necessary packages:

.. code-block:: go

    import "your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

Single Row Update
*****************

First retrieve the model you want to update using a filter and the ``Get()`` method. Once retrieved, modify the fields using setter methods, and save back using the ``Update()`` method.

.. code-block:: go

    db, err := models.NewDB(&models.Options{Url: dbUrl})
    if err != nil {
        panic(err)
    }

    ctx := context.Background()
    account := models.NewAccount(ctx, db)

    // Filter by ID
    account.Where(account.IsIDEqual(targetID))

    // Retrieve from database
    err = account.Get()
    if err != nil {
        log.Fatal(err)
    }

    // Set new name and update
    account.SetName("new name")
    err = account.Update()
    if err != nil {
        log.Fatal(err)
    }

Bulk Update
***********

To update multiple models by their primary keys, use the ``Update`` method on the list struct (e.g., ``AccountList``). This updates all specified records using the values in the first record.

.. code-block:: go

    accountList := models.NewAccountList(ctx, db)

    // Set values on the first instance
    acc1 := models.NewAccount(ctx, db)
    acc1.SetName("Updated Group Name")

    // Additional models to update (only their IDs are used to target rows)
    acc2 := models.NewAccount(ctx, db)
    acc2.SetID(id2)
    
    acc3 := models.NewAccount(ctx, db)
    acc3.SetID(id3)

    // Execute bulk update
    err = accountList.Update(acc1, acc2, acc3)
    if err != nil {
        log.Fatal(err)
    }

Conditional Update (UpdateWhere)
********************************

You can execute a SQL ``UPDATE`` statement with a ``WHERE`` clause directly on the database without loading the entities first. This is done using the ``UpdateWhere`` method on the list struct.

.. code-block:: go

    accountList := models.NewAccountList(ctx, db)
    
    // Apply filters
    accountList.Where(accountList.IsSurnameEqual("Smith"))

    // Define columns to set
    updateValues := map[string]any{
        "status": "active",
        "role":   "user",
    }

    // Run the update
    rowsAffected, err := accountList.UpdateWhere(updateValues)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("Updated %d rows\n", rowsAffected)