Delete Model
==================

For deleting a new model on table first import necessary packages

.. code-block:: golang

    import "/your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"

Then use ``Delete()`` function to delete row from table.

.. attention::

  Delete() uses ``SQL DELETE`` so it will remove from table

.. code-block:: golang

   err = account.Delete()
   if err != nil {
        log.Fatal(err)
    }