---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839233"
---
| Resurs | Gräns |
| --- | --- |
| Cachestorlek |1,2 TB |
| Databaser |64 |
| Maximalt antal anslutna klienter |40,000 |
| Azure cache för Redis-repliker för hög tillgänglighet |1 |
| Fragment i en premium-cache med klustring |10 |

Azure Redis-Cache begränsar och storlekar är olika för varje prisnivå. Om du vill se pris nivåerna och deras associerade storlekar, se [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/).

Mer information om Azure Cache Redis configuration gränser för finns i [Default Redis server configuration](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Eftersom konfiguration och hantering av Azure Cache för Redis-instanser görs av Microsoft, stöds inte alla Redis-kommandon i Azure Cache för Redis. Mer information finns i [Redis-kommandon som inte stöds i Azure Cache för Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

