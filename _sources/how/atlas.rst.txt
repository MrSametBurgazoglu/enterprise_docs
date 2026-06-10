Enterprise Schema Migration with Atlas
======================================

Enterprise delegates all database schema migration tasks to the state-of-the-art [Atlas Migration Engine](https://atlasgo.io) by Ariga.

Schema Reflection
*****************

At migration time, Enterprise:
1. Translates the Go-defined tables (from your ``db_models`` package) into Atlas schema object graphs using the internal ``TransformSchemaToAtlasSchema`` mapper.
2. Inspects the current state of the database schemas.
3. Computes the diff between the current database state and the desired schema state.
4. Generates standard DDL migration plans.

Auto-Apply vs. File-Based migrations
************************************

Enterprise exposes two ways to run migrations:

File-Based Migrations (Recommended for Production)
--------------------------------------------------

This method generates raw SQL migration files inside a specified directory. You can commit these files to your version control and apply them using the Atlas CLI or another migration runner.

.. code-block:: go

    migrate.Migrate(
        ctx,
        postgresUrl,
        "./migrations", // output directory
        "create_accounts_table", // migration name
        tables,
    )

Auto-Apply Migrations (Recommended for Development & Testing)
--------------------------------------------------------------

This method computes the schema diff and executes the changes directly on the target database on the fly, without writing any migration files.

.. code-block:: go

    migrate.AutoApplyMigration(
        ctx,
        postgresUrl,
        "auto_migration_plan",
        tables,
    )

Table and Schema Exclusion
**************************

To prevent Atlas from trying to manage or delete internal migration history tables, the default migration methods accept an exclude option. By default, Enterprise ignores:
- ``atlas_schema_revisions``
- ``schema_migrations``

You can specify custom exclusions using ``MigrateWithExclude`` and ``AutoApplyMigrationWithExclude``.
