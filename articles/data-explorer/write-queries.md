---
title: Skriva frågor för Azure Data Explorer
description: I det här indataprogrammet får du lära dig hur du utför grundläggande och mer avancerade frågor för Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 80d3eaaf7e588766d62f5e5885d75e61c590970e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881183"
---
# <a name="write-queries-for-azure-data-explorer"></a>Skriva frågor för Azure Data Explorer

I den här artikeln får du lära dig hur du använder frågespråket i Azure Data Explorer för att utföra grundläggande frågor med de vanligaste operatörerna. Du får också exponering för några av de mer avancerade funktionerna i språket.

## <a name="prerequisites"></a>Krav

Du kan köra frågorna i den här artikeln på ett av två sätt:

- I *hjälpklustret för* Azure Data Explorer som vi har konfigurerat för att underlätta inlärningen.
    [Logga in i klustret](https://dataexplorer.azure.com/clusters/help/databases/samples) med ett organisations-e-postkonto som är medlem i Azure Active Directory.

- På ditt eget kluster som innehåller Exempeldata för StormEvents. Mer information finns i [Snabbstart: Skapa ett Azure Data Explorer-kluster och databas](create-cluster-database-portal.md) och [matar in exempeldata i Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Översikt över frågespråket

Ett frågespråk i Azure Data Explorer är en skrivskyddad begäran om att bearbeta data och returnera resultat. Begäran anges i oformaterad text med hjälp av en dataflödesmodell som utformats för att göra syntaxen lätt att läsa, skapa och automatisera. Frågan använder schemaentiteter som är ordnade i en hierarki som liknar SQL: databaser, tabeller och kolumner.

Frågan består av en sekvens av frågesatser, avgränsade med`;`ett semikolon ( ), med minst en sats som en tabelluttryckssats, vilket är en sats som producerar data ordnade i ett tabellliknande nät med kolumner och rader. Frågans tabelluttryckssatser ger resultatet av frågan.

Syntaxen för den tabelluttryckssats har tabelldataflöde från en tabellfrågaoperator till en annan, med början med datakälla (till exempel en tabell i en databas eller en operator som`|`producerar data) och sedan flödar genom en uppsättning dataomvandlingsoperatorer som binds samman med hjälp av pipe ( ).

Följande fråga har till exempel en enda sats, som är en tabelluttryckssats. Satsen börjar med en referens `StormEvents` till en tabell som heter (databasen som är värd för den här tabellen är implicit här och en del av anslutningsinformationen). Data (rader) för den tabellen filtreras sedan efter `StartTime` kolumnens värde och filtreras `State` sedan efter kolumnens värde. Frågan returnerar sedan antalet "överlevande" rader.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

I det här fallet är resultatet:

|Antal|
|-----|
|   23|
| |

Mer information finns i [referensen för frågespråk](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>De vanligaste operatörerna

De operatorer som omfattas av det här avsnittet är byggstenarna för att förstå frågor i Azure Data Explorer. De flesta frågor du skriver kommer att innehålla flera av dessa operatorer.

Om du vill köra frågor i hjälpklustret väljer **du Klicka för att köra frågan** ovanför varje fråga.

Så här kör du frågor i ditt eget kluster:

1. Kopiera varje fråga till det webbaserade frågeprogrammet och markera sedan frågan eller placera markören i frågan.

1. Högst upp i programmet väljer du **Kör**.

### <a name="count"></a>count

[**antal**](https://docs.microsoft.com/azure/kusto/query/countoperator): Returnerar antalet rader i tabellen.

Följande fråga returnerar antalet rader i tabellen StormEvents.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>Ta

[**ta**](https://docs.microsoft.com/azure/kusto/query/takeoperator): Returnerar upp till det angivna antalet rader med data.

Följande fråga returnerar fem rader från tabellen StormEvents. *Nyckelordsgränsen* är ett alias för *tagning.*

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Det finns ingen garanti för vilka poster som returneras om inte källdata sorteras.

### <a name="project"></a>projekt

[**projekt**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Väljer en delmängd av kolumner.

Följande fråga returnerar en viss uppsättning kolumner.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>där

[**där:**](https://docs.microsoft.com/azure/kusto/query/whereoperator)Filtrerar en tabell till den delmängd av rader som uppfyller ett predikat.

Följande fråga filtrerar `EventType` data `State`efter och .

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>sortera

[**sortera**](https://docs.microsoft.com/azure/kusto/query/sortoperator): Sortera raderna i indatatabellen i ordning efter en eller flera kolumner.

Följande fråga sorterar data i `DamageProperty`fallande ordning efter .

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> Ordningen på verksamheten är viktig. Försök `take 5` att `sort by`sätta före . Får du olika resultat?

### <a name="top"></a>överst

[**överst**](https://docs.microsoft.com/azure/kusto/query/topoperator): Returnerar de första *N-posterna* sorterade efter de angivna kolumnerna.

Följande fråga returnerar samma resultat som ovan med en operator mindre.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>Utöka

[**utöka**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Beräknar härledda kolumner.

Följande fråga skapar en ny kolumn genom att beräkna ett värde på varje rad.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Uttryck kan innehålla alla vanliga operatorer (+, -, *, /, %), och det finns en rad användbara funktioner som du kan anropa.

### <a name="summarize"></a>Sammanfatta

[**sammanfatta**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Aggregat grupper av rader.

Följande fråga returnerar antalet `State`händelser efter .

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

Operatorn **summarize** grupperar ihop rader som har samma värden i **satsen** efter och använder sedan aggregeringsfunktionen (t.ex. **antal)** för att kombinera varje grupp till en enda rad. Så, i det här fallet, det finns en rad för varje tillstånd, och en kolumn för antalet rader i det tillståndet.

Det finns en rad aggregeringsfunktioner och du kan använda flera av dem i en **sammanfatta operator** för att producera flera beräknade kolumner. Till exempel kan du få antalet stormar i varje stat och det unika antalet stormar per stat, sedan använda **toppen** för att få de mest stormdrabbade staterna.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Resultatet av en **sammanfattningsåtgärd** har:

- Varje kolumn som **namnges av**

- En kolumn för varje beräknat uttryck

- En rad för varje kombination av värden

### <a name="render"></a>Göra

[**återge**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Återger resultat som ett grafiskt utdata.

I följande fråga visas ett stapeldiagram.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

Följande fråga visar ett enkelt tidsdiagram.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

Följande fråga räknar händelser efter tiden modulo en dag, binned i timmar och visar ett tidsschema.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

Följande fråga jämför flera dagliga serier i ett tidsschema.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> **Renderingsoperatören** är en funktion på klientsidan i stället för en del av motorn. Det är integrerat i språket för enkel användning. Webbprogrammet stöder följande alternativ: barchart, columnchart, piechart, timechart och linechart. 

## <a name="scalar-operators"></a>Skalningsoperatorer

Det här avsnittet täcker några av de viktigaste skaläroperatorerna.

### <a name="bin"></a>fackplats()

[**lagerplats()**](https://docs.microsoft.com/azure/kusto/query/binfunction): Avrundar värden nedåt till en heltalsmultvist av en viss lagerplatsstorlek.

Följande fråga beräknar antalet med en bucket size på en dag.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>ärende()

[**case()**](https://docs.microsoft.com/azure/kusto/query/casefunction): Utvärderar en lista över predikat och returnerar det första resultatuttrycket vars predikat är uppfyllt, eller det slutliga **else-uttrycket.** Du kan använda den här operatorn för att kategorisera eller gruppera data:

Följande fråga returnerar `deaths_bucket` en ny kolumn och grupperar dödsfallen efter antal.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>utdrag()

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): Får en matchning för ett reguljärt uttryck från en textsträng.

Följande fråga extraherar specifika attributvärden från en spårning.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Den här frågan använder en **let-sats** som `MyData`binder ett namn (i det här fallet) till ett uttryck. För resten av scopet, där **lr-satsen** visas (globalt scope eller i ett funktionstextomfång), kan namnet användas för att referera till dess bundna värde.

### <a name="parse_json"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Tolkar en sträng som ett JSON-värde och returnerar värdet som dynamiskt. Det är överlägsen att använda **extractjson()** funktion när du behöver extrahera mer än ett element i en sammansatt JSON objekt.

Följande fråga extraherar JSON-elementen från en matris.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Följande fråga extraherar JSON-elementen.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Följande fråga extraherar JSON-elementen med en dynamisk datatyp.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>sedan()

[**sedan()**](https://docs.microsoft.com/azure/kusto/query/agofunction): Subtraherar det angivna tidsspannet från den aktuella UTC-klocktiden.

Följande fråga returnerar data för de senaste 12 timmarna.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>start av veckan()

[**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): Returnerar början av veckan som innehåller datumet, förskjutet med en förskjutning, om den

Följande fråga returnerar början av veckan med olika förskjutningar.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Den här **range** frågan använder områdesoperatorn, som genererar en tabell med en kolumn med värden. Se även: [**startofday()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**startofyear()**](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [**startofmonth()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek()**](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [**endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction)och [**endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>Mellan()

[**mellan()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Matchar ingången som finns inom det inkluderande intervallet.

Följande fråga filtrerar data efter ett visst datumintervall.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

Följande fråga filtrerar data efter ett visst datumintervall, med`3d`en liten variation på tre dagar ( ) från startdatumet.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Tabelloperatorer

Kusto har många tabelloperatörer, varav några ingår i andra delar av denna artikel. Här ska vi fokusera på **parsa**. 

### <a name="parse"></a>parse

[**tolkning:**](https://docs.microsoft.com/azure/kusto/query/parseoperator)Utvärderar ett stränguttryck och tolkar dess värde i en eller flera beräknade kolumner. Det finns tre sätt att tolka: enkel (standard), regex och avslappnad.

Följande fråga tolkar en spårning och extraherar relevanta värden med hjälp av en standard för enkel tolkning. Uttrycket (kallas StringConstant) är ett vanligt strängvärde och matchningen är strikt: utökade kolumner måste matcha de obligatoriska typerna.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

Följande fråga tolkar en spårning och extraherar `kind = regex`relevanta värden med hjälp av . StringConstant kan vara ett reguljärt uttryck.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

Följande fråga tolkar en spårning och extraherar `kind = relaxed`relevanta värden med hjälp av . StringConstant är ett vanligt strängvärde och matchningen är avslappnad: utökade kolumner kan delvis matcha de nödvändiga typerna.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Time Series-analys

### <a name="make-series"></a>gör-serier

[**make-series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): sammanställer grupper av rader som [sammanfattar](https://docs.microsoft.com/azure/kusto/query/summarizeoperator), men genererar en (tid) serievektor per varje kombination av värden.

Följande fråga returnerar en uppsättning tidsserier för antalet stormhändelser per dag. Frågan täcker en tremånadersperiod för varje tillstånd och fyller på saknade lagerplatser med konstanten 0:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

När du har skapat en uppsättning (tids)serier kan du använda seriefunktioner för att identifiera avvikande former, säsongsmönster och mycket mer.

Följande fråga extraherar de tre översta tillstånden som hade flest händelser under den specifika dagen:

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Mer information finns i den fullständiga listan över [seriefunktioner](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Avancerade aggregeringar

Vi täckte grundläggande aggregeringar, som **räkna** och **sammanfatta**, tidigare i den här artikeln. I det här avsnittet introduceras mer avancerade alternativ.

### <a name="top-nested"></a>toppkapslade

[**toppkapslade:**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator)Ger hierarkiska toppresultat, där varje nivå är en detaljgranskning baserat på tidigare nivåvärden.

Den här operatorn är användbar för visualiseringsscenarier på instrumentpanelen, eller när det är nödvändigt att svara på en fråga som följande: "Hitta de översta N-värdena för K1 (med hjälp av viss aggregering); för var och en av dem, hitta vad som är de högsta M-värdena för K2 (med en annan aggregering); ..."

Följande fråga returnerar en `State` hierarkisk tabell med `Sources`på den översta nivån, följt av .

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>pivot() plugin

[**pivot() plugin:**](https://docs.microsoft.com/azure/kusto/query/pivotplugin)Roterar en tabell genom att omvandla de unika värdena från en kolumn i indatatabellen till flera kolumner i utdatatabellen. Operatören utför aggregeringar där de krävs på eventuella återstående kolumnvärden i den slutliga utdata.

Följande fråga tillämpar ett filter och pivoterar raderna till kolumner.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): Returnerar en uppskattning av antalet distinkta värden för ett uttryck i gruppen. Använd [**count()**](https://docs.microsoft.com/azure/kusto/query/countoperator) för att räkna alla värden.

Följande fråga räknas `Source` `State`distinkt efter .

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): Returnerar en uppskattning av antalet distinkta värden för uttrycket för rader som predikatet utvärderar till true.

Följande fråga räknar de `Source` distinkta värdena för var `DamageProperty < 5000`.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Beräknar **dcount** från HyperLogLog-resultat (genererat av [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) eller [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

Följande fråga använder HLL-algoritmen för att generera antalet.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Söker efter en rad i gruppen som maximerar ett uttryck och returnerar värdet för ett annat uttryck (eller * för att returnera hela raden).

Följande fråga returnerar tiden för den senaste översvämningsrapporten i varje tillstånd.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): Returnerar en dynamisk (JSON) matris av uppsättningen av distinkta värden som ett uttryck tar i gruppen.

Följande fråga returnerar alla tider då en översvämning rapporterades av varje tillstånd och skapar en matris från uppsättningen med distinkta värden.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-expandera

[**mv-expand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Expanderar flera värdesamlingar från en kolumn med dynamiskt skrivna så att varje värde i samlingen får en separat rad. Alla andra kolumner i en utökad rad dupliceras. Det är motsatsen till makelist.

Följande fråga genererar exempeldata genom att skapa en uppsättning och sedan använda den för att demonstrera **mv-expand-funktionerna.**

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiler()

[**percentiler()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Returnerar en uppskattning för den angivna [**percentilen närmast rangordning**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) av den population som definieras av ett uttryck. Noggrannheten beror på befolkningstätheten i percentilens region. Kan endast användas i samband med aggregering inuti [**sammanfatta**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator).

Följande fråga beräknar percentiler för stormvaraktighet.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Följande fråga beräknar percentiler för storm varaktighet efter tillstånd och normaliserar`5m`data med fem minuters lagerplatser ( ).

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Korsdatauppsättning

Det här avsnittet innehåller element som gör att du kan skapa mer komplexa frågor, koppla data över tabeller och fråga över databaser och kluster.

### <a name="let"></a>Låt

[**låt**](https://docs.microsoft.com/azure/kusto/query/letstatement): Förbättrar modularitet och återanvändning. Med **let-satsen** kan du dela upp ett potentiellt komplext uttryck i flera delar, var och en bunden till ett namn, och komponera dessa delar tillsammans. En **let** let-sats kan också användas för att skapa användardefinierade funktioner och vyer (uttryck över tabeller vars resultat ser ut som en ny tabell). Uttryck som är **let** bundna av en let-sats kan vara av skalärtyp, av tabelltyp eller användardefinierad funktion (lambdas).

I följande exempel skapas en tabelltypsvariabel och den används i ett efterföljande uttryck.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**koppling:**](https://docs.microsoft.com/azure/kusto/query/joinoperator)Sammanfoga raderna med två tabeller för att skapa en ny tabell genom att matcha värdena för de angivna kolumnerna från varje tabell. Kusto stöder ett komplett utbud av kopplingstyper: **fullouter**, **inre,** **innerstil,** **leftanti**, **leftantisemi**, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter**, **rightsemi**.

I följande exempel kopplas två tabeller med en inre koppling.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> Använd **var** och **projektoperatorer** för att minska antalet rader och kolumner i indatatabellerna, före kopplingen. Om den ena tabellen alltid är mindre än den andra använder du den som vänster (ledad) sida av kopplingen. Kolumnerna för kopplingsmatchningen måste ha samma namn. Använd **projektoperatorn** om det behövs för att byta namn på en kolumn i en av tabellerna.

### <a name="serialize"></a>Serialisera

[**serialisera**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): Serialiserar raduppsättningen så att du kan använda funktioner som kräver serialiserade data, som **row_number()**.

Följande fråga lyckas eftersom data serialiseras.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

Raduppsättningen betraktas också som serialiserad om den är ett resultat av: **sortera**, **överst**eller **intervalloperatorer,** eventuellt följt av **projekt**, **projekt-away**, **utöka**, **där**, **tolka**, **mv-expandera**eller **ta** operatorer.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Frågor mellan databaser och korskluster

[Frågor mellan databaser och korskluster:](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries)Du kan fråga en databas `database("MyDatabase").MyTable`i samma kluster genom att referera den som . Du kan fråga en databas i ett fjärrkluster genom att referera till den som `cluster("MyCluster").database("MyDatabase").MyTable`.

Följande fråga anropas från ett kluster `MyCluster` och frågar efter data från klustret. Om du vill köra den här frågan använder du ditt eget klusternamn och databasnamn.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Användaranalys

Det här avsnittet innehåller element och frågor som visar hur enkelt det är att utföra analys av användarbeteenden i Kusto.

### <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics plugin

[**activity_counts_metrics plugin:**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin)Beräknar användbara aktivitetsmått (totala räknevärden, distinkta räknevärden, distinkt antal nya värden och aggregerat distinkt antal). Mått beräknas för varje tidsfönster, sedan jämförs de och aggregeras till och med alla tidigare tidsfönster.

Följande fråga analyserar användarens införande genom att beräkna dagliga aktivitetsantal.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activity_engagement-plugin"></a>activity_engagement plugin

[**activity_engagement plugin:**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin)Beräknar aktivitetsengagemangförhållande baserat på ID-kolumn över ett glidande tidslinjefönster. **activity_engagement plugin** kan användas för beräkning av DAU, WAU och MAU (dagliga, veckovisa och månatliga aktiva användare).

Följande fråga returnerar förhållandet mellan totalt antal distinkta användare som använder ett program dagligen jämfört med totalt antal distinkta användare som använder programmet varje vecka, i ett rörligt sjudagarsfönster.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> När du beräknar DAU/MAU ändrar du slutdata och den rörliga fönsterperioden (OuterActivityWindow).

### <a name="activity_metrics-plugin"></a>activity_metrics plugin

[**activity_metrics plugin:**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin)Beräknar användbara aktivitetsmått (distinkta räknevärden, distinkt antal nya värden, kvarhållningshastighet och omsättningshastighet) baserat på det aktuella periodfönstret jämfört med föregående periodfönster.

Följande fråga beräknar omsättnings- och kvarhållningshastigheten för en viss datauppsättning.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="new_activity_metrics-plugin"></a>new_activity_metrics plugin

[**new_activity_metrics plugin:**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin)Beräknar användbara aktivitetsmått (distinkta räknevärden, distinkt antal nya värden, kvarhållningshastighet och omsättningshastighet) för kohorten av nya användare. Begreppet denna plugin liknar [**activity_metrics plugin**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), men fokuserar på nya användare.

Följande fråga beräknar en kvarhållnings- och omsättningshastighet med ett veckofönster för den nya användarkohorten (användare som anlände den första veckan).

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="session_count-plugin"></a>session_count plugin

[**session_count plugin:**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin)Beräknar antalet sessioner baserat på ID-kolumn över en tidslinje.

Följande fråga returnerar antalet sessioner. En session anses aktiv om ett användar-ID visas minst en gång inom en tidsram på 100-tidsplatser, medan sessionsfönstret är 41-tidsplatser.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnel_sequence-plugin"></a>funnel_sequence plugin

[**funnel_sequence plugin:**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin)Beräknar det distinkta antalet användare som har tagit en sekvens av stater; visar fördelningen av tidigare och nästa tillstånd som har lett till eller följts av sekvensen.

Följande fråga visar vilken händelse som händer före och efter alla Tornado-händelser under 2007.

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion plugin

[**funnel_sequence_completion plugin:**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin)Beräknar tratten för avslutade sekvenssteg inom olika tidsperioder.

Följande fråga kontrollerar sekvensens slutförandetratt: `Hail -> Tornado -> Thunderstorm -> Wind` i "övergripande" tider på en`[1h, 4h, 1d]`timme, fyra timmar och en dag ( ).

**\[**[**Klicka för att köra fråga**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Funktioner

Det här avsnittet omfattar [**funktioner:**](https://docs.microsoft.com/azure/kusto/query/functions)återanvändbara frågor som lagras på servern. Funktioner kan anropas av frågor och andra funktioner (rekursiva funktioner stöds inte).

> [!NOTE]
> Du kan inte skapa funktioner i hjälpklustret, som är skrivskyddat. Använd ditt eget testkluster för den här delen.

I följande exempel skapas en funktion`MyState`som tar ett tillståndsnamn ( ) som ett argument.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

Följande exempel anropar en funktion som hämtar data för delstaten Texas.

```Kusto
MyFunction ("Texas")
| summarize count()
```

I följande exempel tas funktionen som skapades i det första steget bort.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Nästa steg

[Referens för Språk för Kusto-frågor](https://aka.ms/kustolangref)
