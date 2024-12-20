How to hook Database Operations
==================

A general hook function looks like this. When you use `create,get,refresh,update,delete` operations on `Account` this function will be executed.

.. code-block:: golang

    var databaseAccountOperationHook = func(operationInfo *client.OperationInfo, model *Account, operationFunc func() error) error {
        return operationFunc()
    }

You can set this operation hook by function below. Your hook function can get operation info, db model itself and operation function will be executed.

.. code-block:: golang

    func SetDatabaseAccountOperationHook(f func(operationInfo *client.OperationInfo, model *Account, operationFunc func() error) error) {
       databaseAccountOperationHook = f
    }

Let's say we don't want our account models have empty name and surname before updated. We can put a check on hook to control update function.

.. code-block:: golang

    func HookAccount(operationInfo *client.OperationInfo, model *Account, operationFunc func() error) error {
        switch operationInfo.OperationType{
            case client.OperationType.Update:
                if model.GetName() == "" || model.GetSurName() == ""{
                    return errors.New("Account name and surname can't be empty")
                }
        }
    }

For List models you can use below functions.

.. code-block:: golang

    var databaseAccountListOperationHook = func(operationInfo *client.OperationInfo, model *AccountList, operationFunc func() error) error {
        return operationFunc()
    }

    func SetDatabaseAccountListOperationHook(f func(operationInfo *client.OperationInfo, model *AccountList, operationFunc func() error) error) {
       databaseAccountListOperationHook = f
    }