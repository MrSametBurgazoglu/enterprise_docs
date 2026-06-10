Base Field Configuration
=========================

All specialized database field builders (such as `UUIDField`, `StringField`, `IntField`) embed a common base ``Field`` configuration struct. This base struct manages metadata, default values, nullability, serial auto-incrementing status, and custom types.

Base Field Structure
********************

Under the hood, Enterprise maps field metadata using the following structure:

.. code-block:: go

    type Field struct {
        FieldType        int      // Core data type identifier
        Name             string   // Go struct field name (e.g. "CreatedAt")
        DBName           string   // PostgreSQL column name (e.g. "created_at")
        Type             string   // Go type signature (e.g. "time.Time" or "*time.Time")
        BaseType         string   // Base Go type name without pointers
        Nillable         bool     // True if nullable (represented as pointer type in Go)
        HaveDefault      bool     // True if a default value is defined
        IsPrepare        bool     // Internally used for query preprocessing
        IsGreater        bool     // True if field supports comparison operators (> , <)
        HaveCustomType   bool     // True if it is a user-defined custom DB field
        IsTime           bool     // True if column represents a timestamp
        IsUUID           bool     // True if column represents a UUID
        CanIn            bool     // True if the field supports IN / NOT IN filtering
        Serial           bool     // True if this column auto-increments (serial/bigserial)
        CustomDBType     string   // Raw database column type (e.g. "text" or unsupported type)
        RequiredPackages []string // External packages that must be imported in the model file
    }

Design Choice: Package Resolution for Default Functions
*******************************************************

When you define a dynamic default value using ``DefaultFunc(f func() Type)`` (such as passing ``uuid.New`` or ``time.Now``), Enterprise inspects the function using reflection at code generation time.

It retrieves the package path (e.g. ``github.com/google/uuid`` or ``time``) and automatically registers it inside ``RequiredPackages``.

Why is this done?
-----------------

This is a core design choice:
1. **Separation of Concerns**: Your schema definition files (under the ``db_models`` package) can import whatever packages are needed for default values without coupling your main runtime packages to them.
2. **Clean Imports**: The code generator resolves the package imports programmatically, guaranteeing that the generated code compiles correctly without manual import intervention.
3. **Model Decoupling**: Once generation completes, your application only needs to import the clean, generated ``models`` package; it does not need to reference the original `db_models` schema files.
