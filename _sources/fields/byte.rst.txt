Byte Field
==========

Byte fields are used to store binary data in the database, mapping to PostgreSQL's ``bytea`` column type.

.. code-block:: go

    type ByteDBField struct {
        *Field
        DefaultValue      []byte
        DefaultFuncStruct *FuncStruct
    }

    func (i *ByteDBField) Default(v []byte) *ByteDBField {
        i.DefaultValue = v
        i.HaveDefault = true
        return i
    }

    func (i *ByteDBField) DefaultFunc(v func() []byte) *ByteDBField {
        i.Field.DefaultFunc(v)
        return i
    }

Usage
*****

Define a byte field in your schema:

.. code-block:: go

    models.ByteField("Data")

Nullability
***********

By default, byte fields map to a ``[]byte`` in Go. You can make it nullable using ``SetNillable()``.