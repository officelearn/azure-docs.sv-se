---
title: Hög tillgänglighet i Azure Cosmos DB
description: Den här artikeln beskriver hur Azure Cosmos DB ger hög tillgänglighet
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 2/13/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: b5e99b421e66f087a1793f5301736e192ef75c08
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311247"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Hög tillgänglighet med Azure Cosmos DB

Azure Cosmos DB replikerar data transparent i alla de Azure-regioner som är associerade med ditt Cosmos-konto. Cosmos DB använder flera lager för redundans för dina data som visas i följande bild:

![Fysiska partitionering](./media/high-availability/cosmosdb-data-redundancy.png)

- Data i Cosmos-behållare är horisontellt partitionerade.

- I varje region är varje partition skyddas av en replikuppsättning med alla skrivningar replikeras och hållbarheten har säkerställts med en majoritet av repliker. Replikerna är fördelade på upp till 10-20-feldomäner.

- Varje partition i alla regioner replikeras. Varje region kan innehåller alla data-partitionerna för en Cosmos-behållare och acceptera skrivningar och leverera läsningar.  

Om ditt Cosmos-konto är fördelade på N-Azure-regioner, det är minst N x fyra kopior av dina data. Förutom att tillhandahålla dataåtkomst med låg latens och skala dataflöde för skrivning/läsning över regioner som är associerade med ditt Cosmos-konto, förbättras med fler regioner (högre N) även tillgängligheten.  

## <a name="slas-for-availability"></a>Serviceavtal för tillgänglighet

Cosmos DB tillhandahåller omfattande serviceavtal som omfattar genomflöde, svarstid på 99: e percentilen, konsekvens och hög tillgänglighet som en globalt distribuerad databas. Tabellen nedan visar garantier för hög tillgänglighet som tillhandahålls av Cosmos DB för konton för enskilda och flera regioner. Konfigurera Cosmos-konton om du vill ha flera Skriv-regioner för hög tillgänglighet.

|Åtgärdstyp  | En enda region |Flera regioner (skriver en enda region)|Flera regioner (flera regioner skriver) |
|---------|---------|---------|-------|
|Skrivningar    | 99.99    |99.99   |99.999|
|Läsningar     | 99.99    |99.999  |99.999|

> [!NOTE]
> I praktiken är faktiska Skriv-tillgänglighet för begränsad föråldring, session, konsekventa prefix och slutlig konsekvensmodeller betydligt högre än de publicerade serviceavtal. Den faktiska lästillgänglighet för alla konsekvensnivåer är betydligt högre än de publicerade serviceavtal.

## <a name="high-availability-with-cosmos-db-in-the-face-of-regional-outages"></a>Hög tillgänglighet med Cosmos DB för att hantera regionala avbrott

Regionala avbrott är inte ovanligt och Azure Cosmos DB gör att din databas är alltid tillgängliga. Följande information avbilda Cosmos DB-beteende under ett avbrott, beroende på ditt konto Cosmos-konfiguration:

- Med Cosmos DB innan skrivning bekräftas till klienten, data är hållbarheten har säkerställts genom att ett kvorum av repliker i den region som accepterar skrivåtgärder.

- Flera regioner konton som konfigurerats med flera – Skriv-regioner ska ha hög tillgänglighet för både skrivningar och läsningar. Regionala redundanstestningar är omedelbara och kräver inte några ändringar från programmet.

- Konton för flera regioner med en enda skrivregionen: Under ett avbrott på skrivning region förblir dessa konton med hög tillgänglighet för läsningar. Men för skrivningar måste du ”aktivera automatisk redundans” på Cosmos-konto för att redundansväxla den berörda regionen till en annan region som är associerade. Redundans inträffar prioritsordning region du har angett. Så småningom när den berörda regionen är online igen, görs icke-replikerade data på den berörda skrivregionen under avbrottet tillgänglig via det orsakar en konflikt feed. Program kan läsa konflikterna feed konfliktlösning baserat på programspecifika logik och skriva uppdaterade data tillbaka till Cosmos-behållare efter behov. När den tidigare berörda skrivregionen återställer blir automatiskt tillgängliga som en läsregionen. Du kan anropa en manuell redundans och konfigurera den berörda regionen som skrivregionen. Du kan göra en manuell redundans med hjälp av [Azure CLI eller Azure-portalen](how-to-manage-database-account.md#manual-failover). Det finns **förlora data eller tillgänglighet** före, under eller efter manuell växling vid fel. Programmet fortsätter att ha hög tillgänglighet. 

- Konton för flera regioner med en enda skrivregionen: Under ett avbrott på läsregionen förblir dessa konton med hög tillgänglighet för läsning och skrivning. Regionen påverkas kopplas ifrån automatiskt från skrivregionen och kommer att markeras offline. Cosmos DB SDK omdirigerar Läs anrop till den nästa tillgängliga regionen i listan över önskad region. Om ingen av regionerna i listan över önskad region är tillgänglig kan återgår anrop automatiskt till att den aktuella skrivregionen. Det krävs inga ändringar i din programkod för att hantera läsregionen avbrott. Så småningom när den berörda regionen är online igen, tidigare berörda läsregionen synkroniseras automatiskt med den aktuella skrivregionen och blir tillgänglig igen för att hantera läsbegäranden. Efterföljande läsningar omdirigeras till den återställda regionen utan några ändringar i din programkod. Under både redundans och ansluta igen i en tidigare misslyckade region, fortsätta läsa konsekvensgarantier att att användas av Cosmos DB.

- En region konton kan förlora tillgänglighet efter ett regionalt strömavbrott. Vi rekommenderar att du ställa in minst två regioner (helst minst två Skriv-regioner) med ditt Cosmos-konto för att garantera hög tillgänglighet vid alla tidpunkter.

- Även i en mycket sällsynt och olycklig händelse när Azure-regionen är permanent oåterkalleligt, finns det ingen potentiell dataförlust om ditt konto för flera regioner Cosmos är konfigurerad med standard-konsekvensnivå av stark. I händelse av en permanent oåterkalleligt skrivregionen för flera regioner Cosmos-konton som konfigurerats med begränsad föråldring, konsekvens, är potentiella dataförlustfönstret begränsad till föråldring-fönstret. för sessionen, konsekvent prefix och slutlig konsekvensnivåer är potentiella dataförlustfönstret begränsad till högst fem sekunder.

## <a name="building-highly-available-applications"></a>Att skapa program med hög tillgänglighet

- Konfigurera Cosmos-konto för att omfatta minst två regioner med flera – Skriv-regioner för att säkerställa hög skrivning och lästillgänglighet. Den här konfigurationen tillhandahåller tillgänglighet, lägsta svarstid och skalbarhet för både läsningar och skrivningar uppbackat av serviceavtal. Mer information finns i så här [konfigurera Cosmos-konto med flera Skriv-regioner](tutorial-global-distribution-sql-api.md). För att konfigurera multimaster i dina program, se [så här konfigurerar du multimaster](how-to-multi-master.md).

- För flera regioner Cosmos-konton som är konfigurerade med en enda skrivregionen, [aktivera automatisk redundans med hjälp av Azure CLI eller Azure-portalen](how-to-manage-database-account.md#automatic-failover). När du har aktiverat automatisk redundans när det finns ett regionalt haveri redundansväxlas Cosmos DB automatiskt ditt konto.  

- Även om ditt Cosmos-konto är med hög tillgänglighet kan kanske programmet inte korrekt utformas för att fortsätta att vara tillgänglig. Om du vill testa tillgängligheten slutpunkt till slutpunkt för ditt program med jämna mellanrum anropa den [manuell redundans med hjälp av Azure CLI eller Azure-portalen](how-to-manage-database-account.md#manual-failover), som en del av din Programtestning eller haveriberedskap (DR) tester.

- I en globalt distribuerad databas för miljön att det finns en direkt relation mellan konsekvens nivå och data hållbarhet när det finns ett avbrott på inom hela regionen. När du utvecklar din affärskontinuitetsplan måste du förstå den högsta acceptabla tiden innan programmet är helt återställt efter en avbrottshändelse. Den tid som krävs för ett program för att återställa kallas återställningstid (RTO). Du måste också att förstå den längsta tid för senaste datauppdateringar som programmet kan tolerera att förlora när det återställs efter en avbrottshändelse. Tid då uppdateringar som du kanske har råd att förlora kallas mål för återställningspunkt (RPO). Om du vill se vilket RPO och RTO för Azure Cosmos DB, se [konsekvens nivåer och data hållbarhet](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Nästa steg

Därefter kan du lära dig om att skala dataflöde i följande artikel:

* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Skala globalt etablerat dataflöde](scaling-throughput.md)
* [Global distribution – under huven](global-dist-under-the-hood.md)
* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
* [Så här konfigurerar du multimaster i dina program](how-to-multi-master.md)
