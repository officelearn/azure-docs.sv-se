---
title: "Konfigurera Redis klustring för Premium Azure Redis-Cache | Microsoft Docs"
description: "Lär dig hur du skapar och hanterar Redis klustring för Premium-nivån Azure Redis-Cache-instanser"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: 86a4a605dbb3b11924c14ff42238009742f72898
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-redis-cache"></a>Konfigurera Redis klustring för Premium Azure Redis-Cache
Azure Redis-Cache har olika cache-erbjudanden som ger flexibilitet vid val av cachestorlek och funktioner, inklusive funktioner för Premium-nivån, till exempel klustring, beständiga och stöd för virtuella nätverk. Den här artikeln beskriver hur du konfigurerar kluster i en premium Azure Redis-Cache-instans.

Information om andra cache premium-funktioner finns [introduktion till Azure Redis Cache Premium-nivån](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Vad är Redis-kluster?
Azure Redis-Cache erbjuder Redis cluster som [implementeras i Redis](http://redis.io/topics/cluster-tutorial). Med Redis-kluster får du följande fördelar: 

* Möjligheten att automatiskt dela din datauppsättning mellan flera noder. 
* Möjligheten att fortsätta arbetet när en delmängd av noderna har uppstått fel eller inte kan kommunicera med resten av klustret. 
* Flera genomströmning: genomströmning linjärt ökar om du ökar antalet delar. 
* Flera minnesstorleken: linjärt ökar om du ökar antalet delar.  

Mer information om storlek, genomflöde och bandbredd med premium cacheminnen finns [vilka Redis-Cache erbjudande och vilken storlek ska jag använda?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

I Azure erbjuds Redis cluster som primär replik/modell där varje Fragmentera har två primära/replik med replikering där replikeringen hanteras av Azure Redis-Cache-tjänsten. 

## <a name="clustering"></a>Klustring
Kluster har aktiverats på den **nytt Redis-Cache** bladet under skapande av cachen. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Kluster har konfigurerats på den **Redis Cluster** bladet.

![Klustring][redis-cache-clustering]

Du kan ha upp till 10 delar i klustret. Klicka på **aktiverad** och dra reglaget eller ange ett tal mellan 1 och 10 för **Fragmentera antal** och på **OK**.

Varje Fragmentera är en primär/replik cache-par som hanteras av Azure och den totala storleken på cacheminnet beräknas genom att multiplicera antalet delar med cachestorleken som valts i prisnivån. 

![Klustring][redis-cache-clustering-selected]

När cachen har skapats du ansluter till den och Använd den precis som en icke-klustrade cache och Redis distribuerar data i hela cachen delar. Om diagnostik är [aktiverat](cache-how-to-monitor.md#enable-cache-diagnostics), mätvärden fångas separat för varje Fragmentera och kan vara [visas](cache-how-to-monitor.md) i bladet Redis-Cache. 

> [!NOTE]
> 
> Det finns några mindre skillnader som krävs i ditt klientprogram när kluster är konfigurerat. Mer information finns i [behöver jag göra ändringar i client-program att använda kluster?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Exempel om hur du arbetar med kluster med StackExchange.Redis-klienten, finns det [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) del av den [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exempel.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Ändra klusterstorlek på ett premium-cache
Ändra klusterstorleken på ett premium-cache med aktiverad, klustring klickar du på **Redis klusterstorleken** från den **resurs menyn**.

> [!NOTE]
> Medan Azure Redis Cache Premium-nivån har släppts för allmän tillgänglighet, är Redis klusterstorleken-funktionen för närvarande i förhandsgranskningen.
> 
> 

![Redis klusterstorleken][redis-cache-redis-cluster-size]

Om du vill ändra klusterstorleken skjutreglaget eller ange ett tal mellan 1 och 10 i den **Fragmentera antal** textrutan och klicka på **OK** att spara.

> [!NOTE]
> Skala ett kluster som kör den [MIGRERA](https://redis.io/commands/migrate) kommandot, vilket är en kostsam kommando så för minimal påverkan Överväg att köra den här åtgärden vid låg belastning. Under migreringen visas en topp i belastningen på servern. Skala ett kluster körs lång processen och mängden tid det tar beror på antalet nycklar och storleken på de värden som är associerade med nycklarna.
> 
> 

## <a name="clustering-faq"></a>Kluster för vanliga frågor och svar
I följande lista innehåller svar på vanliga frågor om Azure Redis-Cache-kluster.

* [Behöver jag göra ändringar i client-program att använda kluster?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Hur distribueras nycklar i ett kluster?](#how-are-keys-distributed-in-a-cluster)
* [Vad är den största cachestorleken kan jag skapa?](#what-is-the-largest-cache-size-i-can-create)
* [Stöder alla Redis-klienter kluster?](#do-all-redis-clients-support-clustering)
* [Hur ansluter jag min cacheminne när kluster aktiveras?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Kan jag ansluta direkt till enskilda delar min-cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Kan jag konfigurera klustring för en tidigare skapad cache?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Kan jag konfigurera klustring för basic eller standard-cache?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Kan jag använda kluster med Redis ASP.NET Session State och cachelagring av utdata-providers?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Jag får flytta undantag när du använder StackExchange.Redis och klustring, vad ska jag göra?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Behöver jag göra ändringar i client-program att använda kluster?
* När kluster aktiveras är endast databasen 0 tillgänglig. Om klientprogrammet använder flera databaser och försök att läsa eller skriva till en annan databas än 0, utlöstes följande undantag. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Mer information finns i [Redis-kluster specifikation - implementerad delmängd](http://redis.io/topics/cluster-spec#implemented-subset).
* Om du använder [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), måste du använda 1.0.481 eller senare. Du ansluter till i cacheminnet som använder samma [slutpunkter, portarna och nycklarna](cache-configure.md#properties) som du använder när du ansluter till en cache som inte har aktiverat-kluster. Den enda skillnaden är att alla läsningar och skrivningar måste göras till 0-databasen.
  
  * Andra klienter kan ha olika krav. Se [alla Redis-klienter stöder kluster?](#do-all-redis-clients-support-clustering)
* Om programmet använder flera viktiga åtgärder batchar till ett enda kommando, måste alla nycklar finnas i samma Fragmentera. Du hittar nycklar i samma Fragmentera [hur nycklar distribueras i ett kluster?](#how-are-keys-distributed-in-a-cluster)
* Om du använder sessionstillståndet ASP.NET för Redis-providern måste du använda 2.0.1 eller högre. Se [kan du använda kluster med Redis ASP.NET Session State och cachelagring av utdata-providers?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Hur distribueras nycklar i ett kluster?
Per Redis [nycklar distributionsmodell](http://redis.io/topics/cluster-spec#keys-distribution-model) dokumentation: nyckel kan delas in i 16384 platser. Varje nyckel hashas och tilldelas till en av platserna, som är fördelade på noderna i klustret. Du kan konfigurera vilken del av nyckeln hashas för att säkerställa att flera nycklar finns i samma Fragmentera med hash-taggar.

* Nycklar med en tagg för hash - om någon del av nyckeln omges av `{` och `}`, endast den del av nyckeln hashas för att fastställa hash-plats i en nyckel. Till exempel följande 3 nycklar skulle finnas i samma Fragmentera: `{key}1`, `{key}2`, och `{key}3` eftersom endast den `key` hashas del av namnet. En fullständig lista över nycklar hash-tagg specifikationer finns [nycklar hash-taggar](http://redis.io/topics/cluster-spec#keys-hash-tags).
* Nycklar utan en hash-tagg - hela nyckelnamnet används för hashing. Detta resulterar i en statistiskt jämn fördelning över delar av cachen.

För bästa prestanda och genomflöde fördela nycklarna jämnt. Om du använder nycklar med en hash-tagg är programmets ansvar att se till att nycklarna fördelas jämnt.

Mer information finns i [nycklar distributionsmodell](http://redis.io/topics/cluster-spec#keys-distribution-model), [Redis Cluster data horisontell partitionering](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), och [nycklar hash-taggar](http://redis.io/topics/cluster-spec#keys-hash-tags).

Exempel om hur du arbetar med kluster och hitta nycklar i samma Fragmentera med StackExchange.Redis-klienten, finns det [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) del av den [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exempel.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Vad är den största cachestorleken kan jag skapa?
Den största premium cachestorleken är 53 GB. Du kan skapa upp till 10 shards ger dig en maximal storlek på 530 GB. Om du behöver en större kan du [begära mer](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Mer information finns i [priser för Azure Redis-Cache](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Stöder alla Redis-klienter kluster?
För närvarande inte alla klienter har stöd för Redis-kluster. StackExchange.Redis är ett som har stöd för den. Mer information om andra klienter finns i [spelar med klustret](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) avsnitt i den [Redis cluster kursen](http://redis.io/topics/cluster-tutorial).

> [!NOTE]
> Om du använder StackExchange.Redis som klienten kan du kontrollera att du använder den senaste versionen av [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 eller senare för kluster ska fungera korrekt. Om du har problem med flytta undantag, se [flytta undantag](#move-exceptions) för mer information.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Hur ansluter jag min cacheminne när kluster aktiveras?
Du kan ansluta till ditt cacheminne som använder samma [slutpunkter](cache-configure.md#properties), [portar](cache-configure.md#properties), och [nycklar](cache-configure.md#access-keys) som du använder när du ansluter till en cache som inte har aktiverat-kluster. Redis hanterar kluster på serverdelen så att du inte hantera den från din klient.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Kan jag ansluta direkt till enskilda delar min-cache?
Detta stöds inte officiellt. Med som säger består varje Fragmentera av en primär/replik cache-par gemensamt kallas en cache-instans. Du kan ansluta till instanserna cache med hjälp av verktyget redis-cli i den [instabilt](http://redis.io/download) gren av databasen som Redis på GitHub. Den här versionen implementerar grundläggande stöd när den startas med den `-c` växla. Mer information finns i [spelar med klustret](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) på [http://redis.io](http://redis.io) i den [Redis cluster kursen](http://redis.io/topics/cluster-tutorial).

För icke-ssl, använder du följande kommandon.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Ssl, Ersätt `1300N` med `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Kan jag konfigurera klustring för en tidigare skapad cache?
Du kan för närvarande bara aktivera kluster när du skapar en cache. Du kan ändra klusterstorleken när cachen har skapats, men du kan inte lägga till kluster till ett premium-cacheminne eller ta bort kluster från cache premium när cachen har skapats. Premium-cache med aktiverad klustring och endast en Fragmentera skiljer sig premium cache av samma storlek med utan kluster.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Kan jag konfigurera klustring för basic eller standard-cache?
Kluster är endast tillgängligt för premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Kan jag använda kluster med Redis ASP.NET Session State och cachelagring av utdata-providers?
* **Redis utdatacache providern** -några ändringar krävs.
* **Redis-sessionstillståndsprovider** - om du vill använda kluster, måste du använda [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 eller högre eller ett undantagsfel genereras. Det här är en ny ändring; Mer information finns i [v2.0.0 information om ändringar i bryter](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Jag får flytta undantag när du använder StackExchange.Redis och klustring, vad ska jag göra?
Om du använder StackExchange.Redis och ta emot `MOVE` undantag när du använder kluster, se till att du använder [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) eller senare. Anvisningar om hur du konfigurerar .NET-program att använda StackExchange.Redis finns [konfigurera cache-klienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder flera funktioner som cache premium.

* [Introduktion till Azure Redis Cache Premium-nivån](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







