---
title: Vad är Azure Cache for Redis? | Microsoft Docs
description: Lär dig vad Azure Cache for Redis är och hur den vanligtvis används.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 5b2b3a3ec0e9aec603a69211a7493afc31e83283
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236557"
---
# <a name="what-is-azure-cache-for-redis"></a>Vad är Azure Cache for Redis?

Azure Cache for Redis är baserat på den populära programvaran [Redis](https://redis.io/). Den används vanligtvis som en cache för att förbättra prestanda och skalbarhet i system som är starkt beroende av backend-datalager. Prestanda förbättras genom att data som används ofta tillfälligt kopieras till snabb lagring som finns nära programmet. Med [Azure Cache for Redis](https://redis.io/) finns den här snabba lagringen i Azure Cache for Redis-minnet, i stället för att den ska läsas in från en disk av en databas.

Azure Cache for Redis kan också användas som ett minnesinternt datastrukturarkiv, en distribuerad icke-relationell databas och en asynkron meddelandekö. Genom att utnyttja Redis-motorns korta svarstider och höga dataflöde förbättras programprestanda.

Azure Cache for Redis ger åtkomst till en säker och dedikerad Cache for Redis som hanteras av Microsoft, finns i Azure och som är tillgänglig för alla program i och utanför Azure.

## <a name="why-use-azure-cache-for-redis"></a>Varför ska du använda Azure Cache for Redis?

Det finns många vanliga mönster där Azure Cache for Redis används till att ge stöd för programarkitekturer eller för att förbättra programprestandan. Här följer exempel på några av de vanligaste:

| Mönster      | Beskrivning                                        |
| ------------ | -------------------------------------------------- |
| [Cachereservera](cache-web-app-cache-aside-leaderboard.md) | Eftersom en databas kan vara stor avråder vi dig från att läsa in en hel databas i en cache. [Cache aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside)-mönstret används ofta för att läsa in dataobjekt i cachen endast efter behov. När ändringar görs i backend-data kan även cachen uppdateras, som distribueras till andra klienter. Dessutom kan en giltighetstid definieras för dataobjekt, och en borttagningsprincip kan användas som triggar inläsningen av datauppdateringar i cachen igen.|
| [Cachelagring av innehåll](cache-aspnet-output-cache-provider.md) | De flesta webbsidor skapas från mallar med sidhuvuden, sidfötter, verktygsfält, menyer och så vidare. De ändras inte särskilt ofta och bör inte genereras dynamiskt. Med en minnesintern cache som Azure Cache for Redis, har dina webbservrar snabb åtkomst till den här typen av statiskt innehåll jämfört med serverdelsdatalager. Det här mönstret minskar bearbetningstiden och serverbelastningen som skulle krävts för att generera innehåll dynamiskt. Detta gör webbservrarna mer dynamiska, och innebär ofta att du kan minska antalet servrar som krävs för att hantera belastningen. Azure Cache for Redis stöder det här mönstret med ASP.NET via Redis Output Cache Provider.|
| [Cachelagring av användarsessioner](cache-aspnet-session-state-provider.md) | Det här mönstret används ofta med shoppingvagnar och annan information om användarhistorik som ett webbprogram kanske vill associera med användarcookies. Att lagra för stora datamängder i en cookie kan påverka prestanda negativt då storleken på cookie-filerna ökar och de skickas och verifieras med varje begäran. En typisk lösning är att använda cookien som en nyckel för att fråga efter data i en backend-databas. Ett mycket snabbare sätt än att interagera med en hel relationsdatabas är att använda en minnesintern cache, som Azure Cache for Redis, till att associera information med en användare. |
| Jobb- och meddelandeköer | När program tar emot begäranden krävs det ofta ytterligare tid för att köra de åtgärder som associeras med begäran. Det är ett vanligt mönster att skjuta upp tidskrävande åtgärder genom att placera dem i en kö, som bearbetas senare och kanske av en annan server. Den här metoden att skjuta upp arbete kallas för att placera uppgifter i kö. Det finns många programvarukomponenter som är utformade för att kunna hantera uppgiftsköer. Azure Cache for Redis fungerar även som en distribuerad kö.|
| Distribuerade transaktioner | Ett vanligt krav för program är att de ska kunna köra en rad kommandon mot ett backend-datalager som en enda åtgärd (atomisk). Alla kommandon måste slutföras, eller så måste alla återställas till det ursprungliga tillståndet. Azure Cache for Redis stöder körning av en grupp kommandon som en enda åtgärd i form av [transaktioner](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Erbjudanden för Azure Cache for Redis

Azure Cache for Redis är tillgänglig på följande nivåer:

| Nivå | Beskrivning |
|---|---|
Basic | Cache för en enda nod. Den här nivån stöder flera minnesstorlekar (250 MB–53 GB). Det här är en perfekt nivå för utveckling och testning av icke-kritiska arbetsbelastningar. Basic-nivån har inget serviceavtal (SLA) |
| Standard | En replikerad cache med en primär/sekundär tvånodskonfiguration som hanteras av Microsoft, med ett serviceavtal med hög tillgänglighet (99,9 %). |
| Premium | Premium-nivån motsvarar Enterprise-nivån. Cacheminnen på Premium-nivå stöder fler funktioner och har högre datagenomflöde med kortare svarstider. Cacheminnen på Premium-nivå distribueras på kraftfullare maskinvara som ger bättre prestanda jämfört med Basic- och Standard-nivån. Den här fördelen innebär att datagenomflödet för en cache med samma storlek är högre på Premium-nivå jämfört med Standard-nivån |

> [!TIP]
> Mer information om storlek, dataflöde och bandbredd för cacheminnen på Premium-nivå finns i [Vanliga frågor och svar om Azure Cache for Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Du kan skala upp din cache till en högre nivå i efterhand, när den redan har skapats. Det går inte att skala ned till en lägre nivå. Stegvisa skalningsanvisningar finns i [Skala Azure Cache for Redis](cache-how-to-scale.md) och [Automatisera en skalningsåtgärd](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparision"></a>Funktionsjämförelse

En detaljerad jämförelse av varje nivå finns på sidan med [prissättning för Azure Cache for Redis](https://azure.microsoft.com/pricing/details/cache/). Följande tabell beskriver några av de funktioner som stöds på respektive nivå:

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

* [Snabbstart för ASP.NET-webbapp](cache-web-app-howto.md) Skapa en enkel ASP.NET-webbapp som använder en Azure Cache for Redis.
* [Snabbstart för .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Skapa en .NET-app som använder en Azure Cache for Redis.
* [Snabbstart för .NET Core](cache-dotnet-core-quickstart.md) Skapa en .NET Core-app som använder en Azure Cache for Redis.
* [Snabbstart för Node.js](cache-nodejs-get-started.md) Skapa en enkel Node.js-app som använder en Azure Cache for Redis.
* [Snabbstart för Java](cache-java-get-started.md) Skapa en enkel Java-app som använder en Azure Cache for Redis.
* [Snabbstart för Python](cache-python-get-started.md) Skapa en Python-app som använder en Azure Cache for Redis.
