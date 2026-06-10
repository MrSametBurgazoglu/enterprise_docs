Advanced Filtering
==================

Enterprise provides a type-safe query building engine that supports complex logical queries, wildcard matches, dynamic/conditional filters, and SQL injection protection.

Logical Composition (And / Or)
*******************************

You can compose multiple predicates using ``And`` and ``Or`` operators. Predicates can be nested arbitrarily.

.. code-block:: go

    list := models.NewAccountList(ctx, db)
    
    // Select accounts where (name = "samet" OR name = "alice") AND status = "active"
    list.Where(
        models.And(
            models.Or(
                list.IsNameEqual("samet"),
                list.IsNameEqual("alice"),
            ),
            list.IsStatusEqual("active"),
        ),
    )

    err = list.List()

LIKE & ILIKE Wildcards
**********************

String fields support pattern matching using ``LIKE`` (case-sensitive) and ``ILIKE`` (case-insensitive) operators.

.. code-block:: go

    list := models.NewGroupList(ctx, db)

    // Match surnames starting with "Smith" (case-sensitive)
    list.Where(list.IsSurnameLike("Smith%"))

    // Match surnames starting with "smith" (case-insensitive, matches "Smith", "SMITH", etc.)
    list.Where(list.IsSurnameILike("smith%"))

Conditional Filters (WhereIf & WhereIn)
***************************************

To avoid writing verbose conditional code when building queries dynamically based on user input, you can use ``WhereIf`` and ``WhereIn``. The predicate is only appended to the query if the first argument evaluates to ``true``.

.. code-block:: go

    list := models.NewGroupList(ctx, db)

    // Only filter by name if searchName is not empty
    list.WhereIf(searchName != "", list.IsNameEqual(searchName))

    // Only filter by enum status if searchStatus is not empty
    list.WhereIn(searchStatus != "", list.IsStatusIN("active", "pending"))

Zero Predicates (Unconditional Execution)
*****************************************

Calling ``Where()`` with no arguments, or where all conditional predicates evaluate to false, is a safe no-op. The query runs without a ``WHERE`` clause, returning all records in the table.

.. code-block:: go

    list := models.NewGroupList(ctx, db)
    
    // No-op, executes SELECT without any WHERE clause
    list.Where() 
    err = list.List()
