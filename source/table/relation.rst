DB Relations
==================

Enterprise supports three primary database relation types: **Many-to-Many**, **Many-to-One**, and **One-to-Many**. These relations are declared inside the schema files using the ``models`` relationship helpers, and the generator produces type-safe loading and association methods.

Many to Many
************

A Many-to-Many relationship connects two tables via a join table. For example, an ``Account`` can belong to multiple ``Group`` models, and a ``Group`` can contain multiple ``Account`` models.

Use ``models.ManyToMany`` to declare this in your schema files:

.. code-block:: go

    // models.ManyToMany(targetTable, sourceForeignKey, targetForeignKey, targetPrimaryKey, joinTableName)
    models.ManyToMany("Group", "account_id", "group_id", "id", "account_group")

`account` Schema Definition ``db_models/account.go``
----------------------------------------------------

.. code-block:: go

    package db_models

    import (
        "github.com/MrSametBurgazoglu/enterprise/models"
        "github.com/google/uuid"
    )

    func Account() *models.Table {
        idField := models.UUIDField("ID").DefaultFunc(uuid.New)

        tb := &models.Table{
            Fields: []models.FieldI{
                idField,
                models.StringField("Name"),
                models.StringField("Surname"),
            },
            Relations: []*models.Relation{
                models.ManyToMany("Group", "account_id", "group_id", "id", "account_group"),
            },
        }

        tb.SetTableName("account")
        tb.SetIDField(idField)

        return tb
    }

`group` Schema Definition ``db_models/group.go``
------------------------------------------------

.. code-block:: go

    package db_models

    import (
        "github.com/MrSametBurgazoglu/enterprise/models"
        "github.com/google/uuid"
    )

    func Group() *models.Table {
        idField := models.UUIDField("ID").DefaultFunc(uuid.New)

        tb := &models.Table{
            Fields: []models.FieldI{
                idField,
                models.StringField("Name"),
            },
            Relations: []*models.Relation{
                models.ManyToMany("Account", "group_id", "account_id", "id", "account_group"),
            },
        }

        tb.SetTableName("group")
        tb.SetIDField(idField)

        return tb
    }

Generated Many-to-Many Methods
------------------------------

For Many-to-Many relations, Enterprise generates helper methods to manage row links in the join table:

.. code-block:: go

    // On Account model
    func (t *Account) AddIntoGroup(relationship *Group) error
    func (t *Account) RemoveFromGroup(relationship *Group) error
    func (t *Account) IsInGroup(relationship *Group) (bool, error)

    // On Group model
    func (t *Group) AddIntoAccount(relationship *Account) error
    func (t *Group) RemoveFromAccount(relationship *Account) error
    func (t *Group) IsInAccount(relationship *Account) (bool, error)

Many to One & One to Many
*************************

A Many-to-One / One-to-Many relationship links one table to another via a foreign key on the source table. For example, an ``Account`` belongs to a ``Deneme`` (Many-to-One), and a ``Deneme`` has many ``Account`` records (One-to-Many).

- ``models.ManyToOne``: Used on the table containing the foreign key.
- ``models.OneToMany``: Used on the target table.

`account` Schema Definition (Many-to-One)
-----------------------------------------

.. code-block:: go

    // models.ManyToOne(targetTable, targetPrimaryKey, sourceForeignKey)
    models.ManyToOne("Deneme", "id", "deneme_id")

`deneme` Schema Definition (One-to-Many)
----------------------------------------

.. code-block:: go

    // models.OneToMany(targetTable, sourcePrimaryKey, targetForeignKey)
    models.OneToMany("Account", "id", "deneme_id")

Generated Relation Loading & Eager Filtering
********************************************

For loading relations, Enterprise generates eager loading methods. You can pass inline callback functions to filter the loaded relation rows with a single SQL query.

.. code-block:: go

    account := models.NewAccount(ctx, db)
    account.Where(account.IsIDEqual(targetID))

    // Eagerly load the related Deneme model, filtering it inline
    account.WithDeneme(func(d *models.Deneme) {
        d.Where(d.IsActiveEqual(true))
    })

    err = account.Get()
    if err == nil {
        // Access the loaded relation
        if account.Deneme != nil {
            fmt.Println("Deneme Count:", account.Deneme.GetCount())
        }
    }

Similarly, you can load a list of related records:

.. code-block:: go

    deneme := models.NewDeneme(ctx, db)
    deneme.Where(deneme.IsIDEqual(denemeID))

    // Eagerly load the related accounts list
    deneme.WithAccountList(func(al *models.AccountList) {
        al.Where(al.IsSurnameEqual("Smith"))
    })

    err = deneme.Get()
    if err == nil {
        for _, acc := range deneme.AccountList.Items {
            fmt.Println("Related Account Name:", acc.GetName())
        }
    }