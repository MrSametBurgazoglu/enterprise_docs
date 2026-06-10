Supported Fields
==================

Enterprise supports a variety of field types to map PostgreSQL columns to Go variables.

Supported Types
***************

- **Boolean**: ``models.BoolField(name)`` maps to ``boolean``.
- **Byte**: ``models.ByteField(name)`` maps to ``bytea``.
- **UUID**: ``models.UUIDField(name)`` maps to ``uuid``.
- **Integer**:
  - ``models.IntField(name)`` maps to ``integer``.
  - ``models.SmallIntField(name)`` maps to ``smallint``.
  - ``models.BigIntField(name)`` maps to ``bigint``.
- **Unsigned Integer**: ``models.UintField(name)`` maps to ``integer``.
- **Floating Point**:
  - ``models.Float32Field(name)`` maps to ``real``.
  - ``models.Float64Field(name)`` maps to ``double precision``.
- **String**: ``models.StringField(name)`` maps to ``varchar``.
- **Decimal**: ``models.DecimalField(name, precision, scale)`` maps to ``numeric(precision, scale)``.
- **String Array**: ``models.StringArrayField(name)`` maps to ``text[]``.
- **Enum**: ``models.EnumField(name, values)`` maps to ``varchar`` with validation.
- **Time**: ``models.TimeField(name)`` maps to ``timestamp with time zone``.
- **JSON**: ``models.JSONField(name)`` maps to ``jsonb``.
- **Custom Type**: ``models.CustomField(name, dbType, valuerScannerInstance)`` maps to any user-defined type implementing ``sql.Scanner`` and ``driver.Valuer``.

Common Configuration Methods
****************************

Every field type returns a builder interface that supports chainable configuration methods:

.. code-block:: go

    // Set a custom database column name (defaults to snake_case of field name)
    field.SetDBName("custom_column_name")

    // Mark the field as nullable (scanned as pointer types in Go)
    field.SetNillable()

    // Add serial auto-increment tag (for integer fields)
    field.AddSerial()

    // Define a static default value
    field.Default(value)

    // Define a dynamic default function (evaluated at entity initialization)
    field.DefaultFunc(func)
