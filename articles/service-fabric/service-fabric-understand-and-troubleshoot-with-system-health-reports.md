---
title: "Felsöka med systemet hälsorapporter | Microsoft Docs"
description: "Beskriver hälsorapporter som skickas av Azure Service Fabric-komponenter och deras användning för felsökning kluster eller programproblem"
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: oanapl
ms.openlocfilehash: 42dca05c4d7d104ed0e7e21f1e53411e5983cd38
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Felsök med hjälp av systemhälsorapporter
Azure Service Fabric-komponenter ger systemhälsa rapporter om alla entiteter i klustret direkt ur lådan. Den [hälsoarkivet](service-fabric-health-introduction.md#health-store) skapar och tar bort enheter baserat på systemrapporter. Även ordnar dem i en hierarki som samlar in entiteten interaktioner.

> [!NOTE]
> För att förstå hälsorelaterade begrepp, kan du läsa mer i [Service Fabric-hälsomodell](service-fabric-health-introduction.md).
> 
> 

Rapporter om hälsotillstånd ger inblick i klustret och programfunktioner och flaggan problem. För program och tjänster Kontrollera system hälsorapporter att entiteter implementeras och fungerar korrekt ur ett Service Fabric. Rapporterar ger inte någon hälsoövervakning av affärslogiken för tjänsten eller detekteringen av låsta processer. Användaren tjänster kan utöka hälsa-data med information som är specifika för sin logik.

> [!NOTE]
> Hälsorapporter som skickas av användaren watchdogs visas endast *när* systemkomponenter skapa en entitet. När du tar bort en entitet bort health store automatiskt alla hälsorapporter som associeras med den. Detsamma gäller när en ny instans av entiteten har skapats, till exempel när en ny stateful beständiga tjänstinstans för repliken har skapats. Alla rapporter som är associerade med den gamla instansen tas bort och rensa från store.
> 
> 

Systemkomponenten rapporterar identifieras av källa som börjar med den ”**System.**” prefix. Watchdogs kan inte använda samma prefix för deras källor som avvisas rapporter med ogiltiga parametrar.

Nu ska vi titta på vissa systemrapporter att förstå vad utlöser dem och lär dig att åtgärda eventuella problem som de representerar.

> [!NOTE]
> Service Fabric fortsätter att lägga till rapporter på villkor intressanta som förbättrar inblick i vad som händer i klustret och program befintliga rapporter kan förbättras med mer information för att felsöka problemet snabbare.
> 
> 

## <a name="cluster-system-health-reports"></a>Klustret system hälsorapporter
Entiteten hälsa kluster skapas automatiskt i health store. Om allt fungerar korrekt, har den inte en rapport.

### <a name="neighborhood-loss"></a>Förlust av nätverket
**System.Federation** rapporterar ett fel när den identifierar en förlust av nätverket. Rapporten är från enskilda noder och nod-ID ingår i namnet. Om en nätverket förlorat i hela Service Fabric ringen kan förvänta du vanligtvis två händelser som representerar båda sidor av lucka i rapporten. Om flera neighborhoods går förlorade, finns det flera händelser.

Rapporten anger den globala kontraktstidsgräns som den time to live (TTL). Rapporten skickas varje hälften av TTL-tiden för så länge villkoret förblir aktiv. Händelsen tas bort automatiskt när den upphör. Ta bort när gått beteendet garanteras att rapporten har rensats bort från health store korrekt, även om noden reporting är nere.

* **SourceId**: System.Federation
* **Egenskapen**: börjar med **nätverket** och innehåller information om noden.
* **Nästa steg**: undersöka varför i nätverket går förlorad, till exempel, kontrollera kommunikationen mellan noder i klustret.

### <a name="rebuild"></a>Återskapa

Den **Failover Manager** service (**FM**) hanterar information om klusternoderna. När FM förlorar data och leder till förlust av data den inte kan garantera att har den den allra senaste informationen om klusternoderna. I det här fallet systemet genomgår en **återskapa**, och **System.FM** samlar in data från alla noder i klustret för att återskapa dess tillstånd. Ibland på grund av nätverks- eller problem med noden kan återskapa hämta fastnat eller stoppats. Det kan hända med den **Failover Manager Master** service (**FMM**). Den **FMM** är en tillståndslös systemtjänst som håller reda på om alla de **FMs** i klustret. Den **FMMs** primära är alltid noden med ID som är närmast 0. Om noden utesluts en **återskapa** utlöses.
När något av föregående villkor inträffar, **System.FM** eller **System.FMM** flaggan via en felrapport. Återskapa kan ha fastnat i en av två faser:

* Väntar på sändningen: **FM/FMM** väntar broadcast-meddelande-svar från de andra noderna. **Nästa steg:** undersöka om det finns ett nätverksanslutningsproblem mellan noder.   
* Väntar på noder: **FM/FMM** redan tagit emot ett broadcast svar från de andra noderna och väntar på svar från specifika noder. Hälsorapporten visar noderna som den **FM/FMM** väntar på ett svar. **Nästa steg:** undersöka nätverksanslutningen mellan de **FM/FMM** och de angivna noderna. Ta reda på varje nod som visas för andra möjliga problem.

* **SourceID**: System.FM eller System.FMM
* **Egenskapen**: återskapa.
* **Nästa steg**: undersöka nätverksanslutningen mellan noder, samt status för de specifika noder som visas på beskrivningen av rapporten hälsa.

## <a name="node-system-health-reports"></a>Noden system hälsorapporter
**System.FM**, som representerar Failover Manager-tjänsten är utfärdaren som hanterar information om klusternoderna. Varje nod bör ha en rapport från System.FM som visar sitt tillstånd. Noden enheter tas bort när tillståndet noden tas bort. Mer information finns i [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### <a name="node-updown"></a>Noden upp/ned
System.FM rapporter som OK när noden ansluter ringen (det är igång). Ett fel rapporteras när noden avgår ringen (den är nere, antingen för att uppgradera eller helt enkelt eftersom den inte kunde). Hälsotillstånd hierarkin som skapats av health store fungerar på distribuerade entiteter i samband med System.FM noden rapporter. Noden anser entiteter för alla distribuerade virtuella överordnad. Distribuerade enheterna på noden som är tillgängliga via frågor om noden rapporteras som drift av System.FM med samma instans som den instans som är associerade med entiteterna. När System.FM rapporterar att noden är inte eller startas om som en ny instans health store rensas automatiskt de distribuerade entiteter som kan finnas endast på noden nedåt eller på den föregående versionen av noden.

* **SourceId**: System.FM
* **Egenskapen**: tillstånd.
* **Nästa steg**: om noden avser en uppgradering, det ska gå tillbaka när den har uppgraderats. I det här fallet bör hälsotillståndet växla tillbaka till OK. Om noden inte gå tillbaka eller om det misslyckas måste problemet mer undersökning.

I följande exempel visas System.FM händelse med ett hälsotillstånd OK för noden:

```PowerShell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Certifikatet upphör att gälla
**System.FabricNode** rapporterar en varning när certifikat som används av noden närmar sig förfallodatum. Det finns tre certifikat per nod: **Certificate_cluster**, **Certificate_server**, och **Certificate_default_client**. När upphör att gälla är minst två veckor, är hälsotillståndet för rapporten OK. När upphör att gälla inom två veckor är rapporttypen en varning. TTL-värde på dessa händelser är oändligt och de tas bort när en nod lämnar klustret.

* **SourceId**: System.FabricNode
* **Egenskapen**: börjar med **certifikat** och innehåller mer information om vilken certifikattyp.
* **Nästa steg**: uppdatera certifikat om de är nära förfallodatum.

### <a name="load-capacity-violation"></a>Läsa in kapacitetsöverträdelse
Service Fabric-Belastningsutjämnarens rapporterar en varning när den identifierar en kapacitetsöverträdelse för noden.

* **SourceId**: System.PLB
* **Egenskapen**: börjar med **kapacitet**.
* **Nästa steg**: Kontrollera den angivna måtten och visa den aktuella kapaciteten på noden.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Matchningsfel för noden kapacitet för resursen styrning mått
System.Hosting rapporter en varning om noden kapacitet har definierats i klustermanifestet är större än den verkliga nod kapaciteten för resursen styrning mått (minne och cpu-kärnor). Hälsorapport visas när första tjänstpaket som använder [resurs styrning](service-fabric-resource-governance.md) registrerar på en angiven nod.

* **SourceId**: System.Hosting
* **Egenskapen**: ResourceGovernance
* **Nästa steg**: Detta kan vara ett problem som styrande servicepaket inte tillämpas som förväntat och [resurs styrning](service-fabric-resource-governance.md) fungerar inte korrekt. Uppdatera klustermanifestet med rätt nod kapacitet för de här måtten eller ange dem på alla och låta Service Fabric för att automatiskt identifiera tillgängliga resurser.

## <a name="application-system-health-reports"></a>Programmet system hälsorapporter
**System.CM**, som representerar klustret Manager-tjänsten är utfärdaren som hanterar information om ett program.

### <a name="state"></a>Status
System.CM rapporterar som OK när programmet har skapats eller uppdaterats. Informerar arkivet hälsotillstånd när programmet har tagits bort, så att den kan tas bort från store.

* **SourceId**: System.CM
* **Egenskapen**: tillstånd.
* **Nästa steg**: om programmet har skapats eller uppdaterats, ska det finnas hälsorapport Klusterhanteraren. I annat fall söker tillståndet för programmet genom att utfärda en fråga, till exempel PowerShell-cmdleten **ServiceFabricApplication Get - ApplicationName** *applicationName*.

I följande exempel visas händelsen på den **fabric: / WordCount** program:

```PowerShell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Tjänsten system hälsorapporter
**System.FM**, som representerar Failover Manager-tjänsten är auktoritet som hanterar information om tjänster.

### <a name="state"></a>Status
System.FM rapporter som OK när tjänsten har skapats. Det tar bort enheten från health store när tjänsten har tagits bort.

* **SourceId**: System.FM
* **Egenskapen**: tillstånd.

I följande exempel visas händelsen på tjänsten **fabric: / WordCount/WordCountWebService**:

```PowerShell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Fel i tjänsten korrelation
**System.PLB** rapporterar ett fel när den identifierar att uppdatera en tjänst korreleras med en annan tjänst som skapar en tillhörighetskedja. Rapporten är avmarkerad när uppdateringen sker.

* **SourceId**: System.PLB
* **Egenskapen**: ServiceDescription.
* **Nästa steg**: Kontrollera korrelerade service beskrivningar.

## <a name="partition-system-health-reports"></a>Partitionen system hälsorapporter
**System.FM**, som representerar Failover Manager-tjänsten är utfärdaren som hanterar information om tjänsten partitioner.

### <a name="state"></a>Status
System.FM rapporterar som OK när partitionen har skapats och är felfri. Det tar bort enheten från health store när partitionen tas bort.

Om partitionen är mindre än det minsta antalet, rapporterar ett fel. Om partitionen är inte mindre än det minsta antalet, men den är under målet replikantalet, rapporterar en varning. Om partitionen förlorar kvorum, rapporterar System.FM ett fel.

Andra viktiga händelser innehåller en varning när omkonfiguration tar längre tid än förväntat och när bygga tar längre tid än förväntat. Den förväntade gånger för att bygga och omkonfiguration kan konfigureras baserat på service-scenarier. Till exempel om en tjänst har en terabyte tillstånd, till exempel Azure SQL Database tar bygga längre tid än för en tjänst med en liten mängd tillstånd.

* **SourceId**: System.FM
* **Egenskapen**: tillstånd.
* **Nästa steg**: om hälsotillståndet inte är OK, är det möjligt att repliker inte har skapats, öppnas, eller befordras till primär eller sekundär korrekt. 

Om beskrivningen beskriver förlorar kvorum, sedan tillbaka undersöka detaljerad hälsorapport för repliker som är nere och infogar säkerhetskopiera bidrar till att sätta partitionen online.

Om beskrivningen beskriver en partition som fastnat i [omkonfiguration](service-fabric-concepts-reconfiguration.md), och sedan hälsorapport på den primära repliken tillhandahåller ytterligare information.

För andra System.FM hälsorapporter skulle det finnas rapporter på replikerna eller partition eller tjänst från andra systemkomponenter. 

I följande exempel beskrivs några av de här rapporterna. 

I följande exempel visas en felfri partition:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

I följande exempel visar hälsotillståndet för en partition som är lägre än målet replikantalet. Nästa steg är att hämta partitionen beskrivning, som visar hur den är konfigurerad: **MinReplicaSetSize** är tre och **TargetReplicaSetSize** sju. Hämta sedan antalet noder i klustret, som i det här fallet är fem. Så i det här fallet två repliker kan inte placeras, eftersom målet antal repliker är högre än antalet noder som är tillgängliga.

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

Följande exempel visar hälsotillståndet för en partition som har fastnat i omkonfiguration på grund av att användaren inte respektera annulleringen token i den **RunAsync** metod. Undersöka hälsorapport för alla repliker som markerats som primär (P) hjälper dig att öka detaljnivån ned ytterligare på problemet.

```PowerShell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Den här hälsorapporten visar tillståndet för repliker av partitionen väldigt omkonfiguration: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

För varje replik innehåller hälsorapporten
- Den tidigare konfigurationen roll
- Den aktuella konfigurationen rollen
- [Replikens tillstånd](service-fabric-concepts-replica-lifecycle.md)
- Noden som repliken körs på.
- Replik-ID

I fall som exempel kan ytterligare krävs undersökning. Undersöka hälsotillståndet för varje enskild replik som börjar med repliker som markerats som `Primary` och `Secondary` (131482789658160654 och 131482789688598467) i föregående exempel.

### <a name="replica-constraint-violation"></a>Replik begränsningsfel
**System.PLB** rapporterar en varning om den identifierar en replik begränsningsfel och går inte att placera alla partition repliker. Rapportdetaljer visar vilka villkor och egenskaper förhindra replik-placering.

* **SourceId**: System.PLB
* **Egenskapen**: börjar med **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Replik system hälsorapporter
**System.RA**, som representerar komponenten omkonfiguration agent är auktoritet för replik-tillstånd.

### <a name="state"></a>Status
System.RA rapporterar OK när repliken har skapats.

* **SourceId**: System.RA
* **Egenskapen**: tillstånd.

I följande exempel visas en felfri replik:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus ReplicaCloseStatus, ReplicaChangeRoleStatus
Den här egenskapen används för att ange varningar eller fel vid försök att öppna en replik, stänga en replik eller överföra en replik från en roll till en annan. Mer information finns i [replik livscykel](service-fabric-concepts-replica-lifecycle.md). Felen kan vara undantag från API-anrop eller kraschar för tjänstprocessen värden under denna tid. För fel som beror på API-anrop från C#-kod, Service Fabric lägger till undantag och stackspårning hälsorapporten.

Dessa hälsovarningar aktiveras efter att ha försökt åtgärden lokalt vissa antalet gånger (beroende på principen). Service Fabric görs ett nytt försök upp till ett högsta tröskelvärde. När det största tillåtna tröskelvärdet har uppnåtts, kan den försöker du vidta åtgärder för att rätta till situationen. Det här försöket kan orsaka dessa varningar tas bort när den ger på åtgärden på den här noden. Om en replik misslyckas öppna på en nod, genererar Service Fabric en varning om hälsa. Om repliken fortfarande inte går att öppna, fungerar Service Fabric om du vill reparera själv. Den här åtgärden kan handla om försök samma åtgärd på en annan nod. Detta gör varningen aktiveras för denna replik rensas. 

* **SourceId**: System.RA
* **Egenskapen**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, och **ReplicaChangeRoleStatus**.
* **Nästa steg**: undersöka service kod eller kraschar Dumpar att beskriva varför åtgärden misslyckas.

Följande exempel visar hälsotillståndet för en replik som utlöste `TargetInvocationException` från open-metoden. Beskrivningen innehåller felpunkt, **IStatefulServiceReplica.Open**, undantagstyp **TargetInvocationException**, och stack-spårning.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

I följande exempel visas en replik som ständigt kraschar under stängningen:

```PowerShell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Omkonfiguration
Den här egenskapen används för att ange när en replik som utför en [omkonfiguration](service-fabric-concepts-reconfiguration.md) identifierar att omkonfiguration har stoppats eller fastnar. Den här hälsorapport kanske på repliken vars aktuella roll är primär, förutom i händelse av en primär Omkonfigurering av växlingsutrymme, där det kan vara på repliken degraderas från primär till aktiv sekundär.

Ha kan fastnat omkonfiguration av något av följande skäl:

- En åtgärd på den lokala repliken samma replik som utför omkonfiguration, slutförs inte. I det här fallet innehålla undersöker hälsorapporter på den här repliken från andra komponenter, System.RAP eller System.RE, ytterligare information.

- En åtgärd slutförs inte på en Fjärreplik. Repliker som har väntande åtgärder visas i rapporten hälsa. Ytterligare undersökning bör utföras på hälsorapporter för de fjärranslutna replikerna. Det kan också vara kommunikationsproblem mellan den här noden och fjärrnoden.

I sällsynta fall kan ha fastnat omkonfiguration på grund av kommunikation eller andra problem mellan den här noden och Failover Manager-tjänsten.

* **SourceId**: System.RA
* **Egenskapen**: **omkonfiguration**.
* **Nästa steg**: undersöka lokal eller fjärransluten repliker beroende på beskrivningen av rapporten hälsa.

I följande exempel visas en hälsorapport där en omkonfiguration har fastnat på den lokala repliken. I det här exemplet, det på grund av en tjänst inte respektera cancellation-token.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

Följande exempel visar en rapport om en Omkonfigurering av sitter fast väntar på svar från två fjärranslutna repliker. I det här exemplet att det finns tre repliker i partition, inklusive den aktuella primärt. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Den här hälsorapporten visar att omkonfiguration fastnar väntar på svar från två repliker: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

För varje replik får du följande information:
- Den tidigare konfigurationen roll
- Den aktuella konfigurationen rollen
- [Replikens tillstånd](service-fabric-concepts-replica-lifecycle.md)
- Nod-ID
- Replik-ID

Avblockera omkonfiguration:
- Den **ned** repliker ska hämtas. 
- Den **inbuild** repliker ska slutföra bygga och övergång till redo.

### <a name="slow-service-api-call"></a>Långsam service API-anrop
**System.RAP** och **System.Replicator** rapportera en varning om ett anrop till användarkod för tjänsten tar längre tid än den konfigurerade tidpunkten. Varningen rensas när anropet har slutförts.

* **SourceId**: System.RAP eller System.Replicator
* **Egenskapen**: namnet på den långsamma API. Beskrivningen ger mer information om den tid som API: et har väntande.
* **Nästa steg**: undersöka varför anropet tar längre tid än förväntat.

I följande exempel visar händelsen hälsa från System.RAP för en tillförlitlig tjänst som inte respektera annulleringen token i **RunAsync**:

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

Egenskapen och text kan du ange vilka API har fastnat. Nästa steg ska utföras för olika låsta API: er är olika. API: er på den *IStatefulServiceReplica* eller *IStatelessServiceInstance* är vanligtvis ett fel i kod. I följande avsnitt beskrivs hur dessa översätta till den [Reliable Services modellen](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: den här varningen anger att ett anrop till `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, eller om åsidosätts, `OnOpenAsync` har fastnat.

- **IStatefulServiceReplica.Close** och **IStatefulServiceReplica.Abort**: i de flesta fall är en tjänst som inte respektera annullering token som skickas till `RunAsync`. Det kan också vara som `ICommunicationListener.CloseAsync`, eller om åsidosätts, `OnCloseAsync` har fastnat.

- **IStatefulServiceReplica.ChangeRole (S)** och **IStatefulServiceReplica.ChangeRole(N)**: i de flesta fall är en tjänst som inte respektera annullering token som skickas till `RunAsync`.

- **IStatefulServiceReplica.ChangeRole(P)**: i de flesta fall är att tjänsten inte har returnerat en aktivitet från `RunAsync`.

Andra API-anrop som kan fastna finns på den **IReplicator** gränssnitt. Exempel:

- **IReplicator.CatchupReplicaSet**: den här varningen anger ett av två saker. Antingen finns otillräcklig repliker som kan fastställas genom att titta på replikens status repliker i partitionen eller System.FM hälsorapport för en fast omkonfigurering. Eller replikerna godkänner inte åtgärder. PowerShell-kommandot-let `Get-ServiceFabricDeployedReplicaDetail` kan användas för att fastställa förloppet för alla repliker. Problemet med repliker vars `LastAppliedReplicationSequenceNumber` skyddas av primärt `CommittedSequenceNumber`.

- **IReplicator.BuildReplica (<Remote ReplicaId>)**: den här varningen anger ett problem i build-processen. Mer information finns i [replik livscykel](service-fabric-concepts-replica-lifecycle.md). Det kan bero på en felaktig konfiguration av replikatorn-adress. Mer information finns i [konfigurera tillståndskänsliga Reliable Services](service-fabric-reliable-services-configuration.md) och [ange resurser i en tjänstmanifestet](service-fabric-service-manifest-resources.md). Det kan också vara ett problem i fjärrnoden.

### <a name="replication-queue-full"></a>Replikeringskön är full
**System.Replicator** rapporterar en varning när Replikeringskön är full. På primärt blir Replikeringskön vanligtvis full eftersom en eller flera sekundära repliker tar lång tid att godkänna åtgärder. På sekundärt inträffar detta när tjänsten går långsamt att tillämpa åtgärderna. Varningen rensas när kön är inte längre fullständig.

* **SourceId**: System.Replicator
* **Egenskapen**: **PrimaryReplicationQueueStatus** eller **SecondaryReplicationQueueStatus**, beroende på replik-rollen.

### <a name="slow-naming-operations"></a>Långsam Naming åtgärder
**System.NamingService** rapporterar hälsotillståndet på den primära repliken när en Naming åtgärden tar längre tid än godkända. Exempel på Naming operations är [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) eller [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Flera metoder kan hittas under FabricClient, till exempel [tjänsten metoder för hantering av](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) eller [metoder för hantering av egenskapen](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Tjänsten Naming matchar tjänstnamn till en plats i klustret och gör att användarna kan hantera tjänstnamn och egenskaper. Det är en partitionerad bestående Service Fabric-tjänst. En av partitionerna representerar den *Authority Owner*, som innehåller metadata om alla Service Fabric-namn och tjänster. Service Fabric-namnen mappas till olika partitioner, kallas *Name Owner* partitioner, så att tjänsten kan utökas. Läs mer om den [Naming service](service-fabric-architecture.md).
> 
> 

När en Naming åtgärden tar längre tid än förväntat flaggas igen med en varning-rapport på den primära repliken för Naming service-partition som fungerar igen. Om åtgärden slutförs är varningen avmarkerad. Om åtgärden har slutförts med ett fel, innehåller hälsa rapporten information om felet.

* **SourceId**: System.NamingService
* **Egenskapen**: börjar med prefixet ”**Duration_**” och identifierar långsam igen och Service Fabric-namnet som åtgärden utförs. Till exempel om skapar tjänst med namnet **fabric: / MyApp/MyService** tar för lång, egenskapen är **Duration_AOCreateService.fabric:/MyApp/MyService**. ”AO” pekar på rollen för Naming partition för det här namnet och åtgärden.
* **Nästa steg**: Kontrollera att orsaken Naming åtgärden misslyckas. Varje åtgärd kan ha olika rotorsaker. Till exempel kan ta bort tjänsten ha fastnat. Tjänsten kan ha fastnat eftersom programvärden håller kraschar på en nod på grund av ett programfel för användare i kod.

I följande exempel visas en Skapa-åtgärd i tjänsten. Åtgärden tog längre tid än den konfigurerade varaktigheten. ”AO” återförsök och skickar arbetsobjekt till ”Nej”. ”Nej” slutföra den sista åtgärden med TIMEOUT. I det här fallet är samma replik primär för både ”AO” och ”Nej” roller.

```PowerShell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication system hälsorapporter
**System.Hosting** är auktoritet på distribuerade entiteter.

### <a name="activation"></a>Aktivering
System.Hosting rapporterar som OK när ett program har aktiverats på noden. Annars rapporterar ett fel.

* **SourceId**: System.Hosting
* **Egenskapen**: aktivering, inklusive distribution-version.
* **Nästa steg**: om programmet är i feltillstånd, undersöka varför aktiveringen misslyckades.

I följande exempel visas en lyckad aktivering:

```PowerShell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Ladda ned
System.Hosting rapporterar ett fel om inte paketet ned programmet.

* **SourceId**: System.Hosting
* **Egenskapen**: **hämta:***RolloutVersion*.
* **Nästa steg**: undersöka varför hämtningen misslyckades på noden.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage system hälsorapporter
**System.Hosting** är auktoritet på distribuerade entiteter.

### <a name="service-package-activation"></a>Aktivering av Service-paket
System.Hosting rapporter som OK om tjänsten paketet aktiveringen på noden har genomförts. Annars rapporterar ett fel.

* **SourceId**: System.Hosting
* **Egenskapen**: aktivering.
* **Nästa steg**: undersöka varför aktiveringen misslyckades.

### <a name="code-package-activation"></a>Aktivering av kod paketet
System.Hosting rapporter som OK för varje kodpaketet om aktiveringen genomförs. Om aktiveringen misslyckas rapporterar en varning som konfigurerats. Om **CodePackage** inte kan aktivera eller avslutas med ett fel som är större än den konfigurerade **CodePackageHealthErrorThreshold**, värd rapporterar ett fel. Om en tjänstepaketet innehåller flera kod paket, genereras en aktivering-rapporten för varje kriterium.

* **SourceId**: System.Hosting
* **Egenskapen**: namnområdesprefixet **CodePackageActivation** och innehåller namnet på kodpaketet och startpunkt som **CodePackageActivation:***CodePackageName* :*SetupEntryPoint/EntryPoint*. Till exempel **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Typ av registrering
System.Hosting rapporter som OK om tjänsttypen har registrerats. Ett fel rapporteras om den inte registrerats i tid, som konfigureras med hjälp av **ServiceTypeRegistrationTimeout**. Om körningen är stängt, service-typen är avregistrera från noden och värd rapporterar en varning.

* **SourceId**: System.Hosting
* **Egenskapen**: namnområdesprefixet **ServiceTypeRegistration** och innehåller namnet på tjänsten. Till exempel **ServiceTypeRegistration:FileStoreServiceType**.

I följande exempel visas en felfri distribuerat tjänstpaket:

```PowerShell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Ladda ned
System.Hosting rapporterar ett fel om inte paketet ned service.

* **SourceId**: System.Hosting
* **Egenskapen**: **hämta:***RolloutVersion*.
* **Nästa steg**: undersöka varför hämtningen misslyckades på noden.

### <a name="upgrade-validation"></a>Validering av uppgradering
System.Hosting rapporterar ett fel om valideringen under uppgraderingen misslyckas eller om uppgraderingen misslyckas på noden.

* **SourceId**: System.Hosting
* **Egenskapen**: namnområdesprefixet **FabricUpgradeValidation** och innehåller den uppgradera versionen.
* **Beskrivning**: pekar på fel.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Odefinierad nod kapacitet för resursen styrning mått
System.Hosting rapporterar en varning om noden kapacitet inte har definierats i klustermanifestet och konfigurationen för automatisk identifiering är avstängd. Service Fabric höjer hälsotillstånd varning när tjänstpaket som använder [resurs styrning](service-fabric-resource-governance.md) registrerar på en angiven nod.

* **SourceId**: System.Hosting
* **Egenskapen**: ResourceGovernance
* **Nästa steg**: det bästa sättet att lösa problemet är att ändra klustermanifestet för att aktivera automatisk identifiering av tillgängliga resurser. Ett annat sätt uppdaterar klustermanifestet med korrekt angivna noden kapacitet för de här måtten.

## <a name="next-steps"></a>Nästa steg
[Visa Service Fabric-hälsorapporter](service-fabric-view-entities-aggregated-health.md)

[Hur du rapporten och kontrollera tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)

