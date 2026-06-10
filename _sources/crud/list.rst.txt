List Models
==================

For retrieving and listing models from a table, first import the necessary packages:

.. code-block:: go

    import "your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

Basic Listing
*************

Use the generated ``New[ModelName]List`` function to create a list helper, apply filters with ``Where``, and execute ``List()`` to fetch records.

.. code-block:: go

    db, err := models.NewDB(&models.Options{Url: dbUrl})
    if err != nil {
        panic(err)
    }

    ctx := context.Background()
    accountList := models.NewAccountList(ctx, db)

    // Filter by name
    accountList.Where(accountList.IsNameEqual("samet"))

    // Fetch matching rows
    err = accountList.List()
    if err != nil {
        log.Fatal(err)
    }

    // Access rows in the Items slice
    for index, account := range accountList.Items {
        fmt.Println(index, account.GetSurname())
    }

Paging and Ordering
*******************

You can apply ordering and pagination limits to query results using the list helper methods.

.. code-block:: go

    accountList := models.NewAccountList(ctx, db)
    
    // Set ordering
    accountList.Order("surname")      // ASC
    accountList.OrderDesc("serial")   // DESC

    // Paginate results
    accountList.Limit(10)
    accountList.Skip(5)

    err = accountList.List()

List with Total Count
*********************

If you need both a paginated subset of records and the total number of matching records in the database, use ``ListWithTotal``. This executes a ``COUNT(*)`` query and a paged list query in sequence.

.. code-block:: go

    accountList := models.NewAccountList(ctx, db)
    accountList.Where(accountList.IsStatusEqual("active"))

    // Fetch total count and a single page of results (skip: 10, limit: 5)
    totalCount, err := accountList.ListWithTotal(10, 5)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("Total active accounts: %d, fetched page size: %d\n", totalCount, len(accountList.Items))

Distinct Values
***************

Retrieve all distinct values of a specific field as a slice of strings.

.. code-block:: go

    accountList := models.NewAccountList(ctx, db)
    
    // Get unique surnames, sorted
    accountList.Order("surname")
    surnames, err := accountList.DistinctString(models.AccountTableSurnameField)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Println("Unique Surnames:", surnames)

Record Counting
***************

Retrieve the count of records matching the current filters.

.. code-block:: go

    accountList := models.NewAccountList(ctx, db)
    accountList.Where(accountList.IsStatusEqual("active"))

    count, err := accountList.Count()
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("Active count:", count)

Aggregates
**********

Model list helpers provide pre-generated methods for standard aggregate calculations:

.. code-block:: go

    denemeList := models.NewDenemeList(ctx, db)
    denemeList.Where(denemeList.IsCountGreater(10))

    // Minimum value
    minVal, err := denemeList.MinCount()

    // Maximum value
    maxVal, err := denemeList.MaxCount()

    // Sum
    sumVal, err := denemeList.SumCount()

    // Average
    avgVal, err := denemeList.AvgCount()

Custom Aggregates and Group By
******************************

For more complex calculations, group-by clauses, or SQL expressions, use the custom ``Aggregate`` builder.

.. code-block:: go

    groupList := models.NewGroupList(ctx, db)
    
    var countVal int
    scanFunc, err := groupList.Aggregate(func(a *client.Aggregate) {
        // Run aggregate on a custom SQL expression using client.Raw
        a.Count(client.Raw("COALESCE(name, 'default')"), &countVal)
        a.GroupBy(client.Raw("COALESCE(name, 'default')"))
    })
    if err != nil {
        log.Fatal(err)
    }

    // Scan the first row
    err = scanFunc()

Iterating Over Aggregate Result Sets
************************************

If your query returns multiple grouped rows, you can iterate over them using ``AggregateRows`` or Go 1.23's standard library sequence iterators using ``AggregateRowsSeq``.

.. code-block:: go

    groupList := models.NewGroupList(ctx, db)

    // Using Go 1.23 iter.Seq2 sequence iterator
    var (
        groupName string
        groupSize int
    )
    
    iterator := groupList.AggregateRowsSeq(func(a *client.Aggregate) {
        a.Field("name", &groupName)
        a.Count("id", &groupSize)
        a.GroupBy("name")
    })

    for index, err := range iterator {
        if err != nil {
            log.Fatal(err)
        }
        fmt.Printf("Group %d: Name: %s, Size: %d\n", index, groupName, groupSize)
    }