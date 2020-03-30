---
title: Felsök med systemhälsorapporter
description: Beskriver hälsorapporter som skickas av Azure Service Fabric-komponenter och deras användning för felsökning av kluster- eller programproblem
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: a76ae803b1283ce50d2f4e259943ce5ffcf0274c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282021"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Felsök med hjälp av systemhälsorapporter
Azure Service Fabric-komponenter tillhandahåller systemhälsorapporter för alla entiteter i klustret direkt. [Hälsoarkivet](service-fabric-health-introduction.md#health-store) skapar och tar bort entiteter baserat på systemrapporterna. Den organiserar dem också i en hierarki som fångar entitetsinteraktioner.

> [!NOTE]
> För att förstå hälsorelaterade begrepp, läs mer på [Service Fabric hälsomodell](service-fabric-health-introduction.md).
> 
> 

Systemhälsorapporter ger insyn i kluster- och programfunktioner och flaggproblem. För program och tjänster verifierar systemhälsorapporter att entiteter implementeras och beter sig korrekt ur service fabric-perspektivet. Rapporterna ger ingen hälsoövervakning av affärslogiken för tjänsten eller identifiering av processer som inte svarar. Användartjänster kan berika hälsodata med information som är specifik för deras logik.

> [!NOTE]
> Hälsorapporter som skickas av användarväsnare visas först *när* systemkomponenterna har skapat en entitet. När en entitet tas bort tar hälsoarkivet automatiskt bort alla hälsorapporter som är associerade med den. Detsamma gäller när en ny instans av entiteten skapas. Ett exempel är när en ny tillståndskänslig beständig tjänstreplikinstans skapas. Alla rapporter som är associerade med den gamla instansen tas bort och rensas från arkivet.
> 
> 

Systemkomponentrapporterna identifieras av källan, som börjar med "**System ."** . Watchdogs kan inte använda samma prefix för sina källor, eftersom rapporter med ogiltiga parametrar avvisas.

Låt oss titta på några systemrapporter för att förstå vad som utlöser dem och för att lära sig att korrigera de potentiella problem de representerar.

> [!NOTE]
> Service Fabric fortsätter att lägga till rapporter om villkor av intresse som förbättrar insynen i vad som händer i klustret och programmen. Befintliga rapporter kan förbättras med mer information för att felsöka problemet snabbare.
> 
> 

## <a name="cluster-system-health-reports"></a>Hälsorapporter om klustersystem
Klusterhälsoentiteten skapas automatiskt i hälsoarkivet. Om allt fungerar som det ska har den ingen systemrapport.

### <a name="neighborhood-loss"></a>Förlust av grannskap
**System.Federation** rapporterar ett fel när det upptäcker en grannskapsförlust. Rapporten kommer från enskilda noder och nod-ID:n ingår i egenskapsnamnet. Om ett grannskap går förlorat i hela Service Fabric-ringen kan du vanligtvis förvänta dig två händelser som representerar båda sidor av gaprapporten. Om fler stadsdelar går förlorade, det finns fler händelser.

Rapporten anger tidsgränsen för globalt leasingavtal som time-to-live (TTL). Rapporten skickas tillbaka varje halva av TTL-varaktigheten så länge villkoret förblir aktivt. Händelsen tas automatiskt bort när den upphör att gälla. Ta bort när det har upphört att gälla säkerställer att rapporten rensas från hälsoarkivet korrekt, även om rapportnoden är nere.

* **SourceId**: System.Federation
* **Egenskap**: Börjar med **grannskap** och innehåller nodinformation.
* **Nästa steg**: Undersök varför grannskapet går förlorat. Kontrollera till exempel kommunikationen mellan klusternoder.

### <a name="rebuild"></a>Återskapa

Tjänsten Redundanshanteraren (FM) hanterar information om klusternoderna. När FM förlorar sina data och går in i dataförlust, kan det inte garantera att den har den mest uppdaterade informationen om klusternoderna. I det här fallet går systemet igenom en ombyggnad och samlar System.FM in data från alla noder i klustret för att återskapa sitt tillstånd. Ibland, på grund av nätverk eller nod problem, kan återuppbygga fastna eller stoppas. Detsamma kan hända med tjänsten Failover Manager Master (FMM). FMM är en tillståndslös systemtjänst som håller reda på var alla FMs finns i klustret. FMM:s primära är alltid noden med ID:t närmast 0. Om noden tas bort utlöses en ombyggnad.
När ett av de tidigare villkoren inträffar **flaggar System.FM** eller **System.FMM** det via en felrapport. Ombyggnad kan ha fastnat i en av två faser:

* **Väntar på sändning:** FM/FMM väntar på broadcast-meddelandets svar från de andra noderna.

  * **Nästa steg**: Undersök om det finns ett problem med nätverksanslutningen mellan noder.
* **Väntar på noder:** FM/FMM har redan fått ett broadcast-svar från de andra noderna och väntar på ett svar från specifika noder. Hälsorapporten visar de noder som FM/FMM väntar på för ett svar för.
   * **Nästa steg:** Undersök nätverksanslutningen mellan FM/FMM och de angivna noderna. Undersök varje listad nod för andra möjliga problem.

* **SourceID**: System.FM eller System.FMM
* **Egenskap**: Bygg om.
* **Nästa steg**: Undersök nätverksanslutningen mellan noderna samt tillståndet för alla specifika noder som visas i beskrivningen av hälsorapporten.

### <a name="seed-node-status"></a>Status för seed-nod
**System.FM** rapporterar en varning på klusternivå om vissa frönoder är felaktiga. Seed-noder är de noder som upprätthåller tillgängligheten för det underliggande klustret. Dessa noder hjälper till att säkerställa att klustret förblir uppe genom att upprätta lån med andra noder och fungera som tiebreakers under vissa typer av nätverksfel. Om en majoritet av seed-noderna är nere i klustret och de inte återförs, stängs klustret automatiskt av. 

En seed-nod är felfritt om dess nodstatus är Ned, Borttagen eller Okänd.
Varningsrapporten för seed-nodstatus listar alla felaktiga frönoder med detaljerad information.

* **KällaID**: System.FM
* **Egenskap**: SeedNodeStatus
* **Nästa steg:** Om den här varningen visas i klustret följer du anvisningarna nedan för att åtgärda det: För kluster som kör Service Fabric version 6.5 eller senare: För Service Fabric-kluster på Azure, när seed-noden går ner, försöker Service Fabric ändra den till en nod som inte är seed automatiskt. För att göra detta, se till att antalet icke-utsäde noder i den primära nodtypen är större eller lika med antalet Down seed-noder. Om det behövs lägger du till fler noder i den primära nodtypen för att uppnå detta.
Beroende på klusterstatus kan det ta lite tid att åtgärda problemet. När detta är gjort rensas varningsrapporten automatiskt.

För fristående service fabric-kluster måste alla frönoder bli felfria för att rensa varningsrapporten. Beroende på varför frönoder är ohälsosamma måste olika åtgärder vidtas: om frönoden är Nere måste användarna ta upp den frönoden. Om seed-noden är borttagen eller Okänd måste den här [frönoden tas bort från klustret](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes).
Varningsrapporten rensas automatiskt när alla frönoder blir felfria.

För kluster som kör Service Fabric-version som är äldre än 6.5: I det här fallet måste varningsrapporten rensas manuellt. **Användare bör se till att alla frönoder blir felfria innan rapporten rensas:** om seed-noden är Ned måste användarna ta upp frönoden;om seed-noden tas bort eller okänd måste den frönoden tas bort från klustret.
När alla frönoder blir felfria använder du följande kommando från Powershell för att [rensa varningsrapporten:](https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricclusterhealthreport)

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
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


### <a name="certificate-expiration"></a>Certifikatets förfallodatum
**System.FabricNode** rapporterar en varning när certifikat som används av noden är nära utgångsdatum. Det finns tre certifikat per nod: **Certificate_cluster**, **Certificate_server**och **Certificate_default_client**. När förfallodatumet är minst två veckor bort är rapportens hälsotillstånd OK. När förfallodatumet är inom två veckor är rapporttypen en varning. TTL av dessa händelser är oändlig, och de tas bort när en nod lämnar klustret.

* **SourceId**: System.FabricNode
* **Egenskap**: Börjar med **certifikat** och innehåller mer information om certifikattypen.
* **Nästa steg**: Uppdatera certifikaten om de är nära förfallodatum.

### <a name="load-capacity-violation"></a>Brott mot lastkapacitet
Service Fabric Load Balancer rapporterar en varning när den upptäcker en nodkapacitetsöverträdelse.

* **SourceId**: System.PLB
* **Egenskap**: Börjar med **kapacitet**.
* **Nästa steg**: Kontrollera de angivna måtten och visa den aktuella kapaciteten på noden.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Nodkapacitet matchar inte för mått för resursstyrning
System.Hosting rapporterar en varning om definierade nodkapaciteter i klustermanifestet är större än den verkliga nodkapaciteten för resursstyrningsmått (minne och CPU-kärnor). En hälsorapport visas när det första servicepaketet som använder [resursstyrningsregister](service-fabric-resource-governance.md) på en angiven nod.

* **SourceId**: System.Hosting
* **Egenskap**: **ResourceGovernance**.
* **Nästa steg:** Det här problemet kan vara ett problem eftersom styrning av tjänstpaket inte tillämpas som förväntat och [resursstyrning](service-fabric-resource-governance.md) inte fungerar korrekt. Uppdatera klustermanifestet med rätt nodkapacitet för dessa mått, eller ange dem inte och låt Service Fabric automatiskt identifiera tillgängliga resurser.

## <a name="application-system-health-reports"></a>Hälsorapporter för programsystemet
System.CM, som representerar tjänsten Klusterhanteraren, är den myndighet som hanterar information om ett program.

### <a name="state"></a>Status
System.CM rapporterar som OK när programmet har skapats eller uppdaterats. Den informerar hälsoarkivet när programmet tas bort så att det kan tas bort från butiken.

* **SourceId**: System.CM
* **Egenskap**: Tillstånd.
* **Nästa steg**: Om programmet har skapats eller uppdaterats bör det innehålla hälsorapporten för Klusterhanteraren. Annars kontrollerar du programmets tillstånd genom att utfärda en fråga. Använd till exempel PowerShell cmdlet **Get-ServiceFabricApplication -ApplicationName** *applicationName*.

I följande exempel visas tillståndshändelsen i **programmet fabric:/WordCount:**

```powershell
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

## <a name="service-system-health-reports"></a>Hälsorapporter för servicesystem
System.FM, som representerar tjänsten Redundanshanteraren, är den myndighet som hanterar information om tjänster.

### <a name="state"></a>Status
System.FM rapporterar som OK när tjänsten har skapats. Entiteten tas bort från hälsoarkivet när tjänsten tas bort.

* **SourceId**: System.FM
* **Egenskap**: Tillstånd.

I följande exempel visas tillståndshändelsen på **tjänststrukturen:/WordCount/WordCountWebService**:

```powershell
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

### <a name="service-correlation-error"></a>Korrelationsfel för tjänsten
**System.PLB** rapporterar ett fel när det upptäcker att uppdatering av en tjänst är korrelerad med en annan tjänst som skapar en tillhörighetskedja. Rapporten rensas när en lyckad uppdatering sker.

* **SourceId**: System.PLB
* **Egenskap**: **ServiceDescription**.
* **Nästa steg**: Kontrollera de korrelerade tjänstbeskrivningarna.

## <a name="partition-system-health-reports"></a>Hälsorapporter för partitionssystemet
System.FM, som representerar tjänsten Redundanshanteraren, är den myndighet som hanterar information om tjänstpartitioner.

### <a name="state"></a>Status
System.FM rapporterar som OK när partitionen har skapats och är felfri. Entiteten tas bort från hälsoarkivet när partitionen tas bort.

Om partitionen är lägre än det minsta antalet repliker rapporterar den ett fel. Om partitionen inte ligger under det minsta antalet repliker, men det är under antalet målrepliker, rapporterar den en varning. Om partitionen är i kvorumförlust rapporterar System.FM ett fel.

Andra anmärkningsvärda händelser inkluderar en varning när omkonfigurationen tar längre tid än förväntat och när bygget tar längre tid än förväntat. De förväntade tiderna för bygg- och omkonfigurationen kan konfigureras baserat på tjänstscenarierna. Om en tjänst till exempel har en terabyte tillstånd, till exempel Azure SQL Database, tar build längre tid än för en tjänst med en liten mängd tillstånd.

* **SourceId**: System.FM
* **Egenskap**: Tillstånd.
* **Nästa steg**: Om hälsotillståndet inte är OK, är det möjligt att vissa repliker inte har skapats, öppnats eller befordrats till primär eller sekundär korrekt. 

Om beskrivningen beskriver kvorumförlust, sedan undersöka detaljerad hälsorapport för repliker som är nere och föra dem tillbaka upp hjälper till att föra partitionen online igen.

Om beskrivningen beskriver en partition som fastnat i [omkonfigurationen](service-fabric-concepts-reconfiguration.md)ger hälsorapporten på den primära repliken ytterligare information.

För andra System.FM hälsorapporter skulle det finnas rapporter om replikerna eller partitionen eller tjänsten från andra systemkomponenter. 

Följande exempel beskriver några av dessa rapporter. 

I följande exempel visas en felfri partition:

```powershell
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

I följande exempel visas hälsotillståndet för en partition som ligger under antalet målrepliker. Nästa steg är att få partitionsbeskrivningen, som visar hur den är konfigurerad: **MinReplicaSetSize** är tre och **TargetReplicaSetSize** är sju. Hämta sedan antalet noder i klustret, som i det här fallet är fem. Så i det här fallet kan två repliker inte placeras, eftersom målantalet repliker är högre än antalet tillgängliga noder.

```powershell
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

I följande exempel visas hälsotillståndet för en partition som har fastnat i omkonfiguration på grund av att användaren inte respekterar annulleringstoken i **metoden RunAsync.** Att undersöka hälsorapporten för en replik som markerats som primär (P) kan bidra till att öka detaljnivån ytterligare i problemet.

```powershell
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
Den här hälsorapporten visar tillståndet för replikerna för partitionen som genomgår omkonfiguration: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Hälsorapporten innehåller för varje replik:
- Tidigare konfigurationsroll
- Aktuell konfigurationsroll
- [Repliktillstånd](service-fabric-concepts-replica-lifecycle.md)
- Nod som repliken körs på
- Replik-ID

I ett fall som exemplet behövs ytterligare utredning. Undersök hälsotillståndet för varje enskild replik `Primary` `Secondary` som börjar med replikerna markerade som och (131482789658160654 och 131482789688598467) i föregående exempel.

### <a name="replica-constraint-violation"></a>Överträdelse av replikbegränsning
**System.PLB** rapporterar en varning om den upptäcker en replikbegränsningsöverträdelse och inte kan placera alla partitionsrepliker. Rapportinformationen visar vilka begränsningar och egenskaper som förhindrar replikplaceringen.

* **SourceId**: System.PLB
* **Egenskap**: Börjar med **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Hälsorapporter för repliksystem
**System.RA**, som representerar omkonfigurationsagentkomponenten, är behörigheten för repliktillståndet.

### <a name="state"></a>Status
System.RA rapporterar OK när repliken har skapats.

* **SourceId**: System.RA
* **Egenskap**: Tillstånd.

I följande exempel visas en felfri replik:

```powershell
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
Den här egenskapen används för att ange varningar eller fel när du försöker öppna en replik, stänga en replik eller överföra en replik från en roll till en annan. Mer information finns i [Repliklivscykeln](service-fabric-concepts-replica-lifecycle.md). Felen kan vara undantag som genereras från API-anrop eller krascher av tjänstvärdprocessen under den här tiden. För fel på grund av API-anrop från C#-kod lägger Service Fabric till undantaget och stackspårningen i hälsorapporten.

Dessa hälsovarningar utlöses efter att åtgärden har tagits tillbaka lokalt ett antal gånger (beroende på princip). Service Fabric försöker åtgärden upp till ett maximalt tröskelvärde. När den maximala tröskeln har uppnåtts kan den försöka agera för att rätta till situationen. Det här försöket kan orsaka att dessa varningar rensas när den ger upp åtgärden på den här noden. Om en replik till exempel inte öppnas på en nod, höjer Service Fabric en hälsovarning. Om repliken fortsätter att misslyckas med att öppna, fungerar Service Fabric för att självreparation. Den här åtgärden kan innebära att du försöker med samma åtgärd på en annan nod. Det här försöket gör att varningen som utlöses för den här repliken rensas. 

* **SourceId**: System.RA
* **Egenskap**: **ReplicaOpenStatus**, **ReplicaCloseStatus**och **ReplicaChangeRoleStatus**.
* **Nästa steg**: Undersök servicekoden eller kraschdumparna för att identifiera varför åtgärden misslyckas.

I följande exempel visas hälsotillståndet för `TargetInvocationException` en replik som kastar från dess öppna metod. Beskrivningen innehåller felpunkten, **IStatefulServiceReplica.Open**, undantagstypen **TargetInvocationException**och stackspårningen.

```powershell
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

I följande exempel visas en replik som ständigt kraschar under stängning:

```powershell
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
Den här egenskapen används för att ange när en replik som utför en [omkonfiguration](service-fabric-concepts-reconfiguration.md) upptäcker att omkonfigurationen har stoppats eller fastnat. Den här hälsorapporten kan finnas på repliken vars aktuella roll är primär, utom i fall av en primär omkonfiguration för växling, där den kan finnas på repliken som nedgraderas från primär till aktiv sekundär.

Omkonfigurationen kan ha fastnat av något av följande skäl:

- En åtgärd på den lokala repliken, samma replik som den som utför omkonfigurationen, slutförs inte. I det här fallet kan undersökning av hälsorapporterna på den här repliken från andra komponenter, System.RAP eller System.RE, ge ytterligare information.

- En åtgärd slutförs inte på en fjärrreplik. Repliker för vilka åtgärder väntar visas i hälsorapporten. Ytterligare undersökning bör göras på hälsorapporter för dessa avlägsna repliker. Det kan också finnas kommunikationsproblem mellan den här noden och fjärrnoden.

I sällsynta fall kan omkonfigurationen fastna på grund av kommunikation eller andra problem mellan den här noden och redundanshanteraren.

* **SourceId**: System.RA
* **Egenskap**: Omkonfiguration.
* **Nästa steg**: Undersök lokala repliker eller fjärrrepliker beroende på beskrivningen av hälsorapporten.

I följande exempel visas en hälsorapport där en omkonfiguration har fastnat på den lokala repliken. I det här exemplet beror det på att en tjänst inte respekterar annulleringstoken.

```powershell
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

I följande exempel visas en hälsorapport där en omkonfiguration har fastnat i väntan på ett svar från två fjärrrepliker. I det här exemplet finns det tre repliker i partitionen, inklusive den aktuella primära. 

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

Den här hälsorapporten visar att omkonfigurationen har fastnat i väntan på ett svar från två repliker: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

För varje replik ges följande information:
- Tidigare konfigurationsroll
- Aktuell konfigurationsroll
- [Repliktillstånd](service-fabric-concepts-replica-lifecycle.md)
- Nod-ID
- Replik-ID

Så här avblockerar du omkonfigurationen:
- De **ner** replikerna bör tas upp. 
- **Inbyggda** repliker bör slutföra bygga och övergången till klar.

### <a name="slow-service-api-call"></a>Api-anrop för långsam tjänst
**System.RAP** och **System.Replicator** rapporterar en varning om ett anrop till användartjänstkoden tar längre tid än den konfigurerade tiden. Varningen rensas när samtalet är klart.

* **SourceId**: System.RAP eller System.Replicator
* **Egenskap**: Namnet på det långsamma API:et. Beskrivningen innehåller mer information om den tid då API:et har väntat.
* **Nästa steg**: Undersök varför samtalet tar längre tid än förväntat.

I följande exempel visas hälsohändelsen från System.RAP för en tillförlitlig tjänst som inte respekterar annulleringstoken i **RunAsync:**

```powershell
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

Egenskapen och texten anger vilket API som fastnade. Nästa steg att ta för olika stack API:er är olika. Alla API på *IStatefulServiceReplica* eller *IStatelessServiceInstance* är vanligtvis ett fel i tjänstkoden. I följande avsnitt beskrivs hur dessa översätts till [modellen Reliable Services:](service-fabric-reliable-services-lifecycle.md)

- **IStatefulServiceReplica.Open**: Den här varningen `CreateServiceInstanceListeners` `ICommunicationListener.OpenAsync`anger att ett anrop till , eller om det åsidosätts, `OnOpenAsync` har fastnat.

- **IStatefulServiceReplica.Close** och **IStatefulServiceReplica.Abort**: Det vanligaste fallet är en tjänst `RunAsync`som inte respekterar annulleringstoken som skickas in till . Det kan också `ICommunicationListener.CloseAsync`vara att , `OnCloseAsync` eller om åsidosatt, har fastnat.

- **IStatefulServiceReplica.ChangeRole(S)** och **IStatefulServiceReplica.ChangeRole(N)**: Det vanligaste fallet är en tjänst `RunAsync`som inte respekterar annulleringstoken som skickas in på . I det här fallet är den bästa lösningen att starta om repliken.

- **IStatefulServiceReplica.ChangeRole(P):** Det vanligaste fallet är att tjänsten inte har `RunAsync`returnerat en uppgift från .

Andra API-anrop som kan fastna finns i **IReplicator-gränssnittet.** Ett exempel:

- **IReplicator.CatchupReplicaSet**: Denna varning indikerar en av två saker. Det finns inte tillräckligt med repliker. Om du vill se om så är fallet kan du titta på replikstatusen för replikerna i partitionen eller System.FM hälsorapport för en fast omkonfiguration. Eller replikerna inte bekräftar åtgärder. PowerShell-cmdleten `Get-ServiceFabricDeployedReplicaDetail` kan användas för att fastställa förloppet för alla repliker. Problemet ligger hos repliker vars `LastAppliedReplicationSequenceNumber` värde `CommittedSequenceNumber` ligger bakom primärvärdet.

- **IReplicator.BuildReplica(\<Remote ReplicaId>):** Den här varningen indikerar ett problem i byggprocessen. Mer information finns i [Repliklivscykeln](service-fabric-concepts-replica-lifecycle.md). Det kan bero på en felkonfiguration av replikatoradressen. Mer information finns i [Konfigurera tillståndskänsliga tillförlitliga tjänster](service-fabric-reliable-services-configuration.md) och ange resurser i ett [tjänstmanifest](service-fabric-service-manifest-resources.md). Det kan också vara ett problem på fjärrnoden.

### <a name="replicator-system-health-reports"></a>Hälsorapporter för Replikatorsystemet
**Replikeringskön full:**
**System.Replicator** rapporterar en varning när replikeringskön är full. På den primära, replikeringskön blir oftast full eftersom en eller flera sekundära repliker är långsamma med att bekräfta åtgärder. På den sekundära, detta händer vanligtvis när tjänsten är långsam att tillämpa åtgärderna. Varningen rensas när kön inte längre är full.

* **SourceId**: System.Replicator
* **Egenskap**: **PrimaryReplicationQueueStatus** eller **SecondaryReplicationQueueStatus**, beroende på replikrollen.
* **Nästa steg**: Om rapporten finns på den primära kontrollerar du anslutningen mellan noderna i klustret. Om alla anslutningar är felfria kan det finnas minst en långsam sekundär med en hög diskfördröjning för att tillämpa åtgärder. Om rapporten är på den sekundära kontrollerar du diskanvändningen och prestandan på noden först. Kontrollera sedan den utgående anslutningen från den långsamma noden till den primära.

**RemoteReplicatorConnectionStatus:**
**System.Replicator** på den primära repliken rapporterar en varning när anslutningen till en sekundär (fjärr) replikator inte är felfri. Fjärrreplikatorns adress visas i rapportens meddelande, vilket gör det enklare att identifiera om fel konfiguration har skickats in eller om det finns nätverksproblem mellan replikatorerna.

* **SourceId**: System.Replicator
* **Egenskap**: **RemoteReplicatorConnectionStatus**.
* **Nästa steg:** Kontrollera felmeddelandet och kontrollera att fjärrreplikatoradressen är korrekt konfigurerad. Om fjärrreplikatoren till exempel öppnas med lyssnaradressen "localhost" kan den inte nås från utsidan. Om adressen ser korrekt ut kontrollerar du anslutningen mellan den primära noden och fjärradressen för att hitta eventuella nätverksproblem.

### <a name="replication-queue-full"></a>Replikeringskön är full
**System.Replicator** rapporterar en varning när replikeringskön är full. På den primära, replikeringskön blir oftast full eftersom en eller flera sekundära repliker är långsamma med att bekräfta åtgärder. På den sekundära, detta händer vanligtvis när tjänsten är långsam att tillämpa åtgärderna. Varningen rensas när kön inte längre är full.

* **SourceId**: System.Replicator
* **Egenskap**: **PrimaryReplicationQueueStatus** eller **SecondaryReplicationQueueStatus**, beroende på replikrollen.

### <a name="slow-naming-operations"></a>Långsamma namngivningsåtgärder
**System.NamingService** rapporterar hälsotillståndet på sin primära replik när en namngivningsåtgärd tar längre tid än acceptabelt. Exempel på namngivningsåtgärder är [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) eller [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Fler metoder finns under FabricClient. De finns till exempel under [servicehanteringsmetoder](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) eller [egenskapshanteringsmetoder](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Namngivningstjänsten matchar tjänstnamn till en plats i klustret. Användare kan använda den för att hantera tjänstnamn och egenskaper. Det är en tjänst som är partitionerad i tjänst för service fabric. En av partitionerna representerar *myndighetens ägare*, som innehåller metadata om alla Service Fabric namn och tjänster. Tjänst fabric-namnen mappas till olika *Name Owner* partitioner, så kallade namnägarepartitioner, så tjänsten kan utbyggas. Läs mer om [namngivningstjänsten](service-fabric-architecture.md).
> 
> 

När en namngivningsåtgärd tar längre tid än förväntat flaggas åtgärden med en varningsrapport om den primära repliken av namngivningstjänstpartitionen som betjänar åtgärden. Om åtgärden har slutförts rensas varningen. Om åtgärden slutförs med ett fel innehåller hälsorapporten information om felet.

* **SourceId**: System.NamingService
* **Egenskap**: Börjar med prefixet "**Duration_**" och identifierar den långsamma åtgärden och namnet Service Fabric som åtgärden används på. Om det till exempel tar för lång tid att skapa tjänsten på **namntyget:/MyApp/MyService** är egenskapen **Duration_AOCreateService.fabric:/MyApp/MyService**. "AO" pekar på rollen för namngivningspartitionen för det här namnet och åtgärden.
* **Nästa steg**: Kontrollera varför namngivningsåtgärden misslyckas. Varje åtgärd kan ha olika grundorsasaker. Borttagningstjänsten kan till exempel ha fastnat. Tjänsten kan ha fastnat eftersom programvärden kraschar på en nod på grund av ett användarfel i tjänstkoden.

I följande exempel visas en skapa tjänståtgärd. Åtgärden tog längre tid än den konfigurerade varaktigheten. "AO" försöker och skickar arbete till "NEJ". "NEJ" slutförde den senaste åtgärden med TIMEOUT. I det här fallet är samma replik primär för rollerna "AO" och "NO".

```powershell
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

## <a name="deployedapplication-system-health-reports"></a>Hälsorapporter för Distribuerade system
**System.Hosting** är myndigheten för distribuerade enheter.

### <a name="activation"></a>Aktivering
System.Hosting rapporterar som OK när ett program har aktiverats på noden. Annars rapporterar det ett fel.

* **SourceId**: System.Hosting
* **Egenskap**: **Aktivering**, inklusive distributionsversionen.
* **Nästa steg**: Om programmet är fel, undersöka varför aktiveringen misslyckades.

I följande exempel visas en lyckad aktivering:

```powershell
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
System.Hosting rapporterar ett fel om nedladdningen av programpaketet misslyckas.

* **SourceId**: System.Hosting
* **Egenskap**: **Ladda ner**, inklusive distributionsversionen.
* **Nästa steg**: Undersök varför hämtningen misslyckades på noden.

## <a name="deployedservicepackage-system-health-reports"></a>Hälsorapporter för Distribuerade ServicePackage-system
**System.Hosting** är myndigheten för distribuerade enheter.

### <a name="service-package-activation"></a>Aktivering av servicepaket
System.Hosting rapporterar som OK om aktiveringen av servicepaketet på noden lyckas. Annars rapporterar det ett fel.

* **SourceId**: System.Hosting
* **Egenskap**: Aktivering.
* **Nästa steg**: Undersök varför aktiveringen misslyckades.

### <a name="code-package-activation"></a>Aktivering av kodpaket
System.Hosting rapporterar som OK för varje kodpaket om aktiveringen lyckas. Om aktiveringen misslyckas rapporterar den en varning som konfigurerad. Om **CodePackage** inte aktiveras eller avslutas med ett fel som är större än det konfigurerade **CodePackageHealthErrorThreshold**rapporterar värd ett fel. Om ett servicepaket innehåller flera kodpaket genereras en aktiveringsrapport för var och en.

* **SourceId**: System.Hosting
* **Egenskap**: Använder prefixet **CodePackageActivation** och innehåller namnet på kodpaketet och startpunkten som *CodePackageActivation:CodePackageName:SetupEntryPoint/EntryPoint*. Till exempel **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Registrering av tjänsttyp
System.Hosting rapporterar som OK om tjänsttypen har registrerats. Det rapporterar ett fel om registreringen inte gjordes i tid, som konfigurerats med hjälp av **ServiceTypeRegistrationTimeout**. Om körningen är stängd avregistreras tjänsttypen från noden och värd rapporterar en varning.

* **SourceId**: System.Hosting
* **Egenskap**: Använder prefixet **ServiceTypeRegistration** och innehåller tjänsttypsnamnet. **ServiceTypeRegistration:FileStoreServiceType**.

I följande exempel visas ett felfritt distribuerat tjänstpaket:

```powershell
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
System.Hosting rapporterar ett fel om nedladdningen av servicepaketet misslyckas.

* **SourceId**: System.Hosting
* **Egenskap**: **Ladda ner**, inklusive distributionsversionen.
* **Nästa steg**: Undersök varför hämtningen misslyckades på noden.

### <a name="upgrade-validation"></a>Validera uppgradering
System.Hosting rapporterar ett fel om valideringen under uppgraderingen misslyckas eller om uppgraderingen misslyckas på noden.

* **SourceId**: System.Hosting
* **Egenskap**: Använder prefixet **FabricUpgradeValidation** och innehåller uppgraderingsversionen.
* **Beskrivning**: Pekar på felet som påträffats.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Odefinierad nodkapacitet för resursstyrningsmått
System.Hosting rapporterar en varning om nodkapaciteter inte har definierats i klustermanifestet och konfigurationen för automatisk identifiering är inaktiverad. Service Fabric höjer en hälsovarning när servicepaketet som använder [resursstyrningsregister](service-fabric-resource-governance.md) på en angiven nod.

* **SourceId**: System.Hosting
* **Egenskap**: **ResourceGovernance**.
* **Nästa steg:** Det bästa sättet att lösa det här problemet är att ändra klustermanifestet för att möjliggöra automatisk identifiering av tillgängliga resurser. Ett annat sätt är att uppdatera klustermanifestet med korrekt angivna nodkapaciteter för dessa mått.

## <a name="next-steps"></a>Nästa steg
* [Visa hälsorapporter för Service Fabric](service-fabric-view-entities-aggregated-health.md)

* [Så här rapporterar och kontrollerar du tjänstens hälsa](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Övervaka och diagnostisera tjänster lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)

