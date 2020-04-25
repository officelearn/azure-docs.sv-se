---
title: Felsöka Azure Stream Analytics utdata
description: Den här artikeln beskriver tekniker för att felsöka dina utgående anslutningar i Azure Stream Analytics jobb.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 5652df0cf142af2ff96590368892530abcb3d667
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133218"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Felsöka Azure Stream Analytics utdata

I den här artikeln beskrivs vanliga problem med Azure Stream Analytics utgående anslutningar, hur du felsöker problem med utdata och hur du korrigerar problemen. Många fel söknings steg kräver att resursen och andra diagnostikloggar är aktiverade för ditt Stream Analytics-jobb. Om du inte har aktiverat resurs loggar kan du läsa [felsöka Azure Stream Analytics med hjälp av resurs loggar](stream-analytics-job-diagnostic-logs.md).

## <a name="output-not-produced-by-job"></a>Utdata som inte produceras av jobb

1.  Kontrol lera anslutningen till utdata med knappen **Testa anslutning** för varje utdata.

2.  Titta på [**övervaknings mått**](stream-analytics-monitoring.md) på fliken **övervaka** . Eftersom värdena aggregeras fördröjs måtten med några minuter.
   * Om indata-händelser är större än 0 kan jobbet läsa indata. Om inmatade händelser inte är större än 0, är det ett problem med jobbets indatatyper. Mer information om hur du felsöker problem med anslutnings anslutningar finns i [Felsöka ingångs anslutningar](stream-analytics-troubleshoot-input.md) .
   * Om data konverterings fel är större än 0 och stiger kan du läsa mer i [Azure Stream Analytics data fel](data-errors.md) för detaljerad information om data konverterings fel.
   * Om körnings fel är större än 0 kan jobbet ta emot data, men det genererar fel när frågan bearbetas. Du hittar felen genom att gå till [gransknings loggarna](../azure-resource-manager/management/view-activity-logs.md) och filtrera efter status för *misslyckad* status.
   * Om InputEvents är större än 0 och OutputEvents är lika med 0, är något av följande sant:
      * Frågebearbetningen resulterade i noll utdata-händelser.
      * Händelser eller fält kan vara felaktiga, vilket resulterar i noll utdata efter frågans bearbetning.
      * Jobbet kunde inte skicka data till mottagar mottagare för anslutning eller autentiserings skäl.

   I alla tidigare nämnda fel förklarar Operations logg meddelanden ytterligare information (inklusive vad som händer), förutom i de fall där fråge logiken filtrerade ut alla händelser. Om bearbetningen av flera händelser genererar fel sammanställs felen var tionde minut.

## <a name="job-output-is-delayed"></a>Jobbutdata är fördröjda

### <a name="first-output-is-delayed"></a>Första utdata försenas

När ett Stream Analytics-jobb startas läses indatahändelserna. I vissa situationer kan det dock uppstå en fördröjning i genereringen av utdata.

Stora tids värden i temporala frågeuttryck kan bidra till utdata-fördröjningen. För att skapa korrekta utdata under den stora tiden, startar direkt uppspelnings jobbet genom att läsa data från den senaste tids perioden (upp till sju dagar sedan) för att fylla tids perioden. Under den tiden skapas inga utdata förrän fångsten av de utestående indata-händelserna är klar. Det här problemet kan vara en yta när systemet uppgraderar strömmande jobb, vilket innebär att jobbet startas om. Sådana uppgraderingar inträffar vanligt vis en gång i taget av några månader.

Använd därför noga när du utformar din Stream Analytics fråga. Om du använder ett stort tids fönster (mer än flera timmar, upp till sju dagar) för temporala element i jobbets frågesyntax, kan det leda till en fördröjning för det första resultatet när jobbet startas eller startas om.  

En minskning av den här typen av första fördröjning är att använda Query parallellisering-tekniker (partitionering av data) eller lägga till fler enheter för strömning för att förbättra data flödet tills jobbet har fångats upp.  Mer information finns i [överväganden när du skapar Stream Analytics-jobb](stream-analytics-concepts-checkpoint-replay.md)

Dessa faktorer påverkar tids linjerna för de första utdata som genereras:

1. Användning av agg regeringar (grupper av rullande, hoppande och glidande fönster)
   - I rullande-eller hoppande-fönstret skapas resultat i slutet av fönster tids ramen.
   - För ett glidande fönster genereras resultaten när en händelse går in eller avslutar glidnings fönstret.
   - Om du planerar att använda stor fönster storlek (> 1 timme), är det bäst att välja hoppande eller glidande fönster så att du kan se utdata oftare.

2. Användning av temporala kopplingar (förena med DATEDIFF)
   - Matchningar genereras så fort båda sidor av de matchade händelserna kommer.
   - Data som saknar matchning (vänster yttre koppling) skapas i slutet av DATEDIFF-fönstret med avseende på varje händelse på vänster sida.

3. Användning av temporala analys funktioner (ISFIRST, LAST och fördröjning med gräns längd)
   - För analys funktioner genereras utdata för varje händelse, ingen fördröjning.

### <a name="output-falls-behind"></a>Utdata sjunker efter

Om du hittar jobbets utdata under normal drift av jobbet under (längre och längre latens), kan du hitta rotor saken genom att undersöka följande faktorer:
- Anger om den underordnade sinken är begränsad
- Anger om den överordnade källan är begränsad
- Om bearbetnings logiken i frågan är beräknings intensiv

Om du vill se dessa uppgifter går du till Azure Portal, väljer strömnings jobbet och väljer **jobb diagrammet**. För varje inläsning sker ett händelse mått per partition efter släpning. Om händelse måttet efter släpning fortsätter att öka, är det en indikator att system resurserna är begränsade. Detta kan bero på en begränsning av utgående mottagare eller hög CPU. Mer information om hur du använder jobb diagrammet finns i [data driven fel sökning med hjälp av jobb diagrammet](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Varning om nyckel fel med Azure SQL Database utdata

När du konfigurerar Azure SQL Database som utdata till ett Stream Analytics jobb, infogar den Mass poster i mål tabellen. I allmänhet garanterar Azure Stream Analytics [minst en gång](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) till Utkorgen, men en kan fortfarande [få en exakt leverans]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) till SQL-utdata när SQL-tabellen har ett unikt definierat villkor.

När unika nyckel begränsningar har kon figurer ATS i SQL-tabellen och det finns dubbla poster som infogas i SQL-tabellen, Azure Stream Analytics tar bort dubblettposten. Den delar in data i batchar och infogar dem rekursivt tills en enskild dubblettpost hittas. Om direkt uppspelnings jobbet har ett stort antal dubblettrader, måste den här delnings-och infognings processen ignorera de dubbletter en i taget, vilket är mindre effektivt och tids krävande. Om du ser varnings meddelanden om flera nyckel överträdelser i din aktivitets logg inom den senaste timmen, är det troligt att SQL-utdata är långsammare för hela jobbet.

Lös problemet genom att [Konfigurera indexet]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) som orsakar nyckel överträdelsen genom att aktivera alternativet IGNORE_DUP_KEY. Om du aktiverar det här alternativet kan dubblettvärden ignoreras av SQL under Mass infogningar och SQL Azure bara skapar ett varnings meddelande i stället för ett fel. Azure Stream Analytics genererar inte längre fel i den primära nyckeln.

Observera följande observationer när du konfigurerar IGNORE_DUP_KEY för flera typer av index:

* Du kan inte ange IGNORE_DUP_KEY på en primär nyckel eller en unik begränsning som använder ALTER INDEX. du måste släppa och återskapa indexet.  
* Du kan ställa in IGNORE_DUP_KEY alternativet med hjälp av ALTER INDEX för ett unikt index, som skiljer sig från primär nyckel/unik begränsning och som skapats med CREATE INDEX eller INDEX definition.  

* IGNORE_DUP_KEY gäller inte för kolumn lagrings index eftersom du inte kan genomdriva unika index.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Kolumn namn är lägre – bokstäver genom Azure Stream Analytics
När du använder den ursprungliga kompatibilitetsnivån (1,0), Azure Stream Analytics används för att ändra kolumn namn till gemener. Detta beteende har åtgärd ATS i senare kompatibilitetsnivå. För att kunna behålla fallet rekommenderar vi att kunderna flyttar till kompatibilitetsnivån 1,1 och senare. Du hittar mer information om [kompatibilitetsnivå för Azure Stream Analytics jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).

## <a name="get-help"></a>Få hjälp

Om du behöver ytterligare hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
