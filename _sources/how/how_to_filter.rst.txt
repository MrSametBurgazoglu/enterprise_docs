How Enterprise Filters Tables
=============================

Enterprise employs a highly secure, type-safe filtering mechanism designed to mitigate SQL injection risks and handle edge cases such as database keyword collisions.

Double-Quoted Identifiers
*************************

PostgreSQL contains many reserved keywords (e.g., ``group``, ``order``, ``user``, ``table``). If you have tables or columns with these names, standard unquoted SQL queries will fail.

Enterprise solves this by **double-quoting all identifiers** in the generated SQL:

.. code-block:: sql

    -- Generated SQL example
    SELECT "account"."id", "account"."name" FROM "account" WHERE ("account"."name" = @account__name_1);

Parameter Suffix-Indexing
*************************

Many query builders suffer from parameter collision when the same field filter is applied multiple times with different values (e.g., querying for range checks or multiple conditions).

Enterprise addresses this through **suffix-indexed parameters**. Each parameter is named following the format:

.. code-block:: text

    @<tableName>__<columnName>_<uniqueIndex>

When compiling the where list, the engine checks the existing named arguments map and automatically increments the suffix index:

.. code-block:: go

    // Multiple filters on the same field
    list.Where(
        models.Or(
            list.IsCountEqual(100), // maps to @deneme__count_1
            list.IsCountEqual(200), // maps to @deneme__count_2
        ),
    )

This guarantees parameter uniqueness and prevents values from overwriting each other.

Identifier Validation
*********************

To prevent SQL injection via raw or dynamic column names, all identifiers and cast types are validated against a strict regex whitelist prior to SQL construction:

- Column/Table identifiers must match: ``^[a-z_][a-z0-9_]*$`` (or dotted format for relation tables).
- Cast types must exist in a predefined whitelist (e.g., ``text``, ``uuid``, ``integer``, ``jsonb``).
- If an invalid identifier is passed, Enterprise immediately panics rather than running a compromised query.
- For legitimate raw SQL expressions, you must wrap them explicitly using ``client.Raw("expr")``.
