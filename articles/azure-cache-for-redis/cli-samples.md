---
title: Hantera Azure cache för Redis med Azure CLI
description: 'Azure CLI-exempel för hantering av Azure cache för Redis: skapa en cache, ta bort en cache, Hämta cache-information, värdnamn, portar och nycklar, och Anslut en webbapp.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.openlocfilehash: 345fd0272e0cfe9b4d7329f004e628ea7820c2d9
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421248"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Hantera Azure cache för Redis med Azure CLI

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| Skapa ett cacheminne | Description |
| ------------ | ----------- |
| [Skapa en cache](./scripts/create-cache.md) | Skapar en resurs grupp och en Basic-nivå i Azure cache för Redis. |
| [Skapa en Premium-cache med klustring](./scripts/create-premium-cache-cluster.md) | Skapar en resurs grupp och en cache på Premium-nivå med klustrad aktive rad.|
| [Hämta cache-information](./scripts/show-cache.md) | Hämtar information om en Azure-cache för Redis-instans, inklusive etablerings status. |
| [Hämta värdnamn, portar och nycklar](./scripts/cache-keys-ports.md) | Hämtar värdnamn, portar och nycklar för en Azure-cache för Redis-instansen. |
|**Webbapp plus cache**| **Beskrivning**|
| [Ansluta en webbapp till en Azure Cache for Redis](./../app-service/scripts/cli-connect-to-redis.md) | Skapar en Azure-webbapp och en Azure-cache för Redis och lägger sedan till Redis-anslutningsfel i appinställningar. |
|**Ta bort cache**| **Beskrivning** |
| [Ta bort en cache](./scripts/delete-cache.md) | Tar bort en Azure-cache för Redis-instans  |

Mer information om Azure CLI finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och [komma igång med Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
