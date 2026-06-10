Float Field
==================

.. code-block:: golang

    type Float32DBField struct {
        *Field
        DefaultValue      float32
        DefaultFuncStruct *FuncStruct
    }

    func (i *Float32DBField) Default(v float32) *Float32DBField {
        i.DefaultValue = v
        i.HaveDefault = true
        return i
    }

    func (i *Float32DBField) DefaultFunc(v func() float32) *Float32DBField {
        i.DefaultFuncStruct.DefaultFunc(v)
        i.RequiredPackages = append(i.RequiredPackages, i.DefaultFuncStruct.PackageAddress)
        i.HaveDefault = true
        return i
    }

.. code-block:: golang

    type Float64DBField struct {
        *Field
        DefaultValue      float64
        DefaultFuncStruct *FuncStruct
    }

    func (i *Float64DBField) Default(v float64) *Float64DBField {
        i.DefaultValue = v
        i.HaveDefault = true
        return i
    }

    func (i *Float64DBField) DefaultFunc(v func() float64) *Float64DBField {
        i.DefaultFuncStruct.DefaultFunc(v)
        i.RequiredPackages = append(i.RequiredPackages, i.DefaultFuncStruct.PackageAddress)
        i.HaveDefault = true
        return i
    }