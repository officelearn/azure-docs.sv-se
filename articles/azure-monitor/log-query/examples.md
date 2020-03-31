---
title: Exempel på azure monitor-loggfråga | Microsoft-dokument
description: Exempel på loggfrågor i Azure Monitor med hjälp av frågespråket Kusto.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 18cd74ac9298b7dd058de2b224f677ec0d8f2d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480291"
---
# <a name="azure-monitor-log-query-examples"></a>Exempel på Azure Monitor-loggfråga
Den här artikeln innehåller olika exempel på [frågor](log-query-overview.md) som använder [Kusto-frågespråket](/azure/kusto/query/) för att hämta olika typer av loggdata från Azure Monitor. Olika metoder används för att konsolidera och analysera data, så du kan använda dessa exempel för att identifiera olika strategier som du kan använda för dina egna behov.  

Se [kustospråkreferensen](https://docs.microsoft.com/azure/kusto/query/) för information om de olika nyckelord som används i dessa exempel. Gå igenom en [lektion om hur du skapar frågor](get-started-queries.md) om du inte har tidigare till Azure Monitor.

## <a name="events"></a>Händelser

### <a name="search-application-level-events-described-as-cryptographic"></a>Sök efter händelser på programnivå som beskrivs som "Kryptografiska"
I det här exemplet söker du i tabellen **Händelser** efter poster där **EventLog** är _Application_ och **RenderedDescription** innehåller _kryptografisk_. Inkluderar poster från de senaste 24 timmarna.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Sökhändelser relaterade till unmarshaling
Sök tabeller **Händelse** och **SecurityEvents** för poster som nämner _ta bort teckning_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Pulsslag

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Diagram över en veckovy över antalet datorer som skickar data

I följande exempel kartläggs antalet olika datorer som skickade pulsslag varje vecka.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Hitta inaktuella datorer

I följande exempel hittas datorer som var aktiva under den senaste dagen men inte skickat pulsslag under den senaste timmen.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Hämta den senaste pulsslagsposten per dator-IP

I det här exemplet returneras den senaste pulsslagsposten för varje dator-IP.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Matcha skyddade statusposter med pulsslagsposter

I det här exemplet hittas relaterade skyddsstatusposter och pulsslagsposter, matchade på både dator och tid.
Observera att tidsfältet avrundas till närmaste minut. Vi använde beräkning av körningslagerplats för att göra det: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Servertillgänglighet

Beräkna servertillgänglighetshastigheten baserat på pulsslagsposter. Tillgänglighet definieras som "minst 1 hjärtslag per timme".
Så om en server var tillgänglig 98 av 100 timmar är tillgänglighetspriset 98%.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Flera datatyper

### <a name="chart-the-record-count-per-table"></a>Kartlägga antalet post per tabell
I följande exempel samlas alla poster i alla tabeller från de senaste fem timmarna och hur många poster som fanns i varje tabell. Resultaten visas i en tidsschema.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Räkna alla loggar som samlats in under den senaste timmen efter typ
I följande exempel söker allt som rapporterats under den senaste timmen och posterna för varje tabell räknas efter **typ**. Resultaten visas i ett stapeldiagram.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Räkna Azure-diagnostikposter per kategori
I det här exemplet räknas alla Azure-diagnostikposter för varje unik kategori.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Få en slumpmässig post för varje unik kategori
Det här exemplet får en enda slumpmässig Azure-diagnostikpost för varje unik kategori.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Få den senaste posten per kategori
Det här exemplet hämtar den senaste Azure-diagnostikposten i varje unik kategori.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Nätverksövervakning

### <a name="computers-with-unhealthy-latency"></a>Datorer med felfördröjning
I det här exemplet skapas en lista över olika datorer med felfördröjning.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Prestanda

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Gå med i datorns perf-poster för att korrelera minne och CPU
Det här exemplet korrelerar en viss dators **perf-poster** och skapar två tidsdiagram, den genomsnittliga processorn och maximalt minne.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Diagram för perf CPU-utnyttjande per dator
I det här exemplet beräknas och diagrams CPU-användningen av datorer som börjar med _Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Skyddsstatus

### <a name="computers-with-non-reporting-protection-status-duration"></a>Datorer med statusvaraktighet för icke-rapporteringsskydd
I det här exemplet visas datorer som hade skyddsstatus _som inte rapporterade_ och hur länge de befann sig i den här statusen.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Matcha skyddade statusposter med pulsslagsposter
I det här exemplet hittas relaterade skyddsstatusposter och pulsslagsposter som matchas både på datorn och tiden.
Tidsfältet avrundas till närmaste minut med **lagerplats**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Säkerhetsposter

### <a name="count-security-events-by-activity-id"></a>Räkna säkerhetshändelser efter aktivitets-ID


Det här exemplet är beroende av den fasta\>-\<\>strukturen i kolumnen **Aktivitet:** \<ID-namn .
Det tolkar **aktivitetsvärdet** i två nya kolumner och räknar förekomsten av varje **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Räkna säkerhetshändelser relaterade till behörigheter
I det här exemplet visas antalet **securityEvent-poster,** där kolumnen **Aktivitet** innehåller hela termen _Behörigheter_. Frågan gäller poster som skapats under de senaste 30 minuterna.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Hitta konton som inte kunde logga in från datorer med en säkerhetsidentifiering
Det här exemplet söker efter och räknar konton som inte kunde logga in från datorer där vi identifierar en säkerhetsidentifiering.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Är mina säkerhetsdata tillgängliga?
Starta datautforskning börjar ofta med datatillgänglighetskontroll. I det här exemplet visas antalet **SecurityEvent-poster** under de senaste 30 minuterna.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Tolka aktivitetsnamn och ID
De två exemplen nedan är beroende av den\>-\<\>fasta strukturen i kolumnen **Aktivitet:** \<ID-namn . I det första exemplet används **parsaoperatorn** för att tilldela värden till två nya kolumner: **activityID** och **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

I det här exemplet används **den delade** operatorn för att skapa en matris med separata värden
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Explicita autentiseringsuppgifter processer
I följande exempel visas ett cirkeldiagram över processer som använde explicita autentiseringsuppgifter under den senaste veckan

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Toppgående processer

I följande exempel visas en tidsrad för aktivitet för de fem vanligaste processerna under de senaste tre dagarna.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Hitta upprepade misslyckade inloggningsförsök av samma konto från olika IPs

I följande exempel hittas misslyckade inloggningsförsök av samma konto från mer än fem olika IPs under de senaste sex timmarna.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Hitta användarkonton som inte kunde logga in 
I följande exempel identifieras användarkonton som inte loggade in mer än fem gånger under den senaste dagen och när de senast försökte logga in.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Använda **gå med**, och **låt** uttalanden vi kan kontrollera om samma misstänkta konton senare kunde logga in framgångsrikt.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Användning

Datatypen `Usage` kan användas för att spåra den intjesterade datavolymen efter lösning eller datatyp. Det finns andra tekniker för att studera intjesterade datavolymer efter [dator](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-computer) eller [Azure-prenumeration, resursgrupp eller resurs](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-azure-resource-resource-group-or-subscription).

#### <a name="data-volume-by-solution"></a>Datavolym per lösning

Frågan som används för att visa den fakturerbara datavolymen efter lösning under den senaste månaden (exklusive den sista deldagen) är:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Observera att `where IsBillable = true` satsen filtrerar bort datatyper från vissa lösningar för vilka det inte finns någon inmatningsavgift för.  Även satsen `TimeGenerated` med är bara för att säkerställa att frågeupplevelsen i Azure-portalen kommer att se tillbaka utöver standard 24 timmar. När du använder datatypen Användning `StartTime` och `EndTime` representerar de tidssegment som resultaten presenteras för. 

#### <a name="data-volume-by-type"></a>Datavolym efter typ

Du kan öka detaljnivån ytterligare om du vill se datatrender för datatyp:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Eller om du vill visa en tabell efter lösning och typ för den senaste månaden,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> Vissa fält i datatypen Användning, medan de fortfarande är i schemat, har inaktuellt och kommer att fyllas i deras värden inte längre. Dessa är **dator-** samt fält relaterade till intag (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla,** **BatchesCapped** och **AverageProcessingTimeMs**.

## <a name="updates"></a>Uppdateringar

### <a name="computers-still-missing-updates"></a>Datorer saknas fortfarande uppdateringar
I det här exemplet visas en lista över datorer som saknade en eller flera viktiga uppdateringar för några dagar sedan och som fortfarande saknar uppdateringar.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Nästa steg

- Se [kustospråkreferensen](/azure/kusto/query) för information om språket.
- Gå igenom en [lektion om att skriva loggfrågor i Azure Monitor](get-started-queries.md).