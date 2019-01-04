---
title: Azure Cache för Redis-exempel | Microsoft Docs
description: Så här använder du Azure Cache for Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: c9a2d569641ab0f8f6aa1d7234da1d3cbb0ab147
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744997"
---
# <a name="azure-cache-for-redis-samples"></a>Exempel för Azure Cache for Redis
Det här avsnittet innehåller en lista över Azure Cache för Redis-exempel som täcker scenarier, till exempel ansluta till en cache, läsa och skrivdata till och från ett cacheminne och använda ASP.NET Azure Cache för Redis-leverantörer. Vissa av exemplen är nedladdningsbara projekt och vissa stegvis vägledning och inkludera kodfragment men Länka inte till ett nedladdningsbart projekt.

## <a name="hello-world-samples"></a>Hello world-exempel
Exemplen i det här avsnittet beskrivs grunderna för att ansluta till en Azure-Cache för Redis-instans och läsa och skriva data till cachen med olika språk och Redis-klienter.

Den [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplet visar hur du utför olika cache-åtgärder med hjälp av den [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET-klient.

Det här exemplet visas hur du:

* Använd olika anslutningsalternativ
* Läsa och skriva objekt till och från cachen med synkrona och asynkrona åtgärder
* Använda Redis MGET/MSET-kommandon för att returnera värden av angivna nycklar
* Utför Redis transaktionella åtgärder
* Arbeta med Redis-listor och sorterade uppsättningar
* Store .NET-objekt med hjälp av JsonConvert serializers
* Använd Redis implementerar taggning
* Arbeta med Redis-kluster

Mer information finns i den [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) dokumentation på GitHub och för flera Användningsscenarier finns i den [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) enhetstester.

[Hur du använder Azure Cache för Redis med Python](cache-python-get-started.md) visar hur du kommer igång med Azure Cache för Redis med hjälp av Python och [redis-py](https://github.com/andymccurdy/redis-py) klienten.

[Arbeta med .NET-objekt i cachen](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) visar ett sätt att serialisera .NET-objekt så att du kan skriva dem till och läsa dem från en Azure-Cache för Redis-instans. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Använda Azure Cache för Redis som en skalbar bakplan för ASP.NET SignalR
Den [använder Azure Cache för Redis som en skalbar bakplan för ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) exempel visar hur du kan använda Azure Cache för Redis som ett SignalR-bakplan. Läs mer om bakplan [SignalR Scaleout med Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure Cache för Redis Kundexempel för fråga
Detta exempel visar jämför prestanda mellan kommer åt data från en cache och komma åt data från lagring för persistence. Det här exemplet har två projekt.

* [Demonstrera hur Azure Cache för Redis kan förbättra prestanda genom att cachelagra data](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Dirigera databasen och cachen för demon](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET-sessionstillstånd och cachelagring av utdata
Den [använder Azure Cache att lagra ASP.NET SessionState och OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) exempel visar hur du använder Azure Cache för att lagra ASP.NET-sessionen och utdatacache med SessionState och OutputCache-providers för Redis .

## <a name="manage-azure-cache-for-redis-with-maml"></a>Hantera Azure Cache för Redis med MAML
Den [hantera Azure Cache för Redis med hjälp av Azures hanteringsbibliotek](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) exempel visar hur kan du använda Azure Management Libraries hantering – (skapa / uppdatera / ta bort) ditt cacheminne. 

## <a name="custom-monitoring-sample"></a>Anpassad övervakning exemplet
Den [åtkomst till Azure Cache för Redis-övervakningsdata](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) exempel visar hur du kan komma åt övervakningsdata för din Azure-Cache för Redis utanför Azure-portalen.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>En Twitter-style klon som skrivits med PHP och Redis
Den [Retwis](https://github.com/SyntaxC4-MSFT/retwis) exemplet är den Redis Hello World. Det är en minimal Twitter-style socialt nätverk klon som skrivits med Redis och PHP med hjälp av den [Predis](https://github.com/nrk/predis) klienten. Källkoden är avsedd att vara mycket enkelt och på samma gång för att visa olika Redis datastrukturer.

## <a name="bandwidth-monitor"></a>Övervakare för bandbredd
Den [bandbredd övervakaren](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) exemplet kan du övervaka den bandbredd som används på klienten. För att mäta bandbredd, kör exemplet på klientdatorn cache, göra anrop till cacheminnet och notera den bandbredd som rapporterats av bandbredd övervakaren exemplet.

