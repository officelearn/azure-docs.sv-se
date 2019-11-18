---
title: Skript exempel för Azure CLI – Hämta värdnamn, portar och nycklar för Azure cache för Redis
description: Azure CLI-skript exempel – hämta värdnamn, portar och nycklar för Azure Cache för Redis-instans
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 604ec6de3b95a4bc289176d54d9c7b0a6c42eae6
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122512"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Hämta värdnamn, portar och nycklar för Azure Cache för Redis

Lär dig hur du hämtar den värdnamn, portar och nycklar som används för att ansluta till en Azure-Cache för Redis-instans i det här scenariot.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet används följande kommandon för att hämta värdnamn, nycklar och portar för Azure Cache för Redis-instans. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis) | Hämta information för Azure Cache för Redis-instans. |
| [AZ redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Hämta åtkomstnycklar för Azure Cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure Cache för Redis-CLI-skriptexempel finns i den [Azure Cache för Redis-dokumentation](../cli-samples.md).
