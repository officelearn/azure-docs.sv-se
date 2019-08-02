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
ms.openlocfilehash: 5cca18b881250ce99df35d681bec6091ea4a27b9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642103"
---
I kommandots utdata `identity` visar avsnittet en identitet av typen `SystemAssigned` som anges i uppgiften. `principalId` Är tjänstens huvud namn-ID för identiteten:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 
Använd kommandot [AZ ACR Task show][az-acr-task-show] för att lagra principalId i en variabel som ska användas i senare kommandon:

```azurecli
principalID=$(az acr task show --name dockerhubtask --registry myregistry --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
