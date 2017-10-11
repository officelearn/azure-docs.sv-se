---
title: Azure Redis-Cache-exempel | Microsoft Docs
description: "Lär dig hur du använder Azure Redis-Cache"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
ms.openlocfilehash: 7841fcf0b5f4dcb409abf8bfb804c2e03dad6d3a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-redis-cache-samples"></a>Azure Redis-Cache-exempel
Det här avsnittet innehåller en lista över Azure Redis-Cache sampel, som omfattar scenarier, till exempel ansluta till en cache, läsa och skriva data till och från cache och använda Redis-Cache för ASP.NET-providers. Några exempel är nedladdningsbara projekt och vissa steg för steg-vägledning och innehåller kodavsnitt men Länka inte till ett hämtningsbart projekt.

## <a name="hello-world-samples"></a>Hello world-exempel
Exemplen i det här avsnittet beskrivs grunderna för att ansluta till Azure Redis-Cache-instans och läser och skriver data till cachen med olika språk och Redis-klienter.

Den [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exempel visas hur du utför olika cache-åtgärder med hjälp av den [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET-klienten.

Det här exemplet visas hur du:

* Använd olika anslutningsalternativ
* Läsa och skriva objekt till och från cachen synkrona och asynkrona åtgärder
* Använda Redis MGET/MSET kommandon för att returnera värden av angivna nycklar
* Utföra Redis transaktionella åtgärder
* Arbeta med Redis listor och sorterade uppsättningar
* Lagra .NET-objekt med hjälp av JsonConvert serializers
* Använd Redis anger för att implementera taggning
* Arbeta med Redis-kluster

Mer information finns i [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) dokumentation på github och för flera Användningsscenarier finns i [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) kontroller.

[Hur du använder Azure Redis-Cache med Python](cache-python-get-started.md) visar hur du kommer igång med Azure Redis-Cache med hjälp av Python och [redis-kopiera](https://github.com/andymccurdy/redis-py) klienten.

[Arbeta med .NET-objekt i cacheminnet](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) visar ett sätt att serialisera .NET objekt så att du kan skriva dem till och läsa dem från en Azure Redis-Cache-instans. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Använda Redis-Cache som en skalbar bakplan för ASP.NET SignalR
Den [använda Redis-Cache som en skalbar bakplan för ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) exemplet visar hur du kan använda Azure Redis-Cache som ett SignalR bakplan. Läs mer om bakplan [SignalR Scaleout med Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Redis-Cache kunden frågan exempel
Det här exemplet visar jämför prestanda mellan kommer åt data från en cache och kommer åt data från beständiga lagring. Det här exemplet har två projekt.

* [Demonstrera hur Redis-Cache kan förbättra prestanda genom att cachelagra data](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Startvärde för databasen och Cache för demonstrationen](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Sessionstillståndet ASP.NET och cachelagring av utdata
Den [använder Azure Redis-Cache för lagring av ASP.NET SessionState och OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) exemplet visar hur du använder Azure Redis-Cache för att lagra ASP.NET-sessionen och utdatacache med SessionState och OutputCache providers för Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Hantera Azure Redis-Cache med MAML
Den [hantera Azure Redis-Cache med hjälp av Azure Management Libraries](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) exemplet visar hur kan du använda Azure bibliotek hantering – (skapa / uppdatera / ta bort) ditt cacheminne. 

## <a name="custom-monitoring-sample"></a>Anpassad övervakning exempel
Den [åtkomst Redis-Cache övervakning data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) exemplet visar hur du kan komma åt övervakningsdata för din Azure Redis-Cache utanför Azure-portalen.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>En Twitter-format klon som skrivits med PHP och Redis
Den [Retwis](https://github.com/SyntaxC4-MSFT/retwis) exempel är den Redis Hello World. Det är en minimal Twitter-format sociala nätverk klon som skrivits med Redis och PHP använder den [Predis](https://github.com/nrk/predis) klienten. Källkoden är avsedd att vara mycket enkla och samtidigt för att visa olika Redis datastrukturer.

## <a name="bandwidth-monitor"></a>Övervakare för bandbredd
Den [bandbredd övervakaren](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) exempel kan du övervaka den bandbredd som används på klienten. För att mäta bandbredd, köra exemplet på klientdatorn cache, göra anrop till cacheminnet och se den bandbredd som rapporterats av bandbredd övervakaren exemplet.

