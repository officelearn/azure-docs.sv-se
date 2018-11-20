---
title: Övervaka databasprestanda med Intelligent Insights – Azure SQL Database | Microsoft Docs
description: Azure SQL Database Intelligent Insights använder inbyggd intelligens för att kontinuerligt övervaka databasanvändningen via artificiell intelligens och identifiera störande händelser som kan försämra prestandan.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: e8608782c71f8afc2ae32b16077cb26b5dcc6849
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976196"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>Intelligent Insights med hjälp av AI att övervaka och felsöka databasen prestanda

Azure SQL Database Intelligent Insights får du reda på vad som händer med din SQL-databas och Managed Instance databasprestanda.

Intelligent Insights använder inbyggd intelligens för att kontinuerligt övervaka databasanvändningen via artificiell intelligens och identifiera störande händelser som kan försämra prestandan. När upptäckte, utförs en detaljerad analys som genererar en diagnostiklogg med en intelligent bedömning av problemet. Den här utvärderingen består av en Rotorsaksanalys av prestandaproblem för databasen och, om möjligt, rekommendationer för att öka prestanda.

## <a name="what-can-intelligent-insights-do-for-you"></a>Vad gör smarta insikter för dig

Intelligent Insights är en unik funktion i Azure inbyggd intelligens som ger följande fördelar:

- Proaktiv övervakning
- Skräddarsydda prestandainsikter
- Tidig upptäckt av databasen prestandaförsämring
- Analys av problem som identifieras av Rotorsak
- Rekommendationer om prestandaförbättring prestanda
- Skala ut-funktionen på hundratusentals databaser
- Positiv påverkan på DevOps-resurser och den totala ägandekostnaden

## <a name="how-does-intelligent-insights-work"></a>Hur fungerar Intelligent Insights

Smarta insikter analyserar databasens prestanda genom att jämföra databas-arbetsbelastning från den senaste timmen med de senaste sju dagar baslinje arbetsbelastningen. Databas-arbetsbelastning består av frågor för de viktigaste databasprestanda, till exempel de största och mest upprepade frågorna. Eftersom varje databas är unikt baserat på dess struktur, data, användning och program, är varje arbetsbelastning baslinje som genereras specifika och unik för en enskild instans. Smarta insikter, oberoende av baslinjen arbetsbelastning också övervakar absolut operativa tröskelvärden och identifierar problem med långa väntetider, kritiska undantag och problem med frågan parameterizations som kan påverka prestanda.

Om en försämring prestandaproblem har identifierats från flera observerade mått med hjälp av artificiell intelligens, utförs analysen. En diagnostiklogg skapas med en intelligent insight på vad som händer med din databas. Intelligent Insights gör det enkelt att spåra prestandaproblemet databasen från utseendet första fram till lösning. Alla upptäckta problem spåras under dess livscykel från första problemet identifiering och kontroll av prestandaförbättring den har slutförts.

![Databasen prestanda analysis arbetsflöde](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

De mått som används för att mäta och identifiera databasen prestandaproblem baseras på frågevaraktigheten, timeout för begäranden, långa väntetider och felaktiga begäranden. Mer information om mått finns i den [identifiering mått](sql-database-intelligent-insights.md#detection-metrics) i det här dokumentet.

Identifierat SQL Database prestandaförsämringar registreras i diagnostiklogg med smarta poster som består av följande egenskaper:

| Egenskap              | Information              |
| :------------------- | ------------------- |
| databasinformation | Metadata om en databas som en insikt har identifierats, till exempel en resurs-URI. |
| Observerad tidsintervall | Börja och sluta när perioden för de identifierade insikter. |
| Påverkat mått | Mått som orsakade insikter som ska genereras: <ul><li>Fråga varaktighet ökar [sekunder].</li><li>Långa väntetiden [sekunder].</li><li>Tidsgränsen nåddes för begäranden [procent].</li><li>Felaktiga ut begäranden [procent].</li></ul>|
| Påverkan värde | Värdet för ett mått beräknas. |
| Berörda frågor och felkoder | Fråga hash eller-felkod. Dessa kan användas för att enkelt stämmer med frågor som påverkas. Mått som består av fråga varaktighet ökning, väntetiden, antal tidsgräns eller felkoder finns. |
| Identifieringar | Identifiering av identifierades på databasen vid tidpunkten för en händelse. Det finns 15 identifiering av mönster. Mer information finns i [Felsöka prestandaproblem för databasen med intelligenta insikter](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Rotorsaksanalys | Rotorsak analys av de problem som identifieras i formatet läsbart för människor. Några insikter kan innehålla en rekommendation för förbättring av prestanda när det är möjligt. |
|||

Finns det inbäddade videoklippet för en praktisk översikt om hur du använder intelligenta insikter med Azure SQL Analytics och för vanliga Användningsscenarier:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights glänser identifiering och felsökning av problem med SQL Database-prestanda. För att kunna använda intelligenta insikter för att felsöka prestandaproblem för SQL-databas och hanterad instans-databasen, se [felsöka Azure SQL Database prestandaproblem med smarta insikter](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="configure-intelligent-insights"></a>Konfigurera smarta insikter

Utdata från Intelligent Insights är en diagnostiklogg med smarta prestanda. Den här loggfilen kan användas på flera olika sätt – via strömma det till Azure SQL Analytics, Azure Event Hubs och Azure storage eller en tredje part produkten.

- Använder produkten med [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) att visa insikter via användargränssnittet i Azure Portal. Detta är den inbyggda Azure-lösningen och det vanligaste sättet att visa insikter.
- Använd produkten med Händelsehubbar i Azure för utveckling av anpassade övervakning och avisering scenarier
- Använder produkten med Azure storage för utveckling av anpassade program, till exempel är till exempel anpassade rapportdata, långsiktig arkivering och så vidare.

Integrering av smarta insikter med andra produkter Azure SQL Analytics, Azure Event Hub, Azure storage eller produkter från tredje part för förbrukning utförs via första Aktivera smarta insikter loggning (”SQLInsights”-loggen) i diagnostiken inställningsbladet för en databas och sedan konfigurera smarta insikter logga data strömmas i någon av dessa produkter.

Mer information om hur du aktiverar loggning för intelligenta insikter och konfigurera loggdata strömmas till en konsumerande produkt finns i [Azure SQL Database-mått och diagnostikloggning](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Ställ in med Azure SQL Analytics

Azure SQL Analytics-lösningen innehåller grafiskt användargränssnitt, rapportering och aviseringsfunktioner på databasprestanda, tillsammans med smarta insikter diagnostiklogg för data.

> [!TIP]
> Komma igång snabbt: Det enklaste sättet att få från grunden med hjälp av intelligenta insikter är att använda den tillsammans med Azure SQL Analytics som tillhandahåller ett grafiskt användargränssnitt till prestandaproblem för databasen. Lägg till Azure SQL Analytics-lösningen från marketplace, skapa en arbetsyta i den här lösningen och sedan konfigurera strömning av ”SQLInsights” logga in diagnostik inställningsbladet för en databas till för varje databas som du vill aktivera smarta insikter på den Azure SQL Analytics-arbetsytan.
>

Före kravet är att ha Azure SQL Analytics läggs till instrumentpanelen i Azure portal från marketplace och för att skapa en arbetsyta i [konfigurera Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md#configuration)

Konfigurera smarta insikter loggdata strömmas till Azure SQL Analytics-arbetsytan som du har skapat i föregående steg, för att använda smarta insikter med Azure SQL Analytics, se [Azure SQL Database-mått och diagnostik loggning](sql-database-metrics-diag-logging.md).

I följande exempel visas ett Intelligent Insights visas via Azure SQL Analytics:

![Intelligent Insights-rapport](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Konfigurera med Event Hubs

För att använda smarta insikter med Event Hubs, konfigurera smarta insikter loggdata strömma till Event Hubs kan du läsa [Stream Azure-diagnostikloggar till Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).

Om du vill använda Event Hubs för att konfigurera anpassade övervakning och avisering, se [vad du gör med mått och diagnostik för loggar i Event Hubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Ställ in med Azure Storage

För att använda smarta insikter med lagring, konfigurera smarta insikter loggdata strömmas till Storage kan du läsa [Stream till Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Anpassad integrering av Intelligent Insights log

För att använda smarta insikter med verktyg från tredje part eller anpassade varningar och övervaka utveckling, se [Använd Prestandadiagnostik för Intelligent Insights databasen logga](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Identifiering av mått

Mått som används för identifiering av modeller som genererar smarta insikter baseras på övervakning:

- Frågevaraktighet
- Timeout för begäranden
- Långa väntetiden
- Felaktiga ut begäranden

Varaktighet och timeout frågebegäranden används som primära modeller för identifiering av problem med arbetsbelastningen databasprestanda. De används eftersom de direkt mäta vad som händer med arbetsbelastningen. För att identifiera alla eventuella fall av arbetsbelastningsprestandan försämring, långa Väntetid och felaktiga ut begäranden används som ytterligare modeller för att tyda på problem som påverkar arbetsbelastningens prestanda.

Systemet tar hänsyn till automatiskt ändringar i arbetsbelastningen och förändringar i antalet fråga begäranden som görs till databasen för att dynamiskt bestämma normala databaser med out extraordinära prestanda tröskelvärden.

Alla mått bedöms tillsammans i olika relationer via en vetenskapligt härledda datamodell som kategoriserar varje prestandaproblem som har identifierats. Information som tillhandahålls via en intelligent insight inkluderar:

- Information om prestandaproblemet har identifierats.
- En Rotorsaksanalys för det identifierade problemet.
- Rekommendationer om hur du förbättrar prestandan för övervakade SQL-databasen, där det är möjligt.

## <a name="query-duration"></a>Frågevaraktighet

Frågemodell för försämring av varaktigheten analyserar enskilda frågor och identifierar ökningen i den tid det tar att kompilera och köra en fråga som jämfört med baslinje för prestanda.

Om SQL Database inbyggd intelligens upptäcker en kraftig ökning i frågan kompilerades eller fråga körningstid som påverkar arbetsbelastningens prestanda, de här frågorna har flaggats som frågevaraktigheten prestandan skulle försämras.

Diagnostiklogg Intelligent Insights matar ut fråge-hash frågans försämrad prestanda. Fråge-hash anger om försämrade prestanda har relaterat till frågan kompilerades eller körningstid ökning, som ökade varaktigheten för frågan.

## <a name="timeout-requests"></a>Timeout för begäranden

Timeout för begäranden försämring modellen analyserar enskilda frågor och identifierar en ökning i tidsgränser på nivån för körning av fråga och den övergripande begäran timeout på databasnivå jämfört med baslinjeperioden prestanda.

Några av frågorna som kan timeout även innan de når fasen körning. Sätt avbrutna arbetstagarnas jämfört med begäranden som görs, SQL Database inbyggd intelligens mäter och analyserar alla frågor som nått databasen om de har fått till körning scenen eller inte.

När antalet tidsgränser för utförda frågor eller antal avbrutna begäran arbetare överskrider tröskelvärdet hanteras av datorn, fylls en diagnostiklogg med smarta insikter.

De information som genererats innehålla antalet begäranden som tog för lång tid och antalet frågor som orsakade timeout. Uppgift om försämrade prestanda är relaterad till timeout ökning i körningen skedet eller övergripande databasnivå har angetts. När ökade timeout anses betydande databasprestanda, har de här frågorna flaggats som timeout prestandan skulle försämras.

## <a name="excessive-wait-times"></a>Långa väntetider

Den långa väntetid tid modellen övervakar enskilda databasfrågor. Den identifierar ovanligt stort fråga väntestatistik som uppnås absolut tröskelvärdena hanteras av datorn. Följande fråga långa väntetiden mått jobbnivå genom att använda den nya SQL Server-funktionen Query Store vänta statistik (sys.query_store_wait_stats):

- Nådde resursbegränsningar
- Nådde resursgränser för elastisk pool
- Överdrivet antal worker eller session trådar
- Långa databasen låsning
- Minnesbelastning
- Andra väntestatistik

Nådde resursbegränsningar eller elastisk pool resursbegränsningar anger att användningen av tillgängliga resurser på en prenumeration eller i den elastiska poolen överskred absolut tröskelvärden. Dessa statistik visar arbetsbelastning prestandaförsämring. Ett orimligt antal worker eller session trådar Anger ett villkor där antalet arbetstrådar eller sessioner som initierats överskred absolut tröskelvärden. Dessa statistik visar arbetsbelastning prestandaförsämring.

Långa databasen låsning anger ett villkor där antalet lås på en databas har passerat absolut tröskelvärden. Den här stat anger en arbetsbelastning prestandaförsämring. Minne trycket är ett villkor där antalet trådar som begär minne ger överskred en absolut tröskelvärdet. Den här stat anger en arbetsbelastning prestandaförsämring.

Andra vänta stats identifiering visar ett tillstånd där diverse mått som mäts via Query Store vänta statistiken överskred en absolut tröskelvärdet. Dessa statistik visar arbetsbelastning prestandaförsämring.

När långa väntetider identifieras, beroende på data som är tillgängliga, smarta insikter diagnostiken loggar du in utdata hash-värden för de som påverkar och berörda frågor försämrad prestanda, information om mått som orsakar frågor för att vänta i körningen och uppmätt väntetid.

## <a name="errored-requests"></a>Felaktiga begäranden

Felaktiga begäranden försämring modell Övervakare person frågar och identifierar en ökning i antalet frågor att felaktiga ut jämfört med baslinjeperioden. Den här modellen övervakar även kritiska undantag som uppnås absolut tröskelvärden som hanteras av SQL Database inbyggd intelligens. Antal frågebegäranden som gjorts i databasen och konton för ändringar arbetsbelastning i den övervakade perioden är automatiskt.

När den uppmätta ökade felaktiga begäranden i förhållande till det totala antalet begäranden som görs anses vara relevanta för att arbetsbelastningens prestanda, är frågor som påverkas flaggade som felaktiga begäranden prestandan skulle försämras.

Intelligent Insights-loggen visar antal felaktiga begäranden. Indikerar om försämrade prestanda har relaterat till en ökning av felaktiga begäranden eller korsa en övervakade kritiskt undantag tröskelvärde och uppmätt tid för försämrade prestanda.

Om någon av de övervakade kritiska undantag mellan de absoluta tröskelvärden som hanteras av systemet genereras intelligenta insikter med information om kritiska undantag.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [Felsöka prestandaproblem för SQL-databas med intelligenta insikter](sql-database-intelligent-insights-troubleshoot-performance.md).
- Använd den [diagnostikloggen för Intelligent Insights SQL Database prestanda](sql-database-intelligent-insights-use-diagnostics-log.md).
- Lär dig hur du [övervaka SQL-databas med hjälp av SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
- Lär dig hur du [samla in och använda loggdata från resurserna i Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
