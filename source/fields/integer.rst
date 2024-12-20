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