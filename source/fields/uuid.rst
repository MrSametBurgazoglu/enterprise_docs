UUID Field
==================

.. code-block:: golang

    type UUIDDBField struct {
        *Field
        DefaultFuncStruct *FuncStruct
    }

    func (u *UUIDDBField) DefaultFunc(v func() uuid.UUID) *UUIDDBField {
        u.DefaultFuncStruct.DefaultFunc(v)
        u.HaveDefault = true
        return u
    }

    func (u *UUIDDBField) GetDefault() string {
        if u.DefaultFuncStruct.PackageFunc != "" {
            u.RequiredPackages = append(u.RequiredPackages, u.DefaultFuncStruct.PackageAddress)
            return u.DefaultFuncStruct.PackageFunc + "()"
        }
        return ""
    }


.. attention::

  UUID field uses uuid.UUID struct from github.com/google/uuid package.

.. attention::

  You can create a primary key for your table with
  ``idField := models.UUIDField("ID").DefaultFunc(uuid.New)``