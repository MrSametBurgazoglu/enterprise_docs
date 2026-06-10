String Array Field
==================

.. code-block:: golang

    type StringArrayDBField struct {
        *Field
        DefaultValue      []string
        DefaultFuncStruct *FuncStruct
    }

    func (s *StringArrayDBField) Default(v []string) *StringArrayDBField {
        s.DefaultValue = v
        s.HaveDefault = true
        return s
    }

    func (s *StringArrayDBField) DefaultFunc(v func() []string) *StringArrayDBField {
        s.Field.DefaultFunc(v)
        return s
    }
