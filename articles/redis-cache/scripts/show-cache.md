---
title: "Skriptexempel Azure CLI - hämta information för ett Azure Redis-Cache | Microsoft Docs"
description: "Skriptexempel Azure CLI - hämta information för ett Azure Redis-Cache"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 21db3c9d02c275ec8827062de5135631ed900da8
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="get-details-of-an-azure-redis-cache"></a>Läs en Azure Redis-Cache

Lär dig hur du hämtar information om Azure Redis-Cache-instans, inklusive dess Etableringsstatus i det här scenariot.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att hämta information om en Azure Redis-Cache-instans. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ redis visa](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Hämta information om Azure Redis-Cache-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure Redis-Cache CLI skriptexempel finns i den [dokumentation för Azure Redis-Cache](../cli-samples.md).