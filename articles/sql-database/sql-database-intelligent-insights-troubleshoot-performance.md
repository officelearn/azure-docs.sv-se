---
title: Felsöka Azure SQL Database prestandaproblem med intelligenta insikter | Microsoft Docs
description: Intelligent Insights hjälper dig att felsöka problem med Azure SQL Database-prestanda.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: fff4aa947f878974d2d0f18f373b8c0917ed7d70
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316064"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Felsöka Azure SQL Database prestandaproblem med intelligenta insikter

Den här sidan innehåller information om Azure SQL Database och prestandaproblem för hanterad instans har identifierats genom den [smarta insikter](sql-database-intelligent-insights.md) diagnostiklogg för databas prestanda. Diagnostiklogg telemetri kan strömmas till [Azure Monitor loggar](../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), eller en lösning från tredje part för anpassade DevOps-varningar och rapporteringsfunktioner.

> [!NOTE]
> En snabb SQL Database-prestanda med hjälp av Intelligent Insights felsökningsguide finns den [rekommenderas felsöka flöde](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) flödesschema i det här dokumentet.
>

## <a name="detectable-database-performance-patterns"></a>Flashminnet mönster i prestanda

Intelligent Insights identifierar automatiskt prestandaproblem med SQL-databas och Managed Instance databaser baserat på frågan körningstider vänta, fel eller timeout. Den returnerar identifierade prestandamönster in diagnostik. Flashminnet prestandamönster sammanfattas i tabellen nedan.

| Prestandamönster för flashminnet | Beskrivning för Azure SQL Database och elastiska pooler | Beskrivning för databaser i Managed Instance |
| :------------------- | ------------------- | ------------------- |
| [Nådde resursbegränsningar](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Användning av tillgängliga resurser (dtu: er), databasen arbetstrådar eller databasen inloggningssessioner som är tillgängliga på den övervakade prenumerationen har nått gränserna. Detta påverkar prestandan för SQL Database. | Förbrukning av processorresurserna når Managed Instance gränser. Detta påverkar prestanda för databasen. |
| [Ökning av arbetsbelastning](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Ökning av arbetsbelastning eller kontinuerlig anhopning av arbetsbelastningen för databasen har identifierats. Detta påverkar prestandan för SQL Database. | Ökning av arbetsbelastning har identifierats. Detta påverkar prestanda för databasen. |
| [Minnesbelastning](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Arbetare som begärt minne beviljar måste vänta tills minnesallokeringar statistiskt signifikanta mängder tid. Eller arbetare som begärt minne ger ökad ackumulerade finns. Detta påverkar prestandan för SQL Database. | Arbetare som har begärt minne beviljar väntar minnesallokeringar för en statistiskt betydande tidsperiod. Detta påverkar prestanda för databasen. |
| [Låsning](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Långa databasen låsning upptäcktes påverkar prestandan för SQL Database. | Långa databasen låsning upptäcktes påverka databasens prestanda. |
| [Ökad MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Högsta grad av parallellitet (MAXDOP) har ändrats påverkar frågans körning effektiviteten. Detta påverkar prestandan för SQL Database. | Högsta grad av parallellitet (MAXDOP) har ändrats påverkar frågans körning effektiviteten. Detta påverkar prestanda för databasen. |
| [Pagelatch konkurrens](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Flera trådar försöker samtidigt komma åt samma data i minnet bufferten sidorna vilket resulterar i ökad väntetider och orsakar pagelatch konkurrens. Detta påverkar prestanda för SQL-databas. | Flera trådar försöker samtidigt komma åt samma data i minnet bufferten sidorna vilket resulterar i ökad väntetider och orsakar pagelatch konkurrens. Detta påverkar databas prestanda. |
| [Index som saknas](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Index som saknas upptäcktes att det påverkar prestanda för SQL-databas. | Index som saknas har identifierats som påverkar prestanda för databasen. |
| [Ny fråga](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Ny fråga har identifierats som påverkar prestandan för SQL-databas. | Ny fråga har identifierats som påverkar prestandan i databasen. |
| [Ökad vänta statistik](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Ökad databasen väntetider har identifierats som påverkar prestanda för SQL-databas. | Ökad databasen väntetider identifierades påverka databasens prestanda. |
| [TempDB konkurrens](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Flera trådar försöker komma åt samma TempDB-resurs som orsakar en flaskhals. Detta påverkar prestandan för SQL Database. | Flera trådar försöker komma åt samma TempDB-resurs som orsakar en flaskhals. Detta påverkar prestanda för databasen. |
| [Elastisk pool DTU brist](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Brist på tillgängliga edtu: er i den elastiska poolen påverkar prestandan för SQL Database. | Inte tillgängligt för hanterad instans som den använder vCore-modellen. |
| [Planera Regression](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Ny plan eller en ändring i arbetsbelastningen för en befintlig plan har identifierats. Detta påverkar prestandan för SQL Database. | Ny plan eller en ändring i arbetsbelastningen för en befintlig plan har identifierats. Detta påverkar prestanda för databasen. |
| [Databasbegränsade konfigurationen ändras](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Konfigurationsändring på SQL-databasen har identifierats som påverkar prestanda för databasen. | Konfigurationsändring på databasen har identifierats som påverkar prestanda för databasen. |
| [Långsam klienten](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Långsamma programklienten kan inte använda utdata från databasen tillräckligt snabbt. Detta påverkar prestandan för SQL Database. | Långsamma programklienten kan inte använda utdata från databasen tillräckligt snabbt. Detta påverkar prestanda för databasen. |
| [Priser för nedgradering av nivå](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Priser nivån nedgradering åtgärd minskade tillgängliga resurser. Detta påverkar prestandan för SQL Database. | Priser nivån nedgradering åtgärd minskade tillgängliga resurser. Detta påverkar prestanda för databasen. |

> [!TIP]
> Kontinuerlig prestandaoptimering i SQL Database, aktivera [automatisk justering i Azure SQL Database](sql-database-automatic-tuning.md). Den här unika funktionen i SQL Database inbyggd intelligens kontinuerligt övervakar din SQL-databas, automatiskt justerar index och gäller fråga körning plan korrigeringar.
>

I följande avsnitt beskrivs flashminnet prestandamönster i detalj.

## <a name="reaching-resource-limits"></a>Nådde resursbegränsningar

### <a name="what-is-happening"></a>Vad händer

Det här mönstret flashminnet prestanda kombinerar prestandaproblem som är relaterade till når tillgängliga resursbegränsningar, worker begränsningar och tidsgränser för sessioner. Om det här prestandaproblemet har identifierats, anger en beskrivningsfält för diagnostikloggen om prestandaproblemet är relaterat till resursen, worker eller tidsgränser för sessioner.

Resurser på SQL-databas är normalt refererade till [DTU](sql-database-what-is-a-dtu.md) eller [vCore](sql-database-service-tiers-vcore.md) resurser. Mönstret för att nå resursgränser kan identifieras när identifierats fråga prestandaförsämring orsakas av når någon av de uppmätta resursbegränsningar.

Sessionen gränser resursen avser antalet tillgängliga samtidiga inloggningar till SQL-databasen. Det här mönstret prestanda känns igen när program som är anslutna till SQL-databaser har nått antalet tillgängliga samtidiga inloggningar till databasen. Om program försöker att använda fler sessioner än vad som finns på en databas, påverkas prestanda för frågor.

Nå worker gränser är ett specialfall nå resursbegränsningar eftersom tillgängliga arbeten inte räknas i DTU eller vCore användningen. Nå worker gränser för en databas kan orsaka röstfunktion resursspecifika väntetider, vilket resulterar i fråga prestandaförsämring.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen matar ut fråga hash-värden för frågor som påverkas prestanda och procentandelar för förbrukningen av resursen. Du kan använda den här informationen som utgångspunkt för att optimera din databas-arbetsbelastning. I synnerhet kan du optimera de frågor som påverkar försämrade prestanda genom att lägga till index. Eller du kan optimera program med en mer även distribution av arbetsbelastning. Om du inte att minska arbetsbelastningar eller göra optimeringar kan du överväga att öka prisnivån för prenumerationen för SQL-databas att öka mängden tillgängliga resurser.

Om du har nått gränserna tillgänglig, kan du optimera dina program genom att minska antalet inloggningar som gjorts i databasen. Om du inte att minska antalet inloggningar från dina program till databasen kan du överväga att öka prisnivån för din databas. Eller så kan du dela upp och flytta databasen till flera databaser för en mer avvägd arbetsbelastningsfördelning.

Fler förslag om hur du löser tidsgränser för sessioner finns [hur du arbetar med gränserna för högsta inloggningar för SQL Database](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Se [översikt över resource begränsar på en SQL-databasserver](sql-database-resource-limits-database-server.md) information om begränsningar på de server och prenumeration.

## <a name="workload-increase"></a>Ökning av arbetsbelastning

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda identifierar problem har fått genom en ökning av arbetsbelastning eller i allvarligare format, en arbetsbelastning pile-up.

Den här identifieringen görs genom en kombination av flera mått. Grundläggande måttet mäts är upptäcka en ökning jämfört med tidigare arbetsbelastning baslinjen arbetsbelastning. Den andra formen av identifieringen baseras på mätningar i en stor ökning i aktiva trådar som är tillräckligt stor för att påverka prestanda för frågor.

I sin allvarligare form kan arbetsbelastningen kontinuerligt växer på grund av SQL-databasen oförmåga att hantera arbetsbelastningen. Resultatet är en ständigt växande arbetsbelastningsstorlek är arbetsbelastningen pile-up tillstånd. På grund av det här tillståndet ökar den tid som arbetsbelastningen väntar för körning. Det här tillståndet motsvarar en av databasen allvarligaste problemen. Det här problemet har identifierats genom övervakning ökning av antalet avbrutna trådar. 

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen visar antalet frågor vars körning har ökat och fråge-hash av frågan med största bidraget till arbetsbelastningen ökar. Du kan använda den här informationen som utgångspunkt för att optimera arbetsbelastningen. Frågan som identifieras som är störst inom den arbetsbelastning ökade är särskilt användbart som din startpunkt.

Du kan överväga att distribuera arbetsbelastningar jämnare till databasen. Överväg att optimera den fråga som påverkar prestanda genom att lägga till index. Du kan även distribuera arbetsbelastningen mellan flera databaser. Om lösningarna inte är möjligt kan du överväga att öka prisnivån för prenumerationen för SQL-databas att öka mängden tillgängliga resurser.

## <a name="memory-pressure"></a>Minnesbelastning

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda anger försämring i den aktuella databasprestanda som orsakas av minnesbelastning eller på dess allvarligare sätt det pile-up minne, jämfört med de senaste sju dagar prestanda-baslinjen.

Minnesbelastning anger ett prestandavillkor där det finns ett stort antal trådar som begär minne ger i SQL-databasen. Den stora volymen orsakar en hög tillstånd där SQL-databasen är inte effektivt allokera minne till alla arbeten som begär den. En av de vanligaste orsakerna till det här problemet är relaterat till mängden ledigt minne till SQL-databasen å ena sidan. Å andra sidan, gör en ökning av arbetsbelastning ökningen i arbetstrådar och minnesbelastningen.

Allvarligare form av minnesbelastning är pile-up minneskapacitet. Det här tillståndet anger att ett högre antal arbetstrådar begär minne beviljar än det finns frågor lanserar minnet. Det här antalet trådar som begär minne ger också kan vara ständigt ökar (lagra) eftersom SQL database engine kan inte allokera minne tillräckligt effektivt för att uppfylla behovet. Pile-up minneskapacitet representerar en av databasen allvarligaste problemen.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen visar minnesinformation för objektet store med clerk (det vill säga arbetstråd), som har markerats som högsta orsaken till hög minnesanvändning och relevanta tidsstämplar. Du kan använda den här informationen som grund för felsökning. 

Du kan optimera eller ta bort frågor som rör lagerarbetare med högsta minnesanvändning. Du kan också se till att du inte köra frågor mot data som du inte planerar att använda. Det är en bra idé att alltid använda en WHERE-sats i dina frågor. Vi rekommenderar dessutom att du skapar icke-grupperade index om du vill söka efter data i stället för att skanna den.

Du kan också minska arbetsbelastningen genom att optimera eller fördelat över flera databaser. Eller du kan distribuera arbetsbelastningen mellan flera databaser. Om lösningarna inte är möjligt kan du överväga att öka prisnivån för prenumerationen för SQL-databas att öka mängden minnesresurser som är tillgängliga i databasen.

Ytterligare förslag om felsökning finns i [minne ger stensättningar: Mystiska SQL Server minne konsumenten med många namn](https://blogs.msdn.microsoft.com/sqlmeditation/20../../memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Låsning

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda anger försämring i den aktuella databasprestanda där långa databasen låsning upptäcks jämfört med de senaste sju dagar prestanda-baslinjen. 

I moderna RDBMS är låsning viktigt för att implementera flertrådat system där maximeras prestanda genom att köra flera samtidiga arbetare och parallella databastransaktioner där det är möjligt. Låsning i det här sammanhanget refererar till inbyggd-åtkomstmekanismen där bara en enda transaktion kan endast komma åt rader, sidor, tabeller och filer som krävs och inte konkurrerar med en annan transaktion för resurser. När transaktionen som låsta resurser som ska användas är klar med dem. släpps låset på dessa resurser, vilket gör att andra transaktioner att få åtkomst till resurser som krävs. Läs mer om hur du låser [låsa databasmotorn](https://msdn.microsoft.com/library/ms190615.aspx).

Om transaktioner som utförs av SQL-motor som väntar på under längre tid att få åtkomst till resurser som låst för användning, gör den här väntetid minskningen av arbetsbelastningsprestandan för körning. 

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen visar låsning information som du kan använda som underlag för felsökning. Du kan analysera rapporterade blockerar frågor, det vill säga de frågor som introducerar prestandaförsämring låsning och ta bort dem. I vissa fall kanske du lyckas på att optimera frågorna som blockerar.

Det enklaste och säkraste sättet att åtgärda problemet är att hålla transaktioner kort och att minska Lås storleken för de dyraste frågorna. Du kan dela upp en stor grupp med åtgärder i mindre åtgärder. Det är en bra idé att minska fråga Lås storleken genom att göra frågan så effektivt som möjligt. Minska stora genomsökningar eftersom de ökar risken för låsningar och negativt påverka den övergripande databasens prestanda. Du kan skapa nya index eller lägga till kolumner i det befintliga indexet för att undvika tabellsökningar för identifierade frågor som orsakar låsning. 

Fler förslag finns i [hur du löser blockerande problem som orsakas av Lås eskalering i SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Ökad MAXDOP

### <a name="what-is-happening"></a>Vad händer

Det här mönstret flashminnet prestanda anger ett villkor där en körning för valda frågeplan har parallelliseras fler än det skulle ha varit. Frågeoptimeringen SQL-databas kan förbättra prestanda för arbetsbelastningen genom att köra frågor parallellt för att påskynda saker där det är möjligt. I vissa fall kan lägga mer tid på att vänta på varandra för att synkronisera och slå samman resultat jämfört med samma frågan körs med färre parallella arbeten eller även i vissa fall jämfört med en enda arbetstråd i parallella arbeten bearbetning av en fråga.

Systemet expert analyserar den aktuella databasens prestanda jämfört med baslinjeperioden. Den avgör om en tidigare pågående fråga körs långsammare än tidigare eftersom körningen frågeplanen mer parallelliseras än den ska vara.

MAXDOP serverkonfigurationsalternativet på SQL Database används för att styra hur många CPU-kärnor som kan användas för att köra samma fråga parallellt. 

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen visar fråga hashvärden som rör frågor som varaktighet för körning ökat eftersom de har mer än de skulle ha varit parallelliseras. Loggen visar också CXP väntetider. Den här gången representerar den tid som en enda kalender/coordinator tråd (tråd 0) väntar på att alla trådar slutförts innan du sammanfogar resultaten och flytta framåt. Dessutom diagnostikloggen matar ut väntetider som väntade dålig prestanda-frågor i körningsmiljön för övergripande. Du kan använda den här informationen som grund för felsökning.

Börja optimera eller förenkla komplexa frågor. Det är en bra idé att dela upp långa batch-jobb i mindre. Se dessutom till att du har skapat index för att stödja dina frågor. Du kan också manuellt tillämpa den högsta graden av parallellitet (MAXDOP) för en fråga som har flaggats som dålig utför. För att konfigurera den här åtgärden med hjälp av T-SQL, se [konfigurera serverkonfigurationsalternativet för MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Ställa in MAXDOP-servern konfigurationsalternativet till noll (0) som ett standardvärde anger att SQL-databas kan använda alla tillgängliga logiska CPU-kärnor för att parallellisera trådar för att köra en enskild fråga. Ange MAXDOP till en (1) anger att endast en core kan användas för körning av en enskild fråga. I praktiken kan innebär det att parallellitet är avstängd. Tillgängliga kärnor till databasen och diagnostik logga information beroende på den per fall till fall, du kan finjustera MAXDOP kan antalet kärnor som används för parallell frågekörning som eventuellt kan lösa problemet i ditt fall.

## <a name="pagelatch-contention"></a>Pagelatch konkurrens

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda indikerar den aktuella arbetsbelastningen för databasen prestandaförsämring på grund av pagelatch konkurrens jämfört med de senaste sju dagar arbetsbelastning baslinjen.

Lås är förenklad synkroniseringsmekanismer som används av SQL-databas för att aktivera flertrådsteknik. De garantera InMemory-datastrukturer som innehåller index, datasidor och andra interna strukturer.

Det finns många typer av lås på SQL-databasen. För enkelhetens skull används bufferten Lås för att skydda sidor i minnet i buffertpoolen. I/o-Lås används för att skydda sidor som ännu inte har lästs in i buffertpoolen. När data skrivs till eller läsa från en sida i buffertpoolen, måste en arbetstråd att erhålla en buffert spärr för sidan först. När en arbetstråd försöker komma åt en sida som inte finns i InMemory-buffertpoolen, görs en i/o-begäran att läsa in nödvändig information från lagringen. Den här sekvensen av händelser som indikerar en allvarligare typ av försämrade prestanda.

På sidan Lås konkurrens när flera trådar samtidigt försöker hämta lås på samma struktur i minnet, vilket medför en ökad väntetid för frågekörning. När det gäller pagelatch-i/o-konkurrens, när data ska kunna nås från lagring, är väntetiden ännu större. Det kan påverka arbetsbelastningens prestanda avsevärt. Pagelatch konkurrensen är det vanligaste scenariot för trådar väntar på varandra och tävlar om resurser på flera CPU-system.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen visar pagelatch konkurrens information. Du kan använda den här informationen som grund för felsökning.

Eftersom en pagelatch är en mekanism för interna kontroll i SQL Database, avgör den automatiskt när de ska användas. Programmet beslut, inklusive design av schema, kan påverka pagelatch beteende på grund av deterministisk beteendet för lås.

En metod för att hantera spärr konkurrens är att ersätta en sekventiell indexnyckeln med en sekventiellt nyckel att fördela infogningar i ett index adressintervall. Vanligtvis distribuerar en ledande kolumnen i indexet arbetsbelastningen proportionellt. Ett annat sätt att tänka på är Tabellpartitionering. Skapa en hash partitioneringsschema med en beräknad kolumn för en partitionerad tabell är en vanlig metod för att förhindra överdriven spärr konkurrens. När det gäller pagelatch-i/o-konkurrens hjälper introduktion till index till att minimera det här prestandaproblemet. 

Mer information finns i [diagnostisera och Lös låsa konkurrensen på SQL Server](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (PDF-nedladdning).

## <a name="missing-index"></a>Index som saknas

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda indikerar den aktuella databasen arbetsbelastning försämrade prestanda jämfört med de senaste sju dagar baslinjen på grund av ett index som saknas.

Ett index används för att påskynda prestanda för frågor. Det ger snabb åtkomst till data från tabeller genom att minska antalet sidor i datauppsättningen som måste vara besökt eller genomsöks.

Specifika frågor som orsakade prestandaförsämring identifieras via den här identifieringen skapar index som skulle vara fördelaktigt att prestandan.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen visar frågan hashvärden för de frågor som har identifierats för att påverka arbetsbelastningens prestanda. Du kan skapa index för de här frågorna. Du kan också optimera eller ta bort de här frågorna om de inte behövs. Ett bra prestanda-tips är att undvika att köra frågor mot data som du inte använder.

> [!TIP]
> Visste du att SQL Database inbyggd intelligens automatiskt kan hantera bäst prestanda indexen för din databas?
>
> För kontinuerlig prestandaoptimering i SQL Database, rekommenderar vi att du aktiverar [automatisk justering i SQL Database](sql-database-automatic-tuning.md). Den här unika funktionen i SQL Database inbyggd intelligens kontinuerligt övervakar din SQL-databas och automatiskt justerar och skapar index för dina databaser.
>

## <a name="new-query"></a>Ny fråga

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda indikerar att en ny fråga har identifierats som är sämre prestanda och påverkar arbetsbelastningsprestandan jämfört med prestandabaslinje för sju dagar.

Skriva en fråga med bra prestanda ibland kan det vara en utmaning. Mer information om hur du skriver frågor finns i [skriva SQL-frågor](https://msdn.microsoft.com/library/bb264565.aspx). För att optimera prestanda för befintliga frågor, se [frågejusteringar](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Felsökning

Diagnostiken logginformation utdata upp till två nya de flesta CPU förbrukar frågor, inklusive deras fråga hashvärden. Eftersom identifierade frågan påverkar arbetsbelastningens prestanda, kan du optimera din fråga. Det är en bra idé att hämta endast data som du behöver använda. Vi rekommenderar också att använda frågor med en WHERE-sats. Vi rekommenderar också att du förenkla komplexa frågor och dela upp dem i mindre frågor. En annan bra praxis är att bryta ned stora batchfrågekörning i mindre batch-frågor. Introduktion till index för nya frågor är vanligtvis en god idé att åtgärda prestandaproblemet.

Överväg att använda [Azure SQL Database Query Performance Insight](sql-database-query-performance.md).

## <a name="increased-wait-statistic"></a>Ökad vänta statistik

### <a name="what-is-happening"></a>Vad händer

Det här mönstret flashminnet prestanda anger en arbetsbelastning prestandaförsämring som identifieras dålig prestanda frågor jämfört med de senaste sju dagar arbetsbelastning baslinjen.

I det här fallet systemet kan inte klassificera dålig prestanda frågorna under andra flashminnet standardprestanda-kategorier, men det har upptäckts ansvarar för regressionen vänta statistiken. Därför det anser dem som frågor med *ökat vänta statistik*, där vänta statistiken ansvarar för regressionen också exponeras. 

### <a name="troubleshooting"></a>Felsökning

Diagnostiklogg utdata information om information om ökad vänta tid och fråga hash-värden för frågorna som påverkas.

Eftersom systemet det gick inte att kunna identifiera orsaken till frågorna dålig prestanda, är diagnostikinformationen en bra utgångspunkt för manuell felsökning. Du kan optimera prestanda för dessa frågor. Ett bra tips är att hämta endast data som du behöver för att använda och för att förenkla och bryter ned komplexa frågor i mindre. 

Läs mer om hur du optimerar prestanda för frågor, [frågejusteringar](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>TempDB konkurrens

### <a name="what-is-happening"></a>Vad händer

Det här mönstret flashminnet prestanda anger prestandavillkor databasen där en flaskhals trådar som försöker få åtkomst till tempDB-resurser finns. (Det här tillståndet är inte i/o som är relaterade.) Vanligt scenario för det här prestandaproblemet är hundratals samtidiga frågor att alla skapa, använda och sedan släppa små tempDB-register. Systemet upptäckte att antalet samtidiga frågor med hjälp av samma tempDB-register ökat med tillräckligt med statistisk signifikans kan påverka databasens prestanda jämfört med de senaste sju dagar prestanda-baslinjen.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen visar tempDB konkurrens information. Du kan använda informationen som startpunkt för felsökning. Det finns två saker som du kan välja för att lösa den här typen av konkurrens och öka genomflödet av den övergripande arbetsbelastningen: Du kan sluta använda de temporära tabellerna. Du kan också använda minnesoptimerade tabeller. 

Mer information finns i [introduktion till minnesoptimerade tabeller](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Elastisk pool DTU brist

### <a name="what-is-happening"></a>Vad händer

Det här mönstret flashminnet prestanda anger en försämring i den aktuella databasen arbetsbelastningens prestanda jämfört med grundregel de senaste sju dagar. Det är på grund av brist på tillgängliga dtu: er i den elastiska poolen för din prenumeration. 

Resurser på SQL-databas är vanligtvis kallas [DTU-resurser](sql-database-purchase-models.md#dtu-based-purchasing-model), som består av ett blandat mått av CPU- och IO (data- och transaktionslogg loggning i/o). [Elastisk pool i Azure-resurser](sql-database-elastic-pool.md) används som en pool med tillgängliga eDTU-resurser som delas mellan flera databaser för att skala syften. När tillgängliga eDTU-resurser i en elastisk pool inte är tillräckligt stort för alla databaser i poolen kan har en elastisk pool DTU brist prestandaproblem identifierats av systemet.

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen matar ut information om den elastiska poolen, visas de översta DTU förbrukar databaserna och ger en procentandel av poolens DTU som används av top förbrukar-databasen.

Eftersom den här prestandavillkor är relaterat till flera databaser med samma pool av edtu: er i den elastiska poolen, fokusera felsökningsstegen på de översta DTU förbrukar databaserna. Du kan minska serverns arbetsbelastning förbrukar top-databaser som innehåller optimering för frågorna som förbrukar längst upp på de här databaserna. Du kan också kontrollera att du inte köra frågor mot data som du inte använder. En annan metod är att optimera program med hjälp av de främsta DTU förbrukar databaserna och omdistribuera arbetsbelastningen mellan flera databaser.

Om minskning och optimering av den nuvarande arbetsbelastningen på dina viktigaste DTU förbrukar databaser inte är möjligt kan du överväga att öka din elastiska pool prisnivå. Till exempel öka resulterar i fler tillgängliga dtu: erna i den elastiska poolen.

## <a name="plan-regression"></a>Planera Regression

### <a name="what-is-happening"></a>Vad händer

Det här mönstret flashminnet prestanda anger ett villkor där SQL-databas som använder en icke-optimal frågeplan för körning. Icke-optimal plan gör normalt ökad frågekörning, vilket leder till längre väntetid för aktuella och andra frågor.

SQL-databasen anger frågeplanen för körning med minsta möjliga kostnad för körning av en fråga. Befintliga prenumerationer är ibland inte längre är effektiv som typ av frågor och arbetsbelastningar ändring, eller kanske SQL-databasen inte har gjort en bra utvärdering. Som en följd av korrigering kan frågeplaner för körning manuellt framtvingas.

Det här mönstret flashminnet prestanda kombinerar tre olika fall av plan regression: regression av nya och gamla planen regression befintliga planer som ändrats arbetsbelastning. Viss typ av plan regression som inträffat finns i den *information* egenskap i diagnostikloggen för.

Ny plan regression villkoret refererar till ett tillstånd som SQL Database starta körning av en ny körning frågeplan som inte är så effektivt som den gamla planen. Den gamla planen regression villkoren refererar till tillståndet när SQL Database växlar från att använda en ny, mer effektiv plan för att den gamla prenumerationen är inte lika effektiva som den nya planen. Den befintliga planer som ändrats arbetsbelastning regressionen refererar till tillståndet som gammalt och de nya planerna kontinuerligt alternativ, med saldo kommer fler mot planen dålig prestanda.

Mer information om plan upprepningar finns [vad är plan regression i SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Felsökning

Diagnostikloggen matar ut fråga hashvärden, bra planering ID, felaktig plan-ID och fråga ID: N. Du kan använda den här informationen som grund för felsökning.

Du kan analysera vilken plan som är bättre utför för dina specifika frågor som du kan identifiera med frågan-hashvärden som tillhandahålls. När du har bestämt vilken plan fungerar bäst för dina frågor kan du tvinga den manuellt. 

Mer information finns i [Lär dig hur SQL Server förebygger plan regression](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Visste du att SQL Database inbyggd intelligens kan hantera bäst att genomföra frågan körningsplaner för dina databaser automatiskt?
>
> För kontinuerlig prestandaoptimering i SQL Database, rekommenderar vi att du aktiverar [automatisk justering i SQL Database](sql-database-automatic-tuning.md). Den här unika funktionen i SQL Database inbyggd intelligens kontinuerligt övervakar din SQL-databas och automatiskt justerar och bäst att genomföra frågan skapas körningsplaner för dina databaser.
>

## <a name="database-scoped-configuration-value-change"></a>Databasbegränsade konfigurationen ändras

### <a name="what-is-happening"></a>Vad händer

Det här mönstret flashminnet prestanda visar ett tillstånd som gör en ändring i den databasbegränsade konfigurationen prestanda regression som upptäcks jämfört med de senaste sju dagar databasen arbetsbelastning beteendet. Det här mönstret anger att en ny ändring som gjorts i den databasbegränsade konfigurationen inte verkar vara fördelaktigt att databasens prestanda.

Databasbegränsade konfigurationsändringar kan ställas in för varje enskild databas. Den här konfigurationen används på grundval av fall för att optimera enskilda prestandan för din databas. Följande alternativ kan konfigureras för varje enskild databas: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES och rensa PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Felsökning

Diagnostiklogg utdata databasbegränsade konfigurationsändringar som gjorts nyligen som orsakade försämring jämfört med det tidigare beteendet i sju dagar arbetsbelastning. Du kan återställa konfigurationsändringar i tidigare värden. Du kan även finjustera värde med värde tills den önskade prestandanivån har uppnåtts. Du kan kopiera konfigurationsvärden för databas-omfång från en liknande databas med tillfredsställande prestanda. Om det inte går att felsöka prestanda återgå till standardvärden för standard-SQL-databas och försök att finjustera från och med den här baslinjen.

Läs mer om hur du optimerar databasbegränsade konfigurationen och T-SQL-syntax för att ändra konfigurationen för [Alter database scoped configuration (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Långsam klienten

### <a name="what-is-happening"></a>Vad händer

Det här mönstret flashminnet prestanda visar ett tillstånd där den klient som använder SQL-databasen inte kan använda utdata från databasen så fort databasen skickar resultaten. Eftersom SQL-databas inte är lagra resultaten för de utförda frågorna i en buffert, långsammare och väntar tills klienten att använda utdata överförda fråga innan du fortsätter. Det här tillståndet kan också relateras till ett nätverk som inte är tillräckligt snabbt nog att skicka utdata från SQL-databasen till konsumerande klienten.

Det här tillståndet genereras bara om en prestanda regression upptäcks jämfört med de senaste sju dagar databasen arbetsbelastning beteendet. Det här prestandaproblemet identifieras endast om en statistiskt betydande prestandaförsämring jämfört med beteende för tidigare prestanda.

### <a name="troubleshooting"></a>Felsökning

Det här mönstret flashminnet prestanda anger ett villkor för klientsidan. Felsöka krävs på klientsidan eller klientside-nätverk. Diagnostikloggen visar frågan hashvärden och väntetider som verkar väntar på de flesta för att klienten ska använda dem inom de senaste två timmarna. Du kan använda den här informationen som grund för felsökning.

Du kan optimera prestanda för ditt program för användning av dessa frågor. Du kan också överväga möjliga problem med nätverkssvarstiden. Eftersom försämring prestandaproblemet baserades på ändringen i den senaste baslinjen för prestanda för sju dagar, kan du undersöka om de senaste ändringarna för programmet eller nätverket villkor beror på den här prestandahändelser i regression. 

## <a name="pricing-tier-downgrade"></a>Priser för nedgradering av nivå

### <a name="what-is-happening"></a>Vad händer

Det här mönstret flashminnet prestanda visar ett tillstånd där prisnivån för din SQL Database-prenumeration har nedgraderas. Systemet upptäckte en minskning på grund av minskning av resurser (dtu: er) tillgängliga i databasen i den aktuella databasprestanda jämfört med grundregel de senaste sju dagar.

Dessutom kan uppstå det ett villkor där prisnivån för din SQL-databasprenumeration nedgraderas och sedan uppgradera till en högre nivå inom en kort tidsperiod. Identifiering av den här tillfälliga prestandaförsämring används för utdata i informationsavsnittet loggens diagnostik som en prisnivå nivå nedgradera och uppgradera.

### <a name="troubleshooting"></a>Felsökning

Om du minskar din prisnivå och därför dtu: er tillgängliga för SQL-databas, och du är nöjd med prestanda, är det du behöver inte göra något. Om du minskar din prisnivå och du är nöjd med din SQL database-prestanda, minska arbetsbelastningen i din databas eller överväga att öka prisnivån till en högre nivå.

## <a name="recommended-troubleshooting-flow"></a>Rekommenderade felsöka flöde

 Följ flödesschema för en rekommenderad metod för att felsöka prestandaproblem med hjälp av smarta insikter.

Åtkomst till smarta insikter via Azure portal genom att gå till Azure SQL Analytics. Försök att hitta den inkommande Prestandavarning och markera den. Identifiera vad som händer på sidan identifieringar. Se den tillhandahållna Rotorsaksanalys av problemet, frågetexten, fråga tid trender och incident utvecklingen. Försök att lösa problemet med hjälp av Intelligent Insights-rekommendation för hur du minimerar prestandaproblemet. 

[![Felsökning av flödesschema](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Välj flödesschema för att ladda ned en PDF-version.

Intelligent Insights behöver du normalt en timme för att utföra Rotorsaksanalys av prestandaproblemet. Om du inte hittar ditt problem i intelligenta insikter och det är viktigt att du kan använda Query Store för att identifiera de grundläggande orsakerna till prestandaproblemet manuellt. (Normalt problemen är mindre än en timme gamla.) Mer information finns i [övervaka prestanda med hjälp av Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Nästa steg
- Lär dig [smarta insikter](sql-database-intelligent-insights.md) begrepp.
- Använd den [diagnostikloggen för Intelligent Insights Azure SQL Database prestanda](sql-database-intelligent-insights-use-diagnostics-log.md).
- Övervaka [Azure SQL Database med hjälp av Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Lär dig hur du [samla in och använda loggdata från resurserna i Azure](../azure-monitor/platform/diagnostic-logs-overview.md).
