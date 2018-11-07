---
title: Så här skalar du Azure Redis Cache | Microsoft Docs
description: Lär dig att skala din Azure Redis Cache-instanser
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 350db214-3b7c-4877-bd43-fef6df2db96c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: wesmc
ms.openlocfilehash: 91a2b112000e970b73420abaa26efc7de1caa626
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261464"
---
# <a name="how-to-scale-azure-redis-cache"></a>Så här skalar du Azure Redis Cache
Azure Redis Cache har olika cachefunktioner som ger flexibilitet i valet av cachestorlek och funktioner. När en cache har skapats kan skala du storleken och prisnivå för cachen om kraven för ditt program ändrar. Den här artikeln visar hur du skalar cachen med hjälp av Azure-portalen och verktyg som Azure PowerShell och Azure CLI.

## <a name="when-to-scale"></a>När ska du skala
Du kan använda den [övervakning](cache-how-to-monitor.md) funktioner i Azure Redis Cache och övervaka hälsotillstånd och prestanda för din cachelagring och hjälper dig att avgöra när du ska utöka cachen. 

Du kan övervaka följande mått för att bestämma om du behöver skala.

* Redis-serverbelastning
* Minnesanvändning
* Nätverksbandbredd
* CPU-användning

Om du har fastställt att cacheminnet inte längre uppfyller kraven för ditt program, kan du skala till ett större eller mindre cache prisnivå som passar ditt program. Mer information om hur du bestämmer vilken prisnivå du använder cache finns i [vilket Redis Cache-erbjudande och vilken storlek ska jag använda](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Skala en cache
Du skalar cachen, [bläddrar du till cachen](cache-configure.md#configure-redis-cache-settings) i den [Azure-portalen](https://portal.azure.com) och klicka på **skala** från den **resursmenyn**.

![Skala](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Välj den önskade prisnivån från den **väljer prisnivå** bladet och klickar på **Välj**.

![Prisnivå][redis-cache-pricing-tier-blade]


Du kan skala till en annan prisnivå med följande begränsningar:

* Du kan skala från en högre prisnivå till en lägre prisnivå.
  * Du kan skala från en **Premium** cachelagra ned till en **Standard** eller en **grundläggande** cache.
  * Du kan skala från en **Standard** cachelagra ned till en **grundläggande** cache.
* Du kan skala från en **grundläggande** cachelagra en **Standard** cache, men du kan inte ändra storlek på samma gång. Om du behöver en annan storlek kan göra du en efterföljande skalning åtgärd till önskad storlek.
* Du kan skala från en **grundläggande** cachelagra direkt till en **Premium** cache. Först skala från **grundläggande** till **Standard** i en enda skalning åtgärd och sedan från **Standard** till **Premium** i ett efterföljande skalning åtgärden.
* Du kan skala från en större storlek ned till den **C0 (250 MB)** storlek.
 
När cachen skalning till den nya prisnivån en **skalning** status visas i den **Redis Cache** bladet.

![Skalning][redis-cache-scaling]

När skalning är klar ändras statusen från **skalning** till **kör**.

## <a name="how-to-automate-a-scaling-operation"></a>Så här automatiserar du en åtgärd för skalning
Förutom att skala din cache-instanser i Azure-portalen, du kan skala med hjälp av PowerShell-cmdletar, Azure CLI, och genom att använda Microsoft Azure Management Libraries (MAML). 

* [Skala med hjälp av PowerShell](#scale-using-powershell)
* [Skala med Azure CLI](#scale-using-azure-cli)
* [Skala med MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Skala med hjälp av PowerShell
Du kan skala dina Azure Redis Cache-instanser med PowerShell med hjälp av den [Set-AzureRmRedisCache](https://docs.microsoft.com/powershell/module/azurerm.rediscache/set-azurermrediscache?view=azurermps-6.6.0) cmdlet när den `Size`, `Sku`, eller `ShardCount` egenskaper har ändrats. I följande exempel visas hur du skalar en cache med namnet `myCache` ett 2,5 GB cacheminne. 

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Mer information om skalning med PowerShell finns i [att skala en Redis-cache med hjälp av Powershell](cache-howto-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Skala med Azure CLI
Om du vill skala dina Azure Redis Cache-instanser med Azure CLI, anropa den `azure rediscache set` och skicka i önskad konfiguration ändras som innehåller en ny storlek eller sku klusterstorleken, beroende på önskad skala åtgärd.

Mer information om skalning med Azure CLI finns i [ändra inställningarna för en befintlig Redis-Cache](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Skala med MAML
Att skala din Azure Redis Cache-instanser med hjälp av den [Microsoft Azure Management Libraries (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), anropa den `IRedisOperations.CreateOrUpdate` metoden samt skickar den nya storleken för den `RedisProperties.SKU.Capacity`.

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

Mer information finns i den [hantera Redis Cache med MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) exemplet.

## <a name="scaling-faq"></a>Skalning av vanliga frågor och svar
I följande lista innehåller svar på vanliga frågor om skalning av Azure Redis Cache.

* [Kan jag skala till, från eller inom en Premium-cache?](#can-i-scale-to-from-or-within-a-premium-cache)
* [När du skalar måste jag ändra Mina cache namn eller åtkomst nycklar?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Hur fungerar skalning?](#how-does-scaling-work)
* [Kommer jag att förlora data från mitt cacheminne under skalning?](#will-i-lose-data-from-my-cache-during-scaling)
* [Är Mina anpassade databaser konfigurera berörda under skalning?](#is-my-custom-databases-setting-affected-during-scaling)
* [Mitt cacheminne är tillgängliga under skalning?](#will-my-cache-be-available-during-scaling)
* [Med Geo-replikering konfigureras, varför kan jag inte skala mitt cacheminne eller ändra shards i ett kluster?](#scaling-limitations-with-geo-relication)
* [Åtgärder som inte stöds](#operations-that-are-not-supported)
* [Hur lång tid skalning tar?](#how-long-does-scaling-take)
* [Hur vet jag när skalning är klar?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Kan jag skala till, från eller inom en Premium-cache?
* Du kan skala från en **Premium** cachelagra ned till en **grundläggande** eller **Standard** prisnivå.
* Du kan skala från en **Premium** cache prisnivå till en annan.
* Du kan skala från en **grundläggande** cachelagra direkt till en **Premium** cache. Först skala från **grundläggande** till **Standard** i en enda skalning åtgärd och sedan från **Standard** till **Premium** i ett efterföljande skalning åtgärden.
* Om du har aktiverat kluster när du skapade din **Premium** cache, kan du [ändra klusterstorleken](cache-how-to-premium-clustering.md#cluster-size). Om ditt cacheminne har skapats utan-klustring aktiverat kan konfigurera du klustring vid ett senare tillfälle.
  
  Mer information finns i [Konfigurera klustring för premium Azure Redis-cache](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>När du skalar måste jag ändra Mina cache namn eller åtkomst nycklar?
Nej, ditt cachenamn och dina nycklar har inte ändrats under en skalning.

### <a name="how-does-scaling-work"></a>Hur fungerar skalning?
* När en **grundläggande** cache skalas till en annan storlek, den är avstängd och ett nytt cacheminne har etablerats med hjälp av den nya storleken. Under denna tid cachen är inte tillgänglig och alla data i cacheminnet går förlorade.
* När en **grundläggande** cache skalas till en **Standard** cache, en replik cache är etablerad och data kopieras från den primära cachen till replik-cachen. Cachen fortfarande tillgängligt under skalning.
* När en **Standard** cache skalas till en annan storlek eller till en **Premium** cache, en av replikerna stängs av och nätverkskonfigurationsinställningar till den nya storleken och den data som överförs via och sedan den andra repliken Utför en redundansväxling innan den är nätverkskonfigurationsinställningar, ungefär på samma sätt som sker vid fel på en cachenoderna.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Kommer jag att förlora data från mitt cacheminne under skalning?
* När en **grundläggande** cache skalas till en ny storlek, alla data går förlorade och cachen inte är tillgänglig under skalning åtgärden.
* När en **grundläggande** cache skalas till en **Standard** bevaras vanligtvis cache, data i cacheminnet.
* När en **Standard** cache skalas till en större storlek eller nivå, eller en **Premium** cache skalas till en större storlek, bevaras normalt alla data. När du skalar en **Standard** eller **Premium** cache ned till en mindre storlek på data kan gå förlorade beroende på hur mycket data som finns i cacheminnet relaterade till den nya storleken när det skalas. Om data går förlorade vid Nedskalning kan avlägsnas nycklar med hjälp av den [allkeys lru](http://redis.io/topics/lru-cache) avlägsningsprincipen. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Är Mina anpassade databaser konfigurera berörda under skalning?
Om du har konfigurerat ett anpassat värde för den `databases` konfigurera under Skapa cache, Tänk på att vissa priser nivåerna har olika [databaser gränser](cache-configure.md#databases). Följande är några saker vid skalning i det här scenariot:

* När du skalar till prisnivån med ett lägre `databases` gränsen än den nuvarande nivån:
  * Om du använder standardantalet `databases`, vilket är 16 för alla prisnivåer, ingen data går förlorad.
  * Om du använder ett anpassat antal `databases` som ligger inom gränserna för nivån som du arbetar för att bygga, detta `databases` inställningen sparas och inga data går förlorade.
  * Om du använder ett anpassat antal `databases` som överskrider gränserna för den nya nivån, den `databases` inställningen sjunker till gränserna för den nya nivån och alla data i de borttagna databaserna går förlorade.
* När du skalar till prisnivån med samma eller högre `databases` gränsen än den nuvarande nivån din `databases` inställningen sparas och inga data går förlorade.

Standard och Premium-cacheminnen har ett serviceavtal på 99,9% för tillgänglighet, finns men det inget serviceavtal för förlust av data.

### <a name="will-my-cache-be-available-during-scaling"></a>Mitt cacheminne är tillgängliga under skalning?
* **Standard** och **Premium** cacheminnen fortsatt tillgängliga under skalning igen. Anslutningen signaler kan dock uppstå samtidigt som Standard och Premium-cacheminnen och även vid skalning från Basic till Standard-cache. Dessa anslutning signaler förväntas vara liten och redis-klienter ska kunna återupprätta anslutningen omedelbart.
* **Grundläggande** cacheminnen är offline under skalning till en annan storlek. Grundläggande cacheminnen är tillgängliga när du skalar från **grundläggande** till **Standard** men kan uppleva en liten anslutning blip. Om en anslutning blip inträffar ska redis-klienter kunna återupprätta anslutningen omedelbart.


### <a name="scaling-limitations-with-geo-replication"></a>Skala med Geo-replikering

När du har lagt till en länk för Geo-replikering mellan två cacheminnen kan att du inte längre kunna initiera en skalning åtgärd eller ändra antalet shards i ett kluster. Du måste ta bort länken cache för att utfärda följande kommandon. Mer information finns i [konfigurera Geo-replikering](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Åtgärder som inte stöds
* Du kan skala från en högre prisnivå till en lägre prisnivå.
  * Du kan skala från en **Premium** cachelagra ned till en **Standard** eller en **grundläggande** cache.
  * Du kan skala från en **Standard** cachelagra ned till en **grundläggande** cache.
* Du kan skala från en **grundläggande** cachelagra en **Standard** cache, men du kan inte ändra storlek på samma gång. Om du behöver en annan storlek kan göra du en efterföljande skalning åtgärd till önskad storlek.
* Du kan skala från en **grundläggande** cachelagra direkt till en **Premium** cache. Först skala från **grundläggande** till **Standard** i skalning samtidigt, och sedan skala från **Standard** till **Premium** i ett efterföljande åtgärden.
* Du kan skala från en större storlek ned till den **C0 (250 MB)** storlek.

Om en skalning åtgärd misslyckas tjänsten försöker återställa igen och cachen återgår till den ursprungliga storleken.


### <a name="how-long-does-scaling-take"></a>Hur lång tid skalning tar?
Skala tar cirka 20 minuter beroende på hur mycket data som finns i cacheminnet.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Hur vet jag när skalning är klar?
Du kan se skalning pågår i Azure-portalen. När skalning är klar ändras statusen för cachen till **kör**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



