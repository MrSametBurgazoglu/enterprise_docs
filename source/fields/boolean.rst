Boolean Field
==================

.. code-block:: golang

    type BoolDBField struct {
        *Field
        DefaultValue      bool
        DefaultFuncStruct *FuncStruct
    }

    func (i *BoolDBField) Default(v bool) *BoolDBField {
        i.DefaultValue = v
        i.HaveDefault = true
        return i
    }

    func (i *BoolDBField) DefaultFunc(v func() bool) *BoolDBField {
        i.DefaultFuncStruct.DefaultFunc(v)
        i.RequiredPackages = append(i.RequiredPackages, i.DefaultFuncStruct.PackageAddress)
        i.HaveDefault = true
        return i
    }
