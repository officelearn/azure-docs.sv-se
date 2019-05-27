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
ms.openlocfilehash: c70e2c166bee14ac58ee88bd18baf0cc61702767
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66137338"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Skapa i Cloud Shell en resursgrupp med kommandot [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *Europa, västra*. Om du vill se alla platser som stöds för App Service på Linux på **Basic**-nivån kör du kommandot [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Du skapar vanligtvis din resursgrupp och resurserna i en region nära dig. 

När kommandot har slutförts visar JSON-utdata resursgruppens egenskaper.