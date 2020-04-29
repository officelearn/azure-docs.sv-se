---
title: Hämta värdnamn, portar, nycklar – Azure cache för Redis – Azure CLI
description: Detta exempel på Azure CLI-kod visar hur du hämtar värdnamn, portar och nycklar för en Azure-cache för Redis-instansen.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75411306"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Hämta värdnamn, portar och nycklar för Azure cache för Redis

I det här scenariot får du lära dig hur du hämtar värdnamn, portar och nycklar som används för att ansluta till en Azure-cache för Redis-instansen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att hämta värdnamn, nycklar och portar för en Azure-cache för Redis-instans. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [AZ Redis show](https://docs.microsoft.com/cli/azure/redis) | Hämta information om en Azure-cache för Redis-instansen. |
| [AZ Redis-lista – nycklar](https://docs.microsoft.com/cli/azure/redis) | Hämta åtkomst nycklar för en Azure-cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure-cache för Redis CLI-skript exempel finns i [Azure-cachen för Redis-dokumentationen](../cli-samples.md).
