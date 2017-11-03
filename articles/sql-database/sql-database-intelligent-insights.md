---
title: "Övervaka databasanvändningen med Intelligent Insights - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database Intelligent insikter använder inbyggd intelligens kontinuerligt övervaka databasanvändningen via styrs av datorn och identifiera störande händelser som orsakar prestandaproblem."
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
ms.openlocfilehash: 86011610885ff913bfd70aa46389e4e39989d0a3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="intelligent-insights"></a>Intelligent Insights

Azure SQL Database Intelligent insikter får du reda på vad som händer med databasens prestanda.

Intelligent insikter använder inbyggd intelligens kontinuerligt övervaka databasanvändningen via styrs av datorn och identifiera störande händelser som orsakar prestandaproblem. När identifierat, utförs en detaljerad analys som genereras en logg för diagnostik med en intelligent bedömning av problemet. Denna bedömning består av en Rotorsaksanalys av prestandaproblem databasen och, om möjligt rekommendationer för bättre prestanda. 

## <a name="what-can-intelligent-insights-do-for-you"></a>Vad gör Intelligent insikter du?

Intelligent Insights är en unik funktion i Azure inbyggd intelligens som tillhandahåller följande värde:

- Proaktiv övervakning
- Anpassad prestandan
- Tidig upptäckt av prestandaförsämring i databasen
- Rotorsak analys av problem har identifierats
- Rekommendationer för förbättring av prestanda
- Skala ut kapaciteten på hundratals tusentals databaser
- Positivt DevOps-resurser och den totala ägandekostnaden

## <a name="how-does-intelligent-insights-work"></a>Hur fungerar Intelligent insikter?

Intelligent insikter analyserar SQL-databasens prestanda genom att jämföra arbetsbelastningen databasen från den senaste timmen med den senaste sju dagar baslinje-arbetsbelastningen. Databasen arbetsbelastningen består av frågor som fastställs de viktigaste till databasprestanda som mest upprepade och största frågor. Eftersom varje databas är unikt baserat på dess struktur, data, användning och program är varje arbetsbelastning baslinje som genereras specifika och unik för en enskild instans. Intelligent Insights, oberoende av baslinjen arbetsbelastning också övervakar absolut operativa tröskelvärden och identifierar problem med långa väntetiden, kritiska undantag och problem med frågan parameterizations som kan påverka prestanda.

När ett problem med prestanda försämras identifieras från flera observerade mått med styrs av datorn, utförs analys. En diagnostik loggen genereras med en intelligent inblick i vad som händer med din databas. Intelligent Insights gör det enkelt att spåra prestandaproblemet databas från dess första utseende fram till lösning. Alla upptäckta problemet spåras genom livscykeln från första problemet identifiering och kontroll av prestandaförbättring den har slutförts. Uppdateringar finns i loggen diagnostik var 15: e minut. 

![Databasen prestanda analys arbetsflöde](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Mått som används för att mäta och identifiera problem med databasprestanda baseras på frågan varaktighet, timeout-begäranden, långa väntetiden och felaktiga begäranden. Mer information om mått finns i [identifiering mått](sql-database-intelligent-insights.md#detection-metrics) i det här dokumentet.

## <a name="degradations-detected"></a>Degradations upptäcktes

Identifiera SQL-databas prestandaförsämringar registreras i loggen diagnostik med intelligent poster som består av följande egenskaper:

| Egenskap             | Information              |
| :------------------- | ------------------- |
| Information om databas | Metadata om en databas som en förklaring upptäcktes, som en resurs-URI. |
| Observerade tidsintervall | Start- och sluttid för perioden för den identifierade information. |
| Påverkas mått | Mått som orsakade en förklaring som ska genereras: <ul><li>Fråga varaktighet öka [sekunder].</li><li>Långa väntar [sekunder].</li><li>Timeout begäranden [procent].</li><li>Avbrutna pga begäranden [procent].</li></ul>|
| Påverkan värde | Värdet för ett mått anges. |
| Påverkas frågor och felkoder | Fråga hash eller felkod. Dessa kan användas för att enkelt korrelera till berörda frågor. Mått som består av frågan varaktighet ökning, väntetiden, timeout antal eller felkoder finns. |
| Identifieringar | Identifiering har identifierats på databasen under tid då en händelse. Det finns 15 identifiering mönster. Mer information finns i [Felsök problem med databasprestanda med Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Rotorsaksanalys | Rotorsak analys av problem som identifieras i ett läsbart format. Vissa insikter kan innehålla en rekommendation för förbättring av prestanda när det är möjligt. |
|||

## <a name="issues-state-lifecycle-active-verifying-and-complete"></a>Problem tillstånd livscykel: ”aktiv”, ”verifiera” och ”Slutför”

Prestandaproblem som har registrerats i loggen diagnostik är flaggade med något av tre tillstånd i livscykeln för ett problem: ”aktiv”, ”verifiera” och ”Slutför”. När en prestanda problem upptäcks och länge den har bedömer som finns med SQL-databas inbyggd intelligens problemet har flaggats som ”aktiv”. När problemet anses minskas, kontrolleras och problemet statusen ändras till ”verifiera”. När SQL-databas inbyggd intelligens anser problemet löst, har problemet status flaggats som ”Slutför”.

## <a name="use-intelligent-insights"></a>Använd Intelligent insikter

Du kan skicka loggen Intelligent insikter diagnostik till Azure Log Analytics, Azure Event Hubs och Azure Storage. Mer information finns i [Azure SQL Database mått och diagnostikloggning](sql-database-metrics-diag-logging.md). När du har skickat loggen till någon av dessa mål kan loggen användas för anpassade aviseringar och övervaka utveckling med hjälp av Microsoft eller verktyg från tredje part. 

Mer information om SQL-databasens prestanda felsökning med hjälp av Intelligent insikter finns [felsöka Azure SQL Database prestandaproblem med Intelligent insikter](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="built-in-intelligent-insights-analytics-with-log-analytics"></a>Den inbyggda Intelligent insikter analytics med logganalys 

En lösning för Log Analytics tillhandahåller rapporter och aviseringar funktioner utöver Intelligent insikter diagnostik logga data. I följande exempel visas en Intelligent insikter rapport i Azure SQL Analytics:

![Intelligent insikter rapport](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Intelligent insikter diagnostik loggen är konfigurerad att sända data till SQL Analytics, kan du [övervaka SQL-databasen med hjälp av SQL-Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Anpassad integrering av Intelligent insikter logg

Mer information om övervakning utveckling med hjälp av Microsoft eller verktyg från tredje part och anpassade aviseringar finns [Använd Intelligent insikter databasen prestanda diagnostik logga](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="set-up-intelligent-insights-with-event-hubs"></a>Ställ in Intelligent insikter med Händelsehubbar

- Om du vill konfigurera Intelligent insikter om du vill strömma logghändelser i Händelsehubbar finns [dataströmmen Azure diagnostics loggar för Händelsehubbar](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).
- Om du vill använda Händelsehubbar för anpassade övervakning och avisering finns [vad du gör med mätvärden och diagnostikfunktionerna loggar i Händelsehubbar](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs). 

## <a name="set-up-intelligent-insights-with-storage"></a>Ställ in Intelligent insikter med lagring

- Om du vill konfigurera Intelligent insikter lagras med lagring finns [dataström i Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage).

## <a name="detection-metrics"></a>Identifiering av mått

Mått som används för identifiering av modeller som genererar Intelligent insikter baseras på övervakning:

- frågan varaktighet
- Tidsgränsen för begäran
- Långa väntetiden
- Felaktiga ut begäranden

Varaktighet och timeout frågebegäranden används som primära modeller för identifiering av problem med databasprestanda arbetsbelastning. De används eftersom vad som händer med arbetsbelastningen mäta direkt. För att identifiera alla möjliga fall av arbetsbelastningsprestanda försämras, överdriven Väntetid och avbrutna pga begäranden används som ytterligare modeller för att problem som påverkar arbetsbelastningsprestanda.

Anses automatiskt ändringar i arbetsbelastningen och ändringar i antalet frågebegäranden som görs till databasen för att dynamiskt bestämma databas normal och out normala prestandatrösklarna.

Alla mätvärden bedöms tillsammans i olika relationer via en vetenskapligt härledda datamodell som kategoriserar varje prestandaproblemet upptäcktes. Information som tillhandahålls via en intelligent insight inkluderar:

* Information om prestandaproblemet upptäcktes. 
* En Rotorsaksanalys av det identifierade problemet. 
* Rekommendationer om hur du förbättrar prestandan för övervakade SQL-databasen där det är möjligt.

## <a name="query-duration"></a>frågan varaktighet

Frågan varaktighet försämring modellen analyserar enskilda frågor och identifierar ökningen i den tid det tar att kompilera och köra en fråga jämfört med baslinje för prestanda.

Om SQL-databas inbyggd intelligens upptäcker mycket större i frågan kompilerades eller fråga körningstid som påverkar arbetsbelastningsprestanda, de här frågorna har flaggats som frågan varaktighet problem med prestanda försämras. 

Intelligent insikter diagnostik loggen matar ut frågan hash frågan försämrad prestanda. Frågan hash-värdet anger om försämrade prestanda var relaterat till frågan kompilerades eller körningstid ökning, som ökade frågan varaktighet.

## <a name="timeout-requests"></a>Tidsgränsen för begäran

Timeout för begäranden försämring modellen analyserar enskilda frågor och identifierar en ökning i timeout på nivån för körning av fråga och den övergripande begäran-timeout på databasnivå jämfört med baslinjeperioden prestanda.

Alla frågor kan timeout innan de når fasen körning. Sätt av avbrutna Worker jämfört med begäranden som görs via SQL-databas inbyggd intelligens mäter och analyserar alla frågor som når databasen om de kom till körning steg eller inte. 

När antalet tidsgränser för utförda frågor eller antalet avbrutna begäran arbetare överskrider tröskelvärdet hanteras av datorn, fylls en diagnostik logg med intelligent insikter.

De insikter som genereras innehåller antalet begäranden som orsakade timeout och antalet frågor som orsakade timeout. Uppgift om försämrade prestanda är relaterad till timeout ökning i skedet körningen eller övergripande databasnivå har angetts. När ökade timeout anses relevanta för att databasens prestanda, har de här frågorna flaggats som timeout försämring prestandaproblem. 

## <a name="excessive-wait-times"></a>Långa väntetiden

Den långa väntetid tid modellen övervakar enskilda databasfrågor. Den identifierar ovanligt hög frågan vänta statistik som passeras absolut tröskelvärden hanteras av datorn. Följande fråga långa väntetiden mått observeras med hjälp av den nya SQL Server-funktionen Query Store vänta Stats (sys.query_store_wait_stats):

- Gränserna för att
- Gränserna för att elastisk pool
- Många worker eller session trådar
- Långa databasen låsning
- Minnesbelastning
- Andra vänta statistik

Gränserna för når eller gränserna för elastiska poolen kan du ange att förbrukningen av resurser som är tillgängliga på en prenumeration eller i den elastiska poolen passerat absolut tröskelvärden. Dessa stats indikera arbetsbelastning prestanda försämras. Ett orimligt antal trådar som worker eller session anger ett villkor som antalet arbetstrådar eller sessioner som initierats korsade absolut tröskelvärden. Dessa stats indikera arbetsbelastning prestanda försämras.

Långa databasen låsning anger ett villkor som antalet lås på en databas har passerat absolut tröskelvärden. Den här stat anger en arbetsbelastning prestanda försämras. Minne trycket är ett villkor som ger antalet trådar som begär minnet överskred en absolut tröskelvärdet. Den här stat anger en arbetsbelastning prestanda försämras.

Andra vänta stats identifiering visar ett tillstånd där diverse mått som mäts via Query Store vänta Stats överskred en absolut tröskelvärdet. Dessa stats indikera arbetsbelastning prestanda försämras.

När långa väntetiden identifieras, beroende på data som är tillgängliga, Intelligent insikter diagnostik logga utdata hashvärden som påverkar och berörda frågor försämrad prestanda, information om de mätvärden som orsakar frågor för att vänta i körningen, och uppmätt väntetid.

## <a name="errored-requests"></a>Felaktiga begäranden

Felaktiga begäranden försämring modell Övervakare individuella frågar och identifierar en ökning i antalet frågor som felaktiga ut jämfört med baslinjeperioden. Den här modellen övervakar även kritiska undantag som passeras absolut tröskelvärden som hanteras av SQL-databas inbyggd intelligens. Antal frågebegäranden som gjorts i databasen och konton för ändringar arbetsbelastning i övervakade perioden är automatiskt.

När uppmätta ökningen av felaktiga begäranden i förhållande till det totala antalet begäranden som görs bedöms till arbetsbelastningsprestanda, har berörda frågor flaggats som felaktiga begäranden försämring prestandaproblem.

Intelligent insikter loggen matar ut antal felaktiga begäranden. Anger om prestandaförsämring relaterade till ökad felaktiga begäranden eller passerar ett tröskelvärde för övervakade kritiska undantag och uppmätt tid för försämrade prestanda. 

Om några av de övervakade kritiska undantag mellan absolut tröskelvärden som hanteras av systemet genereras en intelligent kunskaper med kritiska undantagsinformation.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [Felsök prestandaproblem för SQL-databas med Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
* Använd den [Intelligent insikter SQL Database prestanda diagnostik loggen](sql-database-intelligent-insights-use-diagnostics-log.md).
* Lär dig hur du [övervaka SQL-databas med hjälp av SQL-Analytics](../log-analytics/log-analytics-azure-sql.md).
* Lär dig hur du [samla in och använda loggdata från resurserna i Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).


