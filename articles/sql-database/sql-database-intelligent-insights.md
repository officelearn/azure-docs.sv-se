---
title: Övervaka databasens prestanda med Intelligent Insights
description: Azure SQL Database Intelligent Insights använder inbyggd intelligens för att kontinuerligt övervaka databasanvändningen genom artificiell intelligens och identifiera störande händelser som orsakar dåliga prestanda.
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
ms.openlocfilehash: d7b9ada17871dc7882209b7a8a449a8edcd61a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214078"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligenta insikter med AI för att övervaka och felsöka databasprestanda (förhandsgranskning)

Azure SQL Database Intelligent Insights låter dig veta vad som händer med databasens prestanda.

Intelligent Insights använder inbyggd intelligens för att kontinuerligt övervaka databasanvändningen genom artificiell intelligens och upptäcka störande händelser som orsakar dåliga prestanda. När det har identifierats utförs en detaljerad analys som genererar en intelligent insights-resurslogg (kallas SQLInsights) med en intelligent bedömning av problemet. Den här utvärderingen består av en grundorsaksanalys av databasprestandaproblemet och, om möjligt, rekommendationer för prestandaförbättringar.

## <a name="what-can-intelligent-insights-do-for-you"></a>Vad kan Intelligent Insights göra för dig

Intelligent Insights är en unik funktion med inbyggd Azure-intelligens som ger följande värde:

- Proaktiv övervakning
- Skräddarsydda prestandainsikter
- Tidig upptäckt av databasprestandaförsämring
- Rotorsaksanalys av upptäckta problem
- Rekommendationer för prestandaförbättring
- Skala ut kapacitet på hundratusentals databaser
- Positiv inverkan på DevOps-resurser och den totala ägandekostnaden

## <a name="how-does-intelligent-insights-work"></a>Hur fungerar Intelligent Insights

Intelligent Insights analyserar databasprestanda genom att jämföra databasarbetsbelastningen från den senaste timmen med den senaste sjudagarsbaslinjearbetsbelastningen. Databasarbetsbelastning består av frågor som bedöms vara de viktigaste för databasens prestanda, till exempel de mest upprepade och största frågorna. Eftersom varje databas är unik baserat på dess struktur, data, användning och program, är varje arbetsbelastningsbaslinje som genereras specifik och unik för den arbetsbelastningen. Intelligent Insights, oberoende av arbetsbelastningens baslinje, övervakar också absoluta driftströsklar och identifierar problem med alltför långa väntetider, kritiska undantag och problem med frågeparametrar som kan påverka prestanda.

När ett prestandaförsämringsproblem har identifierats från flera observerade mått med hjälp av artificiell intelligens utförs analys. En diagnostiklogg genereras med en intelligent insikt om vad som händer med databasen. Intelligent Insights gör det enkelt att spåra problemet med databasens prestanda från dess första utseende till upplösning. Varje upptäckt problem spåras genom dess livscykel från första problemidentifiering och verifiering av prestandaförbättring till dess slutförande.

![Arbetsflöde för analys av databasprestanda](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

De mått som används för att mäta och identifiera prestandaproblem för databaser baseras på frågevaraktighet, timeout-begäranden, för höga väntetider och felaktiga begäranden. Mer information om mått finns i [Identifieringsmått](#detection-metrics).

Identifierade SQL Database-prestandaförsämring registreras i SQLInsights-loggen med intelligenta poster som består av följande egenskaper:

| Egenskap | Information |
| :------------------- | ------------------- |
| Databasinformation | Metadata om en databas där en insikt upptäcktes, till exempel en resurs-URI. |
| Observerat tidsintervall | Start- och sluttid för perioden för den identifierade insikten. |
| Påverkade mått | Mått som orsakade att en insikt genererades: <ul><li>Ökning av frågans varaktighet [sekunder].</li><li>Överdriven väntan [sekunder].</li><li>Tidsbetalade begäranden [procent].</li><li>Felutfrågningar [procent].</li></ul>|
| Effektvärde | Värdet för ett mått som mäts. |
| Påverkade frågor och felkoder | Fråga hash eller felkod. Dessa kan användas för att enkelt korrelera till berörda frågor. Mått som består av antingen frågevaraktighetsökning, väntetid, timeout-antal eller felkoder tillhandahålls. |
| Upptäckter | Identifiering som identifierats i databasen under tiden för en händelse. Det finns 15 detekteringsmönster. Mer information finns i [Felsöka problem med databasens prestanda med Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Rotorsaksanalys | Rotorsaksanalys av problemet som identifierats i ett läsbart format. Vissa insikter kan innehålla en rekommendation om prestandaförbättring där det är möjligt. |
|||

En praktisk översikt över hur du använder Intelligenta insikter med Azure SQL Analytics och för typiska användningsscenarier finns i den här videon:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights lyser i att upptäcka och felsöka SQL Database prestandaproblem. Information om hur du använder Intelligent Insights för att felsöka prestandaproblem i databasen finns i [Felsöka prestandaproblem i Azure SQL Database med Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Alternativ för intelligenta insikter

Intelligent Insights-alternativ som är tillgängliga i Azure SQL Database är:

| Alternativ för Intelligenta insikter | Stöd för en enda databas och poolad databas | Stöd för instansdatabas |
| :----------------------------- | ----- | ----- |
| **Konfigurera intelligenta insikter** – konfigurera intelligenta insikter för dina databaser. | Ja | Ja |
| **Strömma insikter till Azure SQL Analytics** – Strömma insikter till Azure SQL Analytics-övervakningslösning för Azure SQL Database. | Ja | Ja |
| **Strömma insikter till Event Hub** - Strömma insikter till eventhubbar för ytterligare anpassade integreringar. | Ja | Ja |
| **Strömma insikter till Azure Storage** - Strömma insikter till Azure Storage för ytterligare analys och långsiktig arkivering. | Ja | Ja |

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Konfigurera export av loggen Intelligent Insights

Utdata från Intelligent Insights kan strömmas till en av flera platser för analys:

- Utdata som strömmas till en Log Analytics-arbetsyta kan användas med [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) för att visa insikter via användargränssnittet i Azure-portalen. Det här är den integrerade Azure-lösningen och det mest typiska sättet att visa insikter.
- Utdata som strömmas till Azure Event Hubs kan användas för utveckling av anpassade övervaknings- och varningsscenarier
- Utdata som strömmas till Azure Storage kan användas för anpassad programutveckling, till exempel anpassad rapportering, långsiktig dataarkivering och så vidare.

Integrering av Azure SQL Analytics, Azure Event Hub, Azure Storage eller tredjepartsprodukter för förbrukning utförs genom att först aktivera Intelligent Insights-loggning ("SQLInsights"-loggen) i diagnostikinställningsbladet i en databas och sedan konfigurera intelligenta insikter loggdata som ska strömmas till ett av dessa mål.

Mer information om hur du aktiverar intelligent inloggning av insikter och konfigurerar mått- och resursloggdata som ska strömmas till en förbrukar produkt finns i [Azure SQL Database-mått och diagnostikloggning](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Konfigurera med Azure SQL Analytics

Azure SQL Analytics-lösningen tillhandahåller grafiska användargränssnitt, rapporterings- och varningsfunktioner för databasprestanda med hjälp av intelligenta insikters resursloggdata.

Lägg till Azure SQL Analytics på instrumentpanelen för Azure-portalen från marknadsplatsen och skapa en arbetsyta, se [konfigurera Azure SQL Analytics](../azure-monitor/insights/azure-sql.md#configuration)

Om du vill använda intelligenta insikter med Azure SQL Analytics konfigurerar du Intelligent Insights-loggdata som ska strömmas till Azure SQL Analytics-arbetsytan som du har skapat i föregående steg, se [Azure SQL Database-mått och diagnostikloggning](sql-database-metrics-diag-logging.md).

I följande exempel visas en intelligent statistik som visas via Azure SQL Analytics:

![Rapport om intelligenta insikter](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Konfigurera med eventhubbar

Om du vill använda intelligenta insikter med händelsehubbar konfigurerar du intelligenta insikters loggdata som ska strömmas till eventhubbar, läser [Azure SQL Database-mått och diagnostikloggar och](sql-database-metrics-diag-logging.md) och Strömma [Azure-diagnostikloggar till eventhubbar](../azure-monitor/platform/resource-logs-stream-event-hubs.md).

Information om hur du använder händelsehubbar för att konfigurera anpassad övervakning och avisering finns i [Vad du ska göra med mått och diagnostikloggar i eventhubbar](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Konfigurera med Azure Storage

Om du vill använda Intelligent Insights med Storage konfigurerar du Intelligent Insights-loggdata som ska strömmas till lagring, finns i [Azure SQL Database-mått och diagnostikloggning](sql-database-metrics-diag-logging.md) och [Strömma till Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Anpassade integreringar av loggen Intelligent Insights

Information om hur du använder Intelligent Insights med verktyg från tredje part eller för anpassad avisering och övervakning finns i [Använd loggarna för diagnostik av intelligenta insikter i databasens prestanda](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Identifieringsmått

Mått som används för identifieringsmodeller som genererar intelligenta insikter baseras på övervakning:

- Varaktighet för fråga
- Begäran om timeout
- Överdriven väntetid
- Felade begäranden

Frågevaraktighet och timeout-begäranden används som primära modeller för att identifiera problem med databasarbetsbelastningsprestanda. De används eftersom de direkt mäter vad som händer med arbetsbelastningen. För att identifiera alla möjliga fall av försämrad arbetsbelastningsprestanda används överdriven väntetid och felaktiga begäranden som ytterligare modeller för att ange problem som påverkar arbetsbelastningens prestanda.

Systemet tar automatiskt hänsyn till ändringar av arbetsbelastningen och ändringar i antalet frågebegäranden som görs i databasen för att dynamiskt bestämma normala och out-of-the-ordinary databasprestandatröskelvärden.

Alla mått beaktas tillsammans i olika relationer via en vetenskapligt härledd datamodell som kategoriserar varje prestandaproblem som upptäcks. Information som tillhandahålls genom en intelligent insikt omfattar:

- Information om prestandaproblemet har upptäckts.
- En grundorsaksanalys av problemet har identifierats.
- Rekommendationer om hur du kan förbättra prestanda för den övervakade SQL-databasen, där det är möjligt.

## <a name="query-duration"></a>Varaktighet för fråga

Frågevaraktighetsförsämringsmodellen analyserar enskilda frågor och identifierar ökningen av den tid det tar att kompilera och köra en fråga jämfört med prestandabaslinjen.

Om inbyggd information i SQL Database identifierar en betydande ökning av frågekompilering eller körningstid för frågor som påverkar arbetsbelastningens prestanda, flaggas dessa frågor som problem med prestandaförsämring av frågevaraktighet.

Intelligent Insights diagnostikloggen matar ut frågehh för frågan som försämrats i prestanda. Frågehöst anger om prestandaförsämringen var relaterad till frågekompilering eller körningstidsökning, vilket ökade frågevaraktighetstiden.

## <a name="timeout-requests"></a>Begäran om timeout

Timeout-begäranden för nedbrytningsmodellen analyserar enskilda frågor och identifierar eventuella ökningar av timeout på frågekörningsnivån och de övergripande tidsutgångarna för begäran på databasnivå jämfört med körningsperioden för prestanda.

Vissa av frågorna kan time out redan innan de når körningsfasen. Med hjälp av avbrutna arbetare kontra begäranden som gjorts mäter SQL Database inbyggda intelligensåtgärder och analyserar alla frågor som nådde databasen oavsett om de kom till körningsfasen eller inte.

När antalet timeout för utförda frågor eller antalet avbrutna begärandearbetare korsar det systemhanterade tröskelvärdet fylls en diagnostiklogg med intelligenta insikter.

De insikter som genereras innehåller antalet time-out-begäranden och antalet timed-out-frågor. Indikationen för prestandaförsämring är relaterad till timeout-ökning i körningsfasen eller så tillhandahålls den övergripande databasnivån. När ökningen av timeout bedöms vara betydande för databasens prestanda flaggas dessa frågor som problem med försämring av tidsgränsen för prestanda.

## <a name="excessive-wait-times"></a>Alltför långa väntetider

Den överdrivna väntetidsmodellen övervakar enskilda databasfrågor. Den identifierar ovanligt hög frågeväntestatistik som passerade de systemhanterade absoluta tröskelvärdena. Följande frågemått för överdriven väntetid observeras med hjälp av den nya SQL Server-funktionen Query Store Wait Stats (sys.query_store_wait_stats):

- Nå resursgränser
- Nå begränsningar för elastiska poolresurser
- Alltför många arbets- eller sessionstrådar
- Överdriven databaslåsning
- Minnesbelastning
- Annan väntestatistik

Att nå resursgränser eller begränsningar för elastisk poolresurs anger att förbrukning av tillgängliga resurser för en prenumeration eller i den elastiska poolen passerade absoluta tröskelvärden. Dessa statistik anger försämrad arbetsbelastningsprestanda. Ett alltför stort antal arbets- eller sessionstrådar betecknar ett villkor där antalet arbetstrådar eller sessioner som initierats passerade absoluta tröskelvärden. Dessa statistik anger försämrad arbetsbelastningsprestanda.

Överdriven databaslåsning betecknar ett villkor där antalet lås i en databas har passerat absoluta tröskelvärden. Den här statistiken anger en försämring av arbetsbelastningens prestanda. Minnestryck är ett tillstånd där antalet trådar som begär minnesbidrag passerade ett absolut tröskelvärde. Den här statistiken anger en försämring av arbetsbelastningens prestanda.

Andra vänta statistik identifiering indikerar ett villkor där diverse mått mätt genom Query Store Wait Stats passerat en absolut tröskel. Dessa statistik anger försämrad arbetsbelastningsprestanda.

När alltför långa väntetider har upptäckts, beroende på tillgängliga data, har intelligenta insikters loggarutdata för de berörda och berörda frågor som försämrats i prestanda, information om de mått som gör att frågor väntar i körningen och uppmätt väntetid.

## <a name="errored-requests"></a>Felaktiga begäranden

Den felförsämda modellen för försämring övervakar enskilda frågor och identifierar en ökning av antalet frågor som felade jämfört med baslinjeperioden. Den här modellen övervakar också kritiska undantag som passerade absoluta tröskelvärden som hanteras av inbyggd information i SQL Database. Systemet tar automatiskt hänsyn till antalet frågebegäranden som görs till databasen och tar hänsyn till eventuella arbetsbelastningsändringar under den övervakade perioden.

När den uppmätta ökningen av felförfrågningar i förhållande till det totala antalet begäranden som görs anses vara betydande för arbetsbelastningens prestanda, flaggas berörda frågor som felaktiga begäranden om prestandaförsämringsproblem.

Loggen Intelligent Insights matar ut antalet felaktiga begäranden. Det anger om prestandaförsämringen var relaterad till en ökning av felbegäranden eller till att passera ett övervakat kritiskt undantagströskel och uppmätt tid för prestandaförsämringen.

Om något av de övervakade kritiska undantagen överskrider de absoluta tröskelvärden som hanteras av systemet genereras en intelligent insikt med kritisk undantagsinformation.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [övervakar SQL Database med hjälp av SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Lär dig hur du [felsöker prestandaproblem i SQL Database med Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
