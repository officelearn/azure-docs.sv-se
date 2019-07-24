---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a2994602f857c2c8ff9f935b649a8d3e94c10dca
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444119"
---
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container som Azure-resurser (t.ex. funktionsappar, databaser och lagringskonton) distribueras och hanteras i.

I följande exempel skapas en resursgrupp med namnet `myResourceGroup`.  
Om du inte använder Cloud Shell loggar du först in med `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Du skapar vanligt vis resurs gruppen och resurserna i en [region](https://azure.microsoft.com/global-infrastructure/regions/) nära dig. 
