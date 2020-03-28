---
title: Skapa en Premium Azure-cache för Redis med klustring - Azure CLI
description: Det här Azure CLI-kodexemplet visar hur du skapar en 6 GB Premium-nivå Azure Cache för Redis med kluster aktiverat och två shards.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411151"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Skapa en Premium Azure-cache för Redis med klustring

I det här scenariot lär du dig hur du skapar en 6 GB Premium-nivå Azure Cache för Redis med klustring aktiverat och två shards.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en resursgrupp och en Azure-cache på Premium-nivå för Redis med klusteraktivering. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az redis skapa](https://docs.microsoft.com/cli/azure/redis) | Skapa Azure-cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure-cache för Redis CLI-skriptexempel finns i [Azure Cache for Redis-dokumentationen](../cli-samples.md).
