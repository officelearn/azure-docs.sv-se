---
title: Metodtips för att använda Power BI för att fråga och visualisera Azure Data Explorer-data
description: I den här artikeln får du lära dig metodtips för att använda Power BI för att fråga och visualisera Azure Data Explorer-data.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251744"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Metodtips för att använda Power BI för att fråga och visualisera Azure Data Explorer-data

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. [Power BI](https://docs.microsoft.com/power-bi/) är en affärsanalyslösning som gör att du kan visualisera dina data och dela resultaten i hela organisationen. Azure Data Explorer innehåller tre alternativ för att ansluta till data i Power BI. Använd den inbyggda kopplingen, [importera en fråga från Azure Data Explorer till Power BI](power-bi-imported-query.md)eller använd en [SQL-fråga](power-bi-connector.md). [SQL query](power-bi-sql-query.md) Den här artikeln ger dig tips om hur du frågar och visualiserar dina Azure Data Explorer-data med Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Metodtips för användning av Power BI 

När du arbetar med terabyte med färska rådata följer du dessa riktlinjer för att hålla Power BI-instrumentpaneler och rapporter snabba och uppdaterade:

* **Reselampa** – Ta bara med de data som du behöver för dina rapporter till Power BI. För djup interaktiv analys använder du [webbgränssnittet](web-query-data.md) i Azure Data Explorer som är optimerat för ad hoc-utforskning med Kusto Query Language.

* **Sammansatt modell** - Använd [sammansatt modell](https://docs.microsoft.com/power-bi/desktop-composite-models) för att kombinera aggregerade data för instrumentpaneler på högsta nivå med filtrerade driftrådata. Du kan tydligt definiera när du ska använda rådata och när du ska använda en aggregerad vy. 

* **Importläge jämfört med DirectQuery-läge** – Använd **importläge** för interaktion mellan mindre datauppsättningar. Använd **DirectQuery-läge** för stora, ofta uppdaterade datauppsättningar. Skapa till exempel dimensionstabeller med **importläget** eftersom de är små och inte ändras ofta. Ange uppdateringsintervallet enligt den förväntade datauppdateringshastigheten. Skapa faktatabeller med **DirectQuery-läge** eftersom dessa tabeller är stora och innehåller rådata. Använd dessa tabeller för att presentera filtrerade data med Power [BI-detaljgranskning](https://docs.microsoft.com/power-bi/desktop-drillthrough).

* **Parallellism** – Azure Data Explorer är en linjärt skalbar dataplattform, därför kan du förbättra instrumentpanelsrenderingens prestanda genom att öka parallellismen i det end-to-end-flödet enligt följande:

   * Öka antalet [samtidiga anslutningar i DirectQuery i Power BI](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Använd [svag konsekvens för att förbättra parallellismen](/azure/kusto/concepts/queryconsistency). Detta kan påverka uppgifternas färskhet.

* **Effektiva utsnitt** – Använd [synkroniseringsutsnitt](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) för att förhindra att rapporter läser in data innan du är redo. När du har strukturerat datauppsättningen, placerat alla visuella objekt och markera alla utsnitt kan du välja synkroniseringsutsnittet om du bara vill läsa in de data som behövs.

* **Använda filter** - Använd så många Power BI-filter som möjligt för att fokusera Azure Data Explorer-sökningen på relevanta datashards.

* **Effektiv grafik** – Välj de mest högpresterande visuella objekten för dina data.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Tips om hur du använder Azure Data Explorer-anslutningsappen för Power BI för att fråga data

Följande avsnitt innehåller tips och tricks för att använda Kusto-frågespråk med Power BI. Använda [Azure Data Explorer-anslutningen för Power BI för](power-bi-connector.md) att visualisera data

### <a name="complex-queries-in-power-bi"></a>Komplexa frågor i Power BI

Komplexa frågor uttrycks lättare i Kusto än i Power Query. De bör implementeras som [Kusto-funktioner](/azure/kusto/query/functions)och anropas i Power BI. Den här metoden krävs när `let` du använder **DirectQuery** med satser i din Kusto-fråga. Eftersom Power BI kopplar samman `let` två frågor och satser inte kan användas med operatorn `join` kan syntaxfel uppstå. Spara därför varje del av kopplingen som en Kusto-funktion och låt Power BI ansluta dessa två funktioner tillsammans.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Så här simulerar du en relativ date-time-operator

Power BI innehåller inte en *relativ* date-time operator som `ago()`.
Om du `ago()`vill simulera `DateTime.FixedLocalNow()` använder `#duration` du en kombination av och Power BI-funktioner.

I stället för `ago()` den här frågan med operatorn:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Använd följande motsvarande fråga:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Nå Kusto-frågegränser 

Kusto-frågor returnerar som standard upp till 500 000 rader eller 64 MB, enligt beskrivningen i [frågegränser](/azure/kusto/concepts/querylimits). Du kan åsidosätta dessa standardinställningar genom att använda **avancerade alternativ** i Anslutningsfönstret för Azure Data **Explorer (Kusto):**

![avancerade alternativ](media/power-bi-best-practices/advanced-options.png)

De här alternativen [utfärdar uppsättningsatser](/azure/kusto/query/setstatement) med frågan om du vill ändra standardfrågegränserna:

  * **Begränsa frågeresultatpostnummer** genererar en`set truncationmaxrecords`
  * **Begränsa frågeresultatdatastorleken i Bytes** genererar en`set truncationmaxsize`
  * **Inaktivera trunkering av resultatuppsättningar** genererar en`set notruncation`

### <a name="using-query-parameters"></a>Använda frågeparametrar

Du kan använda [frågeparametrar](/azure/kusto/query/queryparametersstatement) för att ändra frågan dynamiskt. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Använda en frågeparameter i anslutningsinformationen

Använd en frågeparameter för att filtrera information i frågan och optimera frågeprestanda.
 
I fönstret **Redigera frågor,** **Home** > **Advanced Editor**

1. Hitta följande avsnitt i frågan:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Ett exempel:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Ersätt den relevanta delen av frågan med parametern. Dela upp frågan i flera delar och sammanfoga tillbaka dem med ett et-tecken (&), tillsammans med parametern.

   I frågan ovan tar vi till exempel `State == 'ALABAMA'` del och delar `State == '` upp `'` den med: `State` och placerar parametern mellan dem:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Om frågan innehåller citattecken kodar du dem korrekt. Följande fråga: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   visas i **Avancerade redigeraren** på följande sätt med två citattecken:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Den bör ersättas med följande fråga med tre citattecken:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Använda en frågeparameter i frågestegen

Du kan använda en frågeparameter i alla frågesteg som stöder den. Filtrera till exempel resultaten baserat på värdet för en parameter.

![filtrera resultat med hjälp av en parameter](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Använd inte Power BI-datauppdateringsschemaläggare för att utfärda kontrollkommandon till Kusto

Power BI innehåller en datauppdateringsschemaläggare som regelbundet kan utfärda frågor mot en datakälla. Den här mekanismen bör inte användas för att schemalägga kontrollkommandon till Kusto eftersom Power BI förutsätter att alla frågor är skrivskyddade.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI kan bara&lt;skicka korta (2000 tecken) frågor till Kusto

Om det uppstår följande fel när du kör en fråga i Power BI: _"DataSource.Error: Web.Contents failed to get contents from..."_ är frågan förmodligen längre än 2000 tecken. Power BI använder **PowerQuery** för att fråga Kusto genom att utfärda en HTTP GET-begäran som kodar frågan som en del av URI:n som hämtas. Därför är Kusto-frågor som utfärdas av Power BI begränsade till den maximala längden på en URI för begäran (2000 tecken, minus liten förskjutning). Som en lösning kan du definiera en [lagrad funktion](/azure/kusto/query/schema-entities/stored-functions) i Kusto och låta Power BI använda den funktionen i frågan.

## <a name="next-steps"></a>Nästa steg

[Visualisera data med Azure Data Explorer-anslutningen för Power BI](power-bi-connector.md)




