Enum Field
==================

.. code-block:: golang

    type EnumDBField struct {
        *Field
        DefaultValue      string
        DefaultFuncStruct *FuncStruct
        TypeName          string
        Values            []string
    }

    func (s *EnumDBField) Default(v string) *EnumDBField {
        s.DefaultValue = v
        s.HaveDefault = true
        return s
    }

    func (s *EnumDBField) DefaultFunc(v func() string) *EnumDBField {
        s.DefaultFuncStruct.DefaultFunc(v)
        s.HaveDefault = true
        return s
    }

.. attention::

  You can give values to enum like this

  ``colors := []string{"RED", "GREEN", "BLUE"}``
  ``models.EnumField("ColorField", denemeTypeEnumValues)``