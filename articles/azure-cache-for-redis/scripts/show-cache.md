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
ms.openlocfilehash: 7bcdd999a4954766398800e6e6a0ddb8c9727a99
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749167"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Hämta information om Azure Cache för Redis

I det här scenariot kan du lära dig hur du hämtar information om Azure Cache för Redis-instans, inklusive dess Etableringsstatus.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet används följande kommandon för att hämta information om Azure Cache för Redis-instans. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis) | Hämta information för Azure Cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure Cache för Redis-CLI-skriptexempel finns i den [Azure Cache för Redis-dokumentation](../cli-samples.md).
