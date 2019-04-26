---
title: Kom igång med loggfrågor i Azure Monitor | Microsoft Docs
description: Den här artikeln innehåller en självstudie för att komma igång skriva loggfrågor i Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.openlocfilehash: 8c3ef3f115d37400eb72fdaca5df4f326382df5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519986"
---
# <a name="get-started-with-azure-monitor-log-queries"></a>Kom igång med Azure Monitor log-frågor


> [!NOTE]
> Bör du genomföra [Kom igång med Azure Monitor Log Analytics](get-started-portal.md) innan den här kursen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Logga in frågor i den här självstudien lär du dig att skriva Azure Monitor. Det får du lära dig hur du:

- Förstå frågor-struktur
- Sortera frågeresultaten
- Filtrera frågans resultat
- Ange ett tidsintervall
- Välj vilka fält som ska inkluderas i resultaten
- Definiera och Använd anpassade fält
- Sammanställd och gruppera resultat


## <a name="writing-a-new-query"></a>Skriver en ny fråga
Frågor kan börja med antingen ett tabellnamn eller *search* kommando. Du bör börja med ett tabellnamn eftersom den definierar en tydlig omfattning för frågan och förbättrar både frågeprestanda och relevans resultat.

> [!NOTE]
> Kusto-frågespråk som används av Azure Monitor är skiftlägeskänsligt. Nyckelord är vanligen skrivna gemener. När du använder namn på tabeller eller kolumner i en fråga, se till att ha rätt skiftläge, som visas i fönstret schemat.

### <a name="table-based-queries"></a>Tabell-baserade frågor
Azure Monitor organiserar loggdata i tabeller, var och en består av flera kolumner. Alla tabeller och kolumner som visas i fönstret schemat i Log Analytics i Analytics-portalen. Identifiera en tabell att du är intresserad av och sedan ta en titt på en del data:

```Kusto
SecurityEvent
| take 10
```

Returnerar frågan ovan 10 resultat från den *SecurityEvent* tabell, utan någon särskild ordning. Det här är ett mycket vanligt sätt att ta en titt på en tabell och förstå dess struktur och innehåll. Låt oss nu undersöka hur den är uppbyggd:

* Frågan börjar med tabellnamnet *SecurityEvent* – den här delen definierar omfattningen av frågan.
* Vertikalstreck (|) avgränsar kommandon, så resultatet av den första funktionen i indata för kommandot. Du kan lägga till valfritt antal via rörledningar element.
* Efter en pipe är den **ta** kommandot, som returnerar ett visst antal godtyckliga från tabellen.

Det gick att köra frågan faktiskt även utan att lägga till `| take 10` – som fortfarande är giltiga, men det kan gå tillbaka upp till 10 000 resultat.

### <a name="search-queries"></a>Sökfrågor
Sökfrågor är mindre strukturerad och allmänt mer lämpliga för att söka efter poster som innehåller ett specifikt värde i någon av sina kolumner:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Den här frågan söker den *SecurityEvent* tabellen för poster som innehåller frasen ”kryptografiska”. Dessa poster kommer 10 poster returneras och visas. Om vi tar bort den `in (SecurityEvent)` delvis och bara köra `search "Cryptographic"`, sökningen kommer att överskrida *alla* tabeller, som skulle ta längre tid och vara mindre effektiv.

> [!NOTE]
> Som standard ett tidsintervall för _senaste 24 timmarna_ har angetts. Om du vill använda ett annat intervall använder du tidsväljare (bredvid den *Gå* knappen) eller lägga till en explicit tid filter för datumintervall i frågan.

## <a name="sort-and-top"></a>Sortera och upp
Medan **ta** är användbar för att hämta några poster, resultatet är valt och visas i någon särskild ordning. Om du vill ha en ordnad vy, kan du **sortera** efter kolumnen önskade:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Som kan returnera för många resultat om och kan ta lite tid. Frågan ovan sorterar *hela* SecurityEvent tabellen efter kolumnen TimeGenerated. Analytics-portalen begränsar sedan skärmen för att visa endast 10 000 poster. Den här metoden är förstås inte optimalt.

Det bästa sättet att få endast de senaste 10 posterna är att använda **upp**, som sorterar hela tabellen på serversidan och returnerar de översta posterna:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Fallande är standard sorteringsordning, så vi normalt utelämna den **desc** argumentet. Utdata ser ut så här:

![Översta 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Där: filtrering på ett villkor
Filter, som anges av deras namn, filtrera informationen efter ett visst villkor. Det här är det vanligaste sättet att begränsa resultatet av frågan till relevant information.

Lägg till ett filter till en fråga genom att använda den **där** operatorn följt av ett eller flera villkor. Till exempel följande fråga returnerar endast *SecurityEvent* poster där _nivå_ är lika med _8_:

```Kusto
SecurityEvent
| where Level == 8
```

När du skriver filtervillkor ska använda du följande uttryck:

| Uttryck | Beskrivning | Exempel |
|:---|:---|:---|
| == | Kontrollera likhet<br>(skiftlägeskänsligt) | `Level == 8` |
| =~ | Kontrollera likhet<br>(skiftlägesokänsligt) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Kontrollera ojämlikhet<br>(båda uttrycken är identiska) | `Level != 4` |
| *och*, *eller* | Krävs mellan villkor| `Level == 16 or CommandLine != ""` |

Om du vill filtrera efter flera villkor, du kan använda **och**:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

eller skicka flera **där** element som efter varandra:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Värden kan ha olika typer, så du kan behöva konvertera dem för att utföra jämförelse på rätt typ. Till exempel SecurityEvent *nivå* kolumn är av typen String, så måste du skicka den till en numerisk typ som *int* eller *lång*, innan du kan använda numeriska operatorer på den: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Ange ett tidsintervall

### <a name="time-picker"></a>Tidsväljare
Tidsväljare är bredvid knappen Kör och visar vi frågar endast poster från de senaste 24 timmarna. Det här är tidsintervallet standard tillämpas på alla frågor. Om du vill hämta bara poster från den senaste timmen, Välj _senaste timmen_ och kör frågan igen.

![Tidväljaren](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Tidsfiltret i frågan
Du kan också definiera egna tidsintervall genom att lägga till ett tidsfilter i frågan. Det är bäst att placera tidsfiltret omedelbart efter tabellnamnet: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

I ovanstående tidsfiltret `ago(30m)` innebär ”30 minuter tidigare” så att den här frågan returnerar bara poster från de senaste 30 minuterna. Andra tidsenheter inkluderar dagar (2d), minuter (25m) och sekunder (10-tal).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt och utöka: Välj och compute kolumner
Använd **projekt** att markera specifika kolumner ska inkluderas i resultatet:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

I föregående exempel genererar dessa utdata:

![Frågeresultat för projektet](media/get-started-queries/project.png)

Du kan också använda **projekt** att byta namn på kolumner och definiera nya. I följande exempel används projektet för att göra följande:

* Välj endast det *datorn* och *TimeGenerated* ursprungliga kolumner.
* Byt namn på den *aktivitet* kolumnen till *EventDetails*.
* Skapa en ny kolumn med namnet *EventCode*. Den **substring()** funktion används för att hämta endast de första fyra tecken från fältet aktivitet.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**utöka** behålls alla ursprungliga kolumner i resultatuppsättningen och definierar fler håller på att. Följande fråga använder **utöka** att lägga till en *lokal tid* kolumn som innehåller ett lokaliserat TimeGenerated-värde.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend localtime = TimeGenerated-8h
```

## <a name="summarize-aggregate-groups-of-rows"></a>Sammanfattningsvis: sammanställd grupper av rader
Använd **sammanfatta** identifiera grupper av poster, enligt en eller flera kolumner och tillämpliga aggregeringar. Den vanligaste tillämpningen av **sammanfatta** är *antal*, vilket returnerar antalet resultat i varje grupp.

Följande fråga granskar alla *Perf* poster från den senaste timmen, grupperas av *ObjectName*, och räknar posterna i varje grupp: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Ibland bra det att definiera grupper med flera dimensioner. Varje unik kombination av dessa värden definierar en separat grupp:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Ett annat vanligt användningsområde är att utföra matematiska eller statistiska beräkningar på varje grupp. Till exempel följande beräknar medelvärdet *CounterValue* för varje dator:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Resultatet av den här frågan är tyvärr meningslöst eftersom vi blandat ihop olika prestandaräknare. Om du vill göra detta mer beskrivande, man kan beräkna medelvärdet separat för varje kombination av *CounterName* och *datorn*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Summera efter en time-kolumn
Gruppera resultat kan också baseras på en time-kolumn eller en annan kontinuerligt mervärde. Helt enkelt sammanfatta `by TimeGenerated` dock skulle skapa grupper för varje enskild millisekund under tidsintervallet, eftersom dessa är unika värden. 

För att skapa grupper baserat på kontinuerlig värden, det är bäst att bryta intervallet i hanterbara enheter med hjälp av **bin**. Följande fråga analyserar *Perf* poster som mäter ledigt minne (*tillgängliga megabyte*) på en specifik dator. Den beräknar medelvärdet för varje om 1 timme under de senaste 7 dagarna:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Om du vill göra den tydligare utdatan väljer du för att visa den som ett tidsdiagram som visar mängden tillgängligt minne över tid:

![Fråga minne över tid](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [skriva sökfrågor](search-queries.md)
