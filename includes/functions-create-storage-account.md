---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 25cfcefb600bc12de3dad5b6fe2bcb76d00f0198
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444134"
---
## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

I funktioner används ett konto för generell användning i Azure Storage till att lagra status och annan information om dina funktioner. Skapa ett lagringskonto för generell användning i den resursgrupp som du skapade med hjälp av kommandot [az storage account create](/cli/azure/storage/account).

I följande kommando infogar du ett globalt unikt lagringskontonamn i stället för platshållaren `<storage_name>`. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
