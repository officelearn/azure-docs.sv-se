---
title: inkludera fil
description: inkludera fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 012800806aeff81939baa2cee88e78191e4fb6c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82195272"
---
### <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

Skapa en identitet med namnet *myACRTasksId* i din prenumeration med kommandot [AZ Identity Create][az-identity-create] . Du kan använda samma resurs grupp som du använde tidigare för att skapa ett behållar register, eller en annan.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Om du vill konfigurera den användarspecifika identiteten i följande steg, använder du kommandot [AZ Identity show][az-identity-show] för att lagra identitetens resurs-ID, huvud-ID och klient-ID i variabler.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show