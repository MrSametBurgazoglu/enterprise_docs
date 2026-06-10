Integer Fields
==================

.. code-block:: golang

    type IntDBField struct {
        *Field
        DefaultValue      int
        DefaultFuncStruct *FuncStruct
    }

    func (i *IntDBField) Default(v int) *IntDBField {
        i.DefaultValue = v
        i.HaveDefault = true
        return i
    }

    func (i *IntDBField) DefaultFunc(v func() int) *IntDBField {
        i.DefaultFuncStruct.DefaultFunc(v)
        i.RequiredPackages = append(i.RequiredPackages, i.DefaultFuncStruct.PackageAddress)
        i.HaveDefault = true
        return i
    }

.. code-block:: golang

    type SmallIntDBField struct {
        *Field
        DefaultValue      int16
        DefaultFuncStruct *FuncStruct
    }

    func (i *SmallIntDBField) Default(v int16) *SmallIntDBField {
        i.DefaultValue = v
        i.HaveDefault = true
        return i
    }

    func (i *SmallIntDBField) DefaultFunc(v func() int16) *SmallIntDBField {
        i.Field.DefaultFunc(v)
        return i
    }

.. code-block:: golang

    type BigIntDBField struct {
        *Field
        DefaultValue      int64
        DefaultFuncStruct *FuncStruct
    }

    func (i *BigIntDBField) Default(v int64) *BigIntDBField {
        i.DefaultValue = v
        i.HaveDefault = true
        return i
    }

    func (i *BigIntDBField) DefaultFunc(v func() int64) *BigIntDBField {
        i.Field.DefaultFunc(v)
        return i
    }