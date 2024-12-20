Migration
==================

Enterprise uses atlasgo.

To learn about atlasgo https://atlasgo.io.

For migration we recommend to create a package named migrate package and put this script into it.
Execute the script and it will create a directory called migrations and write raw sql for migration.


An example migration script ``your/project/migrate/migrate.go``
When this executed it will create a new directory called migrations and put migrations into it.

.. code-block:: golang

    func main() {
        tables := []*models.Table{
            db_models.Deneme(),
            db_models.Test(),
            db_models.Account(),
            db_models.Group(),
        }

        migrate.Migrate(
            context.TODO(),
            "postgresql://user:password@localhost:5432/db?search_path=public",
            "./migrations",
            "new_plan",
            tables,
        )
    }


We do not recommend using ``AutoApplyMigration`` but as the same way you can use it to auto create-apply migration.

.. code-block:: golang

    func main() {
        tables := []*models.Table{
            db_models.Deneme(),
            db_models.Test(),
            db_models.Account(),
            db_models.Group(),
        }

        migrate.AutoApplyMigration(
            context.TODO(),
            "postgresql://user:password@localhost:5432/db?search_path=public",
            "new_plan",
            tables,
        )
    }

.. attention::

  AutoApplyMigration() do not create migration file