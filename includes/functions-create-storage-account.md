## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Funktioner använder ett allmänt konto i Azure Storage för att bevara tillstånd och annan information om dina funktioner. Skapa ett allmänt lagringskonto i resursgruppen du skapade med hjälp av [az storage-konto skapar](/cli/azure/storage/account#create) kommando.

Ersätta en globalt unik lagringskontonamnet där du ser i följande kommando i `<storage_name>` platshållare. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

När lagringskontot har skapats visas information som liknar följande exempel i Azure CLI:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```