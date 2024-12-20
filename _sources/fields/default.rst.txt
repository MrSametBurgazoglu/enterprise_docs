Set Default Value to Field
====================================

You can set default value or function to give default value. For example for boolean field you can use functions below.

.. code-block:: golang

    func (i *BoolDBField) Default(v bool) *BoolDBField
    func (i *BoolDBField) DefaultFunc(v func() bool) *BoolDBField