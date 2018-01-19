---
title: "Skriptexempel Azure CLI - Get värdnamnet, portarna och nycklar för Azure Redis-Cache | Microsoft Docs"
description: "Skriptexempel Azure CLI - Get värdnamnet, portarna och nycklar för ett Azure Redis-Cache-instans"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 6cfff74ed037835abb6d3e3975fe33d71b9730a3
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Hämta värdnamnet, portarna och nycklarna för Azure Redis-Cache

I detta scenario dig du att hämta värdnamnet, portarna och nycklarna som används för att ansluta till en Azure Redis-Cache-instans.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis Cache")]


## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att hämta värdnamnet, nycklar och portar för en Azure Redis-Cache-instans. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ redis visa](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Hämta information om Azure Redis-Cache-instans. |
| [AZ redis lista nycklar](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Hämta åtkomstnycklarna för en Azure Redis-Cache-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure Redis-Cache CLI skriptexempel finns i den [dokumentation för Azure Redis-Cache](../cli-samples.md).