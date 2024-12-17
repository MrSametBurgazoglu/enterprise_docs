.. Enterprise documentation master file, created by
   sphinx-quickstart on Sun Dec 15 00:24:54 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Enterprise documentation
========================

Enterprise aka Atılgan is a new ORM for postgresql databases. For speed and lightness.


Features
-------------


#. A small number of auto generated database model structs and methods
#. Create, Get, Update, Delete and List functions on table
#. It can load Relation tables and filter them with single query
#. Developer friendly helper methods on models
#. Simple aggregate functionality
#. Simple transaction commit and rollback
#. Customizable hooks
#. Atlas migrations
#. Unique Relation system
#. Fastest ORM in golang for postgresql


.. toctree::
   :maxdepth: 1
   :caption: Getting Started

   installation
   crud
   generating_models
   migration
   benchmark

.. toctree::
   :maxdepth: 1
   :caption: Table

   table/transform.rst
   table/relation.rst
   table/fields.rst

.. toctree::
   :maxdepth: 1
   :caption: CRUD

   crud/create.rst
   crud/retrieve.rst
   crud/update.rst
   crud/delete.rst
   crud/list.rst
   crud/refresh.rst

.. toctree::
   :maxdepth: 1
   :caption: DB Fields

   fields/id.rst
   fields/string.rst
   fields/integer.rst
   fields/boolean.rst
   fields/enum.rst
   fields/json.rst
   fields/nillable.rst
   fields/default.rst
   fields/validators.rst
   fields/custom.rst

.. toctree::
   :maxdepth: 1
   :caption: Operation Hooks

   hooks/hook.rst
   hooks/hook_on_crud.rst
   hooks/hook_on_list.rst

.. toctree::
   :maxdepth: 1
   :caption: Advanced Filters

   filter/advanced_filter.rst
   filter/relation.rst

.. toctree::
   :maxdepth: 1
   :caption: Enterprise Postgresql (Pgx)

   postgresql/postgre.rst

.. toctree::
   :maxdepth: 1
   :caption: How Enterprise Works

   how/how_to_execute.rst
   how/how_to_filter.rst
   how/pgx.rst
   how/atlas.rst