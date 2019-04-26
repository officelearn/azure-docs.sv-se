---
title: Så här fråga loggar från Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Azure Monitor för virtuella datorer lösningen samlar in mätvärden och loggdata till och den här artikeln beskriver posterna och innehåller exempelfrågor.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: magoedte
ms.openlocfilehash: 8b6745a2b9afe8d3101585e3f7a13f2fc978c84a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492096"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Hur du frågar loggar från Azure Monitor för virtuella datorer (förhandsversion)
Azure Monitor för virtuella datorer samlar in prestanda och anslutningsmått, datorn och processen inventeringsdata och hälsotillståndsinformation och vidarebefordrar det till arbetsytan Log Analytics i Azure Monitor.  Informationen är tillgänglig för [fråga](../../azure-monitor/log-query/log-query-overview.md) i Azure Monitor. Du kan använda dessa data för scenarier som omfattar planering av migreringsaktiviteter, kapacitetsanalys, identifiering och prestandafelsökning för på begäran.

## <a name="map-records"></a>Mappa poster
En post skapas per timme för varje unika datornamn och processen, förutom de poster som genereras när en process eller datorn startas inte eller är publicerat till Azure Monitor för virtuella datorer kartan funktion. Dessa poster har egenskaper i följande tabeller. Fält och värden i ServiceMapComputer_CL händelserna mappar till fält i resursen för datorer i Azure Resource Manager-API för ServiceMap. Fält och värden i ServiceMapProcess_CL händelserna mappar till fälten för Process-resurs i ServiceMap Azure Resource Manager API. Fältet ResourceName_s matchar namnfältet i motsvarande Resource Manager-resurs. 

Det finns internt genererade egenskaper som du kan använda för att identifiera unika processer och datorer:

- Dator: Använd *ResourceId* eller *ResourceName_s* att unikt identifiera en dator inom en Log Analytics-arbetsyta.
- Processen: Använd *ResourceId* att unikt identifiera en process i Log Analytics-arbetsytan. *ResourceName_s* är unikt inom kontexten för datorn där processen körs (MachineResourceName_s) 

Eftersom flera poster kan finnas för en angiven process och datorer i ett specifikt tidsintervall kan frågor returnera fler än en post för samma dator eller process. Om du vill inkludera endast den senaste posten, lägger du till ”| dedupliceringen ResourceId ”i frågan.

### <a name="connections-and-ports"></a>Anslutningar och portar
Funktionen Anslutningsmått introducerar två nya tabeller i Azure Monitor-loggfilerna - VMConnection och VMBoundPort. Dessa tabeller innehåller information om anslutningar för en virtuell dator (inkommande och utgående), samt servern portar som är öppen/aktiv på dem. ConnectionMetrics blir också tillgängliga via API: er som tillhandahåller metoder för att hämta ett specifikt mått under ett tidsintervall. TCP-anslutningar som härrör från *accepterar* för en lyssnande socket är inkommande, samtidigt som de som används av *ansluter* till en given IP och port är utgående. Riktning för en anslutning som representeras av egenskapen riktning, som kan vara inställd på antingen **inkommande** eller **utgående**. 

Poster i tabellerna genereras från data som rapporteras av Beroendeagenten. Varje post representerar en områdes under ett tidsintervall för 1 minut. Egenskapen TimeGenerated anger början av tidsintervallet. Varje post innehåller information för att identifiera entiteten respektive, det vill säga, anslutning eller port samt mått som är associerade med denna entitet. För närvarande rapporteras endast nätverksaktivitet som sker med TCP över IPv4. 

#### <a name="common-fields-and-conventions"></a>Vanliga fält och konventioner 
Följande fält och konventioner gäller både VMConnection och VMBoundPort: 

- Dator: Fullständigt kvalificerade domännamnet för rapportering av dator 
- AgentID: Den unika identifieraren för en dator med Log Analytics-agenten  
- Dator: Namnet på Azure Resource Manager-resurs för den datorn som exponeras av ServiceMap. Det är i formatet *m-{GUID}*, där *GUID* är samma GUID som AgentID  
- Processen: Namnet på Azure Resource Manager-resurs för den process som exponeras av ServiceMap. Det är i formatet *p-{hexadecimal sträng}*. Processen är unika inom en datoromfånget och kombinera fält för datorn och processen för att generera en unik process-ID för datorer. 
- ProcessName: Filnamn för rapporterna.
- Alla IP-adresser är strängar i IPv4 kanoniskt format, till exempel *13.107.3.160* 

För att hantera kostnaden och komplexiteten, utgör anslutningen poster inte enskilda fysiska nätverksanslutningar. Flera fysiska nätverksanslutningar är grupperade i en logisk anslutning, som sedan visas i respektive tabell.  Betydelse, registrerar i *VMConnection* tabell representerar en logisk gruppering och inte de enskilda fysiska anslutningar som är som observeras. Fysiska nätverksanslutningen som delar samma värde för följande attribut under ett givet intervall för en minut, slås ihop till en enskild logisk post i *VMConnection*. 

| Egenskap | Beskrivning |
|:--|:--|
|Direction |Riktning för anslutningen värdet är *inkommande* eller *utgående* |
|Machine |Datorn FQDN |
|Process |Identiteten för processen eller grupper av processer, initierar/godkänna anslutningen |
|SourceIp |IP-adressen för källan |
|DestinationIp |IP-adressen för målet |
|DestinationPort |Portnumret för mål |
|Protocol |Protokoll som används för anslutningen.  Värden är *tcp*. |

Information om antalet grupperade fysiska anslutningar finns i följande egenskaper för posten för att redovisa effekten av gruppering:

| Egenskap | Beskrivning |
|:--|:--|
|LinksEstablished |Antal fysiska nätverksanslutningar som har upprättats under tidsperioden för rapportering |
|LinksTerminated |Antal fysiska nätverksanslutningar som har avslutats under tidsperioden för rapportering |
|LinksFailed |Antal fysiska nätverksanslutningar som har misslyckats under tidsperioden för rapportering. Den här informationen är endast tillgänglig för utgående anslutningar. |
|LinksLive |Antal fysiska nätverksanslutningar som var öppna längst ned i tidsfönstret|

#### <a name="metrics"></a>Mått

Förutom antalet anslutningsmått, information om mängden data som skickas och tas emot på en viss logisk anslutning eller nätverksport ingår även i följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
|BytesSent |Sammanlagt antal byte som har skickats under tidsperioden för rapportering |
|BytesReceived |Sammanlagt antal byte som tagits emot under tidsperioden för rapportering |
|Responses |Antal svar som observerats under tidsperioden för rapportering. 
|ResponseTimeMax |Den största svarstid (millisekunder) observerats under tidsperioden för rapportering. Egenskapen är tomt om inget värde.|
|ResponseTimeMin |Den minsta svarstid (millisekunder) observerats under tidsperioden för rapportering. Egenskapen är tomt om inget värde.|
|ResponseTimeSum |Summan av alla svarstider (millisekunder) som observerats under tidsperioden för rapportering. Egenskapen är tomt om inget värde.|

Den tredje typ av data som rapporteras svarstid – hur länge en anropare ägna åt att vänta på en begäran som skickas via en anslutning som ska bearbetas och besvarats av fjärrslutpunkten. Svarstiden som rapporteras är en uppskattning av SANT svarstiden för det underliggande protokollet. Det beräknas med hjälp av heuristik baserat på observationer av flödet av data mellan käll- och slutet av en fysisk anslutning. Den övergripande är skillnaden mellan den tid som den sista byten av en begäran lämnar avsändaren och tid när den sista byten av svaret kommer tillbaka till den. Dessa två tidsstämplar används för att ge en bild av händelser som begäranden och svar på en viss fysisk anslutning. Skillnaden mellan dem representerar svarstiden för en enskild begäran. 

I den första versionen av den här funktionen är vår algoritmen ett approximativt värde som kan fungera med varierande framgång beroende på typ av App-protokollet som används för en viss nätverksanslutning. Exempelvis kan den aktuella metoden fungerar bra för begäranden och svar-baserade protokoll som HTTP (S), men inte fungerar med enkelriktad och message queue-baserade protokoll.

Här följer några viktiga saker att tänka på:

1. Om en process som tar emot anslutningar på samma IP-adress men över flera nätverksgränssnitt, rapporteras en separat post för varje gränssnitt. 
2. Poster med jokertecken IP innehåller ingen aktivitet. De ingår som representerar det faktum att en port på datorn är öppna för inkommande trafik.
3. För att minska detaljnivå och datavolym, utelämnas poster med jokertecken IP när det finns en matchande post (för samma process, port och protokoll) med en specifik IP-adress. När ett jokertecken IP-adressposten utelämnas, ställs egenskapen IsWildcardBind poster med specifika IP-adress ”true” som anger att porten som exponeras över varje gränssnitt för den reporting datorn.
4. Portar som är bundna endast på ett visst gränssnitt har IsWildcardBind inställd *FALSKT*.

#### <a name="naming-and-classification"></a>Namngivning och klassificering
För att underlätta för som IP-adressen för den fjärranslutna datorn för en anslutning ingår i egenskapen RemoteIp. För inkommande anslutningar RemoteIp är samma som SourceIp, och för utgående anslutningar är det samma som DestinationIp. Egenskapen RemoteDnsCanonicalNames representerar de kanoniska DNS-namn som rapporterats av den för RemoteIp. Egenskaperna RemoteDnsQuestions och RemoteClassification är reserverade för framtida användning. 

#### <a name="geolocation"></a>Geoplats
*VMConnection* innehåller även geoplats information för den fjärranslutna datorn för varje post för anslutning av följande egenskaper för posten: 

| Egenskap | Beskrivning |
|:--|:--|
|RemoteCountry |Namnet på det land som är värd för RemoteIp.  Till exempel *USA* |
|RemoteLatitude |Geoplats latitud. Till exempel *47.68* |
|RemoteLongitude |Geoplats longitud. Till exempel *-122.12* |

#### <a name="malicious-ip"></a>Skadlig IP
Varje RemoteIp-egenskapen i *VMConnection* tabell kontrolleras mot en uppsättning IP-adresser med känd skadlig aktivitet. Om RemoteIp identifieras som skadlig följande egenskaper är ifyllda (de är tom, när den IP-Adressen inte anses vara skadlig) i följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
|MaliciousIp |RemoteIp-adress |
|IndicatorThreadType |Threat indikatorn har identifierats är något av följande värden *Botnät*, *C2*, *CryptoMining*, *Darknet*, *DDos* , *MaliciousUrl*, *skadlig kod*, *nätfiske*, *Proxy*, *oönskade program*, *Visningslista*.   |
|Description |Beskrivning av observerade hotet. |
|TLPLevel |Trafikljus Protocol (TLP) är en av de definierade värdena *White*, *grönt*, *gul*, *Red*. |
|Confidence |Värden är *0 – 100*. |
|Severity |Värden är *0 – 5*, där *5* är den mest allvarliga och *0* inte är allvarligt alls. Standardvärdet är *3*.  |
|FirstReportedDateTime |Första gången providern rapporterade indikatorn. |
|LastReportedDateTime |Senast indikatorn har setts av Interflow. |
|IsActive |Anger indikatorer inaktiveras med *SANT* eller *FALSKT* värde. |
|ReportReferenceLink |Länkar till rapporter som rör en viss övervakas. |
|AdditionalInformation |Tillhandahåller ytterligare information om det är tillämpligt, om observerade hotet. |

### <a name="ports"></a>Portar 
Portar på en dator som aktivt acceptera inkommande trafik eller potentiellt kan acceptera trafik, men är inaktiva under tidsperioden för reporting skrivs till tabellen VMBoundPort.  

>[!NOTE]
>Azure Monitor för virtuella datorer stöder inte samla in och registrera portdata i Log Analytics-arbetsytan i följande regioner:  
>- Östra USA  
>- Västra Europa
>
> Data samlas in är aktiverad i den andra [regioner som stöds](vminsights-onboard.md#log-analytics) för Azure Monitor för virtuella datorer. 

Varje post i VMBoundPort identifieras med följande fält: 

| Egenskap | Beskrivning |
|:--|:--|
|Process | Identiteten för processen (eller grupper av processer) som porten som är associerad med.|
|Ip | Port IP-adress (kan vara IP-adress med jokertecken *0.0.0.0*) |
|Port |Portnumret |
|Protocol | Protokollet.  Exempelvis *tcp* eller *udp* (endast *tcp* stöds för närvarande).|
 
Identiteten en port härleds från ovanstående fem fält och lagras i egenskapen PortId. Den här egenskapen kan användas för att snabbt hitta poster för en viss port över tid. 

#### <a name="metrics"></a>Mått 
Port posterna innehåller mått som representerar de anslutningar som är associerade med dem. För närvarande följande mått rapporteras (för varje mått beskrivs i föregående avsnitt): 

- BytesSent och BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Här följer några viktiga saker att tänka på:

- Om en process som tar emot anslutningar på samma IP-adress men över flera nätverksgränssnitt, rapporteras en separat post för varje gränssnitt.  
- Poster med jokertecken IP innehåller ingen aktivitet. De ingår som representerar det faktum att en port på datorn är öppna för inkommande trafik. 
- För att minska detaljnivå och datavolym, utelämnas poster med jokertecken IP när det finns en matchande post (för samma process, port och protokoll) med en specifik IP-adress. När ett jokertecken IP-adressposten utelämnas, den *IsWildcardBind* -egenskapen för posten med den angivna IP-adressen anges till *SANT*.  Detta anger porten som exponeras över varje gränssnitt för den reporting datorn. 
- Portar som är bundna endast på ett visst gränssnitt har IsWildcardBind inställd *FALSKT*. 

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL poster
Poster med en typ av *ServiceMapComputer_CL* har inventeringsdata för servrar med beroendeagenten. Dessa poster har egenskaper i följande tabell:

| Egenskap  | Beskrivning |
|:--|:--|
| Type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Den unika identifieraren för en dator i arbetsytan |
| ResourceName_s | Den unika identifieraren för en dator i arbetsytan |
| ComputerName_s | Datorn FQDN |
| Ipv4Addresses_s | En lista över serverns IPv4-adresser |
| Ipv6Addresses_s | En lista över serverns IPv6-adresser |
| DnsNames_s | En matris med DNS-namn |
| OperatingSystemFamily_s | Windows- eller Linux |
| OperatingSystemFullName_s | Det fullständiga namnet på operativsystemet  |
| Bitness_s | Bitar som inte för datorn (32-bitars eller 64-bitars)  |
| PhysicalMemory_d | Fysiskt minne i MB |
| Cpus_d | Antal processorer |
| CpuSpeed_d | CPU-hastighet i MHz|
| VirtualizationState_s | *Okänd*, *fysiska*, *virtuella*, *hypervisor-programmet* |
| VirtualMachineType_s | *Hyper-v*, *vmware*och så vidare |
| VirtualMachineNativeMachineId_g | VM-ID som tilldelats av dess hypervisor-programmet |
| VirtualMachineName_s | Namnet på den virtuella datorn |
| BootTime_t | Starttiden |

### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL poster
Poster med en typ av *ServiceMapProcess_CL* har inventeringsdata för TCP-anslutna processer på servrar med beroendeagenten. Dessa poster har egenskaper i följande tabell:

| Egenskap  | Beskrivning |
|:--|:--|
| Type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Den unika identifieraren för en process i arbetsytan |
| ResourceName_s | Den unika identifieraren för en process på datorn där den körs|
| MachineResourceName_s | Resursnamnet för datorn |
| ExecutableName_s | Namnet på processprogramfil |
| StartTime_t | Starttid för process-pool |
| FirstPid_d | Första PID i programprocess-poolen |
| Description_s | Beskrivning av process |
| CompanyName_s | Namnet på företaget |
| InternalName_s | Det interna namnet |
| ProductName_s | Namnet på produkten |
| ProductVersion_s | Produktversion |
| FileVersion_s | Filversionen |
| CommandLine_s | Från kommandoraden |
| ExecutablePath _s | Sökvägen till den körbara filen |
| WorkingDirectory_s | Arbetskatalogen |
| UserName | Det konto som processen körs |
| UserDomain | Den domän där processen körs |

## <a name="sample-log-searches"></a>Exempel på loggsökningar

### <a name="list-all-known-machines"></a>Lista över alla kända datorer
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="when-was-the-vm-last-rebooted"></a>När den virtuella datorn senast startades
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc`
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Sammanfattning av virtuella Azure-datorer med bild, plats och SKU
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s`
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Lista över kapacitet för fysiskt minne för alla hanterade datorer.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`
```

### <a name="list-computer-name-dns-ip-and-os"></a>Lista datornamn, DNS, IP- och OS.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Hitta alla processer med ”sql” på kommandoraden
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Hitta en dator (senaste post) efter resursnamn
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Hitta en dator (senaste post) genom att IP-adress
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Lista över alla kända processer på en angiven dator
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-computers-running-sql-server"></a>Lista över alla datorer som kör SQL Server
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista över alla unika produktversioner av curl i mitt datacenter
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Skapa en datorgrupp för alla datorer som kör CentOS
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`
```

### <a name="bytes-sent-and-received-trends"></a>Byte skickade och mottagna trender
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart`
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Vilka virtuella Azure-datorer skickar mest byte
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc`
```

### <a name="link-status-trends"></a>Länken status trender
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart`
```

### <a name="connection-failures-trend"></a>Anslutningen fel trend
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart`
```

### <a name="bound-ports"></a>Bundna portar
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Antal öppna portar för datorer
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Bedöma processer i din arbetsyta genom att antalet portar som de har öppen
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Sammanställd beteendet för varje port
Den här frågan kan sedan användas kan bedöma de portar som aktiviteten, t.ex. portar med de flesta inkommande/utgående trafik, portar med de flesta anslutningar
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
* Om du är nybörjare på att skriva loggfrågor i Azure Monitor kan du granska [hur du använder Log Analytics](../../azure-monitor/log-query/get-started-portal.md) i Azure portal för att skriva loggfrågor.
* Lär dig mer om [skriva sökfrågor](../../azure-monitor/log-query/search-queries.md).
