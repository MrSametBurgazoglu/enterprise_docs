Json Field
==================

.. code-block:: golang

    type JSONDBField struct {
        *Field
        DefaultFuncStruct *FuncStruct
    }

    func (u *JSONDBField) DefaultFunc(v func() map[string]any) *JSONDBField {
        u.DefaultFuncStruct.DefaultFunc(v)
        u.HaveDefault = true
        return u
    }


.. attention::

  Json field uses ``map[string]any``