DB Relations
==================


Many to Many
==================

Let's create connection between Account and Group. An account could be inside more than one group and a group can have more than one account.
Enterprise will create a connection table with integer id and both table's primary keys.

``models.ManyToMany(GroupName, "account_id", "group_id", "id", AccountGroupName),``

#. first variable is name of the other table we want to connect.
#. second variable is our first table's foreign key column name.
#. third variable is our second table's foreign key column name.
#. fourth variable is primary key of second table which is group.
#. And the last variable is connection table's name which connect account and group.

`account` ``your/project/db_models/account.go``

.. code-block:: golang

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
                models.UUIDField("DenemeID").SetNillable(),
                models.UintField("Serial").AddSerial(),
            },
            Relations: []*models.Relation{
                models.ManyToMany(GroupName, "account_id", "group_id", "id", AccountGroupName),
            },
        }

        tb.SetTableName(AccountName)
        tb.SetIDField(idField)

        return tb
    }

You can use the following functions for many-to-many relations.

.. code-block:: golang

    func (t *Account) AddIntoGroup(relationship *Group) error
    func (t *Account) RemoveFromGroup(relationship *Group) error
    func (t *Account) IsInGroup(relationship *Group) (bool, error
