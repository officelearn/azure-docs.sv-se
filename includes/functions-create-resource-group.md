---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c20f86fe7fdcfc7ecc940923a8c98fa1fbf4cf65
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187119"
---
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container som Azure-resurser (t.ex. funktionsappar, databaser och lagringskonton) distribueras och hanteras i.

I följande exempel skapas en resursgrupp med namnet `myResourceGroup`.  
Om du inte använder Cloud Shell loggar du först in med `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Du skapar vanligtvis din resursgrupp och resurserna i en region nära dig. Om du vill se alla platser som stöds för App Service-planer, kör du kommandot [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations).
