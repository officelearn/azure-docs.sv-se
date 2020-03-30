---
title: Övervaka frågor
titleSuffix: Azure Cognitive Search
description: Övervaka frågemått för prestanda och dataflöde. Samla in och analysera frågesträngindata i diagnostikloggar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a3a313ef9cd74ba901f5a6a2d82a18e3c21145dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462537"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Övervaka frågebegäranden i Azure Cognitive Search

I den här artikeln beskrivs hur du mäter frågeprestanda och volym med hjälp av mått och diagnostikloggning. Det förklarar också hur man samlar in indatatermer som används i frågor - nödvändig information när du behöver bedöma nyttan och effektiviteten i din sökning corpus.

Historiska data som matas in i mått bevaras i 30 dagar. För längre kvarhållning, eller för att rapportera om driftdata och frågesträngar, se till att aktivera en [diagnostikinställning](search-monitor-logs.md) som anger ett lagringsalternativ för beständiga loggade händelser och mått.

Villkor som maximerar integriteten för datamätning inkluderar:

+ Använd en fakturerbar tjänst (en tjänst som skapats på antingen Basic- eller Standard-nivån). Den fria tjänsten delas av flera abonnenter, vilket introducerar en viss volatilitet som laster skift.

+ Använd en enda replik och partition, om möjligt, för att skapa en innesluten och isolerad miljö. Om du använder flera repliker beräknas frågemåtten i genomsnitt mellan flera noder, vilket kan sänka resultatprecisionen. På samma sätt innebär flera partitioner att data är uppdelade, med potentialen att vissa partitioner kan ha olika data om indexering också pågår. När du justerar frågeprestanda ger en enda nod och partition en stabilare miljö för testning.

> [!Tip]
> Med ytterligare kod på klientsidan och Application Insights kan du också samla in klickdata för djupare insikt i vad som lockar dina programanvändares intresse. Mer information finns i [Sök trafikanalys](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Frågevolym (QPS)

Volymen mäts som **sökfrågor per sekund** (QPS), ett inbyggt mått som kan rapporteras som ett medelvärde, antal, lägsta eller högsta värden för frågor som körs inom ett minutsfönster. Enminutsintervall (TimeGrain = "PT1M") för mått fastställs i systemet.

Det är vanligt att frågor körs i millisekunder, så endast frågor som mäter som sekunder visas i mått.

| Sammansättningstyp | Beskrivning |
|------------------|-------------|
| Medel | Genomsnittligt antal sekunder inom en minut under vilken körning av frågor inträffade.|
| Antal | Antalet mått som skickas ut till loggen inom ett minutsintervall. |
| Maximal | Det högsta antalet sökfrågor per sekund som registrerats under en minut. |
| Minimum | Det lägsta antalet sökfrågor per sekund som registrerats under en minut.  |
| Summa | Summan av alla frågor som körs inom minuten.  |

Inom en minut kan du till exempel ha ett mönster som detta: en sekund av hög belastning som är den maximala för SearchQueriesPerSecond, följt av 58 sekunder av genomsnittlig belastning, och slutligen en sekund med bara en fråga, vilket är det minsta.

Ett annat exempel: om en nod avger 100 mått, där värdet för varje mått är 40, är "Count" 100, "Summa" 4000, "Genomsnitt" 40 och "Max" är 40.

## <a name="query-performance"></a>Frågeprestanda

Hela tjänsten, frågeprestanda mäts som sökfördröjning (hur lång tid en fråga tar att slutföra) och begränsade frågor som har tagits bort till följd av resurskonkurrens.

### <a name="search-latency"></a>Sök svarstid

| Sammansättningstyp | Svarstid | 
|------------------|---------|
| Medel | Genomsnittlig frågevaraktighet i millisekunder. | 
| Antal | Antalet mått som skickas ut till loggen inom ett minutsintervall. |
| Maximal | Den längsta frågan i exemplet. | 
| Minimum | Kortaste löpande fråga i exemplet.  | 
| Totalt | Total körningstid för alla frågor i exemplet och körs inom intervallet (en minut).  |

Tänk på följande exempel **på svarstidermått:** 86 frågor har samplats med en genomsnittlig varaktighet på 23,26 millisekunder. Minst 0 anger att vissa frågor har tagits bort. Den längsta gående frågan tog 1000 millisekunder att slutföra. Total körningstid var 2 sekunder.

![Sammanläggning av svarstidsaggregeringar](./media/search-monitor-usage/metrics-latency.png "Sammanläggning av svarstidsaggregeringar")

### <a name="throttled-queries"></a>Begränsade frågor

Begränsade frågor refererar till frågor som tas bort i stället för process. I de flesta fall är begränsning en normal del av att köra tjänsten.  Det är inte nödvändigtvis ett tecken på att något är fel.

Begränsning sker när antalet begäranden som för närvarande bearbetas överskrider de tillgängliga resurserna. Du kan se en ökning av begränsade begäranden när en replik tas ur rotation eller under indexering. Både fråge- och indexeringsbegäranden hanteras av samma uppsättning resurser.

Tjänsten avgör om begäranden ska släppas baserat på resursförbrukning. Procentandelen resurser som förbrukas över minne, CPU och disk-IO beräknas i genomsnitt under en tidsperiod. Om den här procentsatsen överskrider ett tröskelvärde begränsas alla begäranden till indexet tills volymen av begäranden minskas. 

Beroende på din klient kan en begränsad begäran anges på följande sätt:

+ En tjänst returnerar ett fel "Du skickar för många begäranden. Försök igen senare.” 
+ En tjänst returnerar en 503-felkod som anger att tjänsten för närvarande inte är tillgänglig. 
+ Om du använder portalen (till exempel Sökutforskaren) tas frågan tyst och du måste klicka på Sök igen.

Om du vill bekräfta begränsade frågor använder du **måttet För begränsade sökfrågor.** Du kan utforska mått i portalen eller skapa ett aviseringsmått enligt beskrivningen i den här artikeln. För frågor som har tagits bort inom samplingsintervallet använder du *Summa* för att hämta procentandelen frågor som inte har körning.

| Sammansättningstyp | Begränsning |
|------------------|-----------|
| Medel | Procentandel av frågor som har tagits bort inom intervallet. |
| Antal | Antalet mått som skickas ut till loggen inom ett minutsintervall. |
| Maximal | Procentandel av frågor som har tagits bort inom intervallet.|
| Minimum | Procentandel av frågor som har tagits bort inom intervallet. |
| Totalt | Procentandel av frågor som har tagits bort inom intervallet. |

För **begränsade sökfrågor Procent**, minimum, maximum, genomsnitt och totalt har alla samma värde: procentandelen sökfrågor som begränsades, från det totala antalet sökfrågor under en minut.

I följande skärmbild är det första numret antalet (eller antalet mått som skickas till loggen). Ytterligare aggregeringar, som visas överst eller när de hovrar över måttet, inkluderar medelvärde, maximum och total. I det här exemplet togs inga begäranden bort.

![Begränsade aggregeringar](./media/search-monitor-usage/metrics-throttle.png "Begränsade aggregeringar")

## <a name="explore-metrics-in-the-portal"></a>Utforska mätvärden i portalen

För en snabb titt på de aktuella talen visar fliken **Övervakning** på sidan Översikt över tjänsten tre mått (**sökfördröjning**, **Sökfrågor per sekund (per sökenhet)**, **Begränsat sökfrågor procent**) över fasta intervall mätt i timmar, dagar och veckor, med möjlighet att ändra aggregeringstypen.

För djupare utforskning öppnar du utforskare av datametri från **övervakningsmenyn** så att du kan lagra, zooma in och visualisera data för att utforska trender eller avvikelser. Läs mer om statistik explorer genom att fylla i den här [självstudien om att skapa ett måttdiagram](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer).

1. Under avsnittet Övervakning väljer du **Mått** för att öppna statistikutforskaren med scopet inställt på söktjänsten.

1. Under Mått väljer du en i listrutan och granskar listan över tillgängliga aggregeringar för en önskad typ. Aggregeringen definierar hur de insamlade värdena ska provtas under varje tidsintervall.

   ![Statistikutforskare för QPS-mått](./media/search-monitor-usage/metrics-explorer-qps.png "Statistikutforskare för QPS-mått")

1. I det övre högra hörnet ställer du in tidsintervallet.

1. Välj en visualisering. Standard är ett linjediagram.

1. Lager ytterligare aggregeringar genom att välja **Lägg till mått** och välja olika aggregeringar.

1. Zooma in i ett område av intresse på linjediagrammet. Placera muspekaren i början av området, klicka och håll ned vänster musknapp, dra till andra sidan av området och släpp knappen. Diagrammet zoomar in på det tidsintervallet.

## <a name="identify-strings-used-in-queries"></a>Identifiera strängar som används i frågor

När du aktiverar diagnostikloggning samlar systemet in frågebegäranden i tabellen **AzureDiagnostics.** Som en förutsättning måste du redan ha aktiverat [diagnostikloggning,](search-monitor-logs.md)ange en logganalysarbetsyta eller ett annat lagringsalternativ.

1. Under avsnittet Övervakning väljer du **Loggar** för att öppna ett tomt frågefönster i Logganalys.

1. Kör följande uttryck för att söka efter Query.Search-åtgärder, returnera en tabellresultatuppsättning som består av åtgärdsnamnet, frågesträngen, indexet som efterfrågas och antalet dokument som hittades. De två sista uttalandena utesluter frågesträngar som består av en tom eller ospecificerad sökning, över ett exempelindex, vilket minskar bruset i resultaten.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Du kan också ange ett kolumnfilter på *Query_s* för att söka över en viss syntax eller sträng. Du kan till exempel filtrera över *lika med* `?api-version=2019-05-06&search=*&%24filter=HotelName`).

   ![Loggade frågesträngar](./media/search-monitor-usage/log-query-strings.png "Loggade frågesträngar")

Även om den här tekniken fungerar för ad hoc-undersökning, kan du genom att skapa en rapport konsolidera och presentera frågesträngarna i en layout som är mer gynnsam för analys.

## <a name="identify-long-running-queries"></a>Identifiera tidskrävande frågor

Lägg till varaktighetskolumnen för att hämta siffrorna för alla frågor, inte bara de som plockas upp som ett mått. Om du sorterar dessa data visas vilka frågor som tar längst tid att slutföra.

1. Under avsnittet Övervakning väljer du **Loggar** för att fråga efter logginformation.

1. Kör följande fråga för att returnera frågor, sorterade efter varaktighet i millisekunder. De mest långvariga frågorna är högst upp.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Sortera frågor efter varaktighet](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Sortera frågor efter varaktighet")

## <a name="create-a-metric-alert"></a>Skapa en måttavisering

En måttavisering fastställer ett tröskelvärde där du antingen får ett meddelande eller utlöser en korrigerande åtgärd som du definierar i förväg. 

För en söktjänst är det vanligt att skapa en måttavisering för sökfördröjning och begränsade frågor. Om du vet när frågor tas bort kan du söka efter åtgärder som minskar belastningen eller ökar kapaciteten. Om begränsade frågor till exempel ökar under indexeringen kan du skjuta upp den tills frågeaktiviteten avtar.

När du tänjer på gränserna för en viss konfiguration av replikpartitioner är det också till hjälp att ställa in aviseringar för frågevolymtrösklar (QPS).

1. Under avsnittet Övervakning väljer du Aviseringar och klickar sedan på **+ Ny varningsregel**. **Alerts** Kontrollera att söktjänsten är markerad som resurs.

1. Klicka på **Lägg till**under Villkor.

1. Konfigurera signallogik. För signaltyp väljer du **mått** och väljer sedan signalen.

1. När du har valt signalen kan du använda ett diagram för att visualisera historiska data för ett välgrundat beslut om hur du ska gå vidare med att ställa in villkor.

1. Bläddra sedan ned till Varningslogik. För proof-of-concept kan du ange ett artificiellt lågt värde för testning.

   ![Varningslogik](./media/search-monitor-usage/alert-logic-qps.png "Varningslogik")

1. Ange eller skapa sedan en åtgärdsgrupp. Detta är svaret på att anropa när tröskelvärdet uppfylls. Det kan vara ett push-meddelande eller ett automatiserat svar.

1. Ange senast Aviseringsinformation. Namnge och beskriv aviseringen, tilldela ett allvarlighetsgrad och ange om regeln ska skapas i ett aktiverat eller inaktiverat tillstånd.

   ![Varningsinformation](./media/search-monitor-usage/alert-details.png "Aviseringsinformation")

Om du har angett ett e-postmeddelande får du ett e-postmeddelande från "Microsoft Azure" med `<your rule name>`ämnesraden "Azure: Aktiverad allvarlighetsgrad: 3".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det kan du läsa grunderna för övervakning av söktjänsten för att lära dig mer om hela utbudet av tillsynsfunktioner.

> [!div class="nextstepaction"]
> [Övervaka åtgärder och aktivitet i Azure Cognitive Search](search-monitor-usage.md)