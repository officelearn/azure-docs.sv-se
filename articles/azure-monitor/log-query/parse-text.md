---
title: Tolka textdata i Azure Monitor-loggar | Microsoft-dokument
description: Beskriver olika alternativ för tolkning av loggdata i Azure Monitor-poster när data intas och när de hämtas i en fråga, jämföra de relativa fördelarna för varje.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: d7a37d51c411488231205fd036f9a287f5206ce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672454"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Tolka textdata i Azure Monitor-loggar
Vissa loggdata som samlas in av Azure Monitor innehåller flera bitar av information i en enda egenskap. Att tolka dessa data i flera egenskaper gör det enklare att använda i frågor. Ett vanligt exempel är en [anpassad logg](../../log-analytics/log-analytics-data-sources-custom-logs.md) som samlar in en hel loggpost med flera värden i en enda egenskap. Genom att skapa separata egenskaper för de olika värdena kan du söka och aggregera på varje.

I den här artikeln beskrivs olika alternativ för att tolka loggdata i Azure Monitor när data förtärs och när de hämtas i en fråga, jämföra de relativa fördelarna för varje.


## <a name="parsing-methods"></a>Tolkningsmetoder
Du kan tolka data antingen vid inmatningstid när data samlas in eller vid frågetillfället när du analyserar data med en fråga. Varje strategi har unika fördelar som beskrivs nedan.

### <a name="parse-data-at-collection-time"></a>Tolka data vid insamling
När du tolkar data vid insamlingstillfället konfigurerar du [anpassade fält](../../log-analytics/log-analytics-custom-fields.md) som skapar nya egenskaper i tabellen. Frågor behöver inte innehålla någon tolkningslogik och helt enkelt använda dessa egenskaper som alla andra fält i tabellen.

Fördelar med den här metoden är följande:

- Enklare att fråga de insamlade data eftersom du inte behöver inkludera tolkningskommandon i frågan.
- Bättre frågeprestanda eftersom frågan inte behöver utföra tolkning.
 
Nackdelar med denna metod inkluderar följande:

- Måste definieras i förväg. Det går inte att inkludera data som redan har samlats in.
- Om du ändrar tolkningslogiken gäller den bara för nya data.
- Färre tolkningsalternativ än tillgängliga i frågor.
- Ökar svarstiden för insamling av data.
- Fel kan vara svåra att hantera.


### <a name="parse-data-at-query-time"></a>Tolka data vid frågetid
När du tolkar data vid frågetillfället tar du med logik i frågan för att tolka data i flera fält. Själva tabellen ändras inte.

Fördelar med den här metoden är följande:

- Gäller alla data, inklusive data som redan har samlats in.
- Ändringar i logik kan tillämpas omedelbart på alla data.
- Flexibla tolkningsalternativ, inklusive fördefinierad logik för vissa datastrukturer.
 
Nackdelar med denna metod inkluderar följande:

- Kräver mer komplexa frågor. Detta kan mildras genom att använda [funktioner för att simulera en tabell](#use-function-to-simulate-a-table).
- Måste replikera tolkningslogik i flera frågor. Kan dela viss logik genom funktioner.
- Kan skapa omkostnader när du kör komplex logik mot mycket stora postuppsättningar (miljarder poster).

## <a name="parse-data-as-its-collected"></a>Tolka data när de samlas in
Se [Skapa anpassade fält i Azure Monitor](../platform/custom-fields.md) för information om tolkningsdata när de samlas in. Detta skapar anpassade egenskaper i tabellen som kan användas av frågor precis som alla andra egenskaper.

## <a name="parse-data-in-query-using-patterns"></a>Tolka data i fråga med hjälp av mönster
När de data som du vill tolka kan identifieras med ett mönster som upprepas mellan poster kan du använda olika operatorer på [Frågespråket Kusto](/azure/kusto/query/) för att extrahera den specifika databiten till en eller flera nya egenskaper.

### <a name="simple-text-patterns"></a>Enkla textmönster

Använd [parsaoperatorn](/azure/kusto/query/parseoperator) i frågan för att skapa en eller flera anpassade egenskaper som kan extraheras från ett stränguttryck. Du anger det mönster som ska identifieras och namnen på de egenskaper som ska skapas. Detta är särskilt användbart för data med nyckelvärdesträngar med ett formulär som liknar _key=value_.

Överväg en anpassad logg med data i följande format.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Följande fråga skulle tolka dessa data i enskilda egenskaper. Raden med _projektet_ läggs till för att bara returnera de beräknade egenskaperna och inte _RawData_, som är den enda egenskapen som innehar hela transaktionen från den anpassade loggen.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Följande är ett annat exempel som bryter ut användarnamnet för ett UPN i tabellen _AzureActivity._

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Reguljära uttryck
Om dina data kan identifieras med ett reguljärt uttryck kan du använda [funktioner som använder reguljära uttryck](/azure/kusto/query/re2) för att extrahera enskilda värden. I följande exempel används [utdrag](/azure/kusto/query/extractfunction) för att bryta ut _UPN-fältet_ från _AzureActivity-poster_ och sedan returnera olika användare.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

För att möjliggöra effektiv tolkning i stor skala använder Azure Monitor re2-versionen av reguljära uttryck, som är liknande men inte identisk med några av de andra varianterna av reguljära uttryck. Mer information finns i syntaxen för [re2-uttryck.](https://aka.ms/kql_re2syntax)


## <a name="parse-delimited-data-in-a-query"></a>Tolka avgränsade data i en fråga
Avgränsade data separerar fält med ett vanligt tecken, till exempel ett kommatecken i en CSV-fil. Använd [delningsfunktionen](/azure/kusto/query/splitfunction) för att tolka avgränsade data med hjälp av en avgränsare som du anger. Du kan använda detta med [utöka operator](/azure/kusto/query/extendoperator) för att returnera alla fält i data eller för att ange enskilda fält som ska ingå i utdata.

> [!NOTE]
> Eftersom split returnerar ett dynamiskt objekt kan resultaten behöva uttryckligen kastas till datatyper som sträng som ska användas i operatorer och filter.

Överväg en anpassad logg med data i följande CSV-format.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Följande fråga skulle tolka dessa data och sammanfatta med två av de beräknade egenskaperna. Den första raden delar upp egenskapen _RawData_ i en strängmatris. Var och en av nästa rad ger ett namn till enskilda egenskaper och lägger till dem i utdata med hjälp av funktioner för att konvertera dem till lämplig datatyp.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Tolka fördefinierade strukturer i en fråga
Om dina data är formaterade i en känd struktur kan du kanske använda någon av funktionerna i [Kusto-frågespråket](/azure/kusto/query/) för att tolka fördefinierade strukturer:

- [Json](/azure/kusto/query/parsejsonfunction)
- [Xml](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [URL-fråga](/azure/kusto/query/parseurlqueryfunction)
- [Filsökväg](/azure/kusto/query/parsepathfunction)
- [Användaragent](/azure/kusto/query/parse-useragentfunction)
- [Sträng för version](/azure/kusto/query/parse-versionfunction)

Följande exempelfråga tolkar _egenskapsfältet_ i tabellen _AzureActivity,_ som är strukturerat i JSON. Resultaten sparas i en dynamisk egenskap som kallas _parsedProp_, som innehåller det enskilda namngivna värdet i JSON. Dessa värden används för att filtrera och sammanfatta frågeresultaten.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Dessa tolkningsfunktioner kan vara processorintensiva, så de bör endast användas när frågan använder flera egenskaper från formaterade data. Annars kommer enkel mönstermatchning bearbetning vara snabbare.

I följande exempel visas uppdelningen av domänkontrollanten TGT Preauth-typ. Typen finns bara i fältet EventData, som är en XML-sträng, men inga andra data från det här fältet behövs. I det här fallet används [parsa](/azure/kusto/query/parseoperator) för att välja ut den data som krävs.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Använda funktionen för att simulera en tabell
Du kan ha flera frågor som utför samma tolkning av en viss tabell. I det här fallet [skapar du en funktion](functions.md) som returnerar tolkade data i stället för att replikera tolkningslogiken i varje fråga. Du kan sedan använda funktionsaliaset i stället för den ursprungliga tabellen i andra frågor.

Tänk på det kommaavgränsade anpassade loggexempelt ovan. Skapa en funktion med följande fråga för att kunna använda tolkade data i flera frågor och spara den med alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Du kan nu använda alias _MyCustomCSVLog_ i stället för det faktiska tabellnamnet i frågor som följande.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggfrågor](log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar.
