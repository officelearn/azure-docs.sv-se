---
title: Ta bort ett Azure-cacheminne för Redis – Azure CLI
description: Detta exempel på Azure CLI-kod visar hur du tar bort en Azure-cache för Redis-instans med kommandot AZ Redis Delete.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: e5178ab4069cc5ffa8607c5feea6ffac86284a5b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75411085"
---
# <a name="delete-an-azure-cache-for-redis"></a>Ta bort ett Azure-cacheminne för Redis

I det här scenariot får du lära dig hur du tar bort en Azure-cache för Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att ta bort en Azure-cache för Redis-instansen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [ta bort AZ Redis](https://docs.microsoft.com/cli/azure/redis) | Ta bort Azure cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure-cache för Redis CLI-skript exempel finns i [Azure-cachen för Redis-dokumentationen](../cli-samples.md).
