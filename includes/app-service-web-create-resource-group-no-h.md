---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8d0367b4e087da5954e9b16828655625978634fb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
[!INCLUDE [resource group intro text](resource-group.md)]

Skapa i Cloud Shell en resursgrupp med kommandot [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *Europa, västra*. Om du vill se alla platser som stöds för App Service kör du kommandot [`az appservice list-locations`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Du skapar vanligtvis din resursgrupp och resurserna i en region nära dig. 

När kommandot har slutförts visar JSON-utdata resursgruppens egenskaper.