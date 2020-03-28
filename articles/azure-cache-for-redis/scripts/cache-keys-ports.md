---
title: Hämta värdnamn, portar, nycklar - Azure Cache för Redis - Azure CLI
description: Det här Azure CLI-kodexemplet visar hur du hämtar värdnamn, portar och nycklar för en Azure-cache för Redis-instans.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411306"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Hämta värdnamn, portar och nycklar för Azure Cache för Redis

I det här fallet får du lära dig hur du hämtar värdnamn, portar och nycklar som används för att ansluta till en Azure Cache för Redis-instans.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att hämta värdnamn, nycklar och portar för en Azure Cache för Redis-instans. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az redis visa](https://docs.microsoft.com/cli/azure/redis) | Hämta information om en Azure-cache för Redis-instans. |
| [az redis list-nycklar](https://docs.microsoft.com/cli/azure/redis) | Hämta åtkomstnycklar för en Azure-cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure-cache för Redis CLI-skriptexempel finns i [Azure Cache for Redis-dokumentationen](../cli-samples.md).
