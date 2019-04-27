---
title: Hög tillgänglighet i Azure Cosmos DB
description: Den här artikeln beskriver hur Azure Cosmos DB ger hög tillgänglighet
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 1e866560ceab342f08a98ba3db05980a2b0947d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888525"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Hög tillgänglighet med Azure Cosmos DB

Azure Cosmos DB replikerar data transparent i alla de Azure-regioner som är associerade med ditt Cosmos-konto. Cosmos DB använder flera lager för redundans för dina data som visas i följande bild:

![Fysiska partitionering](./media/high-availability/cosmosdb-data-redundancy.png)

- Data i Cosmos-behållare är [vågrätt partitionerad](partitioning-overview.md).

- I varje region är varje partition skyddas av en replikuppsättning med alla skrivningar replikeras och hållbarheten har säkerställts med en majoritet av repliker. Replikerna är fördelade på upp till 10-20-feldomäner.

- Varje partition i alla regioner replikeras. Varje region kan innehåller alla data-partitionerna för en Cosmos-behållare och acceptera skrivningar och leverera läsningar.  

Om ditt Cosmos-konto är fördelade på *N* Azure-regioner, kommer det finnas minst *N* x 4 kopior av dina data. Förutom att tillhandahålla dataåtkomst med låg latens och skala dataflöde för skrivning/läsning över regioner som är associerade med ditt Cosmos-konto, med fler regioner (högre *N*) ytterligare förbättrar tillgängligheten.  

## <a name="slas-for-availability"></a>Serviceavtal för tillgänglighet

Cosmos DB tillhandahåller omfattande serviceavtal som omfattar genomflöde, svarstid på 99: e percentilen, konsekvens och hög tillgänglighet som en globalt distribuerad databas. Tabellen nedan visar garantier för hög tillgänglighet som tillhandahålls av Cosmos DB för konton för enskilda och flera regioner. Konfigurera alltid Cosmos-konton om du vill ha flera Skriv-regioner för hög tillgänglighet.

|Åtgärdstyp  | En enda region |Flera regioner (skriver en enda region)|Flera regioner (flera regioner skriver) |
|---------|---------|---------|-------|
|Skrivningar    | 99.99    |99.99   |99.999|
|Läsningar     | 99.99    |99.999  |99.999|

> [!NOTE]
> I praktiken är faktiska Skriv-tillgänglighet för begränsad föråldring, session, konsekventa prefix och slutlig konsekvensmodeller betydligt högre än de publicerade serviceavtal. Den faktiska lästillgänglighet för alla konsekvensnivåer är betydligt högre än de publicerade serviceavtal.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Hög tillgänglighet med Cosmos DB i händelse av regionala avbrott

Regionala avbrott är inte ovanligt och Azure Cosmos DB gör att din databas är alltid med hög tillgänglighet. Följande information används för att samla in Cosmos DB-beteende under ett avbrott, beroende på konfigurationen för Cosmos-kontot:

- Med Cosmos DB innan skrivning bekräftas till klienten, data är hållbarheten har säkerställts genom att ett kvorum av repliker i den region som accepterar skrivåtgärder.

- Flera regioner konton som konfigurerats med flera – Skriv-regioner ska ha hög tillgänglighet för både skrivningar och läsningar. Regionala redundanstestningar är omedelbara och kräver inte några ändringar från programmet.

- **Konton för flera regioner med en enda-skrivregionen (Skriv region avbrott):** Under ett avbrott på skrivning region förblir dessa konton med hög tillgänglighet för läsningar. Men för skrivningar måste du **”aktivera automatisk redundans”** på din Cosmos-konto till redundans påverkas region till en annan region. Redundans inträffar prioritsordning region du har angett. När den berörda regionen är online igen, icke-replikerade data på den berörda skrivregionen under avbrottet görs tillgängliga via den [konflikter feed](how-to-manage-conflicts.md#read-from-conflict-feed). Program kan läsa konflikterna feed konfliktlösning baserat på logiken programspecifika och skriva uppdaterade data tillbaka till Cosmos-behållare efter behov. När den tidigare berörda skrivregionen återställer blir automatiskt tillgängliga som en läsregionen. Du kan anropa en manuell redundans och konfigurera den berörda regionen som skrivregionen. Igen kan du göra en manuell redundans med hjälp av [Azure CLI eller Azure-portalen](how-to-manage-database-account.md#manual-failover). Det finns **förlora data eller tillgänglighet** före, under eller efter manuell växling vid fel. Programmet fortsätter att ha hög tillgänglighet. 

- **Konton för flera regioner med en enda-skrivregionen (läsregionen avbrott):** Under ett avbrott på läsregionen förblir dessa konton med hög tillgänglighet för läsning och skrivning. Regionen påverkas kopplas ifrån automatiskt från skrivregionen och kommer att markeras offline. Den [Cosmos DB SDK](sql-api-sdk-dotnet.md) omdirigering läser anrop till den nästa tillgängliga regionen i listan över önskad region. Om ingen av regionerna i listan över önskad region är tillgänglig kan återgår anrop automatiskt till att den aktuella skrivregionen. Det krävs inga ändringar i din programkod för att hantera läsregionen avbrott. Så småningom när den berörda regionen är online igen, tidigare berörda läsregionen synkroniseras automatiskt med den aktuella skrivregionen och blir tillgänglig igen för att hantera läsbegäranden. Efterföljande läsningar omdirigeras till den återställda regionen utan några ändringar i din programkod. Under både redundans och ansluta igen i en tidigare misslyckade region, läsningskontinuitet garanterar även i fortsättningen att användas av Cosmos DB.

- En region konton kan förlora tillgänglighet efter ett regionalt strömavbrott. Vi rekommenderar alltid att du ställer in **minst två regioner** (helst skriva minst två regioner) med ditt Cosmos-konto för att säkerställa hög tillgänglighet vid alla tidpunkter.

- Även i en mycket sällsynt och olycklig händelse när Azure-regionen är permanent oåterkalleligt, det finns inga data går förlorade om ditt konto för flera regioner Cosmos är konfigurerad med standard-konsekvensnivå av *starka*. I händelse av en permanent oåterkalleligt skrivregionen för flera regioner Cosmos-konton som konfigurerats med begränsad föråldring, konsekvens, potentiella dataförlustfönstret är begränsad till fönstret föråldring (*K* eller *T*); för sessionen, konsekvent prefix och slutlig konsekvensnivåer är potentiella dataförlustfönstret begränsad till högst fem sekunder.

## <a name="building-highly-available-applications"></a>Att skapa program med hög tillgänglighet

- Konfigurera Cosmos-konto för att omfatta minst två regioner med flera – Skriv-regioner för att säkerställa hög skrivning och lästillgänglighet. Den här konfigurationen ger högsta tillgänglighet, lägsta svarstid och bästa skalbarhet för både läsningar och skrivningar uppbackat av serviceavtal. Mer information finns i så här [konfigurera Cosmos-konto med flera Skriv-regioner](tutorial-global-distribution-sql-api.md).

- För flera regioner Cosmos-konton som är konfigurerade med en enda skrivregionen, [aktivera automatisk redundans med hjälp av Azure CLI eller Azure-portalen](how-to-manage-database-account.md#automatic-failover). När du har aktiverat automatisk redundans när det finns ett regionalt haveri redundansväxlas Cosmos DB automatiskt ditt konto.  

- Även om ditt Cosmos-konto är med hög tillgänglighet kan kanske programmet inte korrekt utformas för att fortsätta att vara tillgänglig. Om du vill testa tillgängligheten slutpunkt till slutpunkt för ditt program med jämna mellanrum anropa den [manuell redundans med hjälp av Azure CLI eller Azure-portalen](how-to-manage-database-account.md#manual-failover), som en del av din Programtestning eller haveriberedskap (DR) tester.

- I en globalt distribuerad databas för miljön att det finns en direkt relation mellan konsekvens nivå och data hållbarhet när det finns ett avbrott på inom hela regionen. När du utvecklar din affärskontinuitetsplan måste du förstå den högsta acceptabla tiden innan programmet är helt återställt efter en avbrottshändelse. Den tid som krävs för ett program för att återställa kallas återställningstid (RTO). Du måste också att förstå den längsta tid för senaste datauppdateringar som programmet kan tolerera att förlora när det återställs efter en avbrottshändelse. Tid då uppdateringar som du kanske har råd att förlora kallas mål för återställningspunkt (RPO). Om du vill se vilket RPO och RTO för Azure Cosmos DB, se [konsekvens nivåer och data hållbarhet](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Nästa steg

Därefter kan du läsa följande artiklar:

* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Skala globalt etablerat dataflöde](scaling-throughput.md)
* [Global distribution – under huven](global-dist-under-the-hood.md)
* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
* [Så här konfigurerar du ditt Cosmos-konto med flera Skriv-regioner](how-to-multi-master.md)
