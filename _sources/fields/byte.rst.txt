Byte Field
==================

.. code-block:: golang

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

    func (i *ByteDBField) DefaultFunc(v func() bool) *ByteDBField {
        i.DefaultFuncStruct.DefaultFunc(v)
        i.RequiredPackages = append(i.RequiredPackages, i.DefaultFuncStruct.PackageAddress)
        i.HaveDefault = true
        return i
    }


.. attention::

  Json field uses ``map[string]any``