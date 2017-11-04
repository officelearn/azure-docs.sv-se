---
title: Azure CLI Redis cache-exempel | Microsoft Docs
description: "Azure CLI-exempel för Azure Redis-Cache."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: sdanie
ms.openlocfilehash: a0f3b294f2a655a5ff891d4fd1be9137080349a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Azure CLI-exempel för Azure Redis-Cache

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|---|---|
|**Skapa cachen**||
| [Skapa ett cacheminne](./scripts/create-cache.md) | Skapar en resursgrupp och en grundläggande nivå Azure Redis-Cache. |
| [Skapa en premium-cache med kluster](./scripts/create-premium-cache-cluster.md) | Skapar en resursgrupp och premium-nivån cache med aktiverad klustring.|
| [Hämta information om cache](./scripts/show-cache.md) | Hämtar information om Azure Redis-Cache-instans, inklusive Etableringsstatus. |
| [Hämta värdnamnet, portarna och nycklarna](./scripts/cache-keys-ports.md) | Hämtar värdnamnet, portarna och nycklarna för en Azure Redis-Cache-instans. |
|**Webbprogrammet plus cache**||
| [Ansluta en webbapp till ett redis-cache](./../app-service/scripts/app-service-cli-app-service-redis.md) | Skapar ett Azure webbapp och ett redis-cache sedan lägger till redis-anslutningsinformation till app-inställningar. |
|**Ta bort cache**||
| [Ta bort en cache](./scripts/delete-cache.md) | Tar bort en instans av Azure Redis-Cache  |
| | |

Läs mer om Azure CLI 2.0 [installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) och [Kom igång med Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).