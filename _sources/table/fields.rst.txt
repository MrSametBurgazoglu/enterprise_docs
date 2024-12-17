Supported Fields
==================

.. code-block:: text

   boolean
   byte
   uuid
   int
   uint
   float(32,64)
   string
   enum(string)
   time
   json


All db fields can take default value with ``Default(v type)``. Also you can set with a function with ``Default(v func() type)``
You can use ``SetDBName(string)`` to set dbname. With `SetNillable` you can make a field nillable.
