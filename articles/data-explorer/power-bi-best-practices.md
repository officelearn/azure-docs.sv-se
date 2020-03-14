---
title: Metod tips för att använda Power BI för att fråga och visualisera Azure Datautforskaren-data
description: I den här artikeln lär du dig metod tips för att använda Power BI för att fråga och visualisera Azure Datautforskaren-data.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251744"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Metod tips för att använda Power BI för att fråga och visualisera Azure Datautforskaren-data

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. [Power BI](https://docs.microsoft.com/power-bi/) är en lösning för företags analys som gör att du kan visualisera dina data och dela resultaten i hela organisationen. Azure Datautforskaren tillhandahåller tre alternativ för att ansluta till data i Power BI. Använd den [inbyggda anslutningen](power-bi-connector.md), [Importera en fråga från Azure datautforskaren till Power BI](power-bi-imported-query.md)eller Använd en [SQL-fråga](power-bi-sql-query.md). I den här artikeln får du tips om hur du skickar frågor till och visualiserar dina Azure Datautforskaren-data med Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Metod tips för att använda Power BI 

När du arbetar med terabytes färska rå data följer du rikt linjerna för att hålla Power BI instrument paneler och rapporter fästa och uppdaterade:

* **Res belysning** – Hämta bara de data som du behöver för att rapporter ska Power BI. För djup interaktiva analyser använder du [Azure datautforskaren-WEBBgränssnittet](web-query-data.md) som är optimerat för ad hoc-utforskning med Kusto-frågespråket.

* **Sammansatt modell** – Använd [sammansatt modell](https://docs.microsoft.com/power-bi/desktop-composite-models) för att kombinera sammanställda data för instrument paneler på högsta nivå med filtrerade operativa rå data. Du kan tydligt definiera när du vill använda rå data och när du ska använda en aggregerad vy. 

* **Import läge kontra DirectQuery-läge** – Använd **import** läge för att interagera med mindre data uppsättningar. Använd **DirectQuery** -läge för stora, ofta uppdaterade data uppsättningar. Du kan till exempel skapa dimensions tabeller med hjälp av **import** läge eftersom de är små och inte ändras ofta. Ange uppdaterings intervallet enligt den förväntade frekvensen för data uppdateringar. Skapa fakta tabeller med **DirectQuery** -läge eftersom tabellerna är stora och innehåller rå data. Använd de här tabellerna för att presentera filtrerade data med hjälp av Power BI [drillthrough](https://docs.microsoft.com/power-bi/desktop-drillthrough).

* **Parallelitet** – Azure datautforskaren är en linjär skalbar data plattform, vilket innebär att du kan förbättra prestanda för instrument panels åter givning genom att öka parallellitet för slut punkt till slut punkt på följande sätt:

   * Öka antalet [samtidiga anslutningar i DirectQuery i Power BI](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Använd [svag konsekvens för att förbättra parallellitet](/azure/kusto/concepts/queryconsistency). Detta kan påverka datans aktualitet.

* **Effektiva utsnitt** – Använd [Synkronisera utsnitt](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) för att förhindra att rapporter läser in data innan du är klar. När du har strukturerat data uppsättningen, placerar alla visuella objekt och markerar alla utsnitt, kan du välja det synkroniserade utsnittet för att endast läsa in de data som behövs.

* **Använd filter** – Använd så många Power BI filter som möjligt för att fokusera på Azure datautforskaren-sökningen på relevanta data Shards.

* **Effektiv visualisering** – Välj de mest presterande visuella objekten för dina data.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Tips för att använda Azure Datautforskaren Connector för Power BI för att fråga efter data

I följande avsnitt finns tips och trick för att använda Kusto-frågespråk med Power BI. Använd [Azure datautforskaren-anslutaren för Power BI](power-bi-connector.md) för att visualisera data

### <a name="complex-queries-in-power-bi"></a>Komplexa frågor i Power BI

Komplexa frågor är enklare att uttryckas i Kusto än i Power Query. De bör implementeras som [Kusto-funktioner](/azure/kusto/query/functions)och anropas i Power BI. Den här metoden krävs när du använder **DirectQuery** med `let`-instruktioner i din Kusto-fråga. Eftersom Power BI ansluter till två frågor och `let`-instruktioner inte kan användas med `join`-operatorn kan syntaxfel uppstå. Spara därför varje del av funktionen Join as a Kusto och Tillåt Power BI att koppla samman de här två funktionerna.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Så här simulerar du en relativ datum-och tids operator

Power BI innehåller inte en *relativ* datum-och tids operator som `ago()`.
Om du vill simulera `ago()`använder du en kombination av funktionerna `DateTime.FixedLocalNow()` och `#duration` Power BI.

I stället för den här frågan med operatorn `ago()`:

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

### <a name="reaching-kusto-query-limits"></a>Når gränser för Kusto-frågor 

Kusto-frågor returnerar som standard upp till 500 000 rader eller 64 MB, enligt beskrivningen i [begränsningar för frågor](/azure/kusto/concepts/querylimits). Du kan åsidosätta dessa standardvärden med hjälp av **Avancerade alternativ** i **Azure datautforskaren-anslutnings fönstret (Kusto)** :

![avancerade alternativ](media/power-bi-best-practices/advanced-options.png)

De här alternativen utfärdar [set-instruktioner](/azure/kusto/query/setstatement) med din fråga för att ändra standard antalet frågor:

  * **Begränsa post numret för frågeresultatet** genererar ett `set truncationmaxrecords`
  * **Begränsa data storlek för frågeresultat i byte** genererar ett `set truncationmaxsize`
  * **Inaktivera resulterande trunkering** genererar ett `set notruncation`

### <a name="using-query-parameters"></a>Använda frågeparametrar

Du kan använda [frågeparametrar](/azure/kusto/query/queryparametersstatement) för att ändra frågan dynamiskt. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Använda en frågeparameter i anslutnings informationen

Använd en frågeparameter för att filtrera information i frågan och optimera frågans prestanda.
 
I fönstret **Redigera frågor** , **Start** > **avancerad redigerare**

1. Hitta följande avsnitt i frågan:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Exempel:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Ersätt relevant del av frågan med din parameter. Dela upp frågan i flera delar och slå tillbaka dem med ett et-tecken (&), tillsammans med parametern.

   I frågan ovan tar vi till exempel `State == 'ALABAMA'` delen och delar upp den för att: `State == '` och `'` och vi ska placera `State`-parametern mellan dem:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Koda dem korrekt om frågan innehåller citat tecken. Till exempel följande fråga: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   kommer att visas i **avancerad redigerare** på följande sätt med två citat tecken:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Den ska ersättas med följande fråga med tre citat tecken:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Använd en frågeparameter i stegen i frågan

Du kan använda en frågeparameter i alla fråge steg som stöder den. Filtrera till exempel resultatet baserat på värdet för en parameter.

![Filtrera resultat med en parameter](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Använd inte Power BI data uppdatering Scheduler för att utfärda kontroll kommandon till Kusto

Power BI innehåller en schemaläggare för data uppdatering som regelbundet kan utfärda frågor mot en data källa. Den här mekanismen bör inte användas för att schemalägga kontroll kommandon till Kusto eftersom Power BI förutsätter att alla frågor är skrivskyddade.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI kan bara skicka korta (&lt;2000 tecken) frågor till Kusto

Om du kör en fråga i Power BI resulterar i följande fel: _"DataSource. error: Web. Contents kunde inte hämta innehåll från..."_ frågan är förmodligen längre än 2000 tecken. Power BI använder **PowerQuery** för att fråga Kusto genom att utfärda en HTTP GET-begäran som kodar frågan som en del av den URI som hämtas. Därför är Kusto-frågor som utfärdats av Power BI begränsade till den maximala längden för en begärande-URI (2000 tecken, minus liten offset). Som en lösning kan du definiera en [lagrad funktion](/azure/kusto/query/schema-entities/stored-functions) i Kusto och har Power BI använda funktionen i frågan.

## <a name="next-steps"></a>Nästa steg

[Visualisera data med Azure Datautforskaren Connector för Power BI](power-bi-connector.md)




