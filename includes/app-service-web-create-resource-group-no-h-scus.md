---
title: ta med fil
description: ta med fil
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 458782011623721bb44771d42caa32130e23bbc9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187830"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Skapa i Cloud Shell en resursgrupp med kommandot [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *South Central US* (Södra centrala USA). Om du vill se alla platser som stöds för App Service på **Kostnadsfri** nivå, kör du kommandot [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Du skapar vanligtvis din resursgrupp och resurserna i en region nära dig. 

När kommandot har slutförts visar JSON-utdata resursgruppens egenskaper.