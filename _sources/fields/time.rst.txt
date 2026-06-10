Time Field
==================

.. code-block:: golang

    type TimeDBField struct {
        *Field
        DefaultFuncStruct *FuncStruct
    }

    func (t *TimeDBField) DefaultFunc(v func() time.Time) *TimeDBField {
        t.DefaultFuncStruct.DefaultFunc(v)
        t.HaveDefault = true
        return t
    }


.. attention::

  Time field uses time.Time struct from time package.