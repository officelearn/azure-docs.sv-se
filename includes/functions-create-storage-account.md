---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203159"
---
## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

I funktioner används ett konto för generell användning i Azure Storage till att lagra status och annan information om dina funktioner. Skapa ett lagringskonto för generell användning i den resursgrupp som du skapade med hjälp av kommandot [az storage account create](/cli/azure/storage/account#az-storage-account-create).

I följande kommando infogar du ett globalt unikt lagringskontonamn i stället för platshållaren `<storage_name>`. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
