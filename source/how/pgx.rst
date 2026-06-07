Enterprise and pgx
==================

Enterprise is designed as a lightweight, thin wrapper around the pgx PostgreSQL driver, ensuring you get maximum performance without losing direct control over the database connection.

Interface-Based Design
**********************

Rather than hiding pgx behind complex layers, Enterprise exposes a clean, interface-based connection model. The core query runner expects a ``DatabaseClient``:

.. code-block:: go

    type DatabaseClient interface {
        Exec(ctx context.Context, sql string, arguments ...any) (commandTag pgconn.CommandTag, err error)
        Query(ctx context.Context, sql string, args ...any) (pgx.Rows, error)
        QueryRow(ctx context.Context, sql string, args ...any) pgx.Row
    }

And for transactions:

.. code-block:: go

    type DatabaseTransactionClient interface {
        DatabaseClient
        Commit(ctx context.Context) error
        Rollback(ctx context.Context) error
    }

Both the connection pool wrapper (``Database``) and the active transaction wrapper (``Transaction``) implement these interfaces. This allows you to write mock implementations or custom wrappers easily.

Named Arguments Threading
*************************

Enterprise compiles queries to use PostgreSQL named placeholders (using ``@param`` syntax) and feeds them to the pgx driver using ``pgx.NamedArgs``. Named arguments are cleaner to debug and resolve positional ordering concerns automatically.

Direct pgx Escape Hatch
***********************

If you need to write raw SQL or use complex features of pgx that are not supported by the generated query builder, you can query directly:

.. code-block:: go

    // Get the underlying pool/client and run standard pgx queries
    rows, err := db.Query(ctx, "SELECT * FROM account WHERE name = $1", "samet")
