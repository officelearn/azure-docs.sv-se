---
title: Vad är Azure Cache for Redis?
description: Lär dig mer om Azure Cache för Redis för att aktivera cache-aside, innehållscachelagring, cachelagring av användarsessioner, jobb- och meddelandeköer och distribuerade transaktioner.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: 38936000e426d560237295105b5456429d9ae16d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79126361"
---
# <a name="azure-cache-for-redis-description"></a>Beskrivning av Azure Cache for Redis

Azure Cache för Redis tillhandahåller ett minnesbaserat datalager baserat på programvaran [Redis](https://redis.io/)med öppen källkod. När Redis används som cache förbättrar du prestanda och skalbarhet för system som är starkt beroende av backend-datalager. Prestanda förbättras genom att kopiera data som används ofta till snabb lagring som finns nära programmet. Med Azure Cache för Redis finns den här snabba lagringen i minnet i stället för att läsas in från disken av en databas.

Azure Cache för Redis kan användas som ett minnesbaserat datastrukturlager, en distribuerad icke-relationell databas och en meddelandemäklare. Genom att utnyttja Redis-motorns korta svarstider och höga dataflöde förbättras programprestanda.

Azure Cache för Redis ger åtkomst till en säker, dedikerad Redis-cache. Azure Cache för Redis hanteras av Microsoft, som finns i Azure och är tillgängligt för alla program inom eller utanför Azure.

## <a name="using-azure-cache-for-redis"></a>Använda Azure Cache för Redis

Azure Cache för Redis förbättrar programprestanda genom att stödja vanliga programarkitekturmönster. Här följer exempel på några av de vanligaste:

| Mönster      | Beskrivning                                        |
| ------------ | -------------------------------------------------- |
| [Cachereservera](cache-web-app-cache-aside-leaderboard.md) | Databaser är ofta för stora för att läsas in direkt i en cache. Det är vanligt att använda [cache-aside mönster](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) för att läsa in data i cacheminnet endast efter behov. När systemet gör ändringar i data kan systemet också uppdatera cacheminnet, som sedan distribueras till andra klienter. Dessutom kan systemet ange en förfallodatum för data eller använda en vräkningsprincip för att utlösa datauppdateringar till cachen.|
| [Cachelagring av innehåll](cache-aspnet-output-cache-provider.md) | Många webbsidor genereras från mallar som använder statiskt innehåll som sidhuvuden, sidfötter, banderoller. Dessa statiska objekt bör inte ändras ofta. Om du använder en cache i minnet får du snabb åtkomst till statiskt innehåll jämfört med backend-datalager. Det här mönstret minskar bearbetningstiden och serverbelastningen, vilket gör att webbservrarna blir mer responsiva. Det kan göra att du kan minska antalet servrar som behövs för att hantera belastningar. Azure Cache för Redis tillhandahåller Redis Output Cache Provider för att stödja det här mönstret med ASP.NET.|
| [Cachelagring av användarsessioner](cache-aspnet-session-state-provider.md) | Det här mönstret används ofta med kundvagnar och andra användarhistorikdata som ett webbprogram kanske vill associera med användarcookies. Att lagra för stora datamängder i en cookie kan påverka prestanda negativt då storleken på cookie-filerna ökar och de skickas och verifieras med varje begäran. En typisk lösning använder cookien som en nyckel för att fråga data i en databas. Ett mycket snabbare sätt än att interagera med en hel relationsdatabas är att använda en minnesintern cache, som Azure Cache for Redis, till att associera information med en användare. |
| Jobb- och meddelandeköer | Program lägger ofta till uppgifter i en kö när de åtgärder som är associerade med begäran tar tid att köra. Längre driftåtgärder köas för att bearbetas i följd, ofta av en annan server.  Den här metoden att skjuta upp arbete kallas för att placera uppgifter i kö. Azure Cache för Redis tillhandahåller en distribuerad kö för att aktivera det här mönstret i ditt program.|
| Distribuerade transaktioner | Program kräver ibland en serie kommandon mot ett datalager för säkerhetskopiering för att köras som en enda atomåtgärd. Alla kommandon måste slutföras, eller så måste alla återställas till det ursprungliga tillståndet. Azure Cache för Redis stöder körning av en grupp kommandon som en enda [transaktion](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Erbjudanden för Azure Cache for Redis

Azure Cache for Redis är tillgänglig på följande nivåer:

| Nivå | Beskrivning |
|---|---|
Basic | Cache för en enda nod. Den här nivån stöder flera minnesstorlekar (250 MB - 53 GB) och är idealisk för utveckling/test och icke-kritiska arbetsbelastningar. Basic-nivån har inget serviceavtal (SLA) |
| Standard | En replikerad cache i en konfiguration med två nod, primär/sekundär, som hanteras av Azure med ett SLA med hög tillgänglighet (99,9 %) |
| Premium | Premium-nivån är den Enterprise-klara nivån. Cacheminnen på Premium-nivå stöder fler funktioner och har högre datagenomflöde med kortare svarstider. Cacheminnen på Premium-nivå distribueras på kraftfullare maskinvara som ger bättre prestanda jämfört med Basic- och Standard-nivån. Den här fördelen innebär att dataflödet för en cache av samma storlek kommer att vara högre i Premium jämfört med standardnivå. |

> [!TIP]
> Mer information om storlek, dataflöde och bandbredd för cacheminnen på Premium-nivå finns i [Vanliga frågor och svar om Azure Cache for Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Du kan skala cachen till en högre nivå när den har skapats. Det går inte att skala ned till en lägre nivå. Stegvisa skalningsanvisningar finns i [Skala Azure Cache for Redis](cache-how-to-scale.md) och [Automatisera en skalningsåtgärd](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Jämför funktioner

En detaljerad jämförelse av varje nivå finns på sidan med [prissättning för Azure Cache for Redis](https://azure.microsoft.com/pricing/details/cache/). Följande tabell beskriver några av de funktioner som stöds på respektive nivå:

| Funktionsbeskrivning | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-datapersistens](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-kluster](cache-how-to-premium-clustering.md) |✔|-|-|
| [Säkerhet via brandväggsregler](cache-configure.md#firewall) |✔|✔|✔|
| Kryptering under överföring |✔|✔|✔|
| [Förbättrad säkerhet och isolering med VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importera/exportera](cache-how-to-import-export-data.md) |✔|-|-|
| [Schemalagda uppdateringar](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Geo-replikering](cache-how-to-geo-replication.md) |✔|-|-|
| [Starta om](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Nästa steg

* [Snabbstart för ASP.NET-webbapp](cache-web-app-howto.md) Skapa en enkel ASP.NET-webbapp som använder en Azure Cache for Redis.
* [Snabbstart för .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Skapa en .NET-app som använder en Azure Cache for Redis.
* [Snabbstart för .NET Core](cache-dotnet-core-quickstart.md) Skapa en .NET Core-app som använder en Azure Cache for Redis.
* [Snabbstart för Node.js](cache-nodejs-get-started.md) Skapa en enkel Node.js-app som använder en Azure Cache for Redis.
* [Snabbstart för Java](cache-java-get-started.md) Skapa en enkel Java-app som använder en Azure Cache for Redis.
* [Snabbstart för Python](cache-python-get-started.md) Skapa en Python-app som använder en Azure Cache for Redis.
