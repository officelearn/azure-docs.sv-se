---
title: Så här konfigurerar du Redis-klustring för Premium Azure Cache för Redis | Microsoft Docs
description: Lär dig att skapa och hantera Redis-klustring för din Premium-nivån Azure Cache för Redis-instanser
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: wesmc
ms.openlocfilehash: e0c50046cd3cdb4db7c9e7e3961124b891b3c0a4
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019935"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Så här konfigurerar du Redis-klustring för Premium Azure Cache för Redis
Azure Redis-Cache har olika cachefunktioner som ger flexibilitet i valet av cachestorlek och funktioner, inklusive funktioner på Premiumnivå som klustring, persistence och stöd för virtuella nätverk. Den här artikeln beskriver hur du konfigurerar du klustring i en Azure Cache på premiumnivå för Redis-instans.

Information om andra premiumfunktioner för cache finns i [introduktion till Azure Cache för Premium-nivån Redis](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Vad är Redis-kluster?
Azure Cache för Redis erbjuder Redis-kluster som [implementeras i Redis](http://redis.io/topics/cluster-tutorial). Med Redis-kluster kan du få följande fördelar: 

* Möjligheten att automatiskt dela din datauppsättning bland flera noder. 
* Möjligheten att fortsätta åtgärder när en delmängd av noderna har drabbats av fel eller inte kan kommunicera med resten av klustret. 
* Större dataflöde: dataflödet ökar linjärt när du ökar antalet shards. 
* Mer minnesstorlek: ökar linjärt när du ökar antalet shards.  

Klustring ökar inte antalet anslutningar som är tillgängliga för en klustrad cache. Mer information om storlek, dataflöde och bandbredd med premium-cache finns i [vilket Azure Cache för Redis-erbjudande och vilken storlek ska jag använda?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

I Azure erbjuds Redis-kluster som en primär/replik-modell där varje shard har ett par för primär/replik med replikering där replikeringen hanteras av Azure Cache för Redis-tjänsten. 

## <a name="clustering"></a>Klustring
Klustring är aktiverat på den **nya Azure-Cache för Redis** bladet när cache skapas. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Klustring är konfigurerad på den **Redis-kluster** bladet.

![Klustring][redis-cache-clustering]

Du kan ha upp till 10 shards i klustret. Klicka på **aktiverad** och drar du skjutreglaget eller ange ett tal mellan 1 och 10 för **shardantal** och klicka på **OK**.

Varje shard är ett par för primär/replik-cache som hanteras av Azure och den totala storleken på cacheminnet beräknas genom att multiplicera antalet shards med den cachestorlek som valts i prisnivån. 

![Klustring][redis-cache-clustering-selected]

När cachen har skapats du ansluta till det och Använd den precis som ett icke-klustrade cacheminne och Redis distribuerar data i hela cachen shards. Om diagnostik är [aktiverat](cache-how-to-monitor.md#enable-cache-diagnostics), mått samlas separat för varje fragment och kan vara [visas](cache-how-to-monitor.md) i Azure-Cache för Redis-bladet. 

> [!NOTE]
> 
> Det finns några mindre skillnader som krävs i klientprogrammet när klustring har konfigurerats. Mer information finns i [behöver jag göra ändringar i mitt klientprogram för att använda kluster?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Exempel om hur du arbetar med klustring med StackExchange.Redis-klienten, finns det [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) delen av den [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplet.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Ändra klusterstorlek på en löpande premium-cache
Ändra klusterstorlek på en löpande premium-cache med klustring aktiverat, klickar du på **Redis-klusterstorlek** från den **resursmenyn**.

> [!NOTE]
> Medan Azure Cache Redis Premium-nivån har släppts allmänt tillgängliga, är funktionen Redis-klusterstorlek för närvarande i förhandsversion.
> 
> 

![Redis-klusterstorlek][redis-cache-redis-cluster-size]

Om du vill ändra klusterstorleken använder sedan skjutreglaget eller ange ett tal mellan 1 och 10 i den **shardantal** textrutan och klicka på **OK** att spara.

Öka klusterstorleken ökar det maximala dataflöde och cachestorlek. Öka klusterstorleken ökar inte det största värdet. anslutningar som är tillgängliga för klienter.

> [!NOTE]
> Skala ett kluster kör den [MIGRERA](https://redis.io/commands/migrate) kommando, som är en dyr kommandot, så för minimal påverkan bör du köra den här åtgärden vid låg belastning. Under migreringsprocessen visas en topp i belastningen på servern. Skala ett kluster körs en lång processen och mängden tid det tar beror på hur många nycklar och storleken på de värden som är associerade med nycklarna.
> 
> 

## <a name="clustering-faq"></a>Klustring vanliga frågor och svar
I följande lista innehåller svar på vanliga frågor om Azure Cache för Redis-klustring.

* [Behöver jag göra ändringar i mitt klientprogram för att använda kluster?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Hur fördelas nycklar i ett kluster?](#how-are-keys-distributed-in-a-cluster)
* [Vad är den största cachestorleken kan jag skapa?](#what-is-the-largest-cache-size-i-can-create)
* [Stöder alla Redis-klienter kluster?](#do-all-redis-clients-support-clustering)
* [Hur ansluter jag till min cache när klustring har aktiverats?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Kan jag ansluta direkt till de enskilda cacheskärvor min?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Kan jag konfigurera klustring för tidigare skapade cache?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Kan jag konfigurera klustring för basic eller standard cache?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Kan jag använda kluster med Redis ASP.NET-sessionstillstånd och cachelagring av utdata leverantörer?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Jag får flytta undantag när du använder StackExchange.Redis och klustring, vad ska jag göra?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Behöver jag göra ändringar i mitt klientprogram för att använda kluster?
* När klustring har aktiverats kan är bara databasen 0 tillgänglig. Om klientprogrammet använder flera databaser och försök att läsa eller skriva till en annan databas än 0, genereras följande undantag. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Mer information finns i [specifikation på Redis-kluster - implementerad delmängd](http://redis.io/topics/cluster-spec#implemented-subset).
* Om du använder [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), måste du använda 1.0.481 eller senare. Du ansluter till cachen med samma [slutpunkter, portar och nycklar](cache-configure.md#properties) som du använder när du ansluter till en cache som inte har aktiverad klustring. Den enda skillnaden är att alla läsningar och skrivningar måste göras till 0-databas.
  
  * Andra klienter kan ha olika krav. Se [alla Redis-klienter stöder kluster?](#do-all-redis-clients-support-clustering)
* Om programmet använder flera viktiga åtgärder batchar i ett enda kommando, måste alla nycklar finnas i samma fragment. Du hittar nycklarna i samma fragment [hur nycklar distribueras i ett kluster?](#how-are-keys-distributed-in-a-cluster)
* Om du använder Redis ASP.NET-sessionstillståndsprovider måste du använda 2.0.1 eller högre. Se [kan jag använda kluster med Redis ASP.NET-sessionstillstånd och cachelagring av utdata leverantörer?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Hur fördelas nycklar i ett kluster?
Per Redis [nycklar distributionsmodell](http://redis.io/topics/cluster-spec#keys-distribution-model) dokumentation: området viktiga delas upp i 16384 platser. Varje nyckel är hash-kodas och tilldelats till en av platserna, som är distribuerade över noder i klustret. Du kan konfigurera vilken del av nyckeln hashas för att säkerställa att flera nycklar finns i samma fragment med hjälp av hash-taggar.

* Nycklar med en hash-tagg – om någon del av nyckeln omges `{` och `}`, endast den del av nyckeln hashas för att fastställa hash-fack av en nyckel. Till exempel följande 3 nycklar skulle finnas i samma fragment: `{key}1`, `{key}2`, och `{key}3` eftersom endast den `key` del av namnet hashas. En fullständig lista över nycklar hash-tagg specifikationer, se [nycklar hash-taggar](http://redis.io/topics/cluster-spec#keys-hash-tags).
* Nycklar utan en hash-tagg - hela nyckelnamnet används för hashing. Detta resulterar i en statistiskt jämn fördelning över shards på cacheminnet.

För bästa prestanda och dataflöde rekommenderar vi att distribuera nycklarna jämnt. Om du använder nycklar med en hash-tagg som ansvarar för programmets att se till att nycklarna är jämnt.

Mer information finns i [nycklar distributionsmodell](http://redis.io/topics/cluster-spec#keys-distribution-model), [Redis-kluster data horisontell partitionering](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), och [nycklar hash-taggar](http://redis.io/topics/cluster-spec#keys-hash-tags).

Exempel om hur du arbetar med klustring och hitta nycklar i samma fragment med StackExchange.Redis-klienten, finns det [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) delen av den [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplet.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Vad är den största cachestorleken kan jag skapa?
Den största premium cachestorleken är 53 GB. Du kan skapa upp till 10 shards, vilket ger dig en maximal storlek på 530 GB. Om du behöver en större storlek kan du [begär mer](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Mer information finns i [Azure Cache Redis priser](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Stöder alla Redis-klienter kluster?
Just nu inte alla klienter har stöd för Redis-klustring. StackExchange.Redis är ett program som har stöd för den. Mer information om andra klienter finns i den [spelar med klustret](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) delen av den [Redis-kursen om kluster](http://redis.io/topics/cluster-tutorial). 

Redis-klustring protokollet kräver varje klient att ansluta till varje shard direkt i kluster-läge. Försök att använda en klient som inte stöder kluster kommer troligen att resultera i mycket [FLYTTATS omdirigering undantag](https://redis.io/topics/cluster-spec#moved-redirection).

> [!NOTE]
> Om du använder StackExchange.Redis som din klient, se till att du använder den senaste versionen av [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 eller senare för klustring ska fungera korrekt. Om du har problem med flytten undantag, se [flytta undantag](#move-exceptions) för mer information.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Hur ansluter jag till min cache när klustring har aktiverats?
Du kan ansluta till ditt cacheminne med samma [slutpunkter](cache-configure.md#properties), [portar](cache-configure.md#properties), och [nycklar](cache-configure.md#access-keys) som du använder när du ansluter till en cache som inte har aktiverad klustring. Redis hanterar klustring på serverdelen så att du inte behöver hantera den från klienten.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Kan jag ansluta direkt till de enskilda cacheskärvor min?
Klustring protokollet kräver att klienten gör rätt fragment-anslutningar. Så bör klienten göra detta på rätt sätt för dig. Därmed SA består varje shard av en primär/replik cache-par gemensamt kallas en cache-instans. Du kan ansluta till instanserna cache använder redis-cli-verktyget i den [instabil](http://redis.io/download) gren av Redis-databasen på GitHub. Den här versionen implementerar basic-support när igång med den `-c` växla. Mer information finns i [spelar med klustret](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) på [ http://redis.io ](http://redis.io) i den [Redis-kursen om kluster](http://redis.io/topics/cluster-tutorial).

För icke-ssl, använder du följande kommandon.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Ssl, Ersätt `1300N` med `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Kan jag konfigurera klustring för tidigare skapade cache?
Du kan för närvarande bara aktivera klustring när du skapar en cache. Du kan ändra klusterstorleken när cachen har skapats, men du kan inte lägga till kluster till ett premium-cache eller ta bort kluster från en premium-cache när cachen har skapats. Premium-cache med klustring aktiverat och endast en shard skiljer sig premium-cache med samma storlek med ingen klustring.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Kan jag konfigurera klustring för basic eller standard cache?
Klustring är endast tillgängligt för premium-cache.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Kan jag använda kluster med Redis ASP.NET-sessionstillstånd och cachelagring av utdata leverantörer?
* **Redis utdatacachen providern** -några ändringar krävs.
* **Sessionstillståndsprovider redis** – om du vill använda klustring, måste du använda [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 eller högre eller ett undantagsfel utlöses. Det här är en viktig ändring; Mer information finns i [v2.0.0 viktig information om ändringar](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Jag får flytta undantag när du använder StackExchange.Redis och klustring, vad ska jag göra?
Om du använder StackExchange.Redis och ta emot `MOVE` undantag när du använder klustring, se till att du använder [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) eller senare. Anvisningar om hur du konfigurerar .NET-program till att använda StackExchange.Redis finns i [konfigurera cacheklienterna](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder mer premiumfunktioner för cache.

* [Introduktion till Azure Cache Redis Premium-nivån](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







