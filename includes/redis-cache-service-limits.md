---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71839233"
---
| Resurs | Gräns |
| --- | --- |
| Cachestorlek |1,2 TB |
| Databaser |64 |
| Maximalt anslutna klienter |40 000 |
| Azure Cache för Redis-repliker, för hög tillgänglighet |1 |
| Shards i en premiumcache med klustring |10 |

Azure Cache för Redis-gränser och storlekar är olika för varje prisnivå. Information om hur du ser prisnivåerna och deras associerade storlekar finns i [Azure Cache for Redis pricing](https://azure.microsoft.com/pricing/details/cache/).

Mer information om Azure Cache for Redis-konfigurationsgränser finns i [Standardkonfiguration för Redis-server](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Eftersom konfiguration och hantering av Azure Cache för Redis-instanser görs av Microsoft stöds inte alla Redis-kommandon i Azure Cache för Redis. Mer information finns i [Redis-kommandon som inte stöds i Azure Cache för Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

