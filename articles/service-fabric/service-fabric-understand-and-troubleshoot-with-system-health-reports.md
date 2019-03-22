---
title: Felsöka med systemhälsorapporter | Microsoft Docs
description: Beskriver hälsorapporter som skickas av Azure Service Fabric-komponenter och deras användning för felsökning kluster eller programproblem
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d62fd909d10515c9217a4dd0aa760afa376b8d7c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838909"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Felsök med hjälp av systemhälsorapporter
Azure Service Fabric-komponenter a. systemtillståndsrapporter på alla entiteter i klustret kompletta Den [hälsoarkivet](service-fabric-health-introduction.md#health-store) skapar och tar bort enheter baserat på systemrapporter. Även ordnar dem i en hierarki som samlar in entiteten interaktioner.

> [!NOTE]
> Information om hälsorelaterade principerna, Läs mer i [hälsomodellen för Service Fabric](service-fabric-health-introduction.md).
> 
> 

Systemhälsorapporter ger bättre inblick i klustret och programfunktioner och flaggan problem. För program och tjänster Kontrollera systemhälsorapporter att entiteter implementeras och fungerar korrekt från Service Fabric-perspektivet. Rapporterna innehåller inte några hälsoövervakning av affärslogiken i tjänsten eller detekteringen av låsta processer. Tjänster som kan utöka health-data med information som är specifika för deras logik.

> [!NOTE]
> Hälsorapporter som skickas av användaren watchdogs syns bara *när* systemkomponenter skapar en entitet. När en entitet tas bort, tar hälsoarkivet automatiskt bort alla hälsorapporter som är kopplade till den. Samma sak gäller när en ny instans av entiteten har skapats. Ett exempel är när en ny tillståndskänsliga beständiga repliken tjänstinstans har skapats. Alla rapporter som är associerade med den gamla instansen tas bort och rensas från arkivet.
> 
> 

System-datorn rapporterar identifieras av källan som börjar med den ”**System.**” prefix. Watchdogs kan inte använda samma prefix för deras källor som avvisas rapporter med ogiltiga parametrar.

Låt oss titta på vissa systemrapporter att förstå vad utlöser dem och lär dig hur du åtgärdar de problem som de representerar.

> [!NOTE]
> Service Fabric fortsätter att lägga till rapporter på villkor av intresse som förbättrar bättre inblick i vad som händer i klustret och program. Befintliga rapporter kan förbättras med mer information för att felsöka problemet snabbare.
> 
> 

## <a name="cluster-system-health-reports"></a>Klustret systemhälsorapporter
Klustret hälsotillstånd entitet skapas automatiskt i health store. Om allt fungerar korrekt, har den inte en rapport.

### <a name="neighborhood-loss"></a>Neighborhood förlust
**System.Federation** rapporterar ett fel när den identifierar en stadsdel förlust. Rapporten från enskilda noder och nod-ID som ingår i namnet. Om en stadsdel försvinner i hela Service Fabric ringen, du vanligtvis förväntar sig två händelser som representerar båda sidor av gap-rapporten. Om flera neighborhoods går förlorade, finns det fler händelser.

Rapporten anger tidsgräns för global lån som time to live (TTL). Rapporten skickas varje hälften av TTL varaktigheten för så länge villkoret förblir aktiv. Händelsen tas bort automatiskt när den upphör att gälla. Ta bort när upphört beteendet garanteras att rapporten rensas från health store korrekt, även om noden reporting är nere.

* **SourceId**: System.Federation
* **Egenskapen**: Börjar med **stadsdel** och innehåller information om noden.
* **Nästa steg**: Undersök varför stadsdel går förlorad. Till exempel Kontrollera kommunikationen mellan klusternoderna.

### <a name="rebuild"></a>Återskapa

Tjänsten redundans Manager (FM) hanterar information om klusternoderna. När FM förlorar data och hamnar i förlust av data, kan inte garantera att den har den allra senaste informationen om klusternoderna. I det här fallet systemet går igenom återskapas och System.FM samlar in data från alla noder i klustret för att återskapa tillståndet. Ibland, på grund av nätverks- eller nod problem kan återskapning hämta fastnat eller stoppats. Samma kan inträffa med tjänsten fmm-Master (redundans Manager tjänsten). Den fmm-tjänsten är en tillståndslös systemtjänst som håller koll på där alla FMs finns i klustret. Den fmm-tjänsten primär är alltid nod med ID som är närmast 0. Om noden utesluts utlöses en återskapning.
När något av föregående villkor sker **System.FM** eller **System.FMM** flaggar via en felrapport. Återskapa kan ha fastnat i en av två faser:

* **Väntar på sändningen**: FM/fmm-tjänsten väntar broadcast-meddelande svaret från de andra noderna.

  * **Nästa steg**: Undersök om det finns ett nätverksproblem anslutningen mellan noder.
* **Väntar på noder**: FM/fmm-tjänsten redan tog emot ett sändningssvar från de andra noderna och väntar på ett svar från specifika noder. Hälsorapporten visar de noder som FM/fmm-tjänsten väntar på ett svar.
   * **Nästa steg**: Undersök nätverksanslutningen mellan FM/fmm-tjänsten och de listade noderna. Undersök varje listad nod för andra möjliga problem.

* **SourceID**: System.FM eller System.FMM
* **Egenskapen**: Återskapa.
* **Nästa steg**: Undersök nätverksanslutningen mellan noderna, samt tillståndet för de specifika noder som visas på beskrivningen av hälsorapporten.

## <a name="node-system-health-reports"></a>Noden systemhälsorapporter
System.FM som representerar tjänsten Redundanshanteraren är utfärdaren som hanterar information om klusternoderna. Varje nod bör ha en rapport från System.FM som visar sitt tillstånd. Entiteterna noden tas bort när tillståndet noden tas bort. Mer information finns i [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### <a name="node-updown"></a>Noden upp/ned
System.FM rapporterar som OK när noden läggs till ringen (det är igång). Den rapporterar ett fel när noden avgår ringen (det är inte tillgängligt, antingen för att uppgradera eller helt enkelt eftersom den har inte). Health-hierarkin som skapats av health store ska hantera distribuerade entiteter i korrelation med System.FM-nodrapporter. Det tar hänsyn till noden alla distribuerade entiteter virtuella överordnad. Distribuerade entiteter på noden som är tillgängliga via frågor om noden rapporteras som drift av System.FM med samma instans som den instans som är associerade med entiteter. När System.FM rapporterar att noden är inte igång eller startas om som en ny instans i hälsoarkivet rensas automatiskt de distribuerade entiteter som kan finnas endast på noden nedåt eller på föregående instans av noden.

* **SourceId**: System.FM
* **Egenskapen**: Tillstånd.
* **Nästa steg**: Om noden är nere för en uppgradering kan ska det gå tillbaka när den har uppgraderats. I det här fallet bör hälsotillståndet gå tillbaka till OK. Om noden inte gå tillbaka eller misslyckas, måste problemet mer undersökning.

I följande exempel visas System.FM-händelse med ett hälsotillstånd OK för nod:

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


### <a name="certificate-expiration"></a>Certifikatet löper ut
**System.FabricNode** rapporterar en varning när certifikat som används av noden närmar sig förfallodatum. Det finns tre certifikat per nod: **Certificate_cluster**, **Certificate_server**, och **Certificate_default_client**. När utvärderingsperioden är minst två veckor, är hälsotillståndet rapporten OK. När utvärderingsperioden är inom två veckor, är rapporttypen som en varning. TTL-värde på dessa händelser är oändligt, och de tas bort när en nod lämnar klustret.

* **SourceId**: System.FabricNode
* **Egenskapen**: Börjar med **certifikat** och innehåller mer information om certifikattyp.
* **Nästa steg**: Uppdatera certifikaten om de är snart går ut.

### <a name="load-capacity-violation"></a>Läsa in kapacitetsöverträdelse
Service Fabric-Belastningsutjämnarens rapporterar en varning när den identifierar en kapacitetsöverträdelse för noden.

* **SourceId**: System.PLB
* **Egenskapen**: Börjar med **kapacitet**.
* **Nästa steg**: Kontrollera de tillhandahållna mått och visa den aktuella kapaciteten på noden.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Noden kapacitet matchningsfel för mätvärden för resurs-styrning
System.Hosting rapporterar en varning om nodkapaciteterna har definierats i klustermanifestet är större än de faktiska nodkapaciteterna för mätvärden för resurs-styrning (minne och CPU-kärnor). En hälsorapport visas när det första tjänstpaketet som använder [resursstyrning](service-fabric-resource-governance.md) registrerar sig för en angiven nod.

* **SourceId**: System.Hosting
* **Egenskapen**: **ResourceGovernance**.
* **Nästa steg**: Det här problemet kan vara ett problem Eftersom styrande servicepaket inte tillämpas som förväntat och [resursstyrning](service-fabric-resource-governance.md) fungerar inte korrekt. Uppdatera klustermanifestet med rätt nodkapaciteterna för de här måtten eller inte ange dem och låta Service Fabric identifiera automatiskt tillgängliga resurser.

## <a name="application-system-health-reports"></a>Programmet systemhälsorapporter
System.CM som representerar tjänsten Cluster Manager, är utfärdaren som hanterar information om ett program.

### <a name="state"></a>Status
System.CM rapporterar som OK när programmet har skapats eller uppdaterats. Det informerar health store när programmet tas bort så att den kan tas bort från arkivet.

* **SourceId**: System.CM
* **Egenskapen**: Tillstånd.
* **Nästa steg**: Om programmet har skapats eller uppdaterats, ska den innehålla hälsorapport Cluster Manager. Annars kontrollerar du tillståndet för programmet genom att utfärda en fråga. Till exempel använda PowerShell-cmdleten **Get-ServiceFabricApplication - ApplicationName** *applicationName*.

I följande exempel visas händelsen tillstånd på den **fabric: / WordCount** program:

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

## <a name="service-system-health-reports"></a>Tjänsten systemhälsorapporter
System.FM som representerar tjänsten Redundanshanteraren är utfärdaren som hanterar information om tjänster.

### <a name="state"></a>Status
System.FM rapporterar som OK när tjänsten har skapats. Det tar bort entiteten från health store när tjänsten tas bort.

* **SourceId**: System.FM
* **Egenskapen**: Tillstånd.

I följande exempel visas händelsen tillstånd på tjänsten **fabric: / WordCount/WordCountWebService**:

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
**System.PLB** rapporterar ett fel när den identifierar att uppdatera en tjänst korreleras med en annan tjänst som skapar en tillhörighetskedja. Rapporten rensas när en lyckad uppdatering sker.

* **SourceId**: System.PLB
* **Egenskapen**: **ServiceDescription**.
* **Nästa steg**: Kontrollera korrelerade tjänstbeskrivningar.

## <a name="partition-system-health-reports"></a>Partition systemhälsorapporter
System.FM som representerar tjänsten Redundanshanteraren är utfärdaren som hanterar information om tjänstpartitioner.

### <a name="state"></a>Status
System.FM rapporterar som OK när partitionen har skapats och är felfri. Det tar bort entiteten från health store när partitionen tas bort.

Om partitionen är lägre än det minsta replikantalet, rapporterar ett fel. Om partitionen är inte under det lägsta replikantalet, men den hamnar under replikantal mål, rapporterar en varning. Om partitionen förlorar kvorum, rapporterar System.FM ett fel.

Andra viktiga händelser inkludera en varning när omkonfigurationen tar längre tid än förväntat och när bygget tar längre tid än förväntat. Den förväntade tider för bygg- och omkonfiguration kan konfigureras baserat på tjänst-scenarier. Till exempel om en tjänst har en terabyte tillstånd, till exempel Azure SQL Database, tar bygget längre tid än för en tjänst med en liten mängd tillstånd.

* **SourceId**: System.FM
* **Egenskapen**: Tillstånd.
* **Nästa steg**: Om hälsotillståndet inte är OK, är det möjligt att vissa repliker inte har skapats, öppnas, eller befordras till primär eller sekundär korrekt. 

Om beskrivningen beskriver förlorar kvorum, sedan tillbaka undersöka detaljerad hälsorapporten för repliker som är nere och infogar säkerhetskopiera bidrar till att ta med partitionen online.

Om beskrivningen beskriver en partition som fastnat i [omkonfiguration](service-fabric-concepts-reconfiguration.md), och sedan hälsorapport på den primära repliken tillhandahåller ytterligare information.

För andra System.FM hälsorapporter, skulle det finnas rapporter på replikerna eller partition eller tjänsten från andra systemkomponenter. 

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

I följande exempel visas hälsotillståndet för en partition som är lägre än målet replikantal. Nästa steg är att få partition beskrivning, som visar hur den är konfigurerad: **MinReplicaSetSize** är tre och **TargetReplicaSetSize** sju. Hämta sedan antalet noder i klustret, som i det här fallet är fem. Så i det här fallet två repliker kan inte placeras, eftersom antalet repliker är högre än antalet noder som är tillgängliga.

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

I följande exempel visas hälsotillståndet för en partition som har fastnat i omkonfiguration beror på att användaren inte respekterar annulleringen token i den **RunAsync** metod. Undersöka hälsorapport av alla repliker som markerats som primär (P) hjälper dig att öka detaljnivån ned ytterligare problemet.

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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Den här hälsorapporten visar tillståndet för repliker av partitionen omkonfiguration för tillfället: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

För varje replik innehåller hälsorapporten:
- Den tidigare konfigurationen roll
- Den aktuella konfigurationen roll
- [Replikens tillstånd](service-fabric-concepts-replica-lifecycle.md)
- Noden där replikeringen körs
- Replik-ID

I fall som i exempel ytterligare krävs utredning. Undersöka hälsotillståndet för varje enskild replik som börjar med repliker som markerats som `Primary` och `Secondary` (131482789658160654 och 131482789688598467) i föregående exempel.

### <a name="replica-constraint-violation"></a>Repliken begränsningsfel
**System.PLB** rapporterar en varning om den identifierar ett begränsningsfel för repliken och det går inte att placera alla partitionsrepliker. Rapportinformationen visar vilka begränsningar och egenskaper förhindra placeringen repliken.

* **SourceId**: System.PLB
* **Egenskapen**: Börjar med **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Repliken systemhälsorapporter
**System.RA**, som representerar omkonfigurationsagentkomponent är utfärdaren av replikens tillstånd.

### <a name="state"></a>Status
System.RA rapporterar OK när repliken har skapats.

* **SourceId**: System.RA
* **Egenskapen**: Tillstånd.

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

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Den här egenskapen används för att ange varningar eller fel vid försök att öppna en replik, stänga en replik eller överföra en replik från en roll till en annan. Mer information finns i [livscykel för replik](service-fabric-concepts-replica-lifecycle.md). Felen kan vara undantag från API-anrop eller krascher i processen att tjänsten värd under den här tiden. För fel som beror på API-anrop från C# kod, Service Fabric lägger till undantag och stack spårningen hälsorapporten.

Dessa hälsotillståndsvarningar aktiveras efter att ha försökt åtgärden lokalt vissa antal gånger (beroende på princip). Service Fabric görs ett nytt försök i upp till ett högsta tröskelvärde. När det högsta tröskelvärdet har uppnåtts, kan den försöka vidta åtgärder för att komma tillrätta med situationen. Det här försöket kan orsaka dessa varningar att tas bort när den ger på åtgärden på den här noden. Om en replik misslyckas så öppnas på en nod, till exempel genererar en varning om hälsotillstånd Service Fabric. Om repliken fortfarande inte går att öppna, fungerar Service Fabric för att reparera själv. Den här åtgärden kan handla om försöker samma åtgärd på en annan nod. Det här försöket gör varningen har aktiverats för den här repliken rensas. 

* **SourceId**: System.RA
* **Egenskapen**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, och **ReplicaChangeRoleStatus**.
* **Nästa steg**: Undersöka kod eller kraschdumpar för att identifiera anledningen till att åtgärden misslyckas.

I följande exempel visas hälsotillståndet för en replik som kastar `TargetInvocationException` från dess open-metod. Beskrivningen innehåller felpunkt, **IStatefulServiceReplica.Open**, Undantagstypen **TargetInvocationException**, och stackspårning.

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

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

I följande exempel visas en replik som ständigt kraschar när den stängdes:

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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Omkonfiguration
Den här egenskapen används för att ange när en replik som utför en [omkonfiguration](service-fabric-concepts-reconfiguration.md) identifierar att omkonfigurationen har stoppats eller fastnar. Den här hälsorapport kan vara på vars aktuella roll är primär, utom för en primär omkonfiguration växling, där det kan vara på den repliken degraderas från primära till aktiva sekundära repliken.

Omkonfigurationen kan ha fastnat för någon av följande orsaker:

- En åtgärd på den lokala repliken samma repliken som den utför omkonfiguration, slutförs inte. I det här fallet innehålla undersöka hälsorapporter på den här repliken från andra komponenter, System.RAP eller System.RE, ytterligare information.

- En åtgärd slutförs inte på en Fjärreplik. Repliker som åtgärder har slutförts visas i hälsorapporten. Ytterligare utredning ska göras på hälsorapporter för de fjärranslutna replikerna. Det kan också vara kommunikationsproblem mellan den här noden och fjärrnoden.

I sällsynta fall kan omkonfigurationen ha fastnat på grund av kommunikation eller andra problem mellan den här noden och Redundanshanteraren-tjänsten.

* **SourceId**: System.RA
* **Egenskapen**: Omkonfiguration.
* **Nästa steg**: Undersök lokal eller fjärransluten repliker beroende på beskrivningen av hälsorapporten.

I följande exempel visas en hälsorapport där en omkonfigurationen har fastnat på den lokala repliken. I det här exemplet, det på grund av en tjänst inte respekterar annullering-token.

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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

I följande exempel visas en rapport där en av har fastnat väntar på svar från två fjärranslutna repliker. I det här exemplet att det finns tre repliker i partition, inklusive den aktuella primärt. 

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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Den här hälsorapporten visar att omkonfigurationen har fastnat väntar på svar från två repliker: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

För varje replik får du följande information:
- Den tidigare konfigurationen roll
- Den aktuella konfigurationen roll
- [Replikens tillstånd](service-fabric-concepts-replica-lifecycle.md)
- Nod-ID
- Replik-ID

Så här avblockerar du omkonfigurationen:
- Den **ned** repliker ska göras tillgänglig. 
- Den **inbuild** repliker ska slutföra versionen och övergång till redo.

### <a name="slow-service-api-call"></a>Långsam service API-anrop
**System.RAP** och **System.Replicator** rapportera en varning om ett anrop till kod som användaren tar längre tid än den angivna tiden. Varningen rensas när anropet har slutförts.

* **SourceId**: System.RAP eller System.Replicator
* **Egenskapen**: Namnet på långsamma API: et. Beskrivningen innehåller mer information om den tid som API: et har väntande.
* **Nästa steg**: Undersök varför anropet tar längre tid än förväntat.

I följande exempel visar hälsohändelsen från System.RAP för en tillförlitlig tjänst som inte med konfiguration för detta token i **RunAsync**:

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

Egenskapen och text kan du ange vilken API har fastnat. Nästa steg ska vidtas för olika har fastnat API: er är olika. Alla API: er på den *IStatefulServiceReplica* eller *IStatelessServiceInstance* är vanligtvis en bugg i tjänstkoden. I följande avsnitt beskrivs hur dessa översätts till den [Reliable Services-modell](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: Den här varningen anger att ett anrop till `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, eller om åsidosatts gäller `OnOpenAsync` har fastnat.

- **IStatefulServiceReplica.Close** och **IStatefulServiceReplica.Abort**: I de flesta fall är en tjänst som inte respekterar annullering token som skickas till `RunAsync`. Det kan också vara som `ICommunicationListener.CloseAsync`, eller om åsidosatts gäller `OnCloseAsync` har fastnat.

- **IStatefulServiceReplica.ChangeRole (S)** och **IStatefulServiceReplica.ChangeRole(N)**: I de flesta fall är en tjänst som inte respekterar annullering token som skickas till `RunAsync`.

- **IStatefulServiceReplica.ChangeRole(P)**: I de flesta fall är att tjänsten inte har returnerat en uppgift från `RunAsync`.

Andra API-anrop som kan fastna finns på den **IReplicator** gränssnitt. Exempel:

- **IReplicator.CatchupReplicaSet**: Den här varningen anger att en av två saker. Det finns inte tillräckligt med repliker. För att se om så är fallet, visa replikens status repliker i partitionen eller System.FM hälsorapport för en har fastnat omkonfiguration. Eller replikerna bekräfta inte åtgärder. PowerShell-cmdleten `Get-ServiceFabricDeployedReplicaDetail` kan användas för att fastställa förloppet för alla repliker. Problemet har med repliker vars `LastAppliedReplicationSequenceNumber` värdet ligger bakom primärt `CommittedSequenceNumber` värde.

- **IReplicator.BuildReplica(<Remote ReplicaId>)**: Den här varningen anger ett problem i skapandeprocessen. Mer information finns i [livscykel för replik](service-fabric-concepts-replica-lifecycle.md). Det kan bero på en felaktig konfiguration av replikatorn-adress. Mer information finns i [konfigurera tillståndskänsliga Reliable Services](service-fabric-reliable-services-configuration.md) och [ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md). Det kan också vara ett problem i fjärrnoden.

### <a name="replicator-system-health-reports"></a>Replikator systemhälsorapporter
**Replikeringskön är full:**
**System.Replicator** rapporterar en varning när Replikeringskön är full. På primärt blir Replikeringskön vanligtvis full eftersom en eller flera sekundära repliker är långsamt att bekräfta åtgärder. På sekundärt inträffar detta när tjänsten är långsamt att tillämpa åtgärderna. Varningen rensas när kön är inte längre fullständig.

* **SourceId**: System.Replicator
* **Egenskapen**: **PrimaryReplicationQueueStatus** eller **SecondaryReplicationQueueStatus**, beroende på vilken replikroll.
* **Nästa steg**: Om rapporten är på primärt, kontrollera anslutningen mellan noderna i klustret. Om alla anslutningar fungerar felfritt, kan det finnas minst en långsam sekundär med en hög disk fördröjning tillämpas åtgärder. Om rapporten på sekundärt den kontrollerar du på diskanvändning och prestanda på noden först. Kontrollera sedan utgående anslutningen från den långsamma noden till primärt.

**RemoteReplicatorConnectionStatus:**
**System.Replicator** på den primära repliken rapporterar en varning när anslutningen till en sekundär () fjärreplikatorns inte är felfri. Den fjärreplikatorns adress visas i rapportens meddelandet, vilket gör det enklare att identifiera om felaktig konfiguration skickades in eller om det finns nätverksproblem mellan replikatörer.

* **SourceId**: System.Replicator
* **Egenskapen**: **RemoteReplicatorConnectionStatus**.
* **Nästa steg**: Kontrollera felmeddelandet och kontrollera att fjärreplikatorns adressen är rätt konfigurerad. Till exempel om fjärreplikatorns öppnas med lyssna-adress ”localhost”, det är inte kan nås från utsidan. Om adressen ser korrekt ut Kontrollera anslutningen mellan den primära noden och Fjärradress att hitta alla potentiella problem i nätverket.

### <a name="replication-queue-full"></a>Replikeringskön är full
**System.Replicator** rapporterar en varning när Replikeringskön är full. På primärt blir Replikeringskön vanligtvis full eftersom en eller flera sekundära repliker är långsamt att bekräfta åtgärder. På sekundärt inträffar detta när tjänsten är långsamt att tillämpa åtgärderna. Varningen rensas när kön är inte längre fullständig.

* **SourceId**: System.Replicator
* **Egenskapen**: **PrimaryReplicationQueueStatus** eller **SecondaryReplicationQueueStatus**, beroende på vilken replikroll.

### <a name="slow-naming-operations"></a>Långsam namngivning åtgärder
**System.NamingService** rapporterar hälsa på den primära repliken när en åtgärd för namngivning av tar längre tid än godkända. Exempel på åtgärder för namngivning av är [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) eller [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Flera metoder kan hittas under FabricClient. Till exempel de finns [service management metoder](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) eller [metoder för hantering av egenskapen](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> I namngivningstjänsten matchar tjänstnamn till en plats i klustret. Användare kan använda den för att hantera tjänstnamn och egenskaper. Det är en partitionerad bestående Service Fabric-tjänst. En av partitionerna representerar den *Authority Owner*, som innehåller metadata om alla Service Fabric-namn och tjänster. Service Fabric-namnen mappas till olika partitioner, kallas *Name Owner* partitioner, så att tjänsten kan utökas. Läs mer om den [Naming service](service-fabric-architecture.md).
> 
> 

När en åtgärd för namngivning av tar längre tid än förväntat flaggas igen med en varning-rapport på den primära repliken för Naming service-partitionen som fungerar igen. Om åtgärden slutförs, avmarkeras varningen. Om åtgärden har slutförts med fel, innehåller health-rapporten information om felet.

* **SourceId**: System.NamingService
* **Egenskapen**: Börjar med prefixet ”**Duration_**” och identifierar långsam åtgärd och Service Fabric-namnet som åtgärden tillämpas. Till exempel om skapa tjänst vid namn **fabric: / MyApp/MyService** tar för lång, egenskapen är **Duration_AOCreateService.fabric:/MyApp/MyService**. ”AO” pekar på rollen för namngivning av partitionen för det här namnet och åtgärden.
* **Nästa steg**: Kontrollera varför namngivning åtgärden misslyckas. Varje åtgärd kan ha olika rotorsaker. Till exempel kan ta bort tjänsten ha fastnat. Tjänsten kan ha fastnat eftersom programvärden håller kraschar på en nod på grund av ett fel för användaren i kod.

I följande exempel visas en åtgärd för att skapa tjänsten. Åtgärden tog längre tid än den konfigurera varaktigheten. ”AO” återförsök och skickar arbete ”Nej”. ”Nej” slutfört den sista åtgärden med TIMEOUT. I det här fallet är samma replik primär för både ”AO” och ”Nej” roller.

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

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication systemhälsorapporter
**System.Hosting** är utfärdare på distribuerade entiteter.

### <a name="activation"></a>Aktivering
System.Hosting rapporterar som OK när ett program har aktiverats på noden. Annars rapporterar ett fel.

* **SourceId**: System.Hosting
* **Egenskapen**: **Aktivering**, inklusive distribution-version.
* **Nästa steg**: Om programmet är i feltillstånd, kan du undersöka varför aktiveringen misslyckades.

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
System.Hosting rapporterar ett fel om inte paketet ned program.

* **SourceId**: System.Hosting
* **Egenskapen**: **Ladda ned**, inklusive distribution-version.
* **Nästa steg**: Undersök varför nedladdningen misslyckades på noden.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage systemhälsorapporter
**System.Hosting** är utfärdare på distribuerade entiteter.

### <a name="service-package-activation"></a>Service package-aktivering
System.Hosting rapporterar som OK om aktiveringen för service-paketet på noden har genomförts. Annars rapporterar ett fel.

* **SourceId**: System.Hosting
* **Egenskapen**: Aktiveringen.
* **Nästa steg**: Undersök varför aktiveringen misslyckades.

### <a name="code-package-activation"></a>Aktivering av kod paket
System.Hosting rapporterar som OK för varje kodpaketet om aktiveringen genomförs. Om aktiveringen misslyckas rapporterar den en varning som konfigurerats. Om **CodePackage** kan inte aktivera eller avslutas med ett fel som är större än den konfigurerade **CodePackageHealthErrorThreshold**, som är värd för rapporterar ett fel. Om inget tjänstepaket innehåller flera kodpaket, genereras en aktivering-rapporten för vart och ett.

* **SourceId**: System.Hosting
* **Egenskapen**: Namnområdesprefixet **CodePackageActivation** och innehåller namnet på kodpaketet och startpunkt som *CodePackageActivation:CodePackageName:SetupEntryPoint / EntryPoint*. Till exempel **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Typ av registrering
System.Hosting rapporterar som OK om tjänsttypen har registrerats. Den rapporterar ett fel om registreringen inte har gjort i tid, som konfigurerats med hjälp av **ServiceTypeRegistrationTimeout**. Om körningen är stängd visas tjänsttypen avregistreras från noden och som är värd för rapporterar en varning.

* **SourceId**: System.Hosting
* **Egenskapen**: Namnområdesprefixet **ServiceTypeRegistration** och innehåller namnet på tjänsten. Till exempel **ServiceTypeRegistration:FileStoreServiceType**.

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
* **Egenskapen**: **Ladda ned**, inklusive distribution-version.
* **Nästa steg**: Undersök varför nedladdningen misslyckades på noden.

### <a name="upgrade-validation"></a>Uppgradera verifiering
System.Hosting rapporterar ett fel om valideringen under uppgraderingen misslyckas eller om uppgraderingen misslyckas på noden.

* **SourceId**: System.Hosting
* **Egenskapen**: Namnområdesprefixet **FabricUpgradeValidation** och innehåller den uppgradera versionen.
* **Beskrivning**: Pekar på felet som uppstod.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Odefinierat nodens kapacitet för mätvärden för resurs-styrning
System.Hosting rapporterar en varning om nodkapaciteterna inte har definierats i klustermanifestet och konfigurationen för automatisk identifiering är avstängd. Service Fabric genererar ett hälsotillstånd som varning när tjänstpaketet som använder [resursstyrning](service-fabric-resource-governance.md) registrerar sig för en angiven nod.

* **SourceId**: System.Hosting
* **Egenskapen**: **ResourceGovernance**.
* **Nästa steg**: Det bästa sättet att lösa problemet är att ändra klustermanifestet för att aktivera automatisk identifiering av tillgängliga resurser. Ett annat sätt är att uppdatera klustermanifestet med angivet nodkapaciteterna för de här måtten.

## <a name="next-steps"></a>Nästa steg
* [Visa hälsorapporter i Service Fabric](service-fabric-view-entities-aggregated-health.md)

* [Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Service Fabric-Programuppgradering](service-fabric-application-upgrade.md)

