Decimal Field
==================

.. code-block:: golang

    type DecimalDBField struct {
        *Field
        DefaultValue      string
        DefaultFuncStruct *FuncStruct
    }

    func (d *DecimalDBField) Default(v string) *DecimalDBField {
        d.DefaultValue = v
        d.HaveDefault = true
        return d
    }

    func (d *DecimalDBField) DefaultFunc(v func() string) *DecimalDBField {
        d.Field.DefaultFunc(v)
        return d
    }
