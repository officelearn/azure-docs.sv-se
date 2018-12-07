---
title: Skriptexempel för Azure CLI – ta bort en Azure Cache för Redis | Microsoft Docs
description: Skriptexempel för Azure CLI – ta bort en Azure Cache för Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 8d8912b047004a86ad49d855f7b69a6ffaa293e0
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53020076"
---
# <a name="delete-an-azure-cache-for-redis"></a>Ta bort en Azure Cache för Redis

I det här scenariot kan du lära dig hur du tar bort en Azure Cache för Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet används följande kommandon för att ta bort en Azure-Cache för Redis-instans. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ redis delete](https://docs.microsoft.com/cli/azure/redis#az_redis_delete) | Ta bort Azure Cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure Cache för Redis-CLI-skriptexempel finns i den [Azure Cache för Redis-dokumentation](../cli-samples.md).