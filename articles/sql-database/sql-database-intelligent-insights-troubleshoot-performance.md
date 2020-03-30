---
title: Felsöka prestandaproblem med Intelligent Insights
description: Intelligent Insights hjälper dig att felsöka prestandaproblem i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 739bba7ed9ab4770a762c08fccc422ce048ae11d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214082"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Felsöka prestandaproblem i Azure SQL Database med Intelligent Insights

Den här sidan innehåller information om prestandaproblem i Azure SQL Database och hanterade instanser som identifieras via resursloggen [intelligenta insikter.](sql-database-intelligent-insights.md) Mått och resursloggar kan strömmas till [Azure Monitor-loggar,](../azure-monitor/insights/azure-sql.md) [Azure Event Hubs,](../azure-monitor/platform/resource-logs-stream-event-hubs.md) [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage)eller en tredjepartslösning för anpassade DevOps-aviseringar och rapporteringsfunktioner.

> [!NOTE]
> En snabb felsökningsguide för SQL Database-prestanda med hjälp av Intelligenta insikter finns i det [rekommenderade flödesflödet](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) för felsökning i det här dokumentet.

## <a name="detectable-database-performance-patterns"></a>Detekterbara databasprestandamönster

Intelligent Insights identifierar automatiskt prestandaproblem med databaser i Azure SQL Database baserat på väntetider för körning av frågor, fel eller time-outs. Intelligent Insights-utdata har identifierats i prestandamönster i SQL Database-resursloggen. Detekterbara prestandamönster sammanfattas i tabellen nedan.

| Identifierbara prestandamönster | Beskrivning för Azure SQL Database och elastiska pooler | Beskrivning för databaser i hanterad instans |
| :------------------- | ------------------- | ------------------- |
| [Nå resursgränser](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Förbrukning av tillgängliga resurser (DU:er), databasarbetaretrådar eller databasinloggningssessioner som är tillgängliga på den övervakade prenumerationen har nått gränser. Detta påverkar SQL Database-prestanda. | Förbrukning av CPU-resurser når gränser för hanterade instanser. Detta påverkar databasens prestanda. |
| [Arbetsbelastningen ökar](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Arbetsbelastningsökning eller kontinuerlig ackumulering av arbetsbelastning i databasen upptäcktes. Detta påverkar SQL Database-prestanda. | Arbetsbelastningsökning har upptäckts. Detta påverkar databasens prestanda. |
| [Minnestryck](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Arbetare som begärt minnesbidrag måste vänta på minnesallokeringar för statistiskt signifikant tid, eller en ökad ackumulering av arbetare som begärde minnesbidrag finns. Detta påverkar SQL Database-prestanda. | Arbetare som har begärt minnesbidrag väntar på minnesallokeringar under en statistiskt signifikant tid. Detta påverkar databasens prestanda. |
| [Låsning](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Överdriven databaslåsning upptäcktes som påverkar SQL Database-prestanda. | Överdriven databaslåsning upptäcktes som påverkade databasens prestanda. |
| [Ökad MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Den maximala graden av parallellism alternativ (MAXDOP) har ändrats påverkar frågan körning effektivitet. Detta påverkar SQL Database-prestanda. | Den maximala graden av parallellism alternativ (MAXDOP) har ändrats påverkar frågan körning effektivitet. Detta påverkar databasens prestanda. |
| [Pagelatch påstående](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Flera trådar försöker samtidigt komma åt samma minneskortsbuffertsidor, vilket resulterar i ökade väntetider och orsakar pagelatch-konkurrens. Detta påverkar SQL-databasen prestanda. | Flera trådar försöker samtidigt komma åt samma minneskortsbuffertsidor, vilket resulterar i ökade väntetider och orsakar pagelatch-konkurrens. Detta påverkar databasen prestanda. |
| [Index saknas](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Index som saknades upptäcktes som påverkar SQL-databasens prestanda. | Index som saknades upptäcktes som påverkade databasens prestanda. |
| [Ny fråga](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Ny fråga upptäcktes som påverkar den övergripande SQL Database-prestandan. | Ny fråga upptäcktes som påverkar den övergripande databasens prestanda. |
| [Ökad väntestatistik](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Ökade väntetider för databasen upptäcktes som påverkar SQL-databasens prestanda. | Ökade väntetider för databasen upptäcktes som påverkade databasens prestanda. |
| [TempDB Påstående](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Flera trådar försöker komma åt samma TempDB-resurs som orsakar en flaskhals. Detta påverkar SQL Database-prestanda. | Flera trådar försöker komma åt samma TempDB-resurs som orsakar en flaskhals. Detta påverkar databasens prestanda. |
| [Brist på elastisk pool DTU](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Brist på tillgängliga eDFUs i den elastiska poolen påverkar SQL Database-prestanda. | Inte tillgängligt för hanterad instans eftersom den använder vCore-modellen. |
| [Planera regression](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Ny plan, eller en ändring i arbetsbelastningen för en befintlig plan upptäcktes. Detta påverkar SQL Database-prestanda. | Ny plan, eller en ändring i arbetsbelastningen för en befintlig plan upptäcktes. Detta påverkar databasens prestanda. |
| [Ändring av konfigurationsvärde för databasomfattning](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Konfigurationsändring i SQL-databasen upptäcktes som påverkar databasens prestanda. | Konfigurationsändring i databasen upptäcktes som påverkar databasens prestanda. |
| [Långsam klient](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Långsam programklient kan inte förbruka utdata från databasen tillräckligt snabbt. Detta påverkar SQL Database-prestanda. | Långsam programklient kan inte förbruka utdata från databasen tillräckligt snabbt. Detta påverkar databasens prestanda. |
| [Nedgradering av prisnivån](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Nedgraderingsåtgärden för prisnivå minskade tillgängliga resurser. Detta påverkar SQL Database-prestanda. | Nedgraderingsåtgärden för prisnivå minskade tillgängliga resurser. Detta påverkar databasens prestanda. |

> [!TIP]
> Aktivera automatisk justering av [Azure SQL Database](sql-database-automatic-tuning.md)för kontinuerlig prestandaoptimering av SQL Database . Den här unika funktionen i inbyggd INFORMATION i SQL Database övervakar kontinuerligt SQL-databasen, justerar automatiskt index och tillämpar korrigeringar av frågekörningsplan.
>

I följande avsnitt beskrivs detekterbara prestandamönster mer i detalj.

## <a name="reaching-resource-limits"></a>Nå resursgränser

### <a name="what-is-happening"></a>Vad händer

Det här detekterbara prestandamönstret kombinerar prestandaproblem som är relaterade till att nå tillgängliga resursgränser, arbetsgränser och sessionsgränser. När det här prestandaproblemet har identifierats anger ett beskrivningsfält i diagnostikloggen om prestandaproblemet är relaterat till resurs-, arbets- eller sessionsgränser.

Resurser i SQL-databasen refereras vanligtvis till [DTU-](sql-database-what-is-a-dtu.md) eller [vCore-resurser.](sql-database-service-tiers-vcore.md) Mönstret för att nå resursgränser känns igen när identifierade frågeprestandaförsämring orsakas av att någon av de uppmätta resursgränserna nårs.

Resursen begränsar resursen anger antalet tillgängliga samtidiga inloggningar till SQL-databasen. Det här prestandamönstret känns igen när program som är anslutna till SQL-databaserna har nått antalet tillgängliga samtidiga inloggningar till databasen. Om program försöker använda fler sessioner än vad som är tillgängligt i en databas påverkas frågeprestandan.

Att nå arbetargränser är ett specifikt fall av att nå resursgränser eftersom tillgängliga arbetare inte räknas i DTU- eller vCore-användningen. Att nå arbetargränser för en databas kan orsaka ökningen av resursspecifika väntetider, vilket resulterar i försämring av frågeprestanda.

### <a name="troubleshooting"></a>Felsökning

Frågan diagnostikloggutdata har av frågor som påverkade prestanda- och resursförbrukningsprocenten. Du kan använda den här informationen som utgångspunkt för att optimera databasarbetsbelastningen. Du kan särskilt optimera de frågor som påverkar prestandaförsämringen genom att lägga till index. Eller så kan du optimera program med en jämnare arbetsbelastningsfördelning. Om du inte kan minska arbetsbelastningar eller göra optimeringar kan du överväga att öka prisnivån för din SQL-databasprenumeration för att öka mängden tillgängliga resurser.

Om du har nått de tillgängliga sessionsgränserna kan du optimera dina program genom att minska antalet inloggningar som görs i databasen. Om du inte kan minska antalet inloggningar från dina program till databasen kan du överväga att öka prisnivån i databasen. Du kan också dela upp och flytta databasen till flera databaser för en mer balanserad arbetsbelastningsfördelning.

Fler förslag på hur du löser sessionsgränser finns i [Så här hanterar du gränserna för maximala inloggningar](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/)i SQL Database . Se [Översikt över resursbegränsningar på en SQL Database-server](sql-database-resource-limits-database-server.md) för information om begränsningar på server- och prenumerationsnivå.

## <a name="workload-increase"></a>Ökning av arbetsbelastningen

### <a name="what-is-happening"></a>Vad händer

Det här prestandamönstret identifierar problem som orsakas av en arbetsbelastningsökning eller, i dess allvarligare form, en arbetsbelastningshöguppdyrning.

Den här identifieringen görs genom en kombination av flera mått. Det grundläggande måttet som mäts identifierar en ökning av arbetsbelastningen jämfört med den tidigare arbetsbelastningens baslinje. Den andra formen av identifiering baseras på att mäta en stor ökning av aktiva arbetstrådar som är tillräckligt stora för att påverka frågeprestanda.

I sin allvarligare form kan arbetsbelastningen kontinuerligt samlas upp på grund av SQL-databasens oförmåga att hantera arbetsbelastningen. Resultatet är en ständigt växande arbetsbelastningsstorlek, vilket är arbetsbelastningsupphögningsvillkoret. På grund av det här villkoret växer den tid som arbetsbelastningen väntar på körningen. Det här villkoret representerar ett av de allvarligaste prestandaproblemen för databasen. Det här problemet identifieras genom övervakning av ökningen av antalet avbrutna arbetstrådar.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen uttömer antalet frågor vars körning har ökat och frågehhen för frågan med det största bidraget till arbetsbelastningsökningen. Du kan använda den här informationen som utgångspunkt för att optimera arbetsbelastningen. Frågan som identifieras som den största bidragsgivaren till arbetsbelastningsökningen är särskilt användbar som startpunkt.

Du kan överväga att fördela arbetsbelastningarna jämnare till databasen. Överväg att optimera frågan som påverkar prestanda genom att lägga till index. Du kan också distribuera din arbetsbelastning mellan flera databaser. Om dessa lösningar inte är möjliga kan du överväga att öka prisnivån för din SQL-databasprenumeration för att öka mängden tillgängliga resurser.

## <a name="memory-pressure"></a>Minnestryck

### <a name="what-is-happening"></a>Vad händer

Det här prestandamönstret indikerar nedbrytning i den aktuella databasens prestanda som orsakas av minnestryck, eller i dess allvarligare form ett minne pile-up villkor, jämfört med den senaste sju dagars prestanda baslinjen.

Minnestryck betecknar ett prestandatillstånd där det finns ett stort antal arbetstrådar som begär minnesbidrag i SQL-databasen. Den höga volymen orsakar ett tillstånd för hög minnesanvändning där SQL-databasen inte kan allokera minne effektivt till alla arbetare som begär det. En av de vanligaste orsakerna till det här problemet är relaterad till mängden minne som är tillgängligt för SQL-databasen å ena sidan. Å andra sidan orsakar en ökning av arbetsbelastningen ökningen av arbetstrådar och minnestrycket.

Den allvarligare formen av minnestryck är minnet pile-up villkor. Det här villkoret anger att ett större antal arbetstrådar begär minnesbidrag än det finns frågor som frigör minnet. Det här antalet arbetstrådar som begär minnesbidrag kan också öka kontinuerligt (stapla upp) eftersom SQL-databasmotorn inte kan allokera minne tillräckligt effektivt för att möta efterfrågan. Minnesupphögningsvillkoret representerar ett av de allvarligaste prestandaproblemen för databasen.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen matar ut minnesobjektarkivets information med expediten (det vill än arbetartråd) markerat som den högsta orsaken till hög minnesanvändning och relevanta tidsstämplar. Du kan använda den här informationen som grund för felsökning.

Du kan optimera eller ta bort frågor som är relaterade till kontorister med den högsta minnesanvändningen. Du kan också se till att du inte frågar efter data som du inte tänker använda. God praxis är att alltid använda en WHERE-sats i dina frågor. Dessutom rekommenderar vi att du skapar icke-grupperade index för att söka efter data i stället för att skanna dem.

Du kan också minska arbetsbelastningen genom att optimera eller distribuera den över flera databaser. Eller så kan du distribuera din arbetsbelastning mellan flera databaser. Om dessa lösningar inte är möjliga kan du överväga att öka prisnivån för sql-databasprenumerationen för att öka mängden minnesresurser som är tillgängliga för databasen.

Ytterligare felsökningsförslag finns i [Meditation för minnesbidrag: Den mystiska SQL Server-minneskonsumenten med många namn](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994).

## <a name="locking"></a>Låsning

### <a name="what-is-happening"></a>Vad händer

Det här prestandamönstret indikerar försämring i den aktuella databasens prestanda där överdriven databaslåsning upptäcks jämfört med den senaste sjudagarsprestandabaslinjen.

I moderna RDBMS är låsning viktigt för att implementera flertrådade system där prestanda maximeras genom att köra flera samtidiga arbetare och parallella databastransaktioner där det är möjligt. Låsning i det här sammanhanget refererar till den inbyggda åtkomstmekanismen där endast en enskild transaktion uteslutande kan komma åt de rader, sidor, tabeller och filer som krävs och inte konkurrerar med en annan transaktion för resurser. När transaktionen som låste resurserna för användning görs med dem frigörs låset på dessa resurser, vilket gör att andra transaktioner kan komma åt nödvändiga resurser. Mer information om låsning finns [i Lås i databasmotorn](https://msdn.microsoft.com/library/ms190615.aspx).

Om transaktioner som utförs av SQL-motorn väntar under längre tidsperioder på att komma åt resurser som är låsta för användning, medför den här väntetiden avmattningen i prestanda för körning av arbetsbelastningen.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen utdata låsningsinformation som du kan använda som grund för felsökning. Du kan analysera de rapporterade blockeringsfrågorna, det vill säga de frågor som introducerar låsningsprestandaförsämringen och ta bort dem. I vissa fall kan du lyckas optimera blockeringsfrågorna.

Det enklaste och säkraste sättet att minska problemet är att hålla transaktioner korta och minska låsavtrycket för de dyraste frågorna. Du kan dela upp en stor grupp åtgärder i mindre operationer. God praxis är att minska frågelåsavtrycket genom att göra frågan så effektiv som möjligt. Minska stora genomsökningar eftersom de ökar risken för dödlägen och påverkar databasens totala prestanda negativt. För identifierade frågor som orsakar låsning kan du skapa nya index eller lägga till kolumner i det befintliga indexet för att undvika att tabellen genomsöker.

Fler förslag finns i [Så här löser du blockeringsproblem som orsakas av låseskalering i SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Ökad MAXDOP

### <a name="what-is-happening"></a>Vad händer

Det här detekterbara prestandamönstret anger ett villkor där en vald frågekörningsplan var parallelliserad mer än den borde ha varit. SQL Database-frågeoptimeraren kan förbättra arbetsbelastningens prestanda genom att köra frågor parallellt för att snabba upp saker där det är möjligt. I vissa fall spenderar parallella arbetare som bearbetar en fråga mer tid på att vänta på varandra för att synkronisera och sammanfoga resultat jämfört med att köra samma fråga med färre parallella arbetare, eller till och med i vissa fall jämfört med en enda arbetstråd.

Expertsystemet analyserar den aktuella databasens prestanda jämfört med baslinjeperioden. Den avgör om en fråga som körs långsammare än tidigare eftersom frågekörningsplanen är mer parallelliserad än den borde vara.

Maxdop-serverkonfigurationsalternativet i SQL Database används för att styra hur många CPU-kärnor som kan användas för att köra samma fråga parallellt.

### <a name="troubleshooting"></a>Felsökning

Frågan diagnostikloggutdata hashar relaterade till frågor för vilka varaktigheten för körningen ökade eftersom de var parallelliserade mer än de borde ha varit. Loggen matar också ut CXP väntetider. Den här gången representerar den tid en enda organisatör /koordinatortråd (tråd 0) väntar på att alla andra trådar ska slutföras innan resultaten slås samman och går vidare. Dessutom utdataar diagnostikloggen väntetiderna för att de problem som inte presterade väntade i körningen totalt. Du kan använda den här informationen som grund för felsökning.

Först optimera eller förenkla komplexa frågor. God praxis är att dela upp långa batchjobb i mindre. Se dessutom till att du har skapat index som stöder dina frågor. Du kan också manuellt framtvinga maximal grad av parallellism (MAXDOP) för en fråga som har flaggats som dålig prestanda. Hur du konfigurerar den här åtgärden med hjälp av T-SQL finns i [Konfigurera konfigurationsalternativet MAXDOP-server](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Om du ställer in maxdop-serverkonfigurationsalternativet till noll (0) som standardvärde anges att SQL Database kan använda alla tillgängliga logiska CPU-kärnor för att parallellisera trådar för att köra en enskild fråga. Om maxdop anges en (1) anges att endast en kärna kan användas för en enda frågekörning. I praktiken innebär detta att parallellismen är avstängd. Beroende på fall-per-fall-basis, tillgängliga kärnor till databasen och diagnostiklogginformation kan du justera MAXDOP-alternativet till antalet kärnor som används för parallell körning av frågor som kan lösa problemet i ditt fall.

## <a name="pagelatch-contention"></a>Pagelatch Påstående

### <a name="what-is-happening"></a>Vad händer

Det här prestandamönstret anger den aktuella prestandaförsämringen för databasarbetsbelastningen på grund av pagelatch-konkurrens jämfört med den senaste sjudagarsarbetsbelastningens baslinje.

Lås är lätta synkroniseringsmekanismer som används av SQL Database för att aktivera flertrådning. De garanterar konsekvens i minnesstrukturer som innehåller index, datasidor och andra interna strukturer.

Det finns många typer av spärrar som finns tillgängliga i SQL-databasen. För enkelhetens skull används buffertlås för att skydda minnessidor i buffertpoolen. IO-spärrar används för att skydda sidor som ännu inte har lästs in i buffertpoolen. När data skrivs till eller läses från en sida i buffertpoolen måste en arbetstråd hämta en buffertspärr för sidan först. När en arbetstråd försöker komma åt en sida som inte redan är tillgänglig i buffertpoolen i minnet görs en I/O-begäran för att läsa in nödvändig information från lagringen. Denna sekvens av händelser indikerar en allvarligare form av prestandaförsämring.

Konkurrens på sidan spärrar uppstår när flera trådar samtidigt försöker hämta lås på samma minnesstruktur, vilket introducerar en ökad väntetid för att fråga körning. När det gäller pagelatch IO påstående, när data måste nås från lagring, är denna väntetid ännu större. Det kan påverka arbetsbelastningens prestanda avsevärt. Pagelatch påstående är det vanligaste scenariot med trådar som väntar på varandra och konkurrerar om resurser på flera CPU-system.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen utdata pagelatch påstående detaljer. Du kan använda den här informationen som grund för felsökning.

Eftersom en pagelatch är en intern kontrollmekanism i SQL Database avgör den automatiskt när de ska användas. Programbeslut, inklusive schemadesign, kan påverka pagelatch-beteendet på grund av låsens deterministiska beteende.

En metod för hantering av spärrkonkurrens är att ersätta en sekventiell indexnyckel med en icke-sekventiell nyckel för att fördela skären jämnt över ett indexintervall. Vanligtvis distribuerar en inledande kolumn i indexet arbetsbelastningen proportionellt. En annan metod att tänka på är tabellpartitionering. Att skapa ett hash-partitioneringsschema med en beräknad kolumn i en partitionerad tabell är en vanlig metod för att minska överdriven spärrkonkurrens. När det gäller pagelatch IO påstående, införa index bidrar till att minska detta prestandaproblem.

Mer information finns i [Diagnostisera och lösa spärrkonkurrens på SQL Server](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (PDF-hämtning).

## <a name="missing-index"></a>Index saknas

### <a name="what-is-happening"></a>Vad händer

Det här prestandamönstret anger den aktuella prestandaförsämringen för databasarbetsbelastning jämfört med den senaste sjudagarsbaslinjen på grund av ett index som saknas.

Ett index används för att snabba upp prestanda för frågor. Det ger snabb åtkomst till tabelldata genom att minska antalet datauppsättningssidor som behöver besökas eller skannas.

Specifika frågor som orsakade prestandaförsämring identifieras genom den här identifieringen för vilka det skulle vara fördelaktigt att skapa index för prestanda.

### <a name="troubleshooting"></a>Felsökning

Frågan diagnostikloggutdata hashar för de frågor som identifierades för att påverka arbetsbelastningens prestanda. Du kan skapa index för dessa frågor. Du kan också optimera eller ta bort dessa frågor om de inte behövs. En bra prestandapraxis är att undvika att fråga efter data som du inte använder.

> [!TIP]
> Visste du att inbyggd information i SQL Database automatiskt kan hantera de index som ger bäst resultat för dina databaser?
>
> För kontinuerlig prestandaoptimering av SQL Database rekommenderar vi att du aktiverar automatisk inställning till [SQL Database](sql-database-automatic-tuning.md). Den här unika funktionen i inbyggd INFORMATION i SQL Database övervakar kontinuerligt DIN SQL-databas och justerar och skapar automatiskt index för dina databaser.
>

## <a name="new-query"></a>Ny fråga

### <a name="what-is-happening"></a>Vad händer

Det här prestandamönstret anger att en ny fråga identifieras som fungerar dåligt och påverkar arbetsbelastningsprestanda jämfört med sjudagars prestandabaslinje.

Att skriva en bra fråga ibland kan vara en utmanande uppgift. Mer information om hur du skriver frågor finns i [Skriva SQL-frågor](https://msdn.microsoft.com/library/bb264565.aspx). Information om hur du optimerar befintliga frågeprestanda finns i [Frågejustering](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen matar ut information upp till två nya mest processorkrävande frågor, inklusive frågeh hashar. Eftersom den identifierade frågan påverkar arbetsbelastningens prestanda kan du optimera frågan. God praxis är att bara hämta data som du behöver använda. Vi rekommenderar också att du använder frågor med en WHERE-sats. Vi rekommenderar också att du förenklar komplexa frågor och dela upp dem i mindre frågor. En annan bra idé är att dela upp stora batchfrågor i mindre batchfrågor. Att införa index för nya frågor är vanligtvis en bra idé för att minska det här prestandaproblemet.

Överväg att använda [Prestandainsikt för Azure SQL Database Query](sql-database-query-performance.md).

## <a name="increased-wait-statistic"></a>Ökad väntestatistik

### <a name="what-is-happening"></a>Vad händer

Det här detekterbara prestandamönstret indikerar en försämring av arbetsbelastningens prestanda där frågor med dålig prestanda identifieras jämfört med den senaste sjudagars arbetsbelastningsbaslinjen.

I det här fallet kan systemet inte klassificera de problem som inte presterar under andra standardidentifierbara prestandakategorier, men det identifierades väntestatistiken som är ansvarig för regressionen. Därför betraktar den dem som frågor med *ökad väntestatistik*, där även väntestatistiken som ansvarar för regressionen exponeras.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen matar ut information om ökad väntetidsinformation och frågehhes för de berörda frågorna.

Eftersom systemet inte kunde identifiera grundorsaken för de problem som inte fungerar är diagnostikinformationen en bra utgångspunkt för manuell felsökning. Du kan optimera prestanda för dessa frågor. En bra idé är att hämta endast data som du behöver använda och för att förenkla och dela upp komplexa frågor i mindre.

Mer information om hur du optimerar frågeprestanda finns i [Frågejustering](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>TempDB Påstående

### <a name="what-is-happening"></a>Vad händer

Det här detekterbara prestandamönstret anger ett databasprestandatillstånd där det finns en flaskhals med trådar som försöker komma åt tempDB-resurser. (Det här villkoret är inte IO-relaterat.) Det typiska scenariot för det här prestandaproblemet är hundratals samtidiga frågor som alla skapar, använder och sedan släpper små tempDB-tabeller. Systemet upptäckte att antalet samtidiga frågor med samma tempDB-tabeller ökade med tillräcklig statistisk signifikans för att påverka databasens prestanda jämfört med den senaste sjudagars prestandabaslinjen.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen utdata tempDB påstående detaljer. Du kan använda informationen som utgångspunkt för felsökning. Det finns två saker du kan fortsätta med för att lindra den här typen av konkurrens och öka dataflödet för den totala arbetsbelastningen: Du kan sluta använda de temporära tabellerna. Du kan också använda minnesoptimerade tabeller.

Mer information finns i [Introduktion till minnesoptimerade tabeller](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables).

## <a name="elastic-pool-dtu-shortage"></a>Brist på elastisk pool DTU

### <a name="what-is-happening"></a>Vad händer

Det här detekterbara prestandamönstret indikerar en försämring av den aktuella databasarbetsbelastningen jämfört med den senaste sjudagarsbaslinjen. Det beror på bristen på tillgängliga DU:er i den elastiska poolen i din prenumeration.

Resurser i SQL Database kallas vanligtvis [DTU-resurser](sql-database-purchase-models.md#dtu-based-purchasing-model), som består av ett blandat mått på CPU- och IO-resurser (data och transaktionslogg IO). [Elastiska Azure-poolresurser](sql-database-elastic-pool.md) används som en pool med tillgängliga eDTU-resurser som delas mellan flera databaser för skalning. När tillgängliga eDTU-resurser i den elastiska poolen inte är tillräckligt stora för att stödja alla databaser i poolen identifieras ett problem med en elastisk pool DTU-brist i systemet.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen matar ut information på den elastiska poolen, listar de översta DTU-tidskrävande databaserna och ger en procentandel av poolens DTU som används av den mest tidskrävande databasen.

Eftersom det här prestandavillkoret är relaterat till flera databaser med samma pool av eDTU:er i den elastiska poolen fokuserar felsökningsstegen på de översta DTU-tidskrävande databaserna. Du kan minska arbetsbelastningen på de mest tidskrävande databaserna, vilket inkluderar optimering av de mest tidskrävande frågorna i dessa databaser. Du kan också se till att du inte frågar efter data som du inte använder. En annan metod är att optimera program genom att använda de översta DTU-tidskrävande databaserna och omfördela arbetsbelastningen mellan flera databaser.

Om det inte går att minska och optimera den aktuella arbetsbelastningen på dina översta DTU-tidskrävande databaser kan du överväga att öka prisnivån för elastisk pool. En sådan ökning resulterar i ökningen av de tillgängliga DU:erna i den elastiska poolen.

## <a name="plan-regression"></a>Planera regression

### <a name="what-is-happening"></a>Vad händer

Det här detekterbara prestandamönstret betecknar ett villkor där SQL Database använder en underoptimal frågekörningsplan. Den suboptimala planen orsakar vanligtvis ökad frågekörning, vilket leder till längre väntetider för aktuella och andra frågor.

SQL-databasen bestämmer frågekörningsplanen med den lägsta kostnaden för en frågekörning. När typen av frågor och arbetsbelastningar ändras är de befintliga planerna ibland inte längre effektiva, eller kanske SQL-databasen inte gjorde en bra bedömning. Som en fråga om korrigering kan frågekörningsplaner tvingas manuellt.

Det här detekterbara prestandamönstret kombinerar tre olika fall av planregression: ny planregression, gammal planregression och befintliga planer ändrade arbetsbelastning. Den specifika typen av planregression som inträffade finns i *egenskapen details* i diagnostikloggen.

Det nya regressionsvillkoret för planen refererar till ett tillstånd där SQL Database börjar köra en ny frågekörningsplan som inte är lika effektiv som den gamla planen. Det gamla regressionsvillkoret för plan refererar till tillståndet när SQL Database växlar från att använda en ny, effektivare plan till den gamla planen, som inte är lika effektiv som den nya planen. De befintliga planerna ändrade arbetsbelastningsregression refererar till det tillstånd där de gamla och de nya planerna kontinuerligt alternerar, med balansen går mer mot den dåligt presterande planen.

Mer information om planregressioner finns [i Vad är planregression i SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/).

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen matar ut frågan hashar, bra plan-ID, dåligt plan-ID och fråge-ID. Du kan använda den här informationen som grund för felsökning.

Du kan analysera vilken plan som är bättre för dina specifika frågor som du kan identifiera med frågehålarna. När du har bestämt vilken plan som fungerar bättre för dina frågor kan du tvinga den manuellt.

Mer information finns [i Lär dig hur SQL Server förhindrar regressioner](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/)av plan .

> [!TIP]
> Visste du att inbyggd information i SQL Database automatiskt kan hantera de bäst presterande frågekörningsplanerna för dina databaser?
>
> För kontinuerlig prestandaoptimering av SQL Database rekommenderar vi att du aktiverar automatisk inställning till [SQL Database](sql-database-automatic-tuning.md). Den här unika funktionen i inbyggd INFORMATION i SQL Database övervakar kontinuerligt DIN SQL-databas och justerar och skapar automatiskt de bästa frågekörningsplanerna för dina databaser.
>

## <a name="database-scoped-configuration-value-change"></a>Ändring av konfigurationsvärde för databasomfattning

### <a name="what-is-happening"></a>Vad händer

Det här detekterbara prestandamönstret anger ett tillstånd där en ändring i konfigurationen för databasscoped orsakar prestandaregression som identifieras jämfört med det senaste sjudagars arbetsbelastningsbeteendet för databasen. Det här mönstret anger att en nyligen genomförd ändring av konfigurationen för databasscopet inte verkar vara till nytta för databasens prestanda.

Konfigurationsändringar med databasomfattning kan anges för varje enskild databas. Den här konfigurationen används från fall till fall för att optimera databasens individuella prestanda. Följande alternativ kan konfigureras för varje enskild databas: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES och CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen matar ut databasomfattade konfigurationsändringar som nyligen gjordes som orsakade prestandaförsämring jämfört med det föregående arbetsbelastningsbeteendet på sju dagar. Du kan återställa konfigurationsändringarna till tidigare värden. Du kan också justera värde efter värde tills önskad prestandanivå har uppnåtts. Du kan kopiera konfigurationsvärden för databasomfattning från en liknande databas med tillfredsställande prestanda. Om du inte kan felsöka prestanda kan du återgå till standardvärdena för SQL Database och försöka finjustera från den här baslinjen.

Mer information om hur du optimerar konfigurationen för databasscope och T-SQL-syntaxen för att ändra konfigurationen finns i [Ändra konfigurationen för databasomfattade (Transact-SQL).](https://msdn.microsoft.com/library/mt629158.aspx)

## <a name="slow-client"></a>Långsam klient

### <a name="what-is-happening"></a>Vad händer

Det här detekterbara prestandamönstret indikerar ett tillstånd där klienten som använder SQL-databasen inte kan använda utdata från databasen lika snabbt som databasen skickar resultaten. Eftersom SQL Database inte lagrar resultat av de utförda frågorna i en buffert, saktar det ner och väntar på att klienten ska använda de överförda frågeutdata innan du fortsätter. Det här villkoret kan också vara relaterat till ett nätverk som inte är tillräckligt snabbt för att överföra utdata från SQL-databasen till den förbrukande klienten.

Det här villkoret genereras endast om en prestandaregression identifieras jämfört med det senaste sjudagars arbetsbelastningsbeteendet för databasen. Det här prestandaproblemet identifieras endast om en statistiskt signifikant prestandaförsämring inträffar jämfört med tidigare prestandabeteende.

### <a name="troubleshooting"></a>Felsökning

Det här detekterbara prestandamönstret indikerar ett villkor på klientsidan. Felsökning krävs i klientprogrammet eller klientnätverket. Diagnostikloggen matar ut frågan hashar och väntetider som verkar vänta mest på att klienten ska använda dem inom de senaste två timmarna. Du kan använda den här informationen som grund för felsökning.

Du kan optimera prestanda för ditt program för förbrukning av dessa frågor. Du kan också överväga möjliga problem med nätverksfördröjningen. Eftersom prestandaförsämringsproblemet baserades på en ändring i den senaste sjudagars prestandabaslinjen kan du undersöka om de senaste program- eller nätverkstillståndsändringarna orsakade den här prestandaregressionshändelsen.

## <a name="pricing-tier-downgrade"></a>Nedgradering av prisnivån

### <a name="what-is-happening"></a>Vad händer

Det här detekterbara prestandamönstret indikerar ett villkor där prisnivån för din SQL Database-prenumeration nedgraderades. På grund av minskning av resurser (DU) som är tillgängliga för databasen upptäckte systemet en minskning av den aktuella databasens prestanda jämfört med den senaste sjudagarsbaslinjen.

Dessutom kan det finnas ett villkor där prisnivån för din SQL Database-prenumeration nedgraderades och sedan uppgraderades till en högre nivå inom en kort tidsperiod. Identifiering av den här tillfälliga prestandaförsämringen matas ut i informationsavsnittet i diagnostikloggen som en nedgradering och uppgradering av prisnivån.

### <a name="troubleshooting"></a>Felsökning

Om du har minskat din prisnivå och därför dku:erna som är tillgängliga för SQL Database och är nöjd med prestandan finns det inget du behöver göra. Om du har minskat prisnivån och är missnöjd med sql-databasprestandan minskar du databasarbetsbelastningarna eller överväger att öka prisnivån till en högre nivå.

## <a name="recommended-troubleshooting-flow"></a>Rekommenderat felsökningsflöde

 Följ flödesschemat för en rekommenderad metod för att felsöka prestandaproblem med hjälp av Intelligenta insikter.

Få tillgång till intelligenta insikter via Azure-portalen genom att gå till Azure SQL Analytics. Försök att hitta den inkommande prestandavarningen och markera den. Identifiera vad som händer på sidan identifieringar. Observera den angivna grundorsaksanalysen av problemet, frågetext, frågetidstrender och incidentutveckling. Försök att lösa problemet genom att använda rekommendationen Intelligent Insights för att minska prestandaproblemet.

[![Felsöka flödesschema](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Välj det flödesschema som du vill hämta en PDF-version.

Intelligenta insikter behöver vanligtvis en timmes tid för att utföra grundorsaksanalysen av prestandaproblemet. Om du inte kan hitta problemet i Intelligenta insikter och det är viktigt för dig kan du använda Frågearkivet för att manuellt identifiera orsaken till prestandaproblemet. (Vanligtvis är dessa problem mindre än en timme gamla.) Mer information finns i [Övervaka prestanda med hjälp av Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Nästa steg

- Lär dig [intelligenta insikter](sql-database-intelligent-insights.md) begrepp.
- Använd [prestandadiagnostikloggen för Intelligent Insights Azure SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md).
- Övervaka [Azure SQL Database med hjälp av Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Lär dig [att samla in och använda loggdata från dina Azure-resurser](../azure-monitor/platform/platform-logs-overview.md).
