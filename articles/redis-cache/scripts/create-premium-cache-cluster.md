---
title: Azure CLI-skript Sample - skapa en Premium Azure Redis-Cache med kluster | Microsoft Docs
description: "Azure CLI-skript Sample - skapa en nivå för Premium Azure Redis-Cache med kluster"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.openlocfilehash: 45b2a500751830f6ca19fe8450a7781cb0fcb618
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-premium-azure-redis-cache-with-clustering"></a>Skapa en Premium Azure Redis-Cache med kluster

I detta scenario dig du hur du skapar en 6 GB premiumnivån Azure Redis-Cache med aktiverad klustring och två delar.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp och Premium-nivån redis-cache med aktivera-kluster. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ redis](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Skapa Redis-Cache-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure Redis-Cache CLI skriptexempel finns i den [dokumentation för Azure Redis-Cache](../cli-samples.md).