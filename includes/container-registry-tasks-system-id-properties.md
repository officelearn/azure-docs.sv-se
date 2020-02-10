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
ms.openlocfilehash: 700dbfde3be2f24eb57acbdeb9d2841ef2bdfe44
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112339"
---
I kommandots utdata visar `identity` avsnittet en identitet av typen `SystemAssigned` anges i uppgiften. `principalId` är huvud-ID: t för uppgifts identiteten:

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
Använd kommandot [AZ ACR Task show][az-acr-task-show] för att lagra principalId i en variabel som ska användas i senare kommandon. Ersätt namnet på uppgiften och registret i följande kommando:

```azurecli
principalID=$(az acr task show --name mytask --registry myregistry --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
