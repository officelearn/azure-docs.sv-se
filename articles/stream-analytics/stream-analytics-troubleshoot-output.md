---
title: Felsöka Azure Stream Analytics utdata
description: Den här artikeln beskriver tekniker för att felsöka dina utgående anslutningar i Azure Stream Analytics jobb.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d40157523a074547885a14a3d92379f8e8b6f351
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364585"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Felsöka Azure Stream Analytics utdata

Den här sidan beskriver vanliga problem med utgående anslutningar och hur du felsöker och löser dem.

## <a name="output-not-produced-by-job"></a>Utdata som inte produceras av jobb
1.  Kontrol lera anslutningen till utdata med knappen **Testa anslutning** för varje utdata.

2.  Titta på [**övervaknings mått**](stream-analytics-monitoring.md) på fliken **övervaka** . Eftersom värdena aggregeras fördröjs måtten med några minuter.
    - Om indata-händelser > 0 kan jobbet läsa indata. Om inmatade händelser inte > 0, så:
      - Om du vill se om data källan har giltiga data kontrollerar du den med hjälp av [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Den här kontrollen gäller om jobbet använder Händelsehubben som inmatat.
      - Kontrol lera om data Serialiseringens format och data kodning är som förväntat.
      - Om jobbet använder en Händelsehubben kontrollerar du om meddelandets brödtext är *Null*.

    - Om data konverterings fel > 0 och stiger kan följande vara sant:
      - Händelsen utdata stämmer inte överens med schemat för mål mottagaren.
      - Händelse schemat kanske inte matchar det definierade eller förväntade schemat för händelserna i frågan.
      - Data typerna för vissa fält i händelsen kanske inte matchar förväntningarna.

    - Om körnings fel > 0 innebär det att jobbet kan ta emot data men genererar fel när frågan bearbetas.
      - Du hittar felen genom att gå till [gransknings loggarna](../azure-resource-manager/management/view-activity-logs.md) och filtrera efter status för *misslyckad* status.

    - Om InputEvents > 0 och OutputEvents = 0, innebär det att något av följande är sant:
      - Frågebearbetningen resulterade i noll utdata-händelser.
      - Händelser eller dess fält kan vara felaktiga, vilket resulterar i noll utdata efter frågans bearbetning.
      - Jobbet kunde inte skicka data till mottagar mottagare för anslutning eller autentiserings skäl.

    - I alla tidigare nämnda fel förklarar Operations logg meddelanden ytterligare information (inklusive vad som händer), förutom i de fall där fråge logiken filtrerade ut alla händelser. Om bearbetningen av flera händelser genererar fel loggar Stream Analytics de första tre fel meddelandena av samma typ inom 10 minuter till Operations-loggar. Sedan utelämnas ytterligare identiska fel med ett meddelande som läser "fel sker för snabbt, dessa ignoreras."

## <a name="job-output-is-delayed"></a>Jobbets utdata är fördröjt

### <a name="first-output-is-delayed"></a>Första utdata är försenad
När ett Stream Analytics-jobb startas läses indatahändelserna. I vissa situationer kan det dock uppstå en fördröjning i genereringen av utdata.

Stora tid-värdena i den temporala fråga element kan bidra till utdata-fördröjning. Det direktuppspelade jobbet startar genom att läsa data från det senaste gången möjligt (upp till sju dagar sedan) så att den fyller tidsfönstret för att skapa rätt utdata över stora tidsfönster. Under denna tid har skapas inga utdata tills catch-up läsningen av utestående inkommande händelser har slutförts. Det här problemet kan ge när systemet uppgraderas direktuppspelningsjobb, så starta om jobbet. Sådana uppgraderingar inträffar vanligen en gång för varje några månader.

Därför var försiktig när du utformar din Stream Analytics-fråga. Om du använder en lång tidsperiod (mer än timmarna, upp till sju dagar) för den temporala element i frågesyntax för jobbets kan leda till en fördröjning på första utdata när jobbet startas eller startas om.  

En lösning för den här typen av den första utdata fördröjningen är att använda parallellisering frågetekniker (partitionering av data) eller lägga till fler enheter för strömning för att förbättra dataflödet tills jobbet kommer det ikapp.  Mer information finns i [överväganden när du skapar Stream Analytics-jobb](stream-analytics-concepts-checkpoint-replay.md)

Dessa faktorer som påverkar skyndsamhet på första utdata som genereras:

1. Användning av fönsteraggregeringar (grupp av av rullande, hoppar och glidande windows)
   - Rullande eller hoppar fönstret aggregeringar kan genereras resultat i slutet av fönstret tidsramen.
   - För ett skjutfönster genereras resultaten när en händelse anländer till eller lämnar Hoppande fönster.
   - Om du planerar att använda stora fönsterstorlek (> 1 timme), är det bäst att välja Hoppande eller glidande så att du kan se utdata oftare.

2. Användning av temporala kopplingar (koppla ihop med DATEDIFF)
   - Matchar genereras när när båda sidor av matchande händelser anländer.
   - Data som saknar en matchning (vänster yttre koppling) skapas i slutet av fönstret DATEDIFF med avseende på varje händelse till vänster.

3. Användning av temporala analysfunktioner (ISFIRST-, LAST och FÖRDRÖJNING med LIMIT DURATION)
   - För analysfunktioner, utdata genereras för varje händelse finns det ingen fördröjning.

### <a name="output-falls-behind"></a>Utdata som ligger
Under normal drift av jobbet om du hittar jobbets utdata faller bakom (längre och längre svarstid), kan du hitta den bakomliggande orsaken genom att undersöka dessa faktorer:
- Om underordnade mottagare är begränsad
- Om den överordnade källan är begränsad
- Om standardbearbetningslogiken i frågan är beräkningsintensiva

Om du vill se dessa uppgifter går du till Azure Portal, väljer strömnings jobbet och väljer **jobb diagrammet**. För varje indata, finns en per partition eftersläpning händelse mått. Om eftersläpning för händelsen mått fortsätter att öka, är det indikerar att systemresurserna är begränsade. Potentiellt som är på grund av att utdata mottagare begränsning eller hög CPU. Mer information om hur du använder jobb diagrammet finns i [data driven fel sökning med hjälp av jobb diagrammet](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Varning om nyckel fel med Azure SQL Database utdata

När du konfigurerar Azure SQL-databas som utdata för ett Stream Analytics-jobb infogas har masskapat poster i måltabellen. I allmänhet garanterar Azure Stream Analytics [minst en gång](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) till Utkorgen, men en kan fortfarande [få en exakt leverans]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) till SQL-utdata när SQL-tabellen har ett unikt definierat villkor.

När unika nyckelvillkor ställs in på den SQL-tabellen och det finns dubbla poster som infogas i SQL-tabell, Azure Stream Analytics tar du bort dubblettposten. Den delas data i batchar och rekursivt infoga i batchar tills en enda kopia av posten hittas. Om det direktuppspelade jobbet har ett stort antal dubblettrader, den här dela och infoga processen har att ignorera dubbletter en i taget, vilket är mindre effektivt och tidskrävande. Om du ser flera felet varningsmeddelanden i din aktivitetslogg inom den senaste timmen, är det troligt att dina SQL-utdata är långsammare hela projektet.

Lös problemet genom att [Konfigurera indexet]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) som orsakar nyckel överträdelsen genom att aktivera alternativet IGNORE_DUP_KEY. Om du aktiverar det här alternativet kan dubblettvärden ignoreras av SQL under bulkinfogningar och SQL Azure genererar bara ett varningsmeddelande i stället för ett fel. Azure Stream Analytics producerar inte primära felet fel längre.

Observera följande observationer när du konfigurerar IGNORE_DUP_KEY för flera typer av index:

* Du kan inte ange IGNORE_DUP_KEY på en primär nyckel eller en unik begränsning som använder ALTER INDEX måste du ta bort och återskapa indexet.  
* Du kan ange alternativet IGNORE_DUP_KEY med hjälp av ALTER INDEX för ett unikt index som skiljer sig från primär nyckel/UNIQUE-begränsning och skapas med CREATE INDEX eller INDEX.  
* Indexalternativet gäller inte för columnstore-index eftersom du inte kan framtvinga unikhet på sådana index.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Kolumn namn är lägre – bokstäver genom Azure Stream Analytics
När du använder den ursprungliga kompatibilitetsnivån (1,0), Azure Stream Analytics används för att ändra kolumn namn till gemener. Detta beteende har åtgärd ATS i senare kompatibilitetsnivå. För att kunna behålla fallet rekommenderar vi att kunderna flyttar till kompatibilitetsnivån 1,1 och senare. Du hittar mer information om [kompatibilitetsnivå för Azure Stream Analytics jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).


## <a name="get-help"></a>Få hjälp

Om du behöver ytterligare hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
