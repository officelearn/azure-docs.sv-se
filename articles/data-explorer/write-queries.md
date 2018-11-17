---
title: Skriv frågor för Azure Data Explorer
description: I den här anvisningen att lära dig hur du utför grundläggande och mer avancerade frågor för Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 71e39c6430231ae8d175f9c09a9059c3da4c9a1e
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854262"
---
# <a name="write-queries-for-azure-data-explorer"></a>Skriv frågor för Azure Data Explorer

I den här artikeln får du lära dig hur du använder frågespråket i Azure Data Explorer för att utföra grundläggande frågor med de vanligaste operatorer. Du kan även få exponeringen för några av de mer avancerade funktionerna för språket.

## <a name="prerequisites"></a>Förutsättningar

Du kan köra frågor i den här artikeln på något av två sätt:

- På Azure Data Explorer *hjälpklustret* att vi har ställts in för att underlätta learning.
    [Logga in på klustret](https://dataexplorer.azure.com/clusters/help/databases/samples) med ett konto för organisationens e-post som är medlem i Azure Active directory.

- På ett eget kluster innehåller som exempeldata StormEvents. Mer information finns i [Snabbstart: skapa ett Azure Data Explorer-kluster och databasen](create-cluster-database-portal.md) och [mata in exempeldata i Azure Data Explorer](ingest-sample-data.md).

[!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Översikt över frågespråket

Ett frågespråk i Datautforskaren i Azure är en skrivskyddad begäran att bearbeta data och returnera resultat. Begäran anges i oformaterad text, med hjälp av en modell för dataflödet som utformats för att göra det enkelt att läsa, skriva och automatisera syntaxen. Frågan använder schemat entiteter som är ordnade i en hierarki som liknar SQL: databaser, tabeller och kolumner.

Frågan består av en sekvens av frågan rapporter, avgränsade med semikolon (`;`), med minst en instruktion som ett uttryck för tabular-instruktionen, vilket är en instruktion som producerar data ordnade i ett tabell-liknande nät kolumner och rader. Frågans tabular uttryck producerar resultatet av frågan.

Syntaxen för tabular uttrycket har tabelldata flöde från en tabellfråga operatör till en annan, från och med datakälla (till exempel en tabell i en databas eller en operatör som producerar data) och sedan passerar genom en uppsättning data-transformering operatörer som är sammanfogade genom att använda en pipe (`|`) avgränsare.

Följande fråga har till exempel en enskild instruktion, vilket är en tabular uttrycksinstruktion. Instruktionen som börjar med en referens till en tabell med namnet `StormEvents` (den databas som värd för den här tabellen är implicit här och en del av anslutningsinformationen). Data (rader) för tabellen filtreras sedan av värdet för den `StartTime` kolumnen och sedan filtreras efter värdet för den `State` kolumn. Frågan returnerar sedan antalet ”kvarvarande” rader.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

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

Mer information finns i den [Frågespråksreferens](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>De vanligaste operatorer

Operatorer som beskrivs i det här avsnittet är byggblocken för att förstå frågor i Datautforskaren i Azure. De flesta frågor som du skriver innehåller flera av de här operatorerna.

Att köra frågor i hjälpklustret: Välj **Klicka om du vill köra frågan** ovanför varje fråga.

Köra frågor på ett eget kluster:

1. Kopiera varje fråga till webbaserade fråga-program och Välj frågan eller placera markören i frågan.

1. Högst upp på programmet, Välj **kör**.

### <a name="count"></a>antal

[**antal**](https://docs.microsoft.com/azure/kusto/query/countoperator): returnerar antalet rader i tabellen.

Följande fråga returnerar antalet rader i tabellen StormEvents.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>ta

[**Ta**](https://docs.microsoft.com/azure/kusto/query/takeoperator): returnerar upp till det angivna antalet rader med data.

Följande fråga returnerar fem rader från tabellen StormEvents. Nyckelordet *gränsen* är ett alias för *ta.*

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Det finns ingen garanti för vilka poster som returneras om inte källdata sorteras.

### <a name="project"></a>Projekt

[**projektet**](https://docs.microsoft.com/azure/kusto/query/projectoperator): väljer en delmängd med kolumner.

Följande fråga returnerar en specifik uppsättning kolumner.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>där

[**där**](https://docs.microsoft.com/azure/kusto/query/whereoperator): filtrerar en tabell till underuppsättningen av rader som uppfyller ett predikat.

Följande fråga filtrerar data genom att `EventType` och `State`.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>Sortera

[**Sortera**](https://docs.microsoft.com/azure/kusto/query/sortoperator): Sortera rader i indatatabellen i ordning efter en eller flera kolumner.

Följande fråga sorterar data i fallande ordning av `DamageProperty`.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> Ordningen på åtgärder är viktigt. Försök att tillämpa `take 5` innan `sort by`. Kan du få olika resultat?

### <a name="top"></a>längst upp

[**översta**](https://docs.microsoft.com/azure/kusto/query/topoperator): returnerar första *N* poster sorterade efter de angivna kolumnerna.

Följande fråga returnerar samma resultat som ovan med en mindre operator.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>Utöka

[**utöka**](https://docs.microsoft.com/azure/kusto/query/extendoperator): beräkningarna härledda kolumner.

Följande fråga skapar en ny kolumn genom att beräkna ett värde i varje rad.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Uttryck kan innehålla alla vanliga operatorer (+, -, *, /, %), och det finns ett antal användbara funktioner som kan anropas.

### <a name="summarize"></a>Sammanfatta

[**Sammanfatta**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): aggregerar grupper av rader.

Följande fråga returnerar antalet händelser efter `State`.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

Den **sammanfatta** operator för att gruppera rader som har samma värden i den **genom att** -satsen, och använder sedan aggregeringsfunktionen (till exempel **antal**) att kombinera varje grupp till en enda rad. Så, då det finns en rad för varje tillstånd och en kolumn för antalet rader i det aktuella tillståndet.

Det finns en mängd aggregeringsfunktioner och du kan använda flera av dem i en **sammanfatta** operator för att producera flera beräknade kolumner. Du kan till exempel få ett värde för storm i varje tillstånd och unikt nummer för storm per tillstånd och använda **upp** att hämta de mest storm berörda tillstånd.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Resultatet av en **sammanfatta** åtgärd har:

- Varje kolumn med namnet i **av**

- En kolumn för varje beräknade uttryck

- En rad för varje kombination av värden

### <a name="render"></a>rendering

[**Rendera**](https://docs.microsoft.com/azure/kusto/query/renderoperator): renderas resultatet som en grafisk utdata.

Följande fråga visar ett stapeldiagram.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

Följande fråga visar en enkel tidsdiagram.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

Följande fråga räknar händelser med tiden modulo en dag binned till timmar, och visar ett diagram med tiden.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

Följande fråga jämför flera dagliga serier i ett diagram med tiden.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> Den **rendera** operatorn är ett klientside-funktionen i stället för en del av motorn. Det är integrerad i språket för enkel användning. Webbprogrammet har stöd för följande alternativ: barchart, columnchart, piechart, tidsdiagram, och linechart. 

## <a name="scalar-operators"></a>Skalära operatorer

Det här avsnittet beskrivs några av de viktigaste skalära operatorerna.

### <a name="bin"></a>bin()

[**bin()**](https://docs.microsoft.com/azure/kusto/query/binfunction): Avrundar värden till ett heltal multipel av en viss lagerplats storlek.

Följande fråga beräknar antalet med en bucket-storleken på en dag.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>Case()

[**Case()**](https://docs.microsoft.com/azure/kusto/query/casefunction): utvärderar en lista med predikat och returnerar det första resultat uttrycket vars predikatet är uppfyllt eller sista **annan** uttryck. Du kan använda den här operatorn för att kategorisera eller gruppera data:

Följande fråga returnerar en ny kolumn `deaths_bucket` och grupperar dött med tal.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

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

### <a name="extract"></a>extract()

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): hämtar en matchning för ett reguljärt uttryck från en textsträng.

Följande fråga extraherar specifika attributvärden från en spårning.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Den här frågan använder en **låta** -instruktionen, vilket Binder ett namn (i det här fallet `MyData`) till ett uttryck. Under resten av området där det **låta** instruktionen visas (global omfattning eller i en omfattning för funktionen brödtext), namnet kan användas för att referera till dess bundna värde.

### <a name="parsejson"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): tolkar en sträng som ett JSON-värde och returnerar värdet som dynamisk. Det är bättre än med hjälp av den **extractjson()** fungerar när du behöver att extrahera mer än ett element i ett sammansatt JSON-objekt.

Följande fråga extraherar JSON-element från en matris.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Följande fråga extraherar JSON-element.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Följande fråga extraherar JSON-element med en dynamisk datatyp.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()**](https://docs.microsoft.com/azure/kusto/query/agofunction): subtraherar angivna timespan från den aktuella UTC-clock-tid.

Följande fråga returnerar data för de senaste 12 timmarna.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mvexpand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): returnerar början på veckan som innehåller datum, beräkningsarbete genom en förskjutning om

Följande fråga returnerar början på veckan med olika förskjutningar.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Den här frågan använder den **intervallet** operator, vilket genererar en enkolumnstabell med värden. Se även: [ **startofday()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [ **startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [ **startofyear()** ](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [ **startofmonth()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [ **endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [ **endofweek()**  ](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [ **endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction), och [ **endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>between()

[**between()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): matchar indata som är inom intervallet.

Följande fråga filtrerar data efter ett visst datumintervall.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

Följande fråga filtrerar data efter ett visst datumintervall med mindre variation på tre dagar (`3d`) från startdatumet.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Tabular operatorer

Kusto har många tabular operatorer, vilket beskrivs i andra avsnitt i den här artikeln. Här vi att fokusera på **parsa**. 

### <a name="parse"></a>parsa

[**parsa**](https://docs.microsoft.com/azure/kusto/query/parseoperator): utvärderar ett stränguttryck och Parsar dess värde i en eller flera beräknade kolumner. Det finns tre sätt att parsa: enkel (standard), regex, och Avslappnad.

Följande fråga Parsar en spårning och extraherar relevanta värden, med en standard för att enkelt tolka. Uttrycket (kallas StringConstant) är en vanlig strängvärde och matchningen är strikt: utökade kolumner måste matcha de nödvändiga typerna.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

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

Följande fråga tolkar en spårning och extraherar relevanta värden, med hjälp av `kind = regex`. StringConstant kan vara ett reguljärt uttryck.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

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

Följande fråga tolkar en spårning och extraherar relevanta värden, med hjälp av `kind = relaxed`. StringConstant är en vanlig strängvärde och matchningen är Avslappnad: utökade kolumner kan delvis matchar de nödvändiga typerna.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

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

### <a name="make-series"></a>Kontrollera-serien

[**Kontrollera-serien**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): aggregerar tillsammans grupper av rader som [sammanfatta](https://docs.microsoft.com/azure/kusto/query/summarizeoperator), men genererar en (tidsserie) vektor per varje kombination av värden.

Följande fråga returnerar en uppsättning tidsserier för antal storm händelser per dag. Frågan innehåller tre månader för respektive tillstånd, fylla saknas lagerplatser med Ständiga 0:

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

När du skapar en uppsättning (tidsserier) kan använda du Seriefunktioner för att identifiera avvikande former, säsongens mönster och mycket mer.

Följande fråga extraherar de övre tre tillstånd som hade de flesta händelser i specifik dag:

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Mer information finns i en fullständig lista över [Seriefunktioner](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Avancerade aggregeringar

Vi har täckt grundläggande aggregeringar som **antal** och **sammanfatta**tidigare i den här artikeln. Det här avsnittet introducerar mer avancerade alternativ.

### <a name="top-nested"></a>TOP-nested

[**TOP-kapslad**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): skapar hierarkiska översta resultat, där varje nivå är en detaljnivå baserat på föregående värden.

Den här operatorn är användbar för instrumentpanelen för visualisering scenarier eller när det är nödvändigt att besvara en fråga som liknar följande: ”hitta topp-N-värdena för K1 (med vissa aggregering); för var och en av dem hitta vad är top-M-värdena för K2 (med en annan aggregering); ..."

Följande fråga returnerar en hierarkisk tabell med `State` på den översta nivån, följt av `Sources`.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>Pivot() plugin-programmet

[**plugin-programmet för pivot()**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): roterar en tabell genom att aktivera unika värden från en kolumn i indatatabellen i flera kolumner i utdatatabellen. Operatorn utför sammansättningar där de krävs på alla återstående kolumnvärdena i utdata.

Följande fråga använder ett filter och radfälten byter rader till kolumner.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>DCount()

[**DCount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): returnerar en uppskattning av antalet distinkta värden för ett uttrycks i gruppen. Använd [ **antal()** ](https://docs.microsoft.com/azure/kusto/query/countoperator) att räkna alla värden.

Följande fråga räknar distinkta `Source` av `State`.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): returnerar en uppskattning av antalet distinkta värden för uttryck för rader som predikatet utvärderas till SANT.

Följande fråga räknar de distinkta värdena för `Source` där `DamageProperty < 5000`.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcounthll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): beräknar den **dcount** HyperLogLog resultaten (genereras av [**hll** ](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction)   eller [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

Följande fråga använder algoritmen HLL för att generera antalet.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="argmax"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): söker efter en rad i den grupp som maximerar ett uttryck och returnerar värdet på ett annat uttryck (eller * att returnera hela raden).

Följande fråga returnerar tidpunkten för senaste översvämning rapporten i varje tillstånd.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): returnerar en dynamisk (JSON)-matris med uppsättning distinkta värden som tar ett uttryck i gruppen.

Följande fråga returnerar alla tidpunkter när en mängd rapporterades av varje tillstånd och skapar en matris från uppsättningen med distinkta värden.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mvexpand"></a>mvexpand

[**mvexpand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): utökar Flervärde samling(ar) från en dynamisk typ kolumn så att varje värde i samlingen hämtar en separat rad. Alla andra kolumner i en utökad rad dupliceras. Det är motsatsen till makelist.

Följande fråga genererar exempeldata genom att skapa en uppsättning och sedan använder den för att demonstrera den **mvexpand** funktioner.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mvexpand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): returnerar en uppskattning för den angivna [**närmaste rangordning: e percentilen**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) från den population som definieras av ett uttryck. Precisionen är beroende av tätheten av befolkningen i regionen i den: e percentilen. Kan endast användas i kontexten för aggregering i [**sammanfatta**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator).

Följande fråga beräknar percentilerna för storm varaktighet.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Följande fråga beräknar percentilerna för storm varaktighet efter delstat och normaliserar data efter fem minuter lagerplatser (`5m`).

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Mellan datauppsättning

Det här avsnittet beskriver element som hjälper dig att skapa mer komplexa frågor, ansluta till data i tabeller och frågor mellan databaser och -kluster.

### <a name="let"></a>Låt

[**Låt**](https://docs.microsoft.com/azure/kusto/query/letstatement): ger bättre flexibilitet och återanvändning. Den **låta** kan du dela upp ett eventuella komplicerade uttryck i flera delar, var och en bunden till ett namn och sätt ihop dessa delar tillsammans. En **låta** instruktionen kan också användas för att skapa användardefinierade funktioner och vyer (uttryck över tabeller vars resultatet ut en ny tabell). Uttryck bunden av en **låta** kan vara av skalär typ. av tabular typ eller en användardefinierad funktion (lambdas).

I följande exempel skapar en variabel för tabular typ och använder den i ett efterföljande uttryck.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

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

[**Anslut till**](https://docs.microsoft.com/azure/kusto/query/joinoperator): sammanfogar raderna i två tabeller för att skapa en ny tabell genom att matcha värdena för den eller de angivna kolumnerna från varje tabell. Kusto stöder en hel rad join-typer: **fullouter**, **inre**, **innerunique**, **leftanti**, **leftantisemi **, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter **, **rightsemi**.

I följande exempel kopplar ihop två tabeller med en inre koppling.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

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
> Använd **där** och **projekt** operatörer att minska antalet rader och kolumner i tabellerna indata innan kopplingen. Om en tabell alltid är mindre än den andra, kan du använda den som vänster (via rörledningar) sida i kopplingen. Kolumnerna för join-matchning måste ha samma namn. Använd den **projekt** operatör om det behövs för att byta namn på en kolumn i någon av tabellerna.

### <a name="serialize"></a>serialisera

[**serialisera**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): Serialiserar raden så du kan använda funktioner som kräver serialiserade data som **row_number()**.

Följande fråga lyckas eftersom data serialiseras.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

Rad uppsättningen ses även som serialiserad om det är ett resultat av: **sortera**, **upp**, eller **intervallet** operatörer, eventuellt följt av **projekt**, **projekt-away**, **utöka**, **där**, **parsa**, **mvexpand**, eller **ta** operatörer.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Frågor mellan databaser och mellan kluster

[Frågor mellan databaser och mellan kluster](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): du kan fråga en databas i samma kluster genom att referera den som `database("MyDatabase").MyTable`. Du kan fråga en databas på ett fjärrkluster genom att referera till den som `cluster("MyCluster").database("MyDatabase").MyTable`.

Följande fråga anropas från ett kluster och frågar data från `MyCluster` kluster. Använd dina egna klusternamnet och databasnamnet om du vill köra den här frågan.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Användaren Analytics

Det här avsnittet innehåller elementen och frågor som visar hur enkelt det är att analysera användarbeteenden i Kusto.

### <a name="activitycountsmetrics-plugin"></a>activity_counts_metrics plugin-programmet

[**plugin-programmet för activity_counts_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): beräknar användbara aktivitetsmått (totalt antal värden, Distinkt antal värden, Distinkt antal för nya värden och aggregerade Distinkt antal). Mått beräknas för varje tidsperiod och de är jämfört med och aggregeras till och med alla tidigare tidsfönster.

Följande fråga analyserar användning genom att beräkna daglig aktivitet antal.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

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

### <a name="activityengagement-plugin"></a>activity_engagement plugin-programmet

[**plugin-programmet för activity_engagement**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): beräknar aktivitet engagement förhållande baserat på ID-kolumnen under ett skjutfönster tidslinje. **plugin-programmet för activity_engagement** kan användas för att beräkna DAU och WAU MAU (dagliga, veckovisa och månatliga aktiva användare).

Följande fråga returnerar förhållandet mellan totalt antal distinkta användare som använder ett program som dagligen jämfört med Totalt antal distinkta användare som använder programmet varje vecka, på rörligt inom sju dagar.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mvexpand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Vid beräkning av DAU/MAU, ändra datakälla och flytta fönstret perioden (OuterActivityWindow).

### <a name="activitymetrics-plugin"></a>activity_metrics plugin-programmet

[**plugin-programmet för activity_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): beräknar användbara aktivitetsmått (Distinkt antal värden, Distinkt antal för nya värden och kvarhållningsfrekvensen omsättningshastighet) baserat på det aktuella period fönstret jämfört med föregående period fönster.

Följande fråga beräknar omsättning och kvarhållning avgiften för den angivna datauppsättningen.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mvexpand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="newactivitymetrics-plugin"></a>new_activity_metrics plugin-programmet

[**plugin-programmet för new_activity_metrics**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): beräknar användbara aktivitetsmått (Distinkt antal värden, Distinkt antal för nya värden och kvarhållningsfrekvensen omsättningsfrekvensen) för kohorten för nya användare. Konceptet med det här pluginprogrammet liknar [**activity_metrics plugin-programmet**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), men fokuserar på nya användare.

Följande fråga beräknar ett kvarhållning och omsättning pris med ett vecka over vecka fönster för den nya kohort för användare (användare som anlänt på den första veckan).

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mvexpand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="sessioncount-plugin"></a>session_count plugin-programmet

[**plugin-programmet för session_count**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): beräknar antalet sessioner baserat på ID-kolumnen över en tidslinje.

Följande fråga returnerar antalet sessioner. En session anses vara aktiv om ett användar-ID visas minst en gång i en tidsram 100-time-fack titt bak sessionsfönstret är 41-time-platser.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

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

### <a name="funnelsequence-plugin"></a>funnel_sequence plugin-programmet

[**plugin-programmet för funnel_sequence**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): beräknar Distinkt antal användare som har tagit en sekvens av tillstånd; visar fördelningen av föregående och nästa tillstånd som har lett till eller har följt av sekvensen.

Följande fråga visar vilka händelse inträffar före och efter alla storm-händelser i 2007.

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnelsequencecompletion-plugin"></a>funnel_sequence_completion plugin-programmet

[**plugin-programmet för funnel_sequence_completion**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): beräknar tratten för slutförda sekvens steg inom olika tidsperioder.

Följande fråga kontrollerar slutförande tratten ordningen: `Hail -> Tornado -> Thunderstorm -> Wind` i ”övergripande” gånger på en timme, fyra timmar och en dag (`[1h, 4h, 1d]`).

**\[**[**Klicka om du vill köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

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

Det här avsnittet beskriver [ **functions**](https://docs.microsoft.com/azure/kusto/query/functions): återanvändbara frågor som lagras på servern. Functions kan anropas av frågor och andra funktioner (rekursiva funktioner inte stöds).

> [!NOTE]
> Du kan inte skapa funktioner på Hjälp-kluster, vilket är skrivskyddad. Använd dina egna testkluster för den här delen.

I följande exempel skapas en funktion som tar en Tillståndsnamn (`MyState`) som ett argument.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

I följande exempel anropar en funktion som hämtar data för tillstånd Texas.

```Kusto
MyFunction ("Texas")
| summarize count()
```

I följande exempel tar bort den funktion som har skapats i det första steget.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Nästa steg

[Språkreferens för Kusto-fråga](https://aka.ms/kustolangref)
