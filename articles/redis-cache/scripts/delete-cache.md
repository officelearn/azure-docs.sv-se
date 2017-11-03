---
title: Azure CLI skriptexempel - ta bort en Azure Redis-Cache | Microsoft Docs
description: Azure CLI skriptexempel - ta bort en Azure Redis-Cache
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.openlocfilehash: 11d228cc633fee8c6a2984e2eb23690a43d4c0fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="delete-an-azure-redis-cache"></a>Ta bort en Azure Redis-Cache

Lär dig hur du tar bort en Azure Redis-Cache i det här scenariot.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att ta bort en Azure Redis-Cache-instans. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [ta bort AZ-redis](https://docs.microsoft.com/cli/azure/redis#az_redis_delete) | Ta bort Redis-Cache-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure Redis-Cache CLI skriptexempel finns i den [dokumentation för Azure Redis-Cache](../cli-samples.md).