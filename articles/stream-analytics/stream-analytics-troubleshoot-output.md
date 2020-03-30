---
title: Felsöka Azure Stream Analytics-utdata
description: I den här artikeln beskrivs tekniker för att felsöka dina utdataanslutningar i Azure Stream Analytics-jobb.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d40157523a074547885a14a3d92379f8e8b6f351
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254292"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Felsöka Azure Stream Analytics-utdata

På den här sidan beskrivs vanliga problem med utdataanslutningar och hur du felsöker och åtgärdar dem.

## <a name="output-not-produced-by-job"></a>Produktion som inte produceras per jobb
1.  Verifiera anslutningen till utdata med hjälp av knappen **Testa anslutning** för varje utdata.

2.  Titta på [**övervakningsmått**](stream-analytics-monitoring.md) på fliken **Övervakare.** Eftersom värdena aggregeras försenas måtten med några minuter.
    - Om indatahändelser > 0 kan jobbet läsa indata. Om indatahändelser inte är > 0:
      - Om du vill se om datakällan har giltiga data kontrollerar du den med hjälp av [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Den här kontrollen gäller om jobbet använder Event Hub som indata.
      - Kontrollera om dataserieringsformatet och datakodningen är som förväntat.
      - Om jobbet använder en händelsehubb kontrollerar du om meddelandets brödtext är *Null*.

    - Om datakonverteringsfel > 0 och klättring kan följande vara sant:
      - Utdatahändelsen överensstämmer inte med schemat för målmottagaren.
      - Händelseschemat kanske inte matchar det definierade eller förväntade schemat för händelserna i frågan.
      - Datatyperna för vissa fält i händelsen kanske inte matchar förväntningarna.

    - Om körningsfel > 0 innebär det att jobbet kan ta emot data men genererar fel när frågan bearbetas.
      - Om du vill hitta felen går du till [granskningsloggarna](../azure-resource-manager/management/view-activity-logs.md) och filtrerar efter *misslyckad* status.

    - Om InputEvents > 0 och OutputEvents = 0 betyder det att något av följande är sant:
      - Frågebearbetningen resulterade i noll utdata-händelser.
      - Händelser eller dess fält kan vara felformaterade, vilket resulterar i noll utdata efter frågebearbetning.
      - Jobbet kunde inte skicka data till utdatamottagaren av anslutnings- eller autentiseringsskäl.

    - I alla tidigare nämnda felfall förklarar verksamhetsloggmeddelanden ytterligare information (inklusive vad som händer), utom i de fall där frågelogiken filtrerade bort alla händelser. Om bearbetningen av flera händelser genererar fel loggar Stream Analytics de tre första felmeddelandena av samma typ inom 10 minuter till operationsloggar. Det undertrycker sedan ytterligare identiska fel med ett meddelande som lyder "Fel sker för snabbt, dessa undertrycks."

## <a name="job-output-is-delayed"></a>Jobbutdata är fördröjda

### <a name="first-output-is-delayed"></a>Första utgången försenas
När ett Stream Analytics-jobb startas läses indatahändelserna. I vissa situationer kan det dock uppstå en fördröjning i genereringen av utdata.

Stora tidsvärden i tidsmässiga frågeelement kan bidra till utdatafördröjningen. Om du vill producera rätt utdata under de stora tidsfönstren startar strömningsjobbet genom att läsa data från den senaste möjliga tiden (upp till sju dagar sedan) för att fylla tidsfönstret. Under den tiden produceras ingen utdata förrän catch-up-läsningen av de utestående indatahändelserna är klar. Det här problemet kan visas när systemet uppgraderar streamingjobben och därmed startar om jobbet. Sådana uppgraderingar sker i allmänhet en gång varannan månad.

Använd därför diskretion när du utformar din Stream Analytics-fråga. Om du använder ett stort tidsfönster (mer än flera timmar, upp till sju dagar) för temporala element i jobbets frågesyntax, kan det leda till en fördröjning på den första utdata när jobbet startas eller startas om.  

En begränsning för den här typen av första utdatafördröjning är att använda frågeparallaliseringstekniker (partitionering av data) eller lägga till fler strömningsenheter för att förbättra dataflödet tills jobbet hinner ikapp.  Mer information finns i [Överväganden när du skapar Stream Analytics-jobb](stream-analytics-concepts-checkpoint-replay.md)

Dessa faktorer påverkar aktualiteten för den första produktionen som genereras:

1. Användning av fönsterade aggregat (GROUP BY of Tumbling, Hopping och Sliding windows)
   - För tumlande eller hoppande fönsteraggregat genereras resultat i slutet av fönstrets tidsram.
   - För ett skjutfönster genereras resultatet när en händelse kommer in i eller avslutar skjutfönstret.
   - Om du planerar att använda stora fönsterstorlek (> 1 timme), är det bäst att välja hoppande eller skjutfönster så att du kan se utdata oftare.

2. Användning av temporala kopplingar (JOIN med DATEDIFF)
   - Matchningar genereras så snart båda sidor av de matchade händelserna anländer.
   - Data som saknar en matchning (LEFT OUTER JOIN) genereras i slutet av DATEIFF-fönstret med avseende på varje händelse på vänster sida.

3. Användning av temporala analytiska funktioner (ISFIRST, LAST och LAG MED LIMIT DURATION)
   - För analytiska funktioner genereras utdata för varje händelse, det finns ingen fördröjning.

### <a name="output-falls-behind"></a>Produktionen ligger efter
Under normal drift av jobbet, om du hittar jobbets produktion släpar efter (längre och längre latens), kan du identifiera de grundläggande orsakerna genom att undersöka dessa faktorer:
- Om sänket i nedströms begränsas
- Om uppströmskällan begränsas
- Om bearbetningslogiken i frågan är beräkningsintensiv

Om du vill visa dessa detaljer väljer du direktuppspelningsjobbet i Azure-portalen och väljer **jobbdiagrammet**. För varje indata finns det en händelsemått för per partitionseftersläpning. Om eftersläpningshändelsemåttet fortsätter att öka är det en indikator på att systemresurserna är begränsade. Potentiellt som beror på av utdatamottagare begränsning, eller hög CPU. Mer information om hur du använder jobbdiagrammet finns i [Datadriven felsökning med hjälp av jobbdiagrammet](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Varning för nyckelfel med Azure SQL Database-utdata

När du konfigurerar Azure SQL-databas som utdata till ett Stream Analytics-jobb infogar den poster i måltabellen. I allmänhet garanterar Azure stream analytics [minst en gång leverans](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) till utdatamottagaren, man kan fortfarande uppnå exakt en gång leverans till SQL-utdata när [SQL-tabellen]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) har en unik begränsning definierad.

När unika nyckelbegränsningar har ställts in i SQL-tabellen och det finns dubblettposter som infogas i SQL-tabellen, tar Azure Stream Analytics bort dubblettposten. Data delas upp i batchar och infogar batcharna rekursivt tills en enda dubblettpost hittas. Om direktuppspelningsjobbet har ett stort antal dubblettrader måste den här delnings- och infogningsprocessen ignorera dubbletterna en efter en, vilket är mindre effektivt och tidskrävande. Om du ser flera varningsmeddelanden för nyckelfel i aktivitetsloggen under den senaste timmen är det troligt att din SQL-utdata saktar ned hela jobbet.

För att lösa problemet bör du [konfigurera indexet]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) som orsakar nyckelfelet genom att aktivera alternativet IGNORE_DUP_KEY. Om du aktiverar det här alternativet kan dubblettvärden ignoreras av SQL under massinfogningar och SQL Azure skapar helt enkelt ett varningsmeddelande i stället för ett fel. Azure Stream Analytics ger inte längre fel i primärnyckelfel.

Observera följande observationer när du konfigurerar IGNORE_DUP_KEY för flera typer av index:

* Du kan inte ange IGNORE_DUP_KEY på en primärnyckel eller ett unikt villkor som använder ALTER INDEX, måste du släppa och återskapa indexet.  
* Du kan ange alternativet IGNORE_DUP_KEY med ALTER INDEX för ett unikt index, som skiljer sig från primärnyckel/unik begränsning och skapas med HJÄLP AV CREATE INDEX- eller INDEX-definition.  
* IGNORE_DUP_KEY gäller inte för kolumnlagringsindex eftersom du inte kan framtvinga unikhet för sådana index.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Kolumnnamn är gemener av Azure Stream Analytics
När du använder den ursprungliga kompatibilitetsnivån (1.0) används Azure Stream Analytics för att ändra kolumnnamn till gemener. Detta åtgärdades i senare kompatibilitetsnivåer. För att bevara ärendet rekommenderar vi kunderna att flytta till kompatibilitetsnivå 1.1 och senare. Du hittar mer information på [kompatibilitetsnivå för Azure Stream Analytics-jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).


## <a name="get-help"></a>Få hjälp

Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
