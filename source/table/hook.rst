How to Hook Database Operations
===============================

Enterprise provides an advanced hooks system that intercepts all database mutations (inserts, updates, deletes, retrieves, and listings). This lets you inject validation logic, modify entity fields before saving, implement auditing, or trigger side effects.

Registering Hooks
*****************

Enterprise generates package-level hook functions and registration setters for every model. Hooks are executed around the actual SQL operation.

A hook function receives:
1. The request ``context.Context``
2. An ``*client.OperationInfo`` containing the table name and the type of operation (Create, Update, Delete, etc.)
3. The model instance being mutated
4. An `operationFunc` callback representing the database action. You **must** call this function to commit the action, or return an error to abort it.

Registering a Single Entity Hook
--------------------------------

For a model named ``Account``, use ``SetDatabaseAccountOperationHook``:

.. code-block:: go

    package main

    import (
        "context"
        "errors"
        "log"
        "time"

        "github.com/MrSametBurgazoglu/enterprise/client"
        "your/project/models"
    )

    func main() {
        // Register the hook at application startup
        models.SetDatabaseAccountOperationHook(func(
            ctx context.Context,
            opInfo *client.OperationInfo,
            model *models.Account,
            operationFunc func() error,
        ) error {
            log.Printf("Executing %s on table %s", opInfo.OperationType, opInfo.TableName)

            // 1. Pre-execution logic: Validation
            if opInfo.OperationType == client.OperationTypeUpdate || opInfo.OperationType == client.OperationTypeCreate {
                if model.GetName() == "" {
                    return errors.New("account name cannot be empty")
                }
            }

            // 2. Pre-execution logic: Modify fields (e.g. Audit Fields)
            // If you have a last_modified timestamp, you can set it here
            
            // 3. Execute the actual SQL query
            err := operationFunc()
            if err != nil {
                log.Printf("Database operation failed: %v", err)
                return err
            }

            // 4. Post-execution logic: Auditing or side-effects
            log.Printf("Successfully completed %s", opInfo.OperationType)
            return nil
        })
    }

Registering a List Hook
-----------------------

To hook operations performed on model slices (such as bulk creates, bulk deletes, or list fetches), register a list hook using ``SetDatabase[ModelName]ListOperationHook``:

.. code-block:: go

    models.SetDatabaseAccountListOperationHook(func(
        ctx context.Context,
        opInfo *client.OperationInfo,
        modelList *models.AccountList,
        operationFunc func() error,
    ) error {
        // Intercept bulk insertions
        if opInfo.OperationType == client.OperationTypeBulkCreate {
            for _, acc := range modelList.Items {
                if acc.GetSurname() == "" {
                    return errors.New("all bulk-inserted accounts must have a surname")
                }
            }
        }

        return operationFunc()
    })

Use Cases for Hooks
*******************

1. **Auto-populating UUIDs or Timestamps**:
   You can auto-populate creation/modification timestamps when calling `Create` or `Update`.
2. **Field Encryption/Hashing**:
   Encrypt or hash sensitive data (such as user passwords) before inserting them into the database.
3. **Soft Deletes**:
   Change a `Delete` operation into an `Update` operation that toggles an `is_deleted` flag (by returning custom updates and omitting the original `operationFunc` call).
4. **Data Normalization**:
   Lowercase email addresses or trim trailing spaces on string columns before writing.