---
title: "Felsöka Azure SQL Database prestandaproblem med Intelligent insikter | Microsoft Docs"
description: "Intelligent Insights hjälper dig att felsöka problem med Azure SQL Database prestanda."
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 85da2a521af0ca92c07d8b2041e92b98f98e9661
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Felsöka Azure SQL Database prestandaproblem med Intelligent insikter

Den här sidan innehåller information om Azure SQL Database prestandaproblem som identifieras via den [Intelligent insikter](sql-database-intelligent-insights.md) databasen prestanda diagnostik loggar. Den här diagnostiken loggen kan skickas till [Azure logganalys](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), eller en fristående lösning för anpassade DevOps avisering och rapportering funktioner.

> [!NOTE]
> En SQL-databas prestanda felsökning snabbguide via Intelligent insikter, finns det [rekommenderas felsökning flödet](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) flödesschema i det här dokumentet.
>

## <a name="detectable-database-performance-patterns"></a>Kan upptäckas databasen prestandamönster

Intelligent insikter identifierar automatiskt prestandaproblem med SQL Database baserat på frågan körningstider vänta, fel eller timeout. Därefter visas den identifierade prestandamönster i diagnostik-loggen. Kan upptäckas prestandamönster sammanfattas i följande tabell:

| Kan upptäckas prestandamönster | Information för utdata |
| :------------------- | ------------------- |
| [Nå gränserna](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Användningen av tillgängliga resurser (Dtu), databasen arbetstrådar eller databasen inloggningsessioner som är tillgängliga på den övervakade prenumerationen har nått gränser, vilket gör SQL Database prestandaproblem. |
| [Ökning av arbetsbelastning](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Arbetsbelastningen ökning eller kontinuerlig anhopning av arbetsbelastningen på databasen identifierades, vilket gör SQL Database prestandaproblem. |
| [Minnesbelastning](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Anställda som har begärt minne ger måste vänta tills minnesallokering för statistiskt stora mängder tid. Eller så finns det en ökad anhopning av de anställda som har begärt minne beviljar, som påverkar prestanda för SQL-databasen. |
| [Låsning](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Långa databasen låsning upptäcktes, som påverkar prestanda för SQL-databasen. |
| [Ökad MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Den maximala graden av parallellitet alternativet (MAXDOP) har ändrats och det påverkar frågan körning effektiviteten. |
| [Pagelatch konkurrens](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Pagelatch konkurrens identifierades som påverkar prestanda för SQL-databasen. Flera trådar försöker samtidigt komma åt sidorna buffert samma data i minnet. Detta resulterar i ökad väntetiden som påverkar prestanda för SQL-databasen. |
| [Index som saknas](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | En saknas index problemet upptäcktes, som påverkar prestanda för SQL-databasen. |
| [Ny fråga](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | En ny fråga identifierades som påverkar prestandan för SQL-databas. |
| [Onormal vänta statistik](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Väntetiden för ovanliga databas har upptäckts som påverkar prestanda för SQL-databasen. |
| [TempDB konkurrens](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Flera trådar försöker få åtkomst till samma tempDB-resurser, vilket gör att en flaskhals som påverkar prestanda för SQL-databas. |
| [Elastisk Pool DTU lite](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Brist på tillgängliga edtu: er i den elastiska poolen påverkar prestandan för SQL-databas. |
| [Planera Regression](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | En ny plan eller en ändring i arbetsbelastningen på en befintlig plan upptäcktes, som påverkar prestanda för SQL-databasen. |
| [Databas-omfattande värdet konfigurationsändring](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Ändra konfigurationen för databasen påverkar prestandan för SQL-databas. |
| [Långsam klienten](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | En långsam program-klient som inte kan använda utdata från SQL-databasen tillräckligt snabbt identifierades som påverkar prestanda för SQL-databasen. |
| [Priser för nedgradering av nivå](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | En prisnivå nivå nedgradering åtgärd minskade tillgängliga resurser som påverkar prestanda för SQL-databasen. |

> [!TIP]
> Kontinuerlig prestandaoptimering av SQL-databas, aktivera [Azure SQL Database automatisk justering](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning). Den här unika funktionen för SQL-databas inbyggd intelligens kontinuerligt övervakar din SQL-databas, automatiskt justerar index och gäller frågan körning plan korrigeringar.
>

I följande avsnitt beskrivs tidigare angivna kan upptäckas prestandamönster i detalj.

## <a name="reaching-resource-limits"></a>Nå gränserna

### <a name="what-is-happening"></a>Vad händer

Det här mönstret kan upptäckas prestanda kombinerar prestandaproblem som är relaterade till når tillgänglig resurs för gränser, worker begränsningar och sessionsgränser. När det här prestandaproblemet upptäcks anger en beskrivningsfält diagnostik loggens om prestandaproblem är relaterad till resursen, worker eller tidsgränser för sessioner.

Resurser på SQL-databas är vanligtvis kallas [DTU-resurser](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-what-is-a-dtu). De består av en blandning av processor- och i/o (data och transaktionen loggning i/o) resurser. Mönstret nå gränserna kan identifieras när frågan prestandaförsämring orsakas av når någon uppmätta resurs-gränser.

Resursen session gränser som anger antalet tillgängliga samtidiga inloggningar till SQL-databasen. Det här mönstret prestanda kan identifieras när program som är anslutna till SQL-databaser har uppnått antalet tillgängliga samtidiga inloggningar till databasen. Om program försöker att använda flera sessioner än vad som är tillgängliga på en databas, påverkas prestanda för frågor.

Nå worker gränser är ett specialfall nå gränserna eftersom tillgängliga anställda inte räknas med i DTU-användningen. Nå worker gränserna för en databas kan orsaka ökningen av resursspecifika väntetiden, vilket resulterar i frågan prestanda försämras.

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut frågan hash-värden för frågor som påverkar prestanda och procentandelar för förbrukningen av resursen. Du kan använda den här informationen som en startpunkt för att optimera din databas arbetsbelastning. I synnerhet kan du optimera de frågor som påverkar prestanda försämras genom att lägga till index. Eller så kan du optimera program med en mer även distribution av arbetsbelastning. Om det inte går att minska arbetsbelastningar eller göra optimeringar överväga att öka prisnivån för prenumerationen SQL-databas att öka mängden tillgängliga resurser.

Om du har uppnått de tillgängliga session gränserna, kan du optimera dina program genom att minska antalet inloggningar som gjorts i databasen. Om du inte kan minska antalet inloggningar från ditt program till databasen, bör du öka prisnivån för din databas. Eller dela upp och flytta databasen till flera databaser för en mer avvägd arbetsbelastningsfördelning.

Fler förslag om hur du löser tidsgränser för sessioner, se [hantera gränserna för SQL maximala databasinloggningar](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Om du vill veta tillgänglig resurs för gränser för din prenumeration nivå [gränserna för SQL-databas](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Ökning av arbetsbelastning

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda identifierar problem som orsakas av en ökning av arbetsbelastningen eller i sin allvarligare form pile-up en arbetsbelastning.

Denna identifiering görs genom en kombination av flera mått. Grundläggande måttet mäts identifierar en ökning i arbetsbelastningen jämfört med tidigare arbetsbelastning baslinjen. Den andra formen av identifiering baseras på att mäta en stor ökning av aktiva trådar som är tillräckligt stor för att påverka prestanda för frågor.

I sin allvarligare form kan arbetsbelastningen kontinuerligt växer på grund av SQL-databasen oförmåga att hantera arbetsbelastningen. Resultatet är en ständigt växande arbetsbelastningsstorlek är arbetsbelastning pile-up villkor. Den tid som arbetsbelastningen väntar körning växer på grund av det här villkoret. Det här villkoret representerar en av de mest allvarliga problem med databasprestanda. Det här problemet har identifierats genom övervakning ökning av antalet avbrutna trådar. 

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut hur många frågor vars körning har ökat och fråga hash för frågan med största bidrar till ökad belastning. Du kan använda den här informationen som en startpunkt för att optimera arbetsbelastningen. Frågan som är mest till ökad arbetsbelastning är särskilt användbar som startpunkt.

Du kan överväga att distribuera arbetsbelastningar jämnare till databasen. Överväg att optimera den fråga som påverkar prestanda genom att lägga till index. Du kan även distribuera arbetsbelastningen mellan flera databaser. Om lösningarna inte är möjligt bör du öka prisnivån för prenumerationen SQL-databas att öka mängden tillgängliga resurser.

## <a name="memory-pressure"></a>Minnesbelastning

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda anger försämring i den aktuella databasens prestanda på grund av minnesbelastning eller på dess allvarligare sätt det pile-up minne, jämfört med de senaste sju dagar prestanda-baslinjen.

Minnesbelastning anger ett prestandavillkor där det finns ett stort antal trådar som begär minne ger i SQL-databasen. Hög volym gör en hög tillstånd där SQL-databasen är inte effektivt allokera minne för alla arbetare som begär den. En av de vanligaste orsakerna till det här problemet beror på mängden ledigt minne i SQL-databas på ena sidan. Å andra sidan orsakar en ökning av arbetsbelastning ökningen på trådar och minnesbelastning.

Allvarligare formen av minnesbelastning är pile-up minne. Det här tillståndet anger att ett högre antal arbetstrådar begär ger minne än vad det finns frågor frigöra minne. Det här antalet trådar som begär minne ger också kan vara kontinuerligt ökar (samlas) eftersom SQL database engine kan inte allokera minne tillräckligt effektivt för att uppfylla behovet. Pile-up minne representerar en av de mest allvarliga problem med databasprestanda.

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut minne objektet store information med clerk (det vill säga arbetstråd) som markerats som högsta anledningen till hög minnesanvändning och relevanta tidsstämplar. Du kan använda den här informationen som grund för felsökning. 

Du kan optimera eller ta bort frågor som rör lagerarbetare med den högsta minnesanvändningen. Du kan också se till att du inte hämtning av data som du inte planerar att använda. Praxis är att alltid använda en WHERE-sats i dina frågor. Vi rekommenderar dessutom att du skapar icke-grupperade index om du vill söka efter data i stället för att skanna den.

Du kan också minska arbetsbelastningen genom optimerar eller distribuera den över flera databaser. Eller så kan du distribuera din arbetsbelastningen mellan flera databaser. Om lösningarna inte är möjligt bör du öka prisnivån för SQL-databas till prenumerationen ökar du mängden minne till databasen.

Ytterligare felsökning förslag finns [minne ger stensättningar: komplicerade SQL Server minne konsumenten med många namn](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Låsning

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda anger försämring i den aktuella databasprestanda där långa databasen låsning upptäcks jämfört med de senaste sju dagar prestanda-baslinjen. 

I moderna RDBMS krävs låsning för att implementera flertrådade system där prestanda är maximerat genom att köra flera samtidiga arbetare och parallella databastransaktioner där det är möjligt. Låsning i den här kontexten refererar till den inbyggda åtkomstmekanism där bara en enskild transaktion kan endast komma åt rader, sidor, tabeller och filer som krävs och inte konkurrerar med en annan transaktion för resurser. När den transaktion som låst resurser för användning är klar med dem. släpps låset för resurserna, vilket gör att andra transaktioner att få åtkomst till resurser som krävs. Läs mer om hur du låser [låsa databasmotorn](https://msdn.microsoft.com/library/ms190615.aspx).

Om transaktioner som utförs av SQL-databasmotor som väntar på under längre tid att få åtkomst till resurser som låst för användning, gör väntetiden minskningen av arbetsbelastningsprestanda för körning. 

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut låsning information som du kan använda som underlag för felsökning. Du kan analysera rapporterade blockerande frågor, det vill säga de frågor som införa låsning prestandaförsämring och ta bort dem. I vissa fall kanske du lyckas optimera blockerande frågor.

Det enklaste och säkraste sättet att åtgärda problemet är att hålla transaktionerna kort och minska Lås storleken för de dyraste frågorna. Du kan dela upp en stor grupp med åtgärder på mindre åtgärder. Praxis är att minska frågan Lås storleken genom att göra frågan så effektivt som möjligt. Minska stora sökningar eftersom de ökar risken för deadlocks och påverkar prestandan för databasen. Du kan skapa nya index eller lägga till kolumner i det befintliga indexet för att undvika tabellsökningar för identifierade frågor som orsakar låsning. 

Fler förslag finns [hur du löser blockerande problem som orsakas av Lås eskalering i SQL Server](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Ökad MAXDOP

### <a name="what-is-happening"></a>Vad händer

Det här mönstret kan upptäckas prestanda anger ett villkor som en valda frågeplan körning var paralleliserad mer än vad som skulle ha varit. Frågeoptimeraren SQL-databas kan förbättra arbetsbelastningens prestanda genom att köra frågor parallellt påskynda saker där det är möjligt. I vissa fall kan ta parallella arbetare bearbetning av en fråga mer tid som väntar på att synkronisera och slå samman resultaten jämförs med samma frågan kördes med färre parallella arbetare eller även i vissa fall jämfört med en enda arbetstråd.

Expert analyseras den aktuella databasens prestanda jämfört med baslinjeperioden. Den avgör om en tidigare pågående fråga körs långsammare än innan eftersom frågeplan för körning mer paralleliserad än det ska vara.

Konfigurationsalternativet MAXDOP server på SQL-databas används för att styra hur många processorkärnor som kan användas för att köra samma fråga parallellt. 

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut frågan hashvärden relaterade till frågor som varaktigheten för körning av ökat eftersom de har mer än de skulle ha varit paralleliserad. Loggen visar också CXP väntetiden. Den här gången representerar den tid som en enda organizer/coordinator tråd (tråden 0) väntar på att andra trådar slutförts innan du kopplar resultaten och flytta vidare. Dessutom diagnostik loggen matar ut väntetiden som frågorna dålig prestanda väntade övergripande körning. Du kan använda den här informationen som grund för felsökning.

Först optimera eller förenkla komplexa frågor. Praxis är att dela upp långa batchjobb i mindre. Kontrollera dessutom att du har skapat index för att stödja dina frågor. Du kan också manuellt tillämpa högsta grad av parallellitet (MAXDOP) för en fråga som har flaggats som dålig utför. Om du vill konfigurera den här åtgärden med hjälp av T-SQL finns [konfigurera MAXDOP serverkonfigurationsalternativet](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Ange MAXDOP-server konfigurationsalternativet till noll (0) som standard anger som SQL-databas kan använda alla tillgängliga logiska processorkärnor parallelize trådar för att köra en enskild fråga. Ange MAXDOP till en (1) anger att endast en kärna kan användas för körning av en enskild fråga. I praktiken innebär detta att parallellitet är avstängd. Beroende på den per fall till fall tillgängliga kärnor till databasen och diagnostik logga information, du kan finjustera MAXDOP alternativet att antal kärnor som används för parallell frågekörning som kan lösa problemet i ditt fall.

## <a name="pagelatch-contention"></a>Pagelatch konkurrens

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda visar den nuvarande arbetsbelastningen för databasen prestandaförsämring på grund av pagelatch konkurrens jämfört med de senaste sju dagar arbetsbelastning baslinjen.

Lås är förenklad synkroniseringsmekanismer som används av SQL-databas för att aktivera flertrådsteknik. De garantera konsekvens i InMemory-datastrukturer som inkluderar index, datasidor och andra interna strukturer.

Det finns många typer av lås på SQL-databasen. För enkelhetens skull används buffert Lås för att skydda sidor i minne i buffertpoolen. I/o-Lås för att skydda sidor som ännu inte har lästs in i buffertpoolen. När data skrivs till eller läses från en sida i buffertpoolen, måste en arbetstråd att erhålla en buffert spärren för sidan först. När en arbetstråd försöker komma åt en sida som inte finns i poolen i minnesbufferten, görs en i/o-begäran att läsa in nödvändig information från lagringsplatsen. Denna sekvens av händelser som indikerar en allvarligare form av försämrade prestanda.

På sidan Lås konkurrens när flera trådar samtidigt försöker hämta lås på samma struktur i minnet, vilket ger en ökad väntetiden för att köra frågor. När det gäller pagelatch i/o konkurrens när data behöver komma åt från lagringen, är väntetiden ännu större. Det kan påverka arbetsbelastningens prestanda avsevärt. Pagelatch konkurrens är det vanligaste scenariot för trådar som väntar på varandra och konkurrerar om resurser på flera CPU-system.

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut pagelatch konkurrens information. Du kan använda den här informationen som grund för felsökning.

Eftersom en pagelatch är en mekanism för intern kontroll av SQL-databas, bestämmer den automatiskt när de ska användas. Programmet beslut, inklusive schemat design, kan påverka pagelatch sätt på grund av deterministiska beteendet för lås.

Ett sätt att hantera spärren konkurrens är att ersätta en sekventiell indexnyckeln med en sekventiellt för att distribuera infogningar jämnt över ett index intervall. Normalt distribuerar en inledande kolumnen i indexet arbetsbelastningen proportionerligt. Ett annat sätt att tänka på är Tabellpartitionering. Att skapa en hash partitioneringsschema med en beräknad kolumn för en partitionerad tabell är en vanlig metod för att minimera konkurrens överdriven spärren. När det gäller pagelatch i/o konkurrens hjälper introduktion till index till att minimera det här prestandaproblemet. 

Mer information finns i [diagnostisera och åtgärda spärr konkurrens på SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (nedladdning PDF).

## <a name="missing-index"></a>Index som saknas

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda anger den aktuella databasen arbetsbelastning försämrade prestanda jämfört med de senaste sju dagar baslinjen på grund av ett index som saknas.

Ett index används för att påskynda prestanda för frågor. Det ger snabb åtkomst till tabelldata genom att minska antalet sidor som dataset som behöver besökt eller genomsöks.

Specifika frågor som orsakade prestandaförsämring identifieras via denna identifiering som skapar index skulle vara praktiskt att prestanda.

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut frågan hashvärden för de frågor som har identifierats för att påverka arbetsbelastningens prestanda. Du kan skapa index för de här frågorna. Du kan också optimera eller ta bort de här frågorna om de inte behövs. En bra prestanda sättet är att undvika datafrågor som du inte använder.

> [!TIP]
> Visste du att SQL-databas inbyggd intelligens automatiskt kan hantera de bäst index för dina databaser?
>
> För kontinuerlig prestandaoptimering av SQL-databas, rekommenderar vi att du aktiverar [SQL-databas automatisk justering](sql-database-automatic-tuning.md). Den här unika funktionen för SQL-databas inbyggd intelligens kontinuerligt övervakar din SQL-databas och justerar och automatiskt skapar index för dina databaser.
>

## <a name="new-query"></a>Ny fråga

### <a name="what-is-happening"></a>Vad händer

Det här mönstret prestanda indikerar att en ny fråga har identifierats som utför dåligt och påverkar arbetsbelastningsprestanda jämfört med prestandabaslinje för sju dagar.

Skriva en fråga med bra prestanda ibland kan vara en utmaning. Mer information om hur du skriver frågor finns [skriva SQL-frågor](https://msdn.microsoft.com/library/bb264565.aspx). För att optimera prestanda för befintliga frågor finns [frågejusteringar](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Felsökning

Diagnostiken logginformation utdata upp till två nya de flesta förbrukning av CPU frågor, inklusive deras hashvärden för frågan. Eftersom identifierade frågan påverkar arbetsbelastningsprestanda, kan du optimera din fråga. Praxis är att hämta bara de data som du behöver använda. Vi rekommenderar också att använda frågor med en WHERE-sats. Vi rekommenderar också att du förenkla komplexa frågor och dela upp dem i mindre frågor. En annan praxis är att dela upp stora batch frågor i mindre batch-frågor. Introduktion till index för nya frågor är vanligtvis en bra idé att minimera det här prestandaproblemet.

Överväg att använda [Azure SQL Database Query Performance Insight](sql-database-query-performance.md).

## <a name="unusual-wait-statistic"></a>Onormal vänta statistik

### <a name="what-is-happening"></a>Vad händer

Det här mönstret kan upptäckas prestanda anger en arbetsbelastning prestandaförsämring där dålig prestanda frågor identifieras jämfört med de senaste sju dagar arbetsbelastning baslinjen.

I det här fallet systemet kan inte klassificera frågorna dålig prestanda under några andra kategorier som standard kan upptäckas prestanda, men det identifieras ansvarar för regressionen vänta statistiken. Därför betraktar dem som frågor med *ovanliga vänta statistik*, där ansvarar för regressionen ovanliga vänta statistiken också exponeras. 

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut information om ovanliga vänta tidsinformation, fråga hash-värden för de berörda frågorna och väntetiden.

Eftersom systemet inte kunde har identifierat grundorsaken för frågor dålig prestanda, är diagnostikinformationen en bra utgångspunkt för manuell felsökning. Du kan optimera prestanda för dessa frågor. Det är en bra idé att hämta bara de data som du måste använda och förenkla och dela upp avancerade frågor i mindre. 

Mer information om hur du optimerar prestanda för frågor finns [frågejusteringar](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>TempDB konkurrens

### <a name="what-is-happening"></a>Vad händer

Det här mönstret kan upptäckas prestanda anger prestandavillkor database där en flaskhals trådar som försöker komma åt resurser i tempDB finns. (Det här villkoret är inte i/o som är relaterade.) Vanligt scenario för det här prestandaproblemet är hundratals samtidiga frågor att alla skapa, använda och ta bort små tempDB-register. Systemet upptäckte att antalet samtidiga frågor med samma tempDB tabeller ökade med tillräckligt statistiska signifikans att påverka databasens prestanda jämfört med de senaste sju dagar prestanda-baslinjen.

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut tempDB konkurrens information. Du kan använda informationen som startpunkt för felsökning. Det finns två saker du kan välja om du vill minska den här typen av konkurrens och öka genomflödet av övergripande arbetsbelastningen: du kan sluta använda de temporära tabellerna. Du kan också använda minnesoptimerade tabeller. 

Mer information finns i [introduktion till minnesoptimerade tabeller](https://docs.microsoft.com/en-us/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Elastisk Pool DTU lite

### <a name="what-is-happening"></a>Vad händer

Det här mönstret kan upptäckas prestanda anger en försämring i den aktuella databasen arbetsbelastningsprestanda jämfört med de senaste sju dagar baslinjen. Det beror på brist på tillgängliga dtu: er i den elastiska poolen för din prenumeration. 

Resurser på SQL-databas är vanligtvis kallas [DTU-resurser](sql-database-what-is-a-dtu.md), som består av en blandning av processor- och i/o (data och transaktionen loggning i/o) resurser. [Azure elastisk pool resurser](sql-database-elastic-pool.md) används som en pool med tillgängliga eDTU-resurserna som delas mellan flera databaser för att skala syften. När tillgängliga eDTU-resurserna i din elastisk pool inte är tillräckligt stor för att stödja alla databaser i poolen, identifierade en elastisk pool DTU lite prestandaproblemet av systemet.

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut information om den elastiska poolen, visas de översta DTU-förbrukning databaserna och ger en procentandel av poolens DTU som används av databasen förbrukar upp.

Eftersom den här prestandavillkor är relaterad till flera databaser med samma pool med edtu: er i den elastiska poolen kan fokusera felsökningsstegen på översta DTU-förbrukning databaserna. Du kan minska belastningen på översta förbrukar-databaser som innehåller optimering av förbrukar top-frågor för dessa databaser. Du kan också kontrollera att du inte hämtning av data som du inte använder. En annan metod är att optimera program med hjälp av översta DTU-förbrukning databaser och omdistribuera arbetsbelastningen mellan flera databaser.

Om inte är möjligt minska och optimering av den nuvarande arbetsbelastningen på översta DTU-förbrukning databaserna kan du överväga att öka din elastisk pool prisnivån. Höjningen resulterar i fler tillgängliga dtu: erna i den elastiska poolen.

## <a name="plan-regression"></a>Planera Regression

### <a name="what-is-happening"></a>Vad händer

Det här mönstret kan upptäckas prestanda anger ett villkor som SQL-databasen använder en något sämre frågeplan för körning. Något sämre planen gör vanligtvis ökad Frågekörningen, vilket leder till att vänta längre tid för de aktuella och andra frågorna.

SQL-databasen anger frågeplanen för körning med minst kostnad för körning av en fråga. Ibland befintliga scheman inte längre är effektivt som typ av ändring av frågor och arbetsbelastningar eller kanske SQL-databasen inte har gjort en bra assessment. Som en följd av korrigering kan körningen frågeplaner manuellt framtvingas.

Det här mönstret kan upptäckas prestanda kombinerar tre olika fall av planen regressionsmodell: regression av nya och gamla planen regressionsmodell befintliga planer ändras arbetsbelastning. Viss typ av planen regressionsmodell som inträffat finns i den *information* egenskap i loggen för diagnostik.

Det nya plan regressionsmodell villkoret refererar till ett tillstånd där startar SQL-databas körs en ny körning frågeplan som inte är så effektivt som den gamla planen. Gamla planen regressionsmodell villkoret refererar till tillståndet när SQL-databas växlar från att använda en ny, mer effektiv plan till den gamla planen som inte är så effektivt som den nya planen. Den befintliga planer ändras arbetsbelastning regressionen refererar till ett tillstånd där gammalt och nya planer kontinuerligt alternativ, Saldo mer går åt planen dålig prestanda.

Mer information om plan regressioner finns [vad är planen regressionsmodell i SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen matar ut frågan hashvärden, bra plan-ID, felaktigt plan-ID och fråga ID: N. Du kan använda den här informationen som grund för felsökning.

Du kan analysera vilka planen är bättre utför för dina specifika frågor som du kan identifiera med frågan hashvärdena tillhandahålls. När du har bestämt vilken fungerar bättre för dina frågor, kan du tvinga den manuellt. 

Mer information finns i [Lär dig hur SQL Server förhindrar plan regressioner](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Visste du att SQL-databas inbyggd intelligens automatiskt kan hantera bäst prestanda frågeplaner av körningen för dina databaser?
>
> För kontinuerlig prestandaoptimering av SQL-databas, rekommenderar vi att du aktiverar [SQL-databas automatisk justering](sql-database-automatic-tuning.md). Den här unika funktionen för SQL-databas inbyggd intelligens kontinuerligt övervakar din SQL-databas och automatiskt justerar och skapar bäst att genomföra frågan körning planer för dina databaser.
>

## <a name="database-scoped-configuration-value-change"></a>Databas-omfattande värdet konfigurationsändring

### <a name="what-is-happening"></a>Vad händer

Det här mönstret kan upptäckas prestanda visar ett tillstånd som gör en ändring i konfigurationen för databas-omfattande prestanda regression som upptäcks jämfört med tidigare sju dagar databasen arbetsbelastning beteendet. Det här mönstret anger att en ändring som gjorts i databas-omfattande konfigurationen inte verkar vara en fördel att databasens prestanda.

Databas-omfattande konfigurationsändringar kan anges för varje enskild databas. Den här konfigurationen används på grundval av fall för att optimera enskilda prestandan för din databas. Följande alternativ kan konfigureras för varje enskild databas: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES och AVMARKERA PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Felsökning

Diagnostiken logga utdata databas-omfattande konfigurationsändringar som gjordes nyligen som orsakade försämrade prestanda jämfört med föregående sju dagar arbetsbelastning beteendet. Du kan återställa konfigurationsändringar i föregående värden. Du kan även finjustera värde med värde tills önskad prestandanivå har uppnåtts. Du kan kopiera databasen scope konfigurationsvärden från en liknande databas med tillfredsställande prestanda. Om det inte går att felsöka prestanda återgå till standardvärden för SQL Database Standard och försöker finjustera från och med denna baslinje.

Mer information om hur du optimerar databas-omfattande konfiguration och T-SQL-syntax för att ändra konfigurationen finns [Alter databas-omfattande configuration (Transact-SQL)](https://msdn.microsoft.com/en-us/library/mt629158.aspx).

## <a name="slow-client"></a>Långsam klienten

### <a name="what-is-happening"></a>Vad händer

Det här mönstret kan upptäckas prestanda visar ett tillstånd där den klient som använder SQL-databasen inte kan använda utdata från databasen snabb databasen skickar resultaten. Eftersom SQL-databas inte är lagra resultaten för frågorna som körs i en buffert, långsammare och väntar på att klienten kan använda utdata överförda fråga innan du fortsätter. Det här tillståndet kan också relateras till ett nätverk som inte är tillräckligt tillräckligt snabbt att skicka utdata från SQL-databasen till konsumerande klienten.

Det här tillståndet genereras bara om en prestanda regression upptäcks jämfört med tidigare sju dagar databasen arbetsbelastning beteendet. Det här prestandaproblemet identifieras bara om det uppstår ett statistiskt märkbar försämring av prestanda jämfört med tidigare prestanda beteende.

### <a name="troubleshooting"></a>Felsökning

Det här mönstret kan upptäckas prestanda visar ett tillstånd för klientsidan. Krävs felsökning på klientsidan eller nätverket på klientsidan. Diagnostik-loggen matar ut frågan hashvärden och väntetiden som verkar väntar på de flesta som klienten ska använda dem under de senaste två timmarna. Du kan använda den här informationen som grund för felsökning.

Du kan optimera prestanda för ditt program för användning av dessa frågor. Du kan också överväga möjliga problem med nätverkssvarstiden. Eftersom prestanda försämras problemet har baserat på ändringen i den senaste baslinjen för prestanda för sju dagar kan undersöka du om ändringar för villkoret program eller nätverket beror prestanda regression händelsen. 

## <a name="pricing-tier-downgrade"></a>Priser för nedgradering av nivå

### <a name="what-is-happening"></a>Vad händer

Det här mönstret kan upptäckas prestanda visar ett tillstånd där prisnivån för din prenumeration för SQL-databasen har nedgraderas. Systemet upptäckte en nedgång i den aktuella databasens prestanda jämfört med de senaste sju dagar baslinjen på grund av minskning av resurser (Dtu) tillgängliga i databasen.

Dessutom kan det vara ett tillstånd där prisnivån för din SQL Database-prenumeration nedgraderas och sedan uppgraderas till en högre nivå inom en kort tidsperiod. Identifiering av den här tillfälliga prestandaförsämring används för utdata i informationsavsnittet i loggen diagnostik som en prisnivå nivå nedgradering och uppgradering.

### <a name="troubleshooting"></a>Felsökning

Om du minskar din prisnivå och därför tillgängliga för SQL Database dtu: er, och du är nöjd med prestanda, finns det inget du behöver göra. Om du minskar din prisnivå och du är nöjd med din SQL-databasens prestanda minska databasarbetsbelastningar eller Överväg att öka prisnivån till en högre nivå.

## <a name="recommended-troubleshooting-flow"></a>Rekommenderade felsökning flöde

 Följ flödesschema en rekommenderad metod för att felsöka problem med prestanda med Intelligent insikter.

Åtkomst Intelligent insikter via Azure portal genom att gå till Azure SQL Analytics. Försök att hitta inkommande Prestandavarning och markera den. Identifiera vad som händer på sidan identifieringar. Se den angivna Rotorsaksanalys av problemet, frågetexten, fråga tid trender och incident utvecklingen. Försök att lösa problemet med hjälp av Intelligent insikter rekommendation för att minimera prestandaproblem. 

[![Flödesschema för felsökning](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Välj flödesschema för att hämta en PDF-version.

Intelligent insikter behöver du normalt en timme för att utföra en Rotorsaksanalys av prestandaproblem. Om du inte kan hitta ditt problem i Intelligent insikter och det är viktigt att du kan använda Query Store för att identifiera orsaken till prestandaproblemet manuellt. (Normalt problemen är mindre än en timme gamla.) Mer information finns i [övervaka prestanda genom att använda Query Store](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Nästa steg
- Läs [Intelligent insikter](sql-database-intelligent-insights.md) begrepp.
- Använd den [Intelligent insikter Azure SQL Database prestanda diagnostik loggen](sql-database-intelligent-insights-use-diagnostics-log.md).
- Övervakaren [Azure SQL Database med hjälp av Azure SQL Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql).
- Lär dig hur du [samla in och använda loggdata från resurserna i Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
