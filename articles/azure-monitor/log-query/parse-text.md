---
title: Parsa textdata i Azure Log Analytics | Microsoft Docs
description: Beskriver olika alternativ för parsning data i Log Analytics-poster när data matas in och när de hämtas i en fråga, jämföra de relativa fördelarna för var och en.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: bwren
ms.openlocfilehash: 03268981bcfe90f14f35c74effe5799dd31e4ac0
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185791"
---
# <a name="parse-text-data-in-log-analytics"></a>Parsa textdata i Log Analytics
Vissa data som samlas in av Log Analytics innehåller flera typer av information i en enskild egenskap. Dela dessa data i flera egenskaper gör det enklare att använda i frågor. Ett vanligt exempel är en [anpassad logg](../../log-analytics/log-analytics-data-sources-custom-logs.md) som samlar in en hel loggpost med flera värden i en enskild egenskap. Du kan söka genom att skapa olika egenskaper för de olika värdena och sammanställda på var och en.

Den här artikeln beskrivs olika alternativ för att parsa data i Log Analytics-poster när data matas in och när de hämtas i en fråga, jämföra de relativa fördelarna för var och en.


## <a name="parsing-methods"></a>Parsning metoder
Du kan parsa data antingen vid inmatning tidpunkt när data har samlats in eller fråga när du analysera data med en fråga. Varje strategi har unika fördelar som beskrivs nedan.

### <a name="parse-data-at-collection-time"></a>Tolka data när samlingen
När du analyserar data samlingen när du konfigurerar [anpassade fält](../../log-analytics/log-analytics-custom-fields.md) som skapar nya egenskaper i tabellen. Frågor inte innehåller någon logik för parsning och bara använda de här egenskaperna som andra fält i tabellen.

Fördelar med att den här metoden är följande:

- Enklare att fråga insamlade data eftersom du inte behöver inkludera parsa kommandon i frågan.
- Bättre frågeprestanda eftersom frågan inte behöver utföra parsning.
 
Nackdelar med att den här metoden är följande:

- Måste definieras i förväg. Det går inte att ta med data som redan har samlats in.
- Om du ändrar parsning logiken det endast att tillämpas för nya data.
- Färre tolkning än tillgängliga i frågor.
- Ökar svarstid tid för att samla in data.
- Fel kan vara svårt att hantera.


### <a name="parse-data-at-query-time"></a>Tolka data när en fråga körs
När du analyserar data i databasfrågor inkludera logik i din fråga för att tolka data i flera fält. Själva faktiska tabellen ändras inte.

Fördelar med att den här metoden är följande:

- Gäller för alla data, inklusive data som redan har samlats in.
- Ändringar i logik kan tillämpas direkt på alla data.
- Flexibel parsning alternativ inklusive fördefinierade logik för viss datastrukturer.
 
Nackdelar med att den här metoden är följande:

- Kräver mer komplexa frågor. Detta kan undvikas genom att använda [functions för att simulera en tabell](#Use-function-to-simulate-a-table).
- Måste replikera parsning logiken i flera frågor. Dela logik via funktioner.
- Kan skapa overhead när du kör komplex logik mot mycket stora post skalningsuppsättningar (miljarder poster).

## <a name="parse-data-as-its-collected"></a>Tolka data eftersom den har samlats in
Se [skapa anpassade fält i Log Analytics](../../log-analytics/log-analytics-custom-fields.md) mer information om parsning data eftersom den har samlats in. Detta skapar anpassade egenskaper i den tabell som kan användas av frågor, precis som någon annan egenskap.

## <a name="parse-data-in-query-using-patterns"></a>Dela upp data från frågan med mönster
När de data du vill parsa kan identifieras genom ett mönster som upprepas över poster, du kan använda olika operatorer i den [Datautforskaren frågespråk](/azure/kusto/query/) att extrahera viss typ av data till en eller flera nya egenskaper.

### <a name="simple-text-patterns"></a>Enkel textmönster

Använd den [parsa](/azure/kusto/query/parseoperator) operator i frågan för att skapa en eller flera anpassade egenskaper som kan extraheras från ett stränguttryck. Anger mönstret som ska identifieras och namnen på egenskaperna för att skapa. Detta är särskilt användbart för data med nyckel / värde-strängar med ett formulär som _nyckel = värde_.

Överväg att en anpassad logg med data i följande format.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Följande fråga skulle parsa dessa data till enskilda egenskaper. Raden med _projekt_ läggs till endast returnera beräknade egenskaper och inte _\data_, vilket är den enda egenskapen hela posten från anpassad logg.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Följande är ett annat exempel som delar upp användarnamnet för ett UPN i den _AzureActivity_ tabell.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Reguljära uttryck
Om dina data kan identifieras med ett reguljärt uttryck, kan du använda [funktioner med reguljära uttryck](/azure/kusto/query/re2) att extrahera enskilda värden. I följande exempel används [extrahera](/kusto/query/extractfunction) kan dela upp den _UPN_ från _AzureActivity_ registrerar och sedan returnera specifika användare.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Om du vill aktivera effektiv parsning i stor skala Log Analytics använder re2 version av reguljära uttryck, vilket är liknande men inte är identiska med några av de andra varianterna för reguljärt uttryck. Referera till den [re2 uttryckssyntax](https://aka.ms/kql_re2syntax) mer information.


## <a name="parse-delimited-data-in-a-query"></a>Parsa avgränsad data i en fråga
Avgränsad data avgränsar fält med en vanliga tecken, till exempel ett kommatecken i en CSV-fil. Använd den [dela](/azure/kusto/query/splitfunction) funktionen att parsa avgränsad data med hjälp av en avgränsare som du anger. Du kan använda detta med [utöka](/azure/kusto/query/extendoperator) operatorn att returnera alla fält i data eller för att ange enskilda fält som ska ingå i utdata.

> [!NOTE]
> Eftersom delning returnerar ett dynamiskt objekt, kan resultaten behöva uttryckligen att omvandla till datatyper, till exempel sträng som ska användas i operatorer och filter.

Överväg att en anpassad logg med data i följande CSV-format.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Följande fråga skulle parsa informationen och summera efter två av de beräknade egenskaperna. Den första raden delar upp de _\data_ egenskapen till en strängmatris. Varje nästa rad ger ett namn till enskilda egenskaper och lägger till dem i utdata med funktioner för att konvertera dem till rätt datatyp.

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

## <a name="parse-predefined-structures-in-a-query"></a>Parsa fördefinierade strukturer i en fråga
Om dina data är formaterad med en känd struktur, du kan använda en av funktionerna i den [Datautforskaren frågespråk](/azure/kusto/query/) för parsning av fördefinierade strukturer:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [URL-frågan](/azure/kusto/query/parseurlqueryfunction)
- [Filsökväg](/azure/kusto/query/parsepathfunction)
- [Användaragent](/azure/kusto/query/parse-useragentfunction)
- [Versionssträng](/azure/kusto/query/parse-versionfunction)

Följande exempelfråga Parsar den _egenskaper_ i den _AzureActivity_ tabellen, vilket är strukturerade i JSON. Det sparar resultatet i en dynamisk egenskap som kallas _parsedProp_, som innehåller de enskilda namngivet värde i JSON. Dessa värden används för att filtrera och sammanfattar resultatet av frågan.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Dessa parsning funktioner kan vara processorintensiva, så att de bör endast användas när din fråga använder flera egenskaper från formaterade data. Annars blir enkla mönstermatchning bearbetning snabbare.

I följande exempel visar fördelningen av domänkontrollanten TGT förauktorisering typen. Typen finns bara i fältet EventData, som är en XML-sträng, men inga andra data från det här fältet krävs. I det här fallet [parsa](/azure/kusto/query/parseoperator) används för att hitta den nödvändiga typ av data.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Använd funktionen för att simulera en tabell
Du kan ha flera frågor som utför samma parsningen av en viss tabell. I det här fallet [skapa en funktion](functions.md) som returnerar tolkade data istället för att replikera parsning logiken i varje fråga. Du kan sedan använda funktionsalias i stället för den ursprungliga tabellen i andra frågor.

Överväg att kommaavgränsad anpassad logg-exemplet ovan. Skapa en funktion med följande fråga för att kunna använda tolkade data i flera frågor, och spara den med alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Du kan nu använda aliaset _MyCustomCSVLog_ i stället för faktiska tabellnamnet i frågor som liknar följande.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga frågor](log-query-overview.md) att analysera data som samlas in från datakällor och lösningar.