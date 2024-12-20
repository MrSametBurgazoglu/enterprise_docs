Base Field
==================

All fields have a base field struct.

.. code-block:: golang

    type Field struct {
        FieldType        int
        Name             string
        DBName           string
        Type             string
        BaseType         string
        Nillable         bool
        HaveDefault      bool
        IsPrepare        bool
        IsGreater        bool
        HaveCustomType   bool
        IsTime           bool
        IsUUID           bool
        CanIn            bool
        Serial           bool
        CustomDBType     string
        RequiredPackages []string
    }


.. attention::

  As you can see when you set a default function for field we get package of your function. We do this because as a design choice, we want db_models package which you put your db schemas can be seperate from other packages.
  You can realize after necessary files generated we don't actually need db_models package
