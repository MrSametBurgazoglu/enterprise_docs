How Enterprise Executes Queries
===============================

Understanding the execution lifecycle of queries in Enterprise helps you write efficient code and debug complex database interactions.

Query Lifecycle Flow
********************

Here is the step-by-step lifecycle of a database read operation (e.g., calling ``Get()`` on a model):

.. mermaid::

   graph TD
       A[Model.Get] --> B[client.CreateSelectQuery]
       B --> C[Build SQL & pgx.NamedArgs]
       C --> D[DatabaseClient.Query]
       D --> E[Trigger QueryObserver.Before]
       E --> F[Execute pgxpool.Query]
       F --> G[Trigger QueryObserver.After]
       G --> H[Log via slog if Debug=true]
       H --> I[Scan rows into model values]
       I --> J[Call model.ScanResult]

1. **SQL Compilation**: The model delegates execution to the core query builder in the ``client`` package. This engine translates model predicates, relations, limit, offset, and order parameters into a parameter-deduplicated SQL query string and a ``pgx.NamedArgs`` map.
2. **Observer Interception**: If a ``QueryObserver`` is set in ``Options``, its ``Before`` method is executed, which can mutate the context.
3. **Driver Invocation**: The compiled SQL is run against the connection pool via `pgx`.
4. **Post-Execution Interception**: The ``QueryObserver.After`` hook is executed with the operation result or error.
5. **Logging**: If ``Options.Debug`` is enabled, the query and its arguments are logged via ``slog`` (JSON handler, LevelDebug).
6. **Result Scanning**: The returned rows are scanned into the pre-allocated value pointers, and the model triggers its internal ``ScanResult`` hook to finalize entity states.

Transactions and Savepoints
***************************

Enterprise supports block-based transactions. If a transaction is active, the model operations are executed using a ``Transaction`` client which implements the same ``DatabaseClient`` interface, ensuring seamless transaction integration.

.. code-block:: go

    err := db.Transaction(ctx, func(tx client.DatabaseTransactionClient) error {
        account := models.NewAccount(ctx, tx)
        account.SetName("Transacted Name")
        return account.Create()
    })
