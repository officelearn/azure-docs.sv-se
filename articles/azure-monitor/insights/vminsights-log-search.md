---
title: Så här frågar du efter loggar från Azure Monitor for VMs (för hands version) | Microsoft Docs
description: Azure Monitor for VMs lösning samlar in Mät värden och loggdata till och den här artikeln beskriver posterna och innehåller exempel frågor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/10/2019
ms.openlocfilehash: 7363f1ec11974dab3e0c0149c18ac4f0bf1c86ee
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555187"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Så här frågar du efter loggar från Azure Monitor for VMs (för hands version)
Azure Monitor for VMs samlar in prestanda-och anslutnings mått, dator-och process inventerings data och hälso tillstånds information och vidarebefordrar den till arbets ytan Log Analytics i Azure Monitor.  Dessa data är tillgängliga för [fråga](../../azure-monitor/log-query/log-query-overview.md) i Azure Monitor. Du kan använda dessa data i scenarier som omfattar migrerings planering, kapacitets analys, identifiering och prestanda fel sökning på begäran.

## <a name="map-records"></a>Mappa poster
En post skapas per timme för varje unik dator och process, förutom de poster som genereras när en process eller dator startar eller aktive ras till Azure Monitor for VMs kart funktion. Dessa poster har egenskaperna i följande tabeller. Fälten och värdena i ServiceMapComputer_CL-händelserna mappar till fält i dator resursen i ServiceMap-Azure Resource Manager-API: et. Fälten och värdena i ServiceMapProcess_CL-händelserna mappar till fälten i process resursen i ServiceMap-Azure Resource Manager-API: et. Fältet ResourceName_s matchar fältet namn i motsvarande Resource Manager-resurs. 

Det finns internt genererade egenskaper som du kan använda för att identifiera unika processer och datorer:

- Dator: Använd *ResourceID* eller *ResourceName_s* för att unikt identifiera en dator i en Log Analytics-arbetsyta.
- Process: Använd *ResourceID* för att unikt identifiera en process inom en Log Analytics-arbetsyta. *ResourceName_s* är unikt inom kontexten för den dator där processen körs (MachineResourceName_s) 

Eftersom det kan finnas flera poster för en angiven process och dator inom ett angivet tidsintervall, kan frågor returnera fler än en post för samma dator eller process. Om du bara vill ta med den senaste posten lägger du till | deduplicera ResourceId "till frågan.

### <a name="connections-and-ports"></a>Anslutningar och portar
Funktionen anslutnings mått introducerar två nya tabeller i Azure Monitor loggar – VMConnection och VMBoundPort. Tabellerna innehåller information om anslutningarna för en dator (inkommande och utgående) samt de Server portar som är öppna/aktiva på dem. ConnectionMetrics exponeras också via API: er som gör det möjligt att hämta ett mått i ett tids fönster. TCP-anslutningar som orsakas av att de *godkänns* på en lyssnande socket är inkommande, medan de som skapas genom att *ansluta* till en specifik IP-adress och port är utgående. Riktningen för en anslutning representeras av egenskapen Direction, som kan anges till antingen **inkommande** eller **utgående**. 

Poster i dessa tabeller genereras från data som rapporteras av Dependency Agent. Varje post representerar en observation över ett tidsintervall på 1 minut. Egenskapen TimeGenerated anger start tiden för tidsintervallet. Varje post innehåller information för att identifiera respektive entitet, det vill säga anslutning eller port, samt mått som är associerade med den entiteten. För närvarande rapporteras endast nätverks aktivitet som sker med TCP över IPv4. 

#### <a name="common-fields-and-conventions"></a>Gemensamma fält och konventioner 
Följande fält och konventioner gäller både VMConnection och VMBoundPort: 

- Dator: fullständigt kvalificerat domän namn för rapporterings datorn 
- AgentID: den unika identifieraren för en dator med Log Analytics agent  
- Dator: namnet på Azure Resource Manager resursen för den dator som exponeras av ServiceMap. Det är i formatet *m-{GUID}* , där *GUID* är samma GUID som AgentID  
- Process: namnet på Azure Resource Manager resursen för den process som exponeras av ServiceMap. Det har formatet *p-{hex-sträng}* . Processen är unik inom ett dator omfång och genererar ett unikt process-ID mellan datorer, kombinera maskin-och process fält. 
- ProcessName: det körbara namnet på rapporterings processen.
- Alla IP-adresser är strängar i kanoniskt IPv4-format, till exempel *13.107.3.160* 

För att hantera kostnader och komplexitet representerar anslutnings posterna inte enskilda fysiska nätverks anslutningar. Flera fysiska nätverks anslutningar är grupperade i en logisk anslutning, som sedan återspeglas i respektive tabell.  Det innebär att poster i *VMConnection* -tabellen representerar en logisk gruppering och inte de enskilda fysiska anslutningar som observeras. Den fysiska nätverks anslutningen som delar samma värde för följande attribut under ett angivet intervall på en minut, aggregeras i en enda logisk post i *VMConnection*. 

| Egenskap | Beskrivning |
|:--|:--|
|Riktning |Anslutningens riktning, värdet är *inkommande* eller *utgående* |
|Dator |Datorns FQDN |
|Process |Identitet för process eller grupper av processer, initierar/accepterar anslutningen |
|SourceIp |Källans IP-adress |
|DestinationIp |Målets IP-adress |
|Destination port |Port nummer för målet |
|Protokoll |Protokoll som används för anslutningen.  Värdena är *TCP*. |

För att kunna utnyttja gruppens påverkan finns information om antalet grupperade fysiska anslutningar i följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
|LinksEstablished |Antalet fysiska nätverks anslutningar som har upprättats under rapport tids perioden |
|LinksTerminated |Antalet fysiska nätverks anslutningar som har avbrutits under rapport tids perioden |
|LinksFailed |Antalet fysiska nätverks anslutningar som har misslyckats under rapporterings tids perioden. Den här informationen är för närvarande endast tillgänglig för utgående anslutningar. |
|LinksLive |Antalet fysiska nätverks anslutningar som öppnades i slutet av tids perioden för rapportering|

#### <a name="metrics"></a>Mått

Förutom antalet anslutningar räknas information om mängden data som skickas och tas emot på en specifik logisk anslutning eller nätverks port också med följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
|Bytes sent |Totalt antal byte som har skickats under rapport tids perioden |
|BytesReceived |Totalt antal byte som har tagits emot under rapport tids perioden |
|Svar |Antalet svar som observerats under rapporterings tids perioden. 
|ResponseTimeMax |Den största svars tid (millisekunder) som observeras under rapporterings tids perioden. Om inget värde anges är egenskapen tom.|
|ResponseTimeMin |Den minsta svars tid (millisekunder) som observeras under rapporterings tids perioden. Om inget värde anges är egenskapen tom.|
|ResponseTimeSum |Summan av alla svars tider (millisekunder) som observeras under rapporterings tids perioden. Om inget värde anges är egenskapen tom.|

Den tredje typen av data som rapporteras är svars tid – hur lång tid en anropare ägnar åt att vänta på att en begäran skickas över en anslutning som ska bearbetas och besvaras av Fjärrslutpunkten. Svars tiden som rapporteras är en uppskattning av den sanna svars tiden för det underliggande applikations protokollet. Den beräknas med hjälp av heuristik baserat på observationen av data flödet mellan käll-och mål slutet av en fysisk nätverks anslutning. Konceptuellt är skillnaden mellan den tid då den sista byten av en begäran lämnar avsändaren och den tid då den sista byten av svaret kommer tillbaka till den. Dessa två tidsstämplar används för att avgränsa begär ande-och svars händelser på en specifik fysisk anslutning. Skillnaden mellan dem representerar svars tiden för en enskild begäran. 

I den här första versionen av den här funktionen är vår algoritm en uppskattning som kan fungera med varierande grad av lyckade beroende på det faktiska program protokollet som används för en viss nätverks anslutning. Till exempel fungerar den aktuella metoden bra för Request-Response-baserade protokoll som t. ex. HTTP (S), men fungerar inte med envägs-eller Message Queue-baserade protokoll.

Här är några viktiga saker att tänka på:

1. Om en process accepterar anslutningar på samma IP-adress men över flera nätverks gränssnitt rapporteras en separat post för varje gränssnitt. 
2. Poster med IP-adress med jokertecken kommer inte att innehålla någon aktivitet. De är inkluderade för att representera det faktum att en port på datorn är öppen för inkommande trafik.
3. För att minska utförligheten och data volymen utelämnas poster med IP-adress med jokertecken när det finns en matchande post (för samma process, port och protokoll) med en speciell IP-adress. När en IP-post med jokertecken utelämnas, anges IsWildcardBind Record-egenskapen med den angivna IP-adressen till "true" för att indikera att porten exponeras för varje gränssnitt på rapporterings datorn.
4. Portar som endast är kopplade till ett speciellt gränssnitt har IsWildcardBind inställt på *false*.

#### <a name="naming-and-classification"></a>Namn och klassificering
IP-adressen för fjärran sluten av en anslutning ingår i egenskapen RemoteIp för att under lätta. För inkommande anslutningar är RemoteIp detsamma som SourceIp, medan för utgående anslutningar är det samma som DestinationIp. Egenskapen RemoteDnsCanonicalNames representerar DNS-kanoniska namn som rapporteras av datorn för RemoteIp. Egenskaperna RemoteDnsQuestions och RemoteClassification är reserverade för framtida användning. 

#### <a name="geolocation"></a>Geoplats
*VMConnection* innehåller också information om geolokalisering för fjärrparten av varje anslutnings post i följande egenskaper för posten: 

| Egenskap | Beskrivning |
|:--|:--|
|RemoteCountry |Namnet på det land/den region som är värd för RemoteIp.  Till exempel *USA* |
|RemoteLatitude |Den geolokalisering på latitud. Till exempel *47,68* |
|RemoteLongitude |Longituden för den här platsen. Till exempel *– 122,12* |

#### <a name="malicious-ip"></a>Skadlig IP
Varje RemoteIp-egenskap i *VMConnection* -tabellen kontrol leras mot en uppsättning IP-adresser med känd skadlig aktivitet. Om RemoteIp identifieras som skadlig kommer följande egenskaper att fyllas i (de är tomma, om IP-adressen inte betraktas som skadlig) i följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
|MaliciousIp |RemoteIp-adressen |
|IndicatorThreadType |En hot indikator upptäcktes av följande värden: *botnät*, *C2*, *CryptoMining*, *Darknet*, *DDoS*, *MaliciousUrl*, *malware*, *phishing*, *proxy*, *oönskade program*,  *Visnings lista*.   |
|Beskrivning |Beskrivning av det observerade hotet. |
|TLPLevel |TLP-nivån (trafik ljus protokoll) är en av de definierade värdena, *vitt*, *grönt*, *gult*, *rött*. |
|tillit |Värdena är *0 – 100*. |
|Allvarsgrad |Värdena är *0 – 5*, där *5* är det allvarligaste och *0* inte är allvarligt. Standardvärdet är *3*.  |
|FirstReportedDateTime |Första gången som providern rapporterade indikatorn. |
|LastReportedDateTime |Den senaste gången indikatorn visades vid ett flöde. |
|IsActive |Anger att indikatorer inaktive ras med värdet *True* eller *false* . |
|ReportReferenceLink |Länkar till rapporter som är relaterade till en bestämd som kan observeras. |
|additionalInformation |Innehåller ytterligare information om det observerade hotet. |

### <a name="ports"></a>Portar 
Portar på en dator som aktivt accepterar inkommande trafik eller som kan acceptera trafik, men som är inaktiva under rapporterings tids perioden, skrivs till tabellen VMBoundPort.  

Varje post i VMBoundPort identifieras med följande fält: 

| Egenskap | Beskrivning |
|:--|:--|
|Process | Identitet för processen (eller grupper av processer) som porten är associerad med.|
|Sökning | Portens IP-adress (kan vara IP *-adress*med jokertecken) |
|Port |Port numret |
|Protokoll | Protokollet.  Exempel: *TCP* eller *UDP* (endast *TCP* stöds för närvarande).|
 
Identiteten som en port härleds från ovanstående fem fält och lagras i egenskapen PortId. Den här egenskapen kan användas för att snabbt hitta poster för en speciell port över tid. 

#### <a name="metrics"></a>Mått 
Port poster innehåller mått som representerar de anslutningar som är kopplade till dem. För närvarande rapporteras följande mått (informationen för varje mått beskrivs i föregående avsnitt): 

- Bytes sent och BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Här är några viktiga saker att tänka på:

- Om en process accepterar anslutningar på samma IP-adress men över flera nätverks gränssnitt rapporteras en separat post för varje gränssnitt.  
- Poster med IP-adress med jokertecken kommer inte att innehålla någon aktivitet. De är inkluderade för att representera det faktum att en port på datorn är öppen för inkommande trafik. 
- För att minska utförligheten och data volymen utelämnas poster med IP-adress med jokertecken när det finns en matchande post (för samma process, port och protokoll) med en speciell IP-adress. När en IP-post med jokertecken utelämnas, anges egenskapen *IsWildcardBind* för posten med den angivna IP-adressen till *True*.  Detta anger att porten exponeras för varje gränssnitt på rapporterings datorn. 
- Portar som endast är kopplade till ett speciellt gränssnitt har IsWildcardBind inställt på *false*. 

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL-poster
Poster med en typ av *ServiceMapComputer_CL* har inventerings data för servrar med beroende agenten. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
| Typ | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Den unika identifieraren för en dator på arbets ytan |
| ResourceName_s | Den unika identifieraren för en dator på arbets ytan |
| ComputerName_s | Datorns FQDN |
| Ipv4Addresses_s | En lista över serverns IPv4-adresser |
| Ipv6Addresses_s | En lista över serverns IPv6-adresser |
| DnsNames_s | En matris med DNS-namn |
| OperatingSystemFamily_s | Windows eller Linux |
| OperatingSystemFullName_s | Det fullständiga namnet på operativ systemet  |
| Bitness_s | Bitness för datorn (32-bitars eller 64-bitars)  |
| PhysicalMemory_d | Fysiskt minne i MB |
| Cpus_d | Antalet processorer |
| CpuSpeed_d | CPU-hastigheten i MHz|
| VirtualizationState_s | *okänd*, *fysisk*, *virtuell*, *hypervisor* |
| VirtualMachineType_s | *HyperV*, *VMware*och så vidare |
| VirtualMachineNativeMachineId_g | Det VM-ID som tilldelats av dess hypervisor |
| VirtualMachineName_s | Namnet på den virtuella datorn |
| BootTime_t | Start tiden |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL-typ poster
Poster med en typ av *ServiceMapProcess_CL* har inventerings data för TCP-anslutna processer på servrar med beroende agenten. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
| Typ | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Den unika identifieraren för en process inom arbets ytan |
| ResourceName_s | Den unika identifieraren för en process på den dator där den körs|
| MachineResourceName_s | Datorns resurs namn |
| ExecutableName_s | Namnet på den körbara processen |
| StartTime_t | Start tid för process bassäng |
| FirstPid_d | Det första PID i lagringspoolen |
| Description_s | Beskrivning av processen |
| CompanyName_s | Företagets namn |
| InternalName_s | Det interna namnet |
| ProductName_s | Produktens namn |
| ProductVersion_s | Produkt versionen |
| FileVersion_s | Fil versionen |
| CommandLine_s | Kommando raden |
| ExecutablePath_s | Sökvägen till den körbara filen |
| WorkingDirectory_s | Arbets katalogen |
| Användar | Kontot under vilket processen körs |
| UserDomain | Domänen som processen körs under |

## <a name="sample-log-searches"></a>Exempel på loggsökningar

### <a name="list-all-known-machines"></a>Visa en lista med alla kända datorer
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="when-was-the-vm-last-rebooted"></a>När den virtuella datorn startades om senast
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc`
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Översikt över virtuella Azure-datorer efter avbildning, plats och SKU
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s`
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Ange kapaciteten för fysiskt minne för alla hanterade datorer.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`
```

### <a name="list-computer-name-dns-ip-and-os"></a>Visa dator namn, DNS, IP och OS.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Hitta alla processer med "SQL" på kommando raden
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Hitta en dator (senaste posten) efter resurs namn
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Hitta en dator (senaste posten) via IP-adress
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Visa en lista med alla kända processer på en angiven dator
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-computers-running-sql-server"></a>Visa en lista med alla datorer som kör SQL Server
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Visa en lista med alla unika produkt versioner av sväng i mitt Data Center
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Skapa en dator grupp för alla datorer som kör CentOS
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`
```

### <a name="bytes-sent-and-received-trends"></a>Trender för skickade och mottagna byte
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart`
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Vilka virtuella Azure-datorer som överför de flesta byte
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc`
```

### <a name="link-status-trends"></a>Länka status trender
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart`
```

### <a name="connection-failures-trend"></a>Trend för anslutnings problem
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart`
```

### <a name="bound-ports"></a>Kopplade portar
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Antal öppna portar på olika datorer
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Poäng processer på arbets ytan med antalet portar som de har öppna
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Mängd beteende för varje port
Den här frågan kan sedan användas för att räkna ut portar efter aktivitet, t. ex. portar med mest inkommande/utgående trafik, portar med de flesta anslutningar
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Sammanfatta utgående anslutningar från en grupp datorer
```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>Nästa steg
* Om du inte har använt att skriva logg frågor i Azure Monitor kan du läsa om [hur du använder Log Analytics](../../azure-monitor/log-query/get-started-portal.md) i Azure Portal för att skriva logg frågor.
* Lär dig mer om att [skriva Sök frågor](../../azure-monitor/log-query/search-queries.md).
