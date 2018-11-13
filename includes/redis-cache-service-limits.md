---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 01d6d933474d4a9c1e428b8df89fc766c9b40f20
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572759"
---
| Resurs | Gräns |
| --- | --- |
| Cachestorlek |530 GB |
| Databaser |64 |
| Högsta antal anslutna klienter |40,000 |
| Redis Cache-repliker (för hög tillgänglighet) |1 |
| Fragment i en premium-cache med klustring |10 |

Azure Redis Cache begränsar och storlekar är olika för varje prisnivå. Prisnivåerna och deras associerade storlekar finns i [priser för Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/).

Mer information om gränser för konfiguration av Azure Redis Cache finns i [Default Redis server configuration](../articles/redis-cache/cache-configure.md#default-redis-server-configuration).

Eftersom konfiguration och hantering av Azure Redis Cache-instanser görs av Microsoft, stöds inte alla Redis-kommandon i Azure Redis Cache. Mer information finns i [Redis-kommandon som inte stöds i Azure Redis Cache](../articles/redis-cache/cache-configure.md#redis-commands-not-supported-in-azure-redis-cache).

