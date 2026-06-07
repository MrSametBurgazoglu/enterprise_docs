Custom Fields
=============

Enterprise supports mapping custom Go types to database columns, provided the types implement the standard ``database/sql/driver.Valuer`` and ``database/sql.Scanner`` interfaces.

Declaration
***********

Use the ``models.CustomField`` helper in your schema. You must specify:
1. The field name.
2. The PostgreSQL database type name.
3. An instance of your custom type implementing the scanner/valuer interfaces.

.. code-block:: go

    // db_models/test.go
    package db_models

    import (
        "github.com/MrSametBurgazoglu/enterprise/models"
        "your/project/custom_data_type"
    )

    func Test() *models.Table {
        // ...
        models.CustomField("Info", "text", custom_data_type.Custom{}).SetNillable()
        // ...
    }

Custom Type Example
*******************

Here is an example custom type implementation:

.. code-block:: go

    package custom_data_type

    import (
        "database/sql/driver"
        "fmt"
    )

    type Custom struct {
        Hello string
        World string
    }

    // Scan implements sql.Scanner to deserialize db column value into Go struct
    func (c *Custom) Scan(src any) (err error) {
        switch v := src.(type) {
        case nil:
        case string:
            _, err = fmt.Sscanf(v, "(%q,%q)", &c.Hello, &c.World)
        case []byte:
            _, err = fmt.Sscanf(string(v), "(%q,%q)", &c.Hello, &c.World)
        }
        return
    }

    // Value implements driver.Valuer to serialize Go struct into db value
    func (c Custom) Value() (driver.Value, error) {
        return fmt.Sprintf("(%q,%q)", c.Hello, c.World), nil
    }

Dynamic Defaults
****************

Because custom fields map to user-defined types, you must use ``DefaultFunc`` if you want to define default values for them.

.. code-block:: go

    models.CustomField("Info", "text", custom_data_type.Custom{}).
        DefaultFunc(func() custom_data_type.Custom {
            return custom_data_type.Custom{Hello: "foo", World: "bar"}
        })
