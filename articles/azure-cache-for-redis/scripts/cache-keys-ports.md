---
title: Azure CLI-skript exempel – hämta värdnamn, portar och nycklar för Azure Cache för Redis | Microsoft Docs
description: Azure CLI-skript exempel – hämta värdnamn, portar och nycklar för Azure Cache för Redis-instans
services: azure-cache-for-redis
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
ms.openlocfilehash: 820161b5ea5c1d071292897cab30f921f1256cf3
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019932"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Hämta värdnamn, portar och nycklar för Azure Cache för Redis

Lär dig hur du hämtar den värdnamn, portar och nycklar som används för att ansluta till en Azure-Cache för Redis-instans i det här scenariot.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Azure Cache for Redis")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet används följande kommandon för att hämta värdnamn, nycklar och portar för Azure Cache för Redis-instans. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Hämta information för Azure Cache för Redis-instans. |
| [AZ redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Hämta åtkomstnycklar för Azure Cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure Cache för Redis-CLI-skriptexempel finns i den [Azure Cache för Redis-dokumentation](../cli-samples.md).