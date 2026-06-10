Retrieve Model
==================

For retrieving a single model from a table, first import the necessary packages:

.. code-block:: go

    import "your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

Basic Retrieval
***************

To get a single record, create a model instance with ``New[ModelName]``, define the retrieval filters using ``Where``, and execute ``Get()``.

.. code-block:: go

    db, err := models.NewDB(&models.Options{Url: dbUrl})
    if err != nil {
        panic(err)
    }

    ctx := context.Background()
    account := models.NewAccount(ctx, db)

    // Specify search criteria
    account.Where(account.IsIDEqual(targetID))

    // Retrieve from database
    err = account.Get()
    if err != nil {
        if errors.Is(err, client.NotFoundError) {
            log.Println("Record not found")
        } else {
            log.Fatal(err)
        }
    }

    fmt.Println("Account Name:", account.GetName())

.. attention::

  ``Get()`` is designed to fetch a single row. If the query matches multiple rows, only the first matching row will be scanned and returned.

Partial Field Selection
***********************

To optimize performance, you can select only a specific subset of columns from the database. This is done using ``GetSelector()`` prior to fetching. Unselected fields will remain at their Go zero value.

.. code-block:: go

    account := models.NewAccount(ctx, db)
    
    // Choose only to retrieve the Surname column
    account.GetSelector().SelectSurname()

    account.Where(account.IsIDEqual(targetID))
    err = account.Get()
    if err == nil {
        fmt.Println("Surname:", account.GetSurname())
        fmt.Println("Name:", account.GetName()) // Will be empty string (not selected)
    }

Query Observers and Debug Logging
*********************************

You can hook into query executions and enable SQL log output at database initialization.

.. code-block:: go

    type MyObserver struct{}

    func (o MyObserver) Before(ctx context.Context, sql string, args pgx.NamedArgs) context.Context {
        log.Printf("Executing query: %s with args: %v", sql, args)
        return ctx
    }

    func (o MyObserver) After(ctx context.Context, sql string, args pgx.NamedArgs, err error) {
        if err != nil {
            log.Printf("Query error: %v", err)
        }
    }

    // Initialize with Options
    opts := &models.Options{
        Url:      dbUrl,
        Debug:    true, // Enables auto logs via slog to stdout
        Observer: MyObserver{},
    }
    db, err := models.NewDB(opts)