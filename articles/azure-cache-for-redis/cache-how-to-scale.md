---
title: Så här skalar du Azure Cache for Redis
description: Lär dig hur du skalar dina Azure-cache för Redis-instanser med Hjälp av Azure-portalen och verktyg som Azure PowerShell och Azure CLI.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/11/2017
ms.openlocfilehash: 68c668561123aee943f54e6fdcbad7c6450957f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278004"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Så här skalar du Azure Cache for Redis
Azure Cache för Redis har olika cacheerbjudanden, vilket ger flexibilitet i valet av cachestorlek och funktioner. När en cache har skapats kan du skala storleken och prisnivån för cacheminnet om kraven för ditt program ändras. Den här artikeln visar hur du skalar cacheminnet med Azure-portalen och verktyg som Azure PowerShell och Azure CLI.

## <a name="when-to-scale"></a>När ska du skala
Du kan använda [övervakningsfunktionerna](cache-how-to-monitor.md) i Azure Cache för Redis för att övervaka hälso- och prestanda för din cache och avgöra när cachen ska skalas. 

Du kan övervaka följande mått för att avgöra om du behöver skala.

* Inläsning av Redis-server
* Minnesanvändning
* Bandbredd för nätverk
* CPU-användning

Om du bedömer att cacheminnet inte längre uppfyller programmets krav kan du skala till en större eller mindre cacheprisnivå som är rätt för ditt program. Mer information om hur du bestämmer vilken cacheprisnivå som ska användas finns i [Vad Azure Cache för Redis-erbjudande och storlek ska jag använda](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Skala en cache
Om du vill skala cacheminnet [bläddrar du till cacheminnet](cache-configure.md#configure-azure-cache-for-redis-settings) i [Azure-portalen](https://portal.azure.com) och klickar på **Skala** från **resursmenyn**.

![Skalning](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Välj önskad prisnivå på bladet **Välj prisnivå** och klicka på **Välj**.

![Prisnivå][redis-cache-pricing-tier-blade]


Du kan skala till en annan prisnivå med följande begränsningar:

* Du kan inte skala från en högre prisnivå till en lägre prisnivå.
  * Du kan inte skala från en **Premium-cache** ned till en **Standard-** eller **Basic-cache.**
  * Du kan inte skala från en **standardcache** ned till en **Basic-cache.**
* Du kan skala från en **Grundläggande** cache till en **standardcache,** men du kan inte ändra storleken samtidigt. Om du behöver en annan storlek kan du göra en efterföljande skalningsåtgärd till önskad storlek.
* Du kan inte skala från en **Basic-cache** direkt till en **Premium-cache.** Skala först från **Basic** till **Standard** i en skalningsåtgärd och sedan från **Standard** till **Premium** i en efterföljande skalningsåtgärd.
* Du kan inte skala från en större storlek ner till **C0 (250 MB)** storlek.
 
Medan cachen skalas till den nya prisnivån visas en **skalningsstatus** i **Azure Cache for Redis-bladet.**

![Skalning][redis-cache-scaling]

När skalningen är klar ändras statusen från **Skalning** till **Kör**.

## <a name="how-to-automate-a-scaling-operation"></a>Så här automatiserar du en skalningsåtgärd
Förutom att skala cacheinstanserna i Azure-portalen kan du skala med PowerShell-cmdlets, Azure CLI och med hjälp av Microsoft Azure Management Libraries (MAML). 

* [Skala med PowerShell](#scale-using-powershell)
* [Skala med Azure CLI](#scale-using-azure-cli)
* [Skala med MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Skala med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan skala azure-cachen för Redis-instanser med PowerShell med hjälp av `Size` `Sku`cmdlet [set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) när egenskaperna , eller `ShardCount` ändras. I följande exempel visas hur `myCache` du skalar en cache med namnet till en 2,5 GB-cache. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Mer information om skalning med PowerShell finns i [Så här skalar du en Azure-cache för Redis med Powershell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Skala med Azure CLI
Om du vill skala dina Azure-cache för `azure rediscache set` Redis-instanser med Azure CLI anropar du kommandot och skickar in önskade konfigurationsändringar som innehåller en ny storlek, sku eller klusterstorlek, beroende på önskad skalningsåtgärd.

Mer information om skalning med Azure CLI finns i [Ändra inställningar för en befintlig Azure-cache för Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Skala med MAML
Om du vill skala dina Azure-cache för Redis-instanser med `IRedisOperations.CreateOrUpdate` hjälp av MICROSOFT Azure Management `RedisProperties.SKU.Capacity` [Libraries (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)anropar du metoden och skickar in den nya storleken för .

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Mer information finns i [hantera Azure-cache för Redis med MAML-exempel.](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)

## <a name="scaling-faq"></a>Vanliga frågor och svar om skalning
Följande lista innehåller svar på vanliga frågor om Azure Cache för Redis-skalning.

* [Kan jag skala till, från eller i en Premium-cache?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Måste jag ändra mitt cachenamn eller mina åtkomstnycklar efter skalningen?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Hur fungerar skalning?](#how-does-scaling-work)
* [Kommer jag att förlora data från min cache under skalning?](#will-i-lose-data-from-my-cache-during-scaling)
* [Påverkas inställningen för anpassade databaser under skalningen?](#is-my-custom-databases-setting-affected-during-scaling)
* [Kommer min cache att vara tillgänglig under skalningen?](#will-my-cache-be-available-during-scaling)
* Med Geo-replikering konfigurerad, varför kan jag inte skala cacheminnet eller ändra shards i ett kluster?
* [Åtgärder som inte stöds](#operations-that-are-not-supported)
* [Hur lång tid tar skalningen?](#how-long-does-scaling-take)
* [Hur vet jag när skalningen är klar?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Kan jag skala till, från eller i en Premium-cache?
* Du kan inte skala från en **Premium-cache** ned till en **basic-** eller **standardprisnivå.**
* Du kan skala **Premium** från en premiumcacheprisnivå till en annan.
* Du kan inte skala från en **Basic-cache** direkt till en **Premium-cache.** Skala först från **Basic** till **Standard** i en skalningsåtgärd och sedan från **Standard** till **Premium** i en efterföljande skalningsåtgärd.
* Om du aktiverade klustring när du skapade **Premium-cachen** kan du [ändra klusterstorleken](cache-how-to-premium-clustering.md#cluster-size). Om cacheminnet skapades utan kluster aktiverat kan du konfigurera klustring vid ett senare tillfälle.
  
  Mer information finns i [Så här konfigurerar du klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Måste jag ändra mitt cachenamn eller mina åtkomstnycklar efter skalningen?
Nej, cachenamnet och nycklarna är oförändrade under en skalningsåtgärd.

### <a name="how-does-scaling-work"></a>Hur fungerar skalning?
* När en **Grundläggande** cache skalas till en annan storlek stängs den av och en ny cache etableras med den nya storleken. Under den här tiden är cachen inte tillgänglig och alla data i cacheminnet går förlorade.
* När en **Basic-cache** skalas till en **standardcache** etableras en replikcache och data kopieras från den primära cachen till replikcachen. Cachen förblir tillgänglig under skalningsprocessen.
* När en **standardcache** skalas till en annan storlek eller till en **Premium-cache,** stängs en av replikerna av och återetableras till den nya storleken och de data som överförs över, och sedan utför den andra repliken en redundans innan den återetableras, liknande den process som inträffar under ett fel på en av cachenoderna.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Kommer jag att förlora data från min cache under skalning?
* När en **Basic-cache** skalas till en ny storlek går alla data förlorade och cachen inte är tillgänglig under skalningsåtgärden.
* När en **Basic-cache** skalas till en **standardcache** bevaras vanligtvis data i cacheminnet.
* När en **standardcache** skalas till en större storlek eller nivå, eller om en **Premium-cache** skalas till en större storlek, bevaras vanligtvis alla data. När du skalar en **Standard-** eller **Premium-cache** till en mindre storlek kan data gå förlorade beroende på hur mycket data som finns i cacheminnet som är relaterade till den nya storleken när den skalas. Om data går förlorade när du skalar ned, stängs nycklarna av med hjälp av [allkeys-lru-vräkningsprincipen.](https://redis.io/topics/lru-cache) 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Påverkas inställningen för anpassade databaser under skalningen?
Om du har konfigurerat `databases` ett anpassat värde för inställningen när cacheminnet skapas bör du tänka på att vissa prisnivåer har olika [databasgränser](cache-configure.md#databases). Här är några saker som bör övervägas när du skalar i det här scenariot:

* När du skalar till en `databases` prisnivå med en lägre gräns än den aktuella nivån:
  * Om du använder standardnumret `databases`för , som är 16 för alla prisnivåer, går inga data förlorade.
  * Om du använder ett `databases` anpassat antal som ligger inom gränserna för den `databases` nivå som du skalar till, behålls den här inställningen och inga data går förlorade.
  * Om du använder ett `databases` anpassat antal som överskrider gränserna `databases` för den nya nivån, sänks inställningen till gränserna för den nya nivån och alla data i de borttagna databaserna går förlorade.
* När du skalar till en prisnivå med samma eller högre `databases` gräns än den aktuella nivån behålls inställningen `databases` och inga data går förlorade.

Medan Standard- och Premium-cacheminnen har ett serviceavtal på 99,9 % för tillgänglighet finns det inget serviceavtal för dataförlust.

### <a name="will-my-cache-be-available-during-scaling"></a>Kommer min cache att vara tillgänglig under skalningen?
* **Standard-** och **Premium-cacheminnen** förblir tillgängliga under skalningsåtgärden. Anslutningen kan dock uppstå när standard- och Premium-cacheminnen skalas, och även samtidigt skala från grundläggande till standardcachar. Dessa anslutning blips förväntas vara små och redis klienter bör kunna återupprätta sin anslutning direkt.
* **Grundläggande** cacheminnen är offline under skalningsåtgärder till en annan storlek. Grundläggande cacheminnen är fortfarande tillgängliga när du skalar från **Basic** till **Standard,** men kan uppleva en liten anslutning blip. Om en anslutning blip inträffar, redis klienter bör kunna återupprätta sin anslutning direkt.


### <a name="scaling-limitations-with-geo-replication"></a>Skalningsbegränsningar med Geo-replication

När du har lagt till en georeplikeringslänk mellan två cacheminnen kan du inte längre initiera en skalningsåtgärd eller ändra antalet shards i ett kluster. Du måste ta bort länken till cachen för att kunna utfärda dessa kommandon. Mer information finns i [Konfigurera georeplikering](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Åtgärder som inte stöds
* Du kan inte skala från en högre prisnivå till en lägre prisnivå.
  * Du kan inte skala från en **Premium-cache** ned till en **Standard-** eller **Basic-cache.**
  * Du kan inte skala från en **standardcache** ned till en **Basic-cache.**
* Du kan skala från en **Grundläggande** cache till en **standardcache,** men du kan inte ändra storleken samtidigt. Om du behöver en annan storlek kan du göra en efterföljande skalningsåtgärd till önskad storlek.
* Du kan inte skala från en **Basic-cache** direkt till en **Premium-cache.** Skala först från **Basic** till **Standard** i en skalningsåtgärd och skala sedan från **Standard** till **Premium** i en efterföljande åtgärd.
* Du kan inte skala från en större storlek ner till **C0 (250 MB)** storlek.

Om en skalningsåtgärd misslyckas försöker tjänsten återställa åtgärden och cacheminnet återgår till den ursprungliga storleken.


### <a name="how-long-does-scaling-take"></a>Hur lång tid tar skalningen?
Skalningstiden beror på hur mycket data som finns i cacheminnet, med större mängder data som tar längre tid att slutföra. Skalning tar cirka 20 minuter. För klustrade cacheminnen tar skalning ungefär 20 minuter per shard.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Hur vet jag när skalningen är klar?
I Azure-portalen kan du se den pågående skalningsåtgärden. När skalningen är klar ändras cachens status till **Kör**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
