---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "67187747"
---
| Resurs | Gräns |
| --- | --- |
| Cachestorlek |530 GB |
| Databaser |64 |
| Maximalt antal anslutna klienter |40,000 |
| Azure cache för Redis-repliker för hög tillgänglighet |1 |
| Fragment i en premium-cache med klustring |10 |

Azure Redis-Cache begränsar och storlekar är olika för varje prisnivå. Om du vill se pris nivåerna och deras associerade storlekar, se [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/).

Mer information om Azure Cache Redis configuration gränser för finns i [Default Redis server configuration](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Eftersom konfiguration och hantering av Azure Cache för Redis-instanser görs av Microsoft, stöds inte alla Redis-kommandon i Azure Cache för Redis. Mer information finns i [Redis-kommandon som inte stöds i Azure Cache för Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

