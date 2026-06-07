Delete Model
==================

For deleting models, first import the necessary packages:

.. code-block:: go

    import "your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

Single Row Deletion
*******************

To delete a specific record, call the ``Delete()`` method on a model instance. The model must have its primary key field set.

.. code-block:: go

    db, err := models.NewDB(&models.Options{Url: dbUrl})
    if err != nil {
        panic(err)
    }

    ctx := context.Background()
    account := models.NewAccount(ctx, db)
    account.SetID(targetID)

    // Delete from table
    err = account.Delete()
    if err != nil {
        log.Fatal(err)
    }

Bulk Deletion by IDs
********************

To delete multiple records by their primary keys, use the ``Delete`` method on the list struct (e.g., ``AccountList``).

.. code-block:: go

    accountList := models.NewAccountList(ctx, db)

    acc1 := models.NewAccount(ctx, db)
    acc1.SetID(id1)

    acc2 := models.NewAccount(ctx, db)
    acc2.SetID(id2)

    // Execute bulk delete
    err = accountList.Delete(acc1, acc2)
    if err != nil {
        log.Fatal(err)
    }

Conditional Deletion (DeleteWhere)
**********************************

You can execute a SQL ``DELETE`` statement with a ``WHERE`` clause directly on the database without retrieving the records first. This is done using the ``DeleteWhere`` method on the list struct.

.. code-block:: go

    accountList := models.NewAccountList(ctx, db)

    // Apply filters
    accountList.Where(accountList.IsStatusEqual("inactive"))

    // Run the deletion
    rowsAffected, err := accountList.DeleteWhere()
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("Deleted %d rows\n", rowsAffected)