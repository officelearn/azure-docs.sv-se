---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: cf8c6f07eb38487dd29624b15be3637536be92fc
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133565"
---
## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

I funktioner används ett konto för generell användning i Azure Storage till att lagra status och annan information om dina funktioner. Skapa ett lagringskonto för generell användning i den resursgrupp som du skapade med hjälp av kommandot [az storage account create](/cli/azure/storage/account#create).

I följande kommando infogar du ett globalt unikt lagringskontonamn i stället för platshållaren `<storage_name>`. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.

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