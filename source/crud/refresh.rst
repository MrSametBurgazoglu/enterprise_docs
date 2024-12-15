Refresh Model
==================

.. attention::

  Refresh() currently under development

For refreshing a model on first import necessary packages

.. code-block:: golang

    import "/your/project/models" // your auto-generated models package
    import "github.com/MrSametBurgazoglu/enterprise/client"


Use ``Refresh()`` function to refresh row from table.

.. code-block:: golang

    err = account.Refresh()//row variables set to account struct
    if err != nil {
        log.Fatal(err)
    }