---
title: Komma igång med loggfrågor i Azure Monitor | Microsoft-dokument
description: Den här artikeln innehåller en självstudiekurs för att komma igång med att skriva loggfrågor i Azure Monitor.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: f56abe2bf6ccea1f55f9b3fe94b75016d449b46b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77670187"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Komma igång med loggfrågor i Azure Monitor

> [!NOTE]
> Du kan arbeta igenom den här övningen i din egen miljö om du samlar in data från minst en virtuell dator. Om inte sedan använda vår [Demo-miljö](https://portal.loganalytics.io/demo), som innehåller massor av exempeldata.

I den här självstudien får du lära dig att skriva loggfrågor i Azure Monitor. Det kommer att lära dig att:

- Förstå frågestruktur
- Sortera frågeresultat
- Filtrera frågeresultat
- Ange ett tidsintervall
- Välj vilka fält som ska inkluderas i resultaten
- Definiera och använda anpassade fält
- Aggregera och gruppresultat

En självstudiekurs om hur du använder Logganalys i Azure-portalen finns i [Komma igång med Azure Monitor Log Analytics](get-started-portal.md).<br>
Mer information om loggfrågor i Azure Monitor finns i [Översikt över loggfrågor i Azure Monitor](log-query-overview.md).

Följ med en videoversion av den här guiden nedan:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Skriva en ny fråga
Frågor kan börja med antingen ett *search* tabellnamn eller sökkommandot. Du bör börja med ett tabellnamn, eftersom det definierar ett tydligt omfång för frågan och förbättrar både frågeprestanda och relevans för resultaten.

> [!NOTE]
> Frågespråket Kusto som används i Azure Monitor är skiftlägeskänsligt. Språkets nyckelord skrivs vanligtvis med gemener. När du använder namn på tabeller eller kolumner i en fråga måste du använda rätt skiftläge, som visas i schemafönstret.

### <a name="table-based-queries"></a>Tabellbaserade frågor
Azure Monitor organiserar loggdata i tabeller, som var och en består av flera kolumner. Alla tabeller och kolumner visas i schemafönstret i Logganalys i Analytics-portalen. Identifiera en tabell som du är intresserad av och ta sedan en titt på lite data:

```Kusto
SecurityEvent
| take 10
```

Frågan som visas ovan returnerar 10 resultat från tabellen *SecurityEvent,* utan någon specifik ordning. Detta är ett mycket vanligt sätt att ta en titt på ett bord och förstå dess struktur och innehåll. Låt oss undersöka hur det är byggt:

* Frågan börjar med tabellnamnet *SecurityEvent* - den här delen definierar frågans omfattning.
* Pipe -tecknet (|) separerar kommandon, så utdata från det första i indata för följande kommando. Du kan lägga till valfritt antal rörelement.
* Efter pipe är **take** take-kommandot, som returnerar ett visst antal godtyckliga poster från tabellen.

Vi skulle faktiskt kunna köra `| take 10` frågan även utan att lägga till - det skulle fortfarande vara giltigt, men det kan returnera upp till 10.000 resultat.

### <a name="search-queries"></a>Sökfrågor
Sökfrågorna är mindre strukturerade och i allmänhet mer lämpade för att hitta poster som innehåller ett visst värde i någon av deras kolumner:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Den här frågan söker i tabellen *SecurityEvent* efter poster som innehåller frasen "Kryptografisk". Av dessa poster returneras 10 poster och visas. Om vi utelämnar delen `in (SecurityEvent)` `search "Cryptographic"`och bara kör, kommer sökningen att gå över *alla* tabeller, vilket skulle ta längre tid och vara mindre effektiv.

> [!WARNING]
> Sökfrågor är vanligtvis långsammare än tabellbaserade frågor eftersom de måste bearbeta mer data. 

## <a name="sort-and-top"></a>Sortera och överst
Ta **take** är användbart för att få några poster, men resultaten väljs och visas inte i någon särskild ordning. Om du vill hämta en ordnad vy kan du **sortera efter** önskad kolumn:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Det kan ge för många resultat dock och kan också ta lite tid. Ovanstående fråga sorterar *hela* SecurityEvent-tabellen efter kolumnen TimeGenerated. Analytics-portalen begränsar sedan visningen så att endast 10 000 poster visas. Detta tillvägagångssätt är naturligtvis inte optimalt.

Det bästa sättet att få bara de senaste 10 posterna är att använda **toppen**, som sorterar hela tabellen på serversidan och sedan returnerar de översta posterna:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Fallande är standardsorteringsordningen, så vi utelämnar vanligtvis **argumentet desc.** Utdata kommer att se ut så här:

![Topp 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Var: filtrering på ett villkor
Filter, som anges med namnet, filtrerar data efter ett visst villkor. Detta är det vanligaste sättet att begränsa frågeresultat till relevant information.

Om du vill lägga till ett filter i en fråga använder du den **där** operatorn följt av ett eller flera villkor. Följande fråga returnerar till exempel endast *SecurityEvent-poster* där _nivå_ är lika med _8:_

```Kusto
SecurityEvent
| where Level == 8
```

När du skriver filtervillkor kan du använda följande uttryck:

| Uttryck | Beskrivning | Exempel |
|:---|:---|:---|
| == | Kontrollera jämställdhet<br>(skiftlägeskänsligt) | `Level == 8` |
| =~ | Kontrollera jämställdhet<br>(skiftlägesokänsligt) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Kontrollera ojämlikhet<br>(båda uttrycken är identiska) | `Level != 4` |
| *och*, *eller* | Krävs mellan villkor| `Level == 16 or CommandLine != ""` |

Om du vill filtrera efter flera villkor kan du antingen använda **och:**

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

eller rörmultna **där** element en efter en:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Värden kan ha olika typer, så du kan behöva casta dem för att utföra jämförelser på rätt typ. Kolumnen SecurityEvent *Level* är till exempel av typen Sträng, så du måste casta den till en numerisk typ, till exempel *int* eller *long*, innan du kan använda numeriska operatorer på den:`SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Ange ett tidsintervall

### <a name="time-picker"></a>Tidplockare
Tidsväljaren är bredvid knappen Kör och anger att vi bara frågar efter poster från de senaste 24 timmarna. Det här är standardtidsintervallet som tillämpas på alla frågor. Om du bara vill hämta poster från den senaste timmen väljer du _Förra timmen_ och kör frågan igen.

![Tidväljaren](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Tidsfilter i fråga
Du kan också definiera ditt eget tidsintervall genom att lägga till ett tidsfilter i frågan. Det är bäst att placera tidsfiltret direkt efter tabellnamnet: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

I ovanstående tidsfilter `ago(30m)` betyder "30 minuter sedan" så den här frågan returnerar bara poster från de senaste 30 minuterna. Andra tidsenheter inkluderar dagar (2d), minuter (25m) och sekunder (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt och utöka: markera och beräkna kolumner
Använd **projekt** för att välja specifika kolumner som ska inkluderas i resultaten:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

I föregående exempel genereras den här utdatan:

![Frågeprojektresultat](media/get-started-queries/project.png)

Du kan också använda **projekt** för att byta namn på kolumner och definiera nya. I följande exempel används projekt för att göra följande:

* Markera endast de ursprungliga kolumnerna *Computer* och *TimeGenerated.*
* Byt namn på kolumnen *Aktivitet* till *EventDetails*.
* Skapa en ny kolumn med namnet *EventCode*. Funktionen **substring()** används för att bara hämta de fyra första tecknen från fältet Aktivitet.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**utöka** behåller alla ursprungliga kolumner i resultatuppsättningen och definierar ytterligare. Följande fråga använder **utöka** för att lägga till *kolumnen EventCode.* Observera att den här kolumnen kanske inte visas i slutet av tabellen resulterar i vilket fall du skulle behöva expandera information om en post för att visa den.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Sammanfattning: aggregerade grupper av rader
Använd **sammanfattning** för att identifiera grupper av poster, enligt en eller flera kolumner, och tillämpa aggregeringar på dem. Den vanligaste användningen av **summarize** är *antal*, som returnerar antalet resultat i varje grupp.

Följande fråga granskar alla *Perf-poster* från den senaste timmen, grupperar dem efter *ObjectName*och räknar posterna i varje grupp: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Ibland är det vettigt att definiera grupper efter flera dimensioner. Varje unik kombination av dessa värden definierar en separat grupp:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

En annan vanlig användning är att utföra matematiska eller statistiska beräkningar på varje grupp. Följande beräknar till exempel det genomsnittliga *motvärde för* varje dator:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Tyvärr är resultatet av den här frågan meningslösa eftersom vi blandade ihop olika prestandaräknare. För att göra detta mer meningsfullt, bör vi beräkna genomsnittet separat för varje kombination av *CounterName* och *dator:*

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Summera med en tidskolumn
Gruppering av resultat kan också baseras på en tidskolumn eller ett annat kontinuerligt värde. Bara sammanfatta `by TimeGenerated` men skulle skapa grupper för varje millisekund över tidsintervallet, eftersom dessa är unika värden. 

Om du vill skapa grupper baserat på kontinuerliga värden är det bäst att dela upp intervallet i hanterbara enheter med **lagerplats**. Följande fråga analyserar *Perf-poster* som mäter ledigt minne (*Tillgängligt MBytes*) på en viss dator. Det beräknar det genomsnittliga värdet för varje 1-timmarsperiod under de senaste 7 dagarna:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Om du vill göra utdata tydligare väljer du att visa den som ett tidsschema som visar det tillgängliga minnet över tid:

![Fråga minne över tid](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du använder strängdata i en loggfråga med [Arbeta med strängar i Azure Monitor-loggfrågor](string-operations.md).
- Läs mer om att samla in data i en loggfråga med [avancerade aggregeringar i Azure Monitor-loggfrågor](advanced-aggregations.md).
- Lär dig hur du ansluter data från flera tabeller med [loggfrågor för Kopplingar i Azure Monitor](joins.md).
- Hämta dokumentation om hela Kusto-frågespråket i [KQL-språkreferensen](/azure/kusto/query/).
