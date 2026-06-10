Unsigned Integer Fields
==================

.. code-block:: golang

    type UintDBField struct {
        *Field
        DefaultValue      uint
        DefaultFuncStruct *FuncStruct
    }

    func (i *UintDBField) Default(v uint) *UintDBField {
        i.DefaultValue = v
        i.HaveDefault = true
        return i
    }

    func (i *UintDBField) DefaultFunc(v func() uint) *UintDBField {
        i.DefaultFuncStruct.DefaultFunc(v)
        i.RequiredPackages = append(i.RequiredPackages, i.DefaultFuncStruct.PackageAddress)
        i.HaveDefault = true
        return i
    }