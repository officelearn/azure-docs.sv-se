---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 8a9322862d63d856f96729e5784b88e0ef098fb6
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53018500"
---
| Resurs | Gräns |
| --- | --- |
| Cachestorlek |530 GB |
| Databaser |64 |
| Högsta antal anslutna klienter |40,000 |
| Azure Cache för Redis-repliker (för hög tillgänglighet) |1 |
| Fragment i en premium-cache med klustring |10 |

Azure Azure Cache för Redis begränsar och storlekar är olika för varje prisnivå. Prisnivåerna och deras associerade storlekar finns i [Azure Azure Cache Redis priser](https://azure.microsoft.com/pricing/details/cache/).

Mer information om Azure Cache för Azure Redis configuration gränser för finns i [Default Redis server configuration](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Eftersom konfiguration och hantering av Azure Azure Cache för Redis-instanser görs av Microsoft, stöds inte alla Redis-kommandon i Azure Azure Cache för Redis. Mer information finns i [Redis-kommandon som inte stöds i Azure Azure Cache för Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

