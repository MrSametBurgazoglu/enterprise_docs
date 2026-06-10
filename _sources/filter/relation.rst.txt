Relation Filtering
==================

Enterprise features a unique relationship loading system that completely eliminates N+1 query patterns. It joins relation tables and filters them in a **single query**.

Usage
*****

When retrieving a parent record, you can filter which child records are eagerly loaded by passing callback functions to the ``With[RelationName]`` or ``With[RelationName]List`` method.

.. code-block:: go

    account := models.NewAccount(ctx, db)
    account.Where(account.IsIDEqual(accountID))

    // Load related Deneme, but only if it is active
    account.WithDeneme(func(d *models.Deneme) {
        d.Where(d.IsActiveEqual(true))
    })

    err = account.Get()
    if err == nil {
        if account.Deneme != nil {
            fmt.Println("Loaded active deneme:", account.Deneme.GetID())
        } else {
            fmt.Println("No active deneme linked to this account")
        }
    }

Similarly, you can load and filter related lists:

.. code-block:: go

    deneme := models.NewDeneme(ctx, db)
    deneme.Where(deneme.IsIDEqual(denemeID))

    // Eagerly load only the related accounts that have the surname "Smith"
    deneme.WithAccountList(func(al *models.AccountList) {
        al.Where(al.IsSurnameEqual("Smith"))
    })

    err = deneme.Get()
    if err == nil {
        // Access loaded slice of children
        for _, acc := range deneme.AccountList.Items {
            fmt.Println("Related Smith Account:", acc.GetName())
        }
    }

How It Works
************

When a relation loader is added, Enterprise:
1. Generates an SQL ``JOIN`` block (e.g. ``LEFT JOIN`` or similar depending on the join layout).
2. appends the custom where clauses defined in the callback directly to the main query's ``WHERE`` list.
3. Automatically maps and scans all retrieved fields into the nested child structs.
