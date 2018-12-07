---
title: Skriptexempel för Azure CLI – hämta information om Azure Cache för Redis | Microsoft Docs
description: Skriptexempel för Azure CLI – hämta information om Azure Cache för Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 94786a17589cdae93ffa498eaaeeb1c1a81ffaf4
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53020079"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Hämta information om Azure Cache för Redis

I det här scenariot kan du lära dig hur du hämtar information om Azure Cache för Redis-instans, inklusive dess Etableringsstatus.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Azure Cache for Redis")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet används följande kommandon för att hämta information om Azure Cache för Redis-instans. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Hämta information för Azure Cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure Cache för Redis-CLI-skriptexempel finns i den [Azure Cache för Redis-dokumentation](../cli-samples.md).