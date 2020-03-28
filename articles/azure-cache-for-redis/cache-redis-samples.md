---
title: Exempel för Azure Cache for Redis
description: 'Lär dig hur du använder Azure Cache för Redis med dessa kodexempel: ansluta till en cache, läsa och skriva data i en cache ASP.NET Azure Cache för Redis-providers.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 8057a7db3d1a2db0e51ec15e4966ed0b31c84226
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75433411"
---
# <a name="azure-cache-for-redis-samples"></a>Exempel för Azure Cache for Redis
Det här avsnittet innehåller en lista över Azure Cache for Redis-exempel som omfattar scenarier som anslutning till en cache, läsning och skrivning av data till och från en cache och med hjälp av ASP.NET Azure Cache för Redis-providers. Några av exemplen är nedladdningsbara projekt, och vissa ger steg-för-steg-vägledning och innehåller kodavsnitt men länkar inte till ett nedladdningsbart projekt.

## <a name="hello-world-samples"></a>Hej världen prover
Exemplen i det här avsnittet visar grunderna för att ansluta till en Azure Cache för Redis-instans och läsa och skriva data till cachen med hjälp av en mängd olika språk och Redis-klienter.

Exemplet [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) visar hur du utför olika cacheåtgärder med [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET-klienten.

Det här exemplet visar hur du:

* Använda olika anslutningsalternativ
* Läsa och skriva objekt till och från cacheminnet med synkron och asynkrona åtgärder
* Använda Kommandona Redis MGET/MSET för att returnera värden för angivna nycklar
* Utföra Redis-transaktionsåtgärder
* Arbeta med Redis-listor och sorterade uppsättningar
* Lagra .NET-objekt med JsonConvert-serialiserare
* Använda Redis-uppsättningar för att implementera taggning
* Arbeta med Redis-kluster

Mer information finns i [StackExchange.Redis-dokumentationen](https://github.com/StackExchange/StackExchange.Redis) på GitHub och för fler användningsscenarier finns i [stackexchange.redis.tests-enhetstesterna.](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests)

[Hur du använder Azure Cache för Redis med Python](cache-python-get-started.md) visar hur du kommer igång med Azure Cache för Redis med Python och [redis-py-klienten.](https://github.com/andymccurdy/redis-py)

[Arbeta med .NET-objekt i cachen](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) visar ett sätt att serialisera .NET-objekt så att du kan skriva dem till och läsa dem från en Azure Cache för Redis-instans. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Använd Azure Cache för Redis som ett utskalningsstödplan för ASP.NET SignalR
Med exemplet [Använd Azure Cache för Redis som ett utskalningsstödplan för ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) visar hur du kan använda Azure Cache for Redis som ett SignalR-backplane. Mer information om backplane finns i [SignalR Scaleout med Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Exempel på Azure Cache för Redis-kundfrågor
Det här exemplet visar jämför prestanda mellan åtkomst till data från en cache och åtkomst till data från lagring av persistens. Det här exemplet har två projekt.

* [Demo hur Azure Cache för Redis kan förbättra prestanda genom att cachelagra data](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Dirigera databasen och cachen för demon](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET sessionstillstånd och cachelagring av utdata
Med exemplet [Använd Azure Cache för Redis för att lagra ASP.NET SessionState och OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) visar hur du använder Azure Cache för Redis för att lagra ASP.NET sessions- och utdatacachen med hjälp av SessionState- och OutputCache-providers för Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Hantera Azure-cache för Redis med MAML
Exemplet [Hantera Azure Cache för Redis med hjälp av Azure Management Libraries](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) visar hur kan du använda Azure Management Libraries för att hantera - (Skapa/ Uppdatera/ ta bort) din cache. 

## <a name="custom-monitoring-sample"></a>Exempel på anpassad övervakning
[Exemplet Access Azure Cache for Redis Monitoring](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) visar hur du kan komma åt övervakningsdata för din Azure-cache för Redis utanför Azure Portal.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>En Twitter-stil klon skriven med PHP och Redis
[Retwis-provet](https://github.com/SyntaxC4-MSFT/retwis) är Redis Hello World. Det är en minimal Twitter-stil sociala nätverk klon skriven med Redis och PHP med [Predis](https://github.com/nrk/predis) klient. Källkoden är utformad för att vara mycket enkel och samtidigt för att visa olika Redis-datastrukturer.

## <a name="bandwidth-monitor"></a>Bandbreddsövervakare
Med exemplet [på bandbreddsövervakaren](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) kan du övervaka den bandbredd som används på klienten. Om du vill mäta bandbredden kör du exemplet på cacheklientdatorn, ringer anrop till cachen och observerar den bandbredd som rapporteras av bandbreddsövervakaren.
