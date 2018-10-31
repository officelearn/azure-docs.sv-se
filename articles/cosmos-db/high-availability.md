---
title: Hög tillgänglighet i Azure Cosmos DB
description: Den här artikeln beskriver hur Azure Cosmos DB ger hög tillgänglighet
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fdf1bf227ed21111c1aa0754a8423c1ee6aa151b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244145"
---
# <a name="high-availability-in-azure-cosmos-db"></a>Hög tillgänglighet i Azure Cosmos DB

Azure Cosmos DB replikerar data transparent i Azure-regioner som är associerade med ditt Cosmos-konto.  Cosmos DB använder flera lager för redundans för dina data. Som visas i bilden nedan:

- Data i Cosmos-behållare är horisontellt partitionerade.
- I varje region är varje partition skyddas av en replikuppsättning med alla skrivningar replikeras och hållbarheten har säkerställts med en majoritet av repliker. Replikerna är fördelade på upp till 10-20-feldomäner.
- I alla regioner replikeras alla partitioner. Varje region kan innehåller alla data-partitionerna i en Cosmos-behållare och acceptera skrivningar och leverera läsningar.  

![Resursen partitionering](./media/high-availability/figure1.png) 

Om ditt Cosmos-konto är fördelade på N-Azure-regioner, det är minst N x 4 kopior av dina data. Förutom att tillhandahålla snabb svarstid till dina data och skala skrivning och läsningsgenomströmning över regioner som är associerade med ditt Cosmos-konto förbättras med fler regioner (högre N) även tillgängligheten.  

## <a name="availability-slas"></a>Serviceavtal för tillgänglighet  

Cosmos DB tillhandahåller omfattande serviceavtal som omfattar genomflöde, svarstid på 99: e percentilen, konsekvens och hög tillgänglighet som en globalt distribuerad databas. I tabellen nedan beskrivs de garantier som hör till hög tillgänglighet som tillhandahålls av Cosmos DB för konton för enskilda och flera regioner. Högsta tillgänglighet rekommenderar vi konfigurerar dina Cosmos-konton för att ha flera Skriv-regioner.

|Åtgärdstyp  | En enda Region |Flera regioner (skriver en enda region)|Flera regioner (flera regioner skriver|
|---------|---------|---------|-------|
|Skrivningar    | 99,99    |99,99   |99,999|
|Läsningar     | 99,99    |99,999  |99,999|

> [!NOTE]
> I praktiken är faktiska Skriv-tillgänglighet för begränsad föråldring, session, konsekventa prefix och slutlig konsekvensmodeller betydligt högre än de publicerade serviceavtal. Och i praktiken den faktiska lästillgänglighet för alla konsekvensnivåer är betydligt högre än de publicerade serviceavtal.

## <a name="regional-outages"></a>Regionala avbrott

Regionala avbrott är inte ovanligt och Azure Cosmos DB gör att din databas är alltid tillgängliga. Följande fångar Cosmos DB-beteende under ett avbrott, beroende på konfigurationen för Cosmos-kontot: 

- Flera regioner konton som konfigurerats med flera – Skriv-regioner förblir med hög tillgänglighet för både skriver och läser under ett regionalt strömavbrott. Regionala redundanstestningar är omedelbara och kräver inte några ändringar från programmet.

- Flera regioner konton med en enda skrivregionen under ett avbrott för skrivning region förblir med hög tillgänglighet för läsningar. Men för skrivningar måste du ”aktivera automatisk redundans” på ditt Cosmos-konto för Cosmos DB att redundansväxla den berörda regionen till en annan region som är associerat med ditt Cosmos-konto. Redundans inträffar prioritsordning region du har angett. Så småningom när den berörda regionen är online igen, görs icke-replikerade data på den berörda skrivregionen under avbrottet tillgänglig via det orsakar en konflikt feed. Program kan läsa konflikterna feed konfliktlösning baserat på programspecifika logik och skriva uppdaterade data tillbaka till Cosmos-behållare efter behov. När den tidigare berörda skrivregionen återställer blir automatiskt tillgängliga som en läsregionen. Du kan anropa en manuell redundans och få tillbaka den berörda regionen som skrivregionen. Du kan göra en manuell redundans med hjälp av Azure CLI eller Azure-portalen.  

- Flera regioner konton med en enda skrivregionen under ett avbrott på läsregionen förblir med hög tillgänglighet för läsning och skrivning. Regionen påverkas kopplas ifrån automatiskt från skrivregionen och kommer att markeras offline. Cosmos DB SDK omdirigerar Läs anrop till den nästa tillgängliga regionen i listan över önskad region. Om ingen av regionerna i listan över önskad region är tillgänglig kan återgår anrop automatiskt till att den aktuella skrivregionen. Det krävs inga ändringar i din programkod för att hantera läsregionen avbrott. Så småningom när den berörda regionen är online igen kan den tidigare berörda läsregionen synkroniseras automatiskt med den aktuella skrivregionen och blir tillgänglig igen för att betjäna läsningar. Efterföljande läsningar omdirigeras till den återställda regionen utan några ändringar i din programkod. Under både redundans och ansluta igen i en tidigare misslyckade region, fortsätta läsa konsekvensgarantier att att användas av Cosmos DB.

- En region konton kan förlora tillgänglighet i händelse av ett regionalt strömavbrott. Vi rekommenderar starkt för att konfigurera minst två regioner (helst minst två Skriv-regioner) med ditt Cosmos-konto för att säkerställa hög tillgänglighet vid alla tidpunkter.

## <a name="building-highly-available-applications"></a>Att skapa program med hög tillgänglighet

- Konfigurera Cosmos-konto för att omfatta minst två regioner med flera – Skriv-regioner för att säkerställa hög skrivning och lästillgänglighet. Den här konfigurationen säkerställer den bästa tillgänglighet, lägsta svarstid och skalbarhet för både läsningar och skrivningar som understöds av serviceavtal. Se hur du [konfigurera Cosmos-konto med flera Skriv-regioner](tutorial-global-distribution-sql-api.md).

- Aktivera ”automatisk växling vid fel” med hjälp av Azure CLI eller Azure-portalen för flera regioner Cosmos-konton som är konfigurerade med en enda skrivregionen.  När du har aktiverat automatisk redundans när det finns ett regionalt haveri redundansväxlas Cosmos DB automatiskt ditt konto.  

- Även om ditt Cosmos-konto är med hög tillgänglighet kan kanske programmet inte korrekt utformas för att fortsätta att vara tillgänglig. Om du vill kontrollera tillgängligheten för slutpunkt till slutpunkt för ditt program med jämna mellanrum anropa ”manuell redundans” med hjälp av Azure CLI eller Azure-portalen som en del av din Programtestning eller haveriberedskap (DR)-tester. Mer information finns i ändra regionala prioriteringar för Cosmos-konto).  

## <a name="next-steps"></a>Nästa steg

Därefter kan du lära dig om att skala dataflöde i följande artikel:

* [Skalning av dataflöden](scaling-throughput.md)

* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)

* [Skala dataflöde globalt](scaling-throughput.md)

* [Global distribution – under huven](global-dist-under-the-hood.md)

* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)


