String Field
==================

.. code-block:: golang

    func (s *StringDBField) Default(v string) *StringDBField {
        s.DefaultValue = v
        s.HaveDefault = true
        return s
    }

    func (s *StringDBField) DefaultFunc(v func() string) *StringDBField {
        s.DefaultFuncStruct.DefaultFunc(v)
        s.HaveDefault = true
        return s
    }