---
title: Vad är Azure Redis Cache? | Microsoft Docs
description: Lär dig vad Azure Redis Cache är och hur den används.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 8f477282e49104e9b034e11656ff50c2a67545f7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-redis-cache"></a>Vad är Azure Redis Cache?

Azure Redis Cache baseras på den populära [Redis-cachen](https://redis.io/) med öppen källkod. Den används vanligtvis som en cache för att förbättra prestanda och skalbarhet i system som är starkt beroende av backend-datalager. Prestanda förbättras genom att data som används ofta tillfälligt kopieras till snabb lagring som finns nära programmet. Med [Redis-cachen](https://redis.io/) finns den här snabba lagringen i minnet i Redis Cache, i stället för att läsas in från en disk av en databas.

Azure Redis Cache kan också användas som ett minnesinternt datastrukturlager, en distribuerad icke-relationell databas och en meddelandekoordinator. Genom att utnyttja Redis-motorns korta svarstider och höga dataflöde förbättras programprestanda.

Azure Redis Cache ger tillgång till en säker, dedikerad Redis-cache, som hanteras av Microsoft, som finns i Azure och som är tillgänglig för alla program i Azure.

## <a name="why-use-azure-redis-cache"></a>Varför bör du använda Azure Redis Cache?

Det finns många vanliga mönster där Redis Cache används för att ge stöd för programarkitekturer eller för att förbättra programprestanda. Här följer exempel på några av de vanligaste:

| Mönster      | Beskrivning                                        |
| ------------ | -------------------------------------------------- |
| [Cachereservera](cache-web-app-cache-aside-leaderboard.md) | Eftersom en databas kan vara stor avråder vi dig från att läsa in en hel databas i en cache. [Cache aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside)-mönstret används ofta för att läsa in dataobjekt i cachen endast efter behov. När ändringar görs i backend-data kan även cachen uppdateras, som distribueras till andra klienter. Dessutom kan en giltighetstid definieras för dataobjekt, och en borttagningsprincip kan användas som triggar inläsningen av datauppdateringar i cachen igen.|
| [Cachelagring av innehåll](cache-aspnet-output-cache-provider.md) | De flesta webbsidor skapas från mallar med sidhuvuden, sidfötter, verktygsfält, menyer och så vidare. De ändras inte särskilt ofta och bör inte genereras dynamiskt. Med en minnesintern cache som Azure Redis Cache har dina webbservrar snabb åtkomst till den här typen av statiskt innehåll jämfört med ett backend-datalager. Det här mönstret minskar bearbetningstiden och serverbelastningen som skulle krävts för att generera innehåll dynamiskt. Detta gör webbservrarna mer dynamiska, och innebär ofta att du kan minska antalet servrar som krävs för att hantera belastningen. Azure Redis Cache stöder det här mönstret med ASP.NET via Redis Output Cache Provider.|
| [Cachelagring av användarsessioner](cache-aspnet-session-state-provider.md) | Det här mönstret används ofta med shoppingvagnar och annan information om användarhistorik som ett webbprogram kanske vill associera med användarcookies. Att lagra för stora datamängder i en cookie kan påverka prestanda negativt då storleken på cookie-filerna ökar och de skickas och verifieras med varje begäran. En typisk lösning är att använda cookien som en nyckel för att fråga efter data i en backend-databas. Ett mycket snabbare sätt än att interagera med en hel relationsdatabas är att använda en minnesintern cache, som Azure Redis Cache, för att associera information med en användare. |
| Jobb- och meddelandeköer | När program tar emot begäranden krävs det ofta ytterligare tid för att köra de åtgärder som associeras med begäran. Det är ett vanligt mönster att skjuta upp tidskrävande åtgärder genom att placera dem i en kö, som bearbetas senare och kanske av en annan server. Den här metoden att skjuta upp arbete kallas för att placera uppgifter i kö. Det finns många programvarukomponenter som är utformade för att kunna hantera uppgiftsköer. Redis Cache tjänar detta syfte väl som en distribuerad kö.|
| Distribuerade transaktioner | Ett vanligt krav för program är att de ska kunna köra en rad kommandon mot ett backend-datalager som en enda åtgärd (atomisk). Alla kommandon måste slutföras, eller så måste alla återställas till det ursprungliga tillståndet. Redis Cache stöder körning av en grupp kommandon som en enda åtgärd i form av [transaktioner](https://redis.io/topics/transactions). |

## <a name="azure-redis-cache-offerings"></a>Azure Redis Cache-produkter

Azure Redis Cache är tillgänglig på följande nivåer:

| Nivå | Beskrivning |
|---|---|
Basic | Cache för en enda nod. Den här nivån stöder flera minnesstorlekar (250 MB–53 GB). Det här är en perfekt nivå för utveckling och testning av icke-kritiska arbetsbelastningar. Basic-nivån har inget serviceavtal (SLA) |
| Standard | En replikerad cache med en primär/sekundär tvånodskonfiguration som hanteras av Microsoft, med ett serviceavtal med hög tillgänglighet (99,9 %). |
| Premium | Premium-nivån motsvarar Enterprise-nivån. Cacheminnen på Premium-nivå stöder fler funktioner och har högre datagenomflöde med kortare svarstider. Cacheminnen på Premium-nivå distribueras på kraftfullare maskinvara som ger bättre prestanda jämfört med Basic- och Standard-nivån. Den här fördelen innebär att datagenomflödet för en cache med samma storlek är högre på Premium-nivå jämfört med Standard-nivån |

> [!TIP]
> Mer information om storlek, dataflöde och bandbredd för cacheminnen på Premium-nivå finns i [Vanliga frågor och svar om Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).
>

Du kan skala upp din cache till en högre nivå i efterhand, när den redan har skapats. Det går inte att skala ned till en lägre nivå. Stegvisa skalningsanvisningar finns i [How to Scale Azure Redis Cache](cache-how-to-scale.md) (Skala Azure Redis Cache) och [How to automate a scaling operation](cache-how-to-scale.md#how-to-automate-a-scaling-operation) (Automatisera en skalningsåtgärd).

### <a name="feature-comparision"></a>Funktionsjämförelse

En detaljerad jämförelse av respektive nivå finns på sidan med [priser för Redis Cache](https://azure.microsoft.com/pricing/details/cache/). Följande tabell beskriver några av de funktioner som stöds på respektive nivå:

| Funktionsbeskrivning | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-datapersistens](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-kluster](cache-how-to-premium-clustering.md) |✔|-|-|
| [Säkerhet via brandväggsregler](cache-configure.md#firewall) |✔|✔|✔|
| [Förbättrad säkerhet och isolering med VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [Schemauppdateringar](cache-administration.md#schedule-updates) |✔|-|-|
| [Geo-replikering](cache-how-to-geo-replication.md) |✔|-|-|
| [Starta om](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Nästa steg

* [Snabbstart för ASP.NET-webbprogram](cache-web-app-howto.md) Skapa ett enkelt ASP.NET-webbprogram som använder Azure Redis Cache.
* [Snabbstart för .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Skapa ett .NET-program som använder Azure Redis Cache.
* [Snabbstart för Node.js](cache-nodejs-get-started.md) Skapa en enkel Node.js-app som använder Azure Redis Cache.
* [Snabbstart för Java](cache-java-get-started.md) Skapa en enkel Java-app som använder Azure Redis Cache.
* [Snabbstart för Python](cache-python-get-started.md) Skapa en Python-app som använder Azure Redis Cache.