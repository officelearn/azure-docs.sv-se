---
title: Skriptexempel Azure CLI - Get värdnamnet, portarna och nycklar för Azure Redis-Cache | Microsoft Docs
description: Skriptexempel Azure CLI - Get värdnamnet, portarna och nycklar för ett Azure Redis-Cache-instans
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 9a2a6a7ed4bb56f93c965a5bfd24b21368117c7b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846459"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Hämta värdnamnet, portarna och nycklarna för Azure Redis-Cache

I detta scenario dig du att hämta värdnamnet, portarna och nycklarna som används för att ansluta till en Azure Redis-Cache-instans.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis Cache")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att hämta värdnamnet, nycklar och portar för en Azure Redis-Cache-instans. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ redis visa](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Hämta information om Azure Redis-Cache-instans. |
| [AZ redis lista nycklar](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Hämta åtkomstnycklarna för en Azure Redis-Cache-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure Redis-Cache CLI skriptexempel finns i den [dokumentation för Azure Redis-Cache](../cli-samples.md).