Generating Models
==================

First create a package named db_models. Create one file for every table. The function name and file name should match.
After write your models. We recommend to create a new package with name generate and put a generate.go for generating models you want.
It will go to create a models package and write files in to it.

An example db table ``your/project/db_models/account.go``

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
                models.ManyToOne(DenemeName, idField.DBName, "deneme_id"),
                models.ManyToMany(GroupName, "account_id", "group_id", "id", AccountGroupName),
            },
        }

        tb.SetTableName(AccountName)
        tb.SetIDField(idField)
        tb.AddIndex("name_surname_index", "Name", "Surname")

        return tb
    }

An example auto-generator table ``your/project/generate/generate.go``
When this executed it will create a new package in current directory called models and put your generated db models in it.

.. code-block:: golang

    package main

    import (
       "github.com/MrSametBurgazoglu/enterprise/generate"
       "your/project/db_models"
    )

    func main() {
        generate.Models(
            db_models.Account(),
            db_models.Group(),
        )
    }
