Enterprise & PostgreSQL
=======================

Enterprise ORM is optimized for PostgreSQL and is built directly on top of [pgx/v5](https://github.com/jackc/pgx), the fastest PostgreSQL driver for Go.

Database Initialization
***********************

To initialize the database client, define a ``models.Options`` struct and call ``models.NewDB``. This establishes a thread-safe connection pool using ``pgxpool``.

.. code-block:: go

    package main

    import (
        "context"
        "log"

        "your/project/models"
    )

    func main() {
        opts := &models.Options{
            Url:   "postgresql://postgres:password@localhost:5432/my_db?sslmode=disable",
            Debug: true, // Output SQL queries to stdout via slog
        }

        db, err := models.NewDB(opts)
        if err != nil {
            log.Fatalf("Failed to connect: %v", err)
        }
        defer db.Exit() // Close the connection pool on application exit

        // Database is ready for queries
    }

Connection Pooling
******************

Under the hood, Enterprise utilizes ``pgxpool.Pool``. You can configure pool-specific parameters (such as connection lifetime, min/max connections, etc.) directly in the connection DSN query string:

.. code-block:: text

    postgresql://user:pass@host:port/db?pool_max_conns=20&pool_min_conns=5&pool_max_conn_lifetime=1h
