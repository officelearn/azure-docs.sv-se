---
title: Fråga loggar från Azure Monitor för virtuella datorer
description: Azure Monitor för virtuella datorer-lösningen samlar in mått och loggdata till och den här artikeln beskriver posterna och innehåller exempelfrågor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 71258b04bad9a7aec4e86564d51d1d6f3f8cac76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283811"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Fråga loggar från Azure Monitor för virtuella datorer

Azure Monitor för virtuella datorer samlar in prestanda- och anslutningsmått, dator- och processlagerdata samt hälsotillståndsinformation och vidarebefordrar den till Log Analytics-arbetsytan i Azure Monitor.  Dessa data är tillgängliga för [frågor](../../azure-monitor/log-query/log-query-overview.md) i Azure Monitor. Du kan använda dessa data på scenarier som omfattar migreringsplanering, kapacitetsanalys, identifiering och felsökning av prestanda på begäran.

## <a name="map-records"></a>Mappa poster

En post genereras per timme för varje unik dator och process, förutom de poster som genereras när en process eller dator startar eller är intecknad i Azure Monitor för virtuella datorer Kartfunktion. Dessa poster har egenskaperna i följande tabeller. Fälten och värdena i ServiceMapComputer_CL händelser mappas till fält för datorresursen i Api:et för ServiceMap Azure Resource Manager. Fälten och värdena i ServiceMapProcess_CL händelser mappas till fälten för processresursen i Api:et för ServiceMap Azure Resource Manager. Fältet ResourceName_s matchar namnfältet i motsvarande Resurshanteraren-resurs. 

Det finns internt genererade egenskaper som du kan använda för att identifiera unika processer och datorer:

- Dator: Använd *ResourceId* eller *ResourceName_s* för att unikt identifiera en dator i en Log Analytics-arbetsyta.
- Process: Använd *ResourceId* för att unikt identifiera en process inom en Log Analytics-arbetsyta. *ResourceName_s* är unik inom ramen för den maskin som processen körs på (MachineResourceName_s) 

Eftersom det kan finnas flera poster för en angiven process och dator i ett angivet tidsintervall, kan frågor returnera mer än en post för samma dator eller process. Om du bara vill ta `| summarize arg_max(TimeGenerated, *) by ResourceId` med den senaste posten lägger du till i frågan.

### <a name="connections-and-ports"></a>Anslutningar och portar

Med funktionen Anslutningsmått introduceras två nya tabeller i Azure Monitor-loggar - VMConnection och VMBoundPort. Dessa tabeller innehåller information om anslutningar för en dator (inkommande och utgående) samt de serverportar som är öppna/aktiva på dem. ConnectionMetrics exponeras också via API:er som ger möjlighet att hämta ett specifikt mått under ett tidsfönster. TCP-anslutningar som härrör från att acceptera på en lyssningsuttag är inkommande, medan de som skapas genom att *ansluta* till en viss IP och port är utgående. *accepting* Riktningen för en anslutning representeras av egenskapen Riktning, som kan ställas in på antingen **inkommande** eller **utgående**. 

Poster i dessa tabeller genereras från data som rapporteras av beroendeagenten. Varje post representerar en observation över ett 1-minutersintervall. Egenskapen TimeGenerated anger början av tidsintervallet. Varje post innehåller information för att identifiera respektive entitet, det vill ha, anslutning eller port, samt mått som är associerade med den entiteten. För närvarande rapporteras endast nätverksaktivitet som inträffar med TCP över IPv4. 

#### <a name="common-fields-and-conventions"></a>Gemensamma områden och konventioner 

Följande fält och konventioner gäller både VMConnection och VMBoundPort: 

- Dator: Fullständigt kvalificerat domännamn för rapporteringsmaskin 
- AgentId: Den unika identifieraren för en dator med Log Analytics-agenten  
- Dator: Namn på Azure Resource Manager-resursen för den dator som exponeras av ServiceMap. Det är av formuläret *m-{GUID}*, där *GUID* är samma GUID som AgentId  
- Process: Namn på Azure Resource Manager-resursen för den process som exponeras av ServiceMap. Det är av formen *p-{hex sträng}*. Processen är unik inom ett maskinomfång och att generera ett unikt process-ID mellan datorer, kombinera fälten Maskin och Process. 
- ProcessName: Körbart namn på rapporteringsprocessen.
- Alla IP-adresser är strängar i IPv4-kanoniskt format, till exempel *13.107.3.160* 

För att hantera kostnader och komplexitet representerar anslutningsposter inte enskilda fysiska nätverksanslutningar. Flera fysiska nätverksanslutningar grupperas i en logisk anslutning, vilket sedan återspeglas i respektive tabell.  Det betyder att poster i *tabellen VMConnection* representerar en logisk gruppering och inte de enskilda fysiska anslutningar som observeras. Fysisk nätverksanslutning som delar samma värde för följande attribut under ett givet enminutsintervall aggregeras till en enda logisk post i *VMConnection*. 

| Egenskap | Beskrivning |
|:--|:--|
|Riktning |Anslutningens riktning, värdet är *inkommande* eller *utgående* |
|Dator |Datorn FQDN |
|Process |Identitet för process eller grupper av processer, initiera/acceptera anslutningen |
|KällaTips |Källans IP-adress |
|DestinationTips |Målets IP-adress |
|DestinationPort (DestinationPort) |Destinationens portnummer |
|Protokoll |Protokoll som används för anslutningen.  Värden är *tcp*. |

För att ta hänsyn till effekten av gruppering finns information om antalet grupperade fysiska anslutningar i postens följande egenskaper:

| Egenskap | Beskrivning |
|:--|:--|
|LinksEtablerade |Antalet fysiska nätverksanslutningar som har upprättats under rapporteringstidsfönstret |
|LänkarTerminerad |Antalet fysiska nätverksanslutningar som har avslutats under rapporteringstidsfönstret |
|LänkarFailerad |Antalet fysiska nätverksanslutningar som har misslyckats under rapporteringstidsfönstret. Den här informationen är för närvarande endast tillgänglig för utgående anslutningar. |
|LänkarLive |Antalet fysiska nätverksanslutningar som var öppna i slutet av rapporteringstidsfönstret|

#### <a name="metrics"></a>Mått

Förutom mått för antalet anslutningar inkluderas även information om mängden data som skickas och tas emot på en viss logisk anslutnings- eller nätverksport i postens följande egenskaper:

| Egenskap | Beskrivning |
|:--|:--|
|BytesSent |Totalt antal byte som har skickats under rapporteringstidsfönstret |
|ByteKom med |Totalt antal byte som har tagits emot under rapporteringstidsfönstret |
|Svar |Antalet svar som observerats under rapporteringstidsfönstret. 
|SvarStidsmax |Den största svarstiden (millisekunder) som observerats under rapporteringstidsfönstret. Om inget värde är egenskapen tom.|
|SvarTimeMin |Den minsta svarstiden (millisekunder) som observerats under rapporteringstidsfönstret. Om inget värde är egenskapen tom.|
|SvarTimeSum |Summan av alla svarstider (millisekunder) som observerats under rapporteringstidsfönstret. Om inget värde är egenskapen tom.|

Den tredje typen av data som rapporteras är svarstid - hur länge spenderar en uppringare på att vänta på att en begäran som skickas över en anslutning ska bearbetas och besvaras av fjärrslutpunkten. Den rapporterade svarstiden är en uppskattning av den verkliga svarstiden för det underliggande programprotokollet. Det beräknas med heuristik baserat på observation av flödet av data mellan källan och målslutet för en fysisk nätverksanslutning. Begreppsmässigt är det skillnaden mellan den tidpunkt då den sista bytet av en begäran lämnar avsändaren och den tidpunkt då den sista bytet av svaret kommer tillbaka till den. Dessa två tidsstämplar används för att avgränsa begärande- och svarshändelser på en viss fysisk anslutning. Skillnaden mellan dem representerar svarstiden för en enskild begäran. 

I den här första versionen av den här funktionen är vår algoritm en approximation som kan fungera med varierande grad av framgång beroende på det faktiska programprotokollet som används för en viss nätverksanslutning. Den aktuella metoden fungerar till exempel bra för protokoll baserade på begäransvar, till exempel HTTP(S), men fungerar inte med enkelriktade eller meddelandeköbaserade protokoll.

Här är några viktiga punkter att tänka på:

1. Om en process accepterar anslutningar på samma IP-adress men via flera nätverksgränssnitt rapporteras en separat post för varje gränssnitt. 
2. Poster med jokertecken-IP innehåller ingen aktivitet. De ingår för att representera det faktum att en port på maskinen är öppen för inkommande trafik.
3. För att minska verbositeten och datavolymen utelämnas poster med jokertecken-IP när det finns en matchande post (för samma process, port och protokoll) med en specifik IP-adress. När en IP-post med jokertecken utelämnas ställs egenskapen IsWildcardBind-post med den specifika IP-adressen in på "True" för att ange att porten exponeras över alla gränssnitt på rapporteringsdatorn.
4. Portar som endast är bundna på ett visst gränssnitt har IsWildcardBind inställt på *False*.

#### <a name="naming-and-classification"></a>Namngivning och klassificering

För enkelhetens skull ingår IP-adressen för fjärrslutet av en anslutning i egenskapen RemoteIp. För inkommande anslutningar är RemoteIp samma som SourceIp, medan för utgående anslutningar är det samma som DestinationIp. Egenskapen RemoteDnsCanonicalNames representerar de DNS-kanoniska namn som rapporteras av datorn för RemoteIp. Egenskaperna RemoteDnsQuestions och RemoteClassification är reserverade för framtida användning. 

#### <a name="geolocation"></a>Geolocation

*VMConnection* innehåller även geolokaliseringsinformation för fjärrslutet av varje anslutningspost i följande egenskaper för posten: 

| Egenskap | Beskrivning |
|:--|:--|
|Fjärrland |Namnet på det land/den region som är värd för RemoteIp.  Till exempel *kan USA* |
|Fjärrlatitude |Geolocation latitud. Till exempel *47,68* |
|Fjärrlånghet |Geolocation longituden. Till exempel *-122,12* |

#### <a name="malicious-ip"></a>Skadlig IP

Varje RemoteIp-egenskap i *VMConnection-tabellen* kontrolleras mot en uppsättning IP-adresser med känd skadlig aktivitet. Om RemoteIp identifieras som skadliga fylls följande egenskaper i (de är tomma, när IP-adressen inte anses vara skadlig) i postens följande egenskaper:

| Egenskap | Beskrivning |
|:--|:--|
|MaliciousIp (skadligt) |RemoteIp-adressen |
|IndikatorDetråd |Hotindikator som upptäckts är ett av följande värden, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|Beskrivning |Beskrivning av det observerade hotet. |
|TLPLevel |TLP-nivå (Traffic Light Protocol) är ett av de definierade värdena, *Vit,* *Grön*, *Gul*, *Röd*. |
|Konfidensbedömning |Värdena är *0 – 100*. |
|Severity |Värdena är *0 – 5*, där *5* är den allvarligaste och *0* inte är allvarlig alls. Standardvärdet är *3*.  |
|FirstReportedDateTime |Första gången leverantören rapporterade indikatorn. |
|SenastrapporteraddateTime |Senast indikatorn sågs av Interflow. |
|IsActive (olikartade) |Anger att indikatorer inaktiveras med *sant* eller *falskt* värde. |
|RapportReferenceLink |Länkar till rapporter relaterade till en viss observerbar. |
|Ytterligare information |Ger ytterligare information, om tillämpligt, om det observerade hotet. |

### <a name="ports"></a>Portar 

Portar på en dator som aktivt accepterar inkommande trafik eller som potentiellt kan acceptera trafik, men är inaktiva under rapporteringstidsfönstret, skrivs till tabellen VMBoundPort.  

Varje post i VMBoundPort identifieras av följande fält: 

| Egenskap | Beskrivning |
|:--|:--|
|Process | Identitet för process (eller grupper av processer) som porten är associerad med.|
|Ip | Port IP-adress (kan vara jokertecken IP, *0.0.0.0)* |
|Port |Portnumret |
|Protokoll | Protokollet.  Exempel, *tcp* eller *udp* (endast *tcp* stöds för närvarande).|
 
Identiteten som en port härleds från ovanstående fem fält och lagras i egenskapen PortId. Den här egenskapen kan användas för att snabbt hitta poster för en viss port över tid. 

#### <a name="metrics"></a>Mått 

Portposter innehåller mått som representerar de anslutningar som är associerade med dem. För närvarande rapporteras följande mått (informationen för varje mått beskrivs i föregående avsnitt): 

- BytesSent och BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Här är några viktiga punkter att tänka på:

- Om en process accepterar anslutningar på samma IP-adress men via flera nätverksgränssnitt rapporteras en separat post för varje gränssnitt.  
- Poster med jokertecken-IP innehåller ingen aktivitet. De ingår för att representera det faktum att en port på maskinen är öppen för inkommande trafik. 
- För att minska verbositeten och datavolymen utelämnas poster med jokertecken-IP när det finns en matchande post (för samma process, port och protokoll) med en specifik IP-adress. När en JOKER IP-post utelämnas ställs egenskapen *IsWildcardBind* för posten med den specifika IP-adressen till *True*.  Detta indikerar att porten är exponerad över alla gränssnitt på rapporteringsdatorn. 
- Portar som endast är bundna på ett visst gränssnitt har IsWildcardBind inställt på *False*. 

### <a name="vmcomputer-records"></a>VMComputer-poster

Poster med en typ av *VMDator* har lagerdata för servrar med beroendeagenten. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
|TenantId | Den unika identifieraren för arbetsytan |
|SourceSystem | *Insikter* | 
|TimeGenerated | Tidsstämpel för posten (UTC) |
|Dator | Datorn FQDN | 
|AgentId (på andra) | Det unika ID:t för Log Analytics-agenten |
|Dator | Namn på Azure Resource Manager-resursen för den dator som exponeras av ServiceMap. Det är av formuläret *m-{GUID}*, där *GUID* är samma GUID som AgentId. | 
|DisplayName | Visningsnamn | 
|FullDisplayName | Fullständigt visningsnamn | 
|Värdnamn | Namnet på maskinen utan domännamn |
|Starttid | Maskinens starttid (UTC) |
|TimeZone | Den normaliserade tidszonen |
|VirtualiseringTillstånd | *virtuell,* *hypervisor*, *fysisk* |
|Ipv4Adresser | Matris med IPv4-adresser | 
|Ipv4SubnetMasks | Matris med IPv4-undernätsmasker (i samma ordning som Ipv4Addresses). |
|Ipv4DefaultGateways | Matris med IPv4-gateways | 
|Ipv6Adresser | Matris med IPv6-adresser | 
|MacAdresser | Matris med MAC-adresser | 
|DnsNames (1) | Matris med DNS-namn som är associerade med datorn. |
|DependencyAgentVersion | Den version av beroendeagenten som körs på datorn. | 
|OperativsystemFamilj | *Linux*, *Windows* |
|OperatingSystemFullName | Operativsystemets fullständiga namn | 
|PhysicalMemoryMB | Det fysiska minnet i megabyte | 
|Processorer | Antal processorer | 
|CpuSpeed | CPU-hastigheten i MHz | 
|VirtualMachineType | *hyperv*, *vmware*, *xen* |
|VirtualMachineNativeId | VM-ID:et som tilldelats av dess hypervisor | 
|VirtualMachineNativeName | Namnet på den virtuella datorn |
|VirtualMachineHypervisorId | Den unika identifieraren för hypervisorn som är värd för den virtuella datorn |
|HypervisorType | *hyperv (hyperv)* |
|HypervisorId (på andra) | Hypervisorns unika ID | 
|HostingProvider | *Azure* |
|_ResourceId | Den unika identifieraren för en Azure-resurs |
|AzureSubscriptionId | En globalt unik identifierare som identifierar din prenumeration | 
|AzureResourceGroup | Namnet på den Azure-resursgrupp som datorn är medlem i. |
|AzureResourceName | Namnet på Azure-resursen |
|AzureLocation (AzureLocation) | Platsen för Azure-resursen |
|AzureUpdateDomain | Namnet på Azure-uppdateringsdomänen |
|AzureFaultDomain | Namnet på Azure-feldomänen |
|AzureVmId | Den unika identifieraren för den virtuella Azure-datorn |
|AzureSize (AzureSize) | Storleken på den virtuella Azure-datorn |
|AzureImagePublisher | Namnet på Azure VM-utgivaren |
|AzureImage Erbjuder | Namnet på erbjudandetypen för Azure VM | 
|AzureImageSku | SKU för Azure VM-avbildningen | 
|AzureImageVersion | Versionen av Azure VM-avbildningen | 
|AzureCloudServiceName | Namnet på Azure-molntjänsten |
|AzureCloudServiceDeployment | Distributions-ID för molntjänsten |
|AzureCloudServiceRoleName | Rollnamn för Molntjänst |
|AzureCloudServiceRoleType | Rolltyp för Molntjänst: *arbetare* eller *webb* |
|AzureCloudServiceInstanceId | ID för molntjänstrollinstans |
|AzureVmScaleSetName | Namnet på skaluppsättningen för den virtuella datorn |
|Villa om AzureVmScaleSet-utläggning | Distributions-ID för distribution av virtuell dator |
|AzureVmScaleSetResourceId | Den unika identifieraren för den virtuella datorn skala uppsättning resurs.|
|AzureVmScaleSetInstanceId | Den unika identifieraren för skaluppsättningen för den virtuella datorn |
|AzureServiceFabricClusterId | Den unika identifer av Azure Service Fabric-klustret | 
|AzureServiceFabricClusterName | Namnet på Azure Service Fabric-klustret |

### <a name="vmprocess-records"></a>VMProcess-poster

Poster med en typ av *VMProcess* har lagerdata för TCP-anslutna processer på servrar med beroendeagenten. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
|TenantId | Den unika identifieraren för arbetsytan |
|SourceSystem | *Insikter* | 
|TimeGenerated | Tidsstämpel för posten (UTC) |
|Dator | Datorn FQDN | 
|AgentId (på andra) | Det unika ID:t för Log Analytics-agenten |
|Dator | Namn på Azure Resource Manager-resursen för den dator som exponeras av ServiceMap. Det är av formuläret *m-{GUID}*, där *GUID* är samma GUID som AgentId. | 
|Process | Den unika identifieraren för servicekartprocessen. Det är i form av *p-{GUID}*. 
|KörbartNamn | Namnet på den körbara processen | 
|DisplayName | Visningsnamn för process |
|Roll | Processroll: *webbserver*, *appServer*, *databaseServer*, *ldapServer*, *smbServer* |
|Grupp | Namn på processgrupp. Processer i samma grupp är logiskt relaterade, t.ex. |
|StartTime | Processpoolens starttid |
|FörstaPid (förstapid) | Det första PID i processpoolen |
|Beskrivning | Processbeskrivningen |
|CompanyName | Namnet på företaget |
|Interntnamn | Det interna namnet |
|ProductName | Produktens namn |
|ProductVersion | Versionen av produkten |
|FileVersion (Filversion) | Den version av filen |
|Körbarpath |Sökvägen till den körbara |
|Kommandorad | Kommandoraden |
|WorkingDirectory | Arbetskatalogen |
|Tjänster | En rad tjänster enligt vilka processen körs |
|UserName | Kontot under vilket processen körs |
|AnvändareDomän | Den domän under vilken processen körs |
|_ResourceId | Den unika identifieraren för en process inom arbetsytan |


## <a name="sample-map-queries"></a>Exempel på kartfrågor

### <a name="list-all-known-machines"></a>Lista alla kända maskiner

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>När startades den virtuella datorn senast om

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Sammanfattning av virtuella Azure-datorer efter avbildning, plats och SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by ComputerName, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Lista den fysiska minneskapaciteten för alla hanterade datorer

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Lista datornamn, DNS, IP och OPERATIVSYSTEM

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Hitta alla processer med "sql" på kommandoraden

```kusto
VMComputer | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Hitta en dator (senaste posten) efter resursnamn

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Hitta en dator (senaste posten) efter IP-adress

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Lista alla kända processer på en angiven dator

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Lista alla datorer som kör SQL Server

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "\*sql\*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista alla unika produktversioner av curl i mitt datacenter

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Skapa en datorgrupp över alla datorer som kör CentOS

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct ComputerName
```

### <a name="bytes-sent-and-received-trends"></a>Byte skickade och mottagna trender

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Vilka virtuella Azure-datorer överför flest byte

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Länka statustrender

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Trend för anslutningsfel

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Bundna portar

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Antal öppna portar mellan datorer

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Poängprocesser på arbetsytan efter antalet portar som de har öppna

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Aggregerat beteende för varje port

Den här frågan kan sedan användas för att poängsätta portar efter aktivitet, t.ex.
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
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
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

## <a name="performance-records"></a>Resultatposter
Poster med en typ av *InsightsMetrics* har prestandadata från den virtuella datorns gästoperativsystem. Dessa poster har egenskaperna i följande tabell:


| Egenskap | Beskrivning |
|:--|:--|
|TenantId | Unik identifierare för arbetsytan |
|SourceSystem | *Insikter* | 
|TimeGenerated | Tid då värdet samlades in (UTC) |
|Dator | Datorn FQDN | 
|Ursprung | *vm.azm.ms* |
|Namnområde | Kategori av prestandaräknaren | 
|Namn | Prestandaräknarens namn |
|Val (val) | Insamt värde | 
|Taggar | Relaterad information om posten. Se tabellen nedan för taggar som används med olika posttyper.  |
|AgentId (på andra) | Unik identifierare för varje dators agent |
|Typ | *InsikterMetri* |
|_ResourceId_ | Resurs-ID för den virtuella datorn |

De prestandaräknare som för närvarande samlas in i tabellen *InsightsMetrics* visas i följande tabell:

| Namnområde | Namn | Beskrivning | Enhet | Taggar |
|:---|:---|:---|:---|:---|
| Dator    | Pulsslag             | Dator hjärtslag                        | | |
| Minne      | Tillgängligt MB           | Tillgängliga byte för minne                    | Byte          | memorySizeMB - Total minnesstorlek|
| Nätverk     | WriteBytesPerSecond   | Nätverksskrivningsbyte per sekund            | BytesPerSecond | NetworkDeviceId - Id för enheten<br>byte - Totalt antal skickade byte |
| Nätverk     | ReadBytesPerSecond    | Läs byte per sekund för nätverk             | BytesPerSecond | networkDeviceId - Id för enheten<br>byte - Totalt antal mottagna byte |
| Processor   | UtnyttjandePercentage | Procentsats för processoranvändning          | Procent        | totalCpus - Totalt antal processorer |
| Logisk disk | SkriverPerSecond       | Logiska diskskrivningar per sekund            | CountPerSecond | mountId - Montera ID för enheten |
| Logisk disk | WriteLatencyMs        | Millisekunder för logisk diskskrivningssvarning    | Millisekunder   | mountId - Montera ID för enheten |
| Logisk disk | WriteBytesPerSecond   | Skrivna byte per sekund för logiska diskar       | BytesPerSecond | mountId - Montera ID för enheten |
| Logisk disk | ÖverföringarPerSecond    | Logiska disköverföringar per sekund         | CountPerSecond | mountId - Montera ID för enheten |
| Logisk disk | TransferLatencyMs     | Fräsningssvarskvarnund för logisk disköverföring | Millisekunder   | mountId - Montera ID för enheten |
| Logisk disk | LäserPerSecond        | Logiska diskläsningar per sekund             | CountPerSecond | mountId - Montera ID för enheten |
| Logisk disk | ReadLatencyMs         | Fräsningskvarnund för logisk disk     | Millisekunder   | mountId - Montera ID för enheten |
| Logisk disk | ReadBytesPerSecond    | Logiska diskläsbyten per sekund        | BytesPerSecond | mountId - Montera ID för enheten |
| Logisk disk | FreeSpacePercentage (frigömt)   | Procent av ledigt utrymme för logisk disk        | Procent        | mountId - Montera ID för enheten |
| Logisk disk | FreeSpaceMB (fri)           | Växlingsbyte för logiskt diskfritt utrymme             | Byte          | mountId - Montera ID för enheten<br>diskSizeMB - Total diskstorlek |
| Logisk disk | BytesPerSecond        | Logiska diskbyten per sekund             | BytesPerSecond | mountId - Montera ID för enheten |


## <a name="next-steps"></a>Nästa steg

* Om du inte har använt loggfrågor i Azure Monitor tidigare kan du granska [hur du använder Log Analytics](../../azure-monitor/log-query/get-started-portal.md) i Azure-portalen för att skriva loggfrågor.

* Läs mer om [hur du skriver sökfrågor](../../azure-monitor/log-query/search-queries.md).
