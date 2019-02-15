---
title: Azure Monitor log-fråga-exempel | Microsoft Docs
description: Exempel på loggfrågor i Azure Monitor med Kusto-frågespråk.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/03/2018
ms.author: bwren
ms.openlocfilehash: c161a2fd8d1be670435d2b1a749749cea7d82bcf
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268455"
---
# <a name="azure-monitor-log-query-examples"></a>Azure Monitor log-fråga-exempel
Den här artikeln innehåller olika exempel på [frågor](log-query-overview.md) med hjälp av den [Kusto-frågespråket](/azure/kusto/query/) att hämta olika typer av loggdata från Azure Monitor. Olika metoder för att konsolidera och analysera data, så du kan använda de här exemplen för att identifiera olika strategier som du kan använda för dina egna behov.  

Se den [Kusto-Språkreferens](https://docs.microsoft.com/azure/kusto/query/) mer information om andra nyckelord som används i exemplen. Gå igenom en [lektion om hur du skapar frågor](get-started-queries.md) om du inte har använt Azure Monitor.

## <a name="events"></a>Händelser

### <a name="search-application-level-events-described-as-cryptographic"></a>Sök på programnivå händelser som kallas ”kryptografiska”
Det här exemplet söker igenom **tabellen över händelser** för poster där **Händelselogg** är _programmet_ och **utförd beskrivning** innehåller _kryptografi_. Poster från de senaste 24 timmarna ingår.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription == "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Sökhändelser relaterade till unmarshaling
Sök tabeller **händelse** och **SecurityEvents** för den uppgiften som registrerar _unmashaling_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Pulsslag

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Skapa diagram över en vecka over vecka överblick över hur många datorer som skickar data

I följande exempel diagram antalet olika datorer som har skickat pulsslag, varje vecka.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Hitta inaktuella datorer

I följande exempel söker efter datorer som var aktiva under den senaste dagen, men inte har skickat pulsslag under den senaste timmen.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Hämta den senaste posten för pulsslag per dator-IP

Det här exemplet returnerar den sista posten för pulsslag för varje dator-IP.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Matcha skyddade status poster med pulsslagsposter

Det här exemplet hittar relaterade protection status poster och pulsslagsposter matchas på både datorn och tid.
Observera att fältet avrundas till närmaste minut. Vi använde runtime bin beräkning för att göra det: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Server-priset för tillgänglighet

Beräkna server ordinarie taxan baserat på pulsslagsposter. Tillgänglighet definieras som ”minst 1 pulsslag per timme”.
Så om en server var tillgängliga 98 100 timmar, är ordinarie taxan 98%.

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


## <a name="multiple-data-types"></a>Olika datatyper

### <a name="chart-the-record-count-per-table"></a>Diagrammet antal poster per tabell
I följande exempel samlar in alla poster i alla tabeller från de senaste fem timmarna och räknar hur många poster som fanns i varje tabell. Resultaten visas i ett tidsdiagram.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Räkna alla loggar som samlats in under den senaste timmen efter typ
I följande exempel söker igenom allt som rapporteras i den senaste timmen och räknar posterna i varje tabell av **typ**. Resultaten visas i ett stapeldiagram.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Antal Azure-diagnostik poster per kategori
Det här exemplet räknar alla poster för Azure-diagnostik för varje unikt kategori.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Hämta en slumpmässig post för varje unikt kategori
Det här exemplet hämtar en enskild slumpmässiga Azure diagnostics-post för varje unikt kategori.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Hämta den senaste posten per kategori
Det här exemplet hämtar den senaste posten för Azure-diagnostik i varje unikt kategori.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Nätverksövervakning

### <a name="computers-with-unhealthy-latency"></a>Datorer med feltillstånd svarstid
Det här exemplet skapar en lista över olika datorer med feltillstånd svarstid.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Prestanda

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Ansluta till datorn perf-poster för att knyta samman minne och processor
Det här exemplet är kopplat till en viss dator **perf** registrerar och skapar två tidsdiagram, Genomsnittlig CPU och maximalt minne.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
and TimeGenerated < EndTime
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

### <a name="perf-cpu-utilization-graph-per-computer"></a>Perf processoranvändning graph per dator
Det här exemplet beräknar och diagram CPU-utnyttjande på datorer som börjar med _Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Skyddsstatus

### <a name="computers-with-non-reporting-protection-status-duration"></a>Datorer med icke-rapporterande varaktighet för status för skydd
Det här exemplet listar datorer med statusen _rapporterar ej_ och hur länge de befann sig i den här statusen.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Matcha skyddade status poster med pulsslagsposter
Det här exemplet hittar relaterade protection status poster och pulsslagsposter som matchade både datorn och tid.
Fältet avrundas till närmaste minut med **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Säkerhetsposter

### <a name="count-security-events-by-activity-id"></a>Antal säkerhetshändelser av aktivitets-ID


Det här exemplet är beroende av den fasta strukturen för de **aktivitet** kolumn: \<ID\>-\<Name\>.
Den tolkar det **aktivitet** värdet till två nya kolumner och antal förekomster av varje **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Antal säkerhetshändelser som rör behörigheter
Det här exemplet visar antalet poster med **säkerhetshändelser** där **aktivitetskolumnen** innehåller hela termen _behörigheter_. Förfrågan gäller för poster som skapats under de senaste 30 minuterna.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Hitta konton som inte gick att logga in från datorer med en säkerhetsidentifiering
Det här exemplet hittar och antalet konton som inte gick att logga in från datorer där vi identifierar en identifiering.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Är Mina säkerhetsdata tillgängligt?
Startar data börjar ofta utforskning med data tillgänglighetskontroll. Det här exemplet visar hur många **SecurityEvent** poster under de senaste 30 minuterna.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Parsa aktivitetsnamn och ID
De två exemplen nedan beroende av den fasta strukturen för de **aktivitet** kolumn: \<ID\>-\<Name\>. Det första exemplet använder den **parsa** operator för att tilldela värden till två nya kolumner: **activityID** och **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Det här exemplet används den **dela** operator för att skapa en matris med separata värden
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Explicita autentiseringsuppgifter processer
I följande exempel visas ett cirkeldiagram av processer som använde explicita autentiseringsuppgifter under den senaste veckan

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Längst upp processer som körs

I följande exempel visar en tidslinje för aktiviteten för de fem vanligaste processerna under de senaste tre dagarna.

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


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Hitta upprepade misslyckade inloggningar med samma konto från olika IP-adresser

I följande exempel söker efter misslyckade inloggningsförsök med samma konto från fler än fem olika IP-adresser under de senaste sex timmarna.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Hitta användarkonton som inte gick att logga in 
I följande exempel identifierar användarkonton som inte gick att logga in mer än fem gånger under den senaste dagen och när de senast försökte logga in.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Med hjälp av **join**, och **låta** instruktioner som vi kan kontrollera om samma misstänkta konton kunde senare kommer du att logga in direkt.

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

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>Beräkna den genomsnittliga storleken på perf användningsrapporter per dator

Det här exemplet beräknar den genomsnittliga storleken på perf användningsrapporter per dator, under de senaste 3 timmarna.
Resultaten visas i ett stapeldiagram.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>Tidsdiagram svarstid percentiler 50 och 95

Det här exemplet beräknar och diagram 50: e och 95: e percentilerna för rapporterade **avgLatency** per timme under de senaste 24 timmarna.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Användningen av vissa datorer idag
Det här exemplet hämtar **användning** data från den sista dagen för datornamn som innehåller strängen _ContosoFile_. Resultaten sorteras efter **TimeGenerated**.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Uppdateringar

### <a name="computers-still-missing-updates"></a>Uppdateringar som datorer fortfarande saknas
Det här exemplet visar en lista med datorer som saknar en eller flera kritiska uppdateringar några dagar sedan och fortfarande saknar uppdateringar.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(3d)..ago(2d))
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| summarize makeset(Computer);

Update
| where TimeGenerated > ago(1d)
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Nästa steg

- Referera till den [Kusto-Språkreferens](/azure/kusto/query) information om vilka språk.
- Gå igenom en [lektion skriver loggfrågor i Azure Monitor](get-started-queries.md).