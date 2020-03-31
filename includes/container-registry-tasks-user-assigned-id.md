---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ceda7bd6bd165df1eece555c6ce8a9a6c863b2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112275"
---
### <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

Skapa en identitet med namnet *myACRTasksId* i din prenumeration med kommandot [az identity create.][az-identity-create] Du kan använda samma resursgrupp som du använde tidigare för att skapa ett behållarregister eller ett annat.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Om du vill konfigurera den användartilldelade identiteten i följande steg använder du kommandot [az identity show][az-identity-show] för att lagra identitetens resurs-ID, huvud-ID och klient-ID i variabler.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show