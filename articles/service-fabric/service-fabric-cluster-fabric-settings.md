---
title: Ändra inställningarna för Azure Service Fabric-kluster | Microsoft Docs
description: Den här artikeln beskriver fabric-inställningar och fabric uppgradera principer som du kan anpassa.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/09/2018
ms.author: aljo
ms.openlocfilehash: 7d32ebd54d501a2eb5d6e353d38834546200c813
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Anpassa inställningar för Service Fabric-kluster och Fabric-uppgradera princip
Det här dokumentet förklarar hur du anpassar olika infrastrukturinställningarna och infrastrukturen uppgradera princip för Service Fabric-klustret. Du kan anpassa dem via den [Azure-portalen](https://portal.azure.com) eller med en Azure Resource Manager-mall.

> [!NOTE]
> Alla inställningar är inte tillgängliga i portalen. Anpassa den med hjälp av en Azure Resource Manager-mall om en inställning nedan inte är tillgängliga via portalen.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Anpassa inställningarna med hjälp av Resource Manager-mallar
Stegen nedan illustrerar hur du lägger till en ny inställning *MaxDiskQuotaInMB* till den *diagnostik* avsnitt.

1. Gå till https://resources.azure.com
2. Navigera till din prenumeration genom att expandera **prenumerationer** -> **\<din prenumeration >** -> **resursgrupper**  ->   **\<Din resursgrupp >** -> **providers** -> **Microsoft.ServiceFabric**  ->  **kluster** -> **\<din klusternamn >**
3. I övre högra hörnet väljer **läsning och skrivning.**
4. Välj **redigera** och uppdatera den `fabricSettings` JSON-element och lägga till ett nytt element:

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Följande är en lista av Fabric-inställningar som du kan anpassa, ordnade efter avsnittet.

### <a name="section-name-diagnostics"></a>Avsnittsnamn: diagnostik
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| ConsumerInstances |Sträng | Dynamisk |Lista över DCA konsumenten instanser. |
| ProducerInstances |Sträng | Dynamisk |Lista över DCA producenten instanser. |
| AppEtwTraceDeletionAgeInDays |Int, standardinställningen är 3 | Dynamisk |Antal dagar efter vilken vi ta bort gamla ETL-filer som innehåller programmet ETW-spårning. |
| AppDiagnosticStoreAccessRequiresImpersonation |Bool, standard är SANT | Dynamisk |Om personifiering är obligatorisk när åtkomst till diagnostiska lagrar åt programmet. |
| MaxDiskQuotaInMB |Int, standard är 65536 | Dynamisk |Diskkvot i MB för Windows Fabric-loggfiler. |
| DiskFullSafetySpaceInMB |Int, standard är 1024 | Dynamisk |Återstående diskutrymme i MB för att skydda från användning av DCA. |
| ApplicationLogsFormatVersion |Int, standardvärdet är 0 | Dynamisk |Versionen för programmet loggar format. Värden som stöds är 0 och 1. Version 1 innehåller flera fält i posten ETW-händelse än version 0. |
| ClusterId |Sträng | Dynamisk |Unikt id för klustret. Det här genereras när klustret skapas. |
| EnableTelemetry |Bool, standard är SANT | Dynamisk |Detta kommer att aktivera eller inaktivera telemetri. |
| EnableCircularTraceSession |Bool, standard är FALSKT | Statisk |Flagga som anger om cirkulär spårningssessioner ska användas. |

### <a name="section-name-traceetw"></a>Avsnittsnamn: Spåra/Etw
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| Nivå |Int, standardvärdet är 4 | Dynamisk |Spårningsnivån för etw kan ha värden 1, 2, 3, 4. Att kunna användas måste du behålla spårningsnivån vid 4 |

### <a name="section-name-performancecounterlocalstore"></a>Avsnittsnamn: PerformanceCounterLocalStore
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| IsEnabled |Bool, standard är SANT | Dynamisk |Flagga som anger om prestandaräknarsamlingen på lokal nod är aktiverad. |
| SamplingIntervalInSeconds |Int, standardvärdet är 60 | Dynamisk |Exempelintervall för prestandaräknare som samlas in. |
| Räknare |Sträng | Dynamisk |Kommaavgränsad lista över prestandaräknare för att samla in. |
| MaxCounterBinaryFileSizeInMB |Int, standardvärdet är 1 | Dynamisk |Maximal storlek (i MB) för varje prestandaräknaren binär fil. |
| NewCounterBinaryFileCreationIntervalInMinutes |Int, standardvärdet är 10 | Dynamisk |Maximalt tidsintervall (i sekunder) efter vilken en ny prestandaräknaren binär fil skapas. |

### <a name="section-name-setup"></a>Avsnittsnamn: installationsprogrammet
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| FabricDataRoot |Sträng | Inte tillåten |Rotkatalogen för Service Fabric-data. Som standard för Azure är d:\svcfab |
| FabricLogRoot |Sträng | Inte tillåten |Service fabric rot loggkatalogen. Detta är SA loggar och spårningar placering. |
| ServiceRunAsAccountName |Sträng | Inte tillåten |Kontonamn som du vill köra infrastrukturvärdtjänsten under. |
| SkipFirewallConfiguration |Bool, standard är FALSKT | Inte tillåten |Anger om brandväggsinställningar måste anges i systemet eller inte. Detta gäller endast om du använder windows-brandväggen. Om du använder brandväggar från tredje part, måste du öppna portar för system och program som ska användas |
|NodesToBeRemoved|sträng, standardvärdet är ””| Dynamisk |Noder som ska tas bort som en del av konfigurationen uppgraderingen. (Endast för distributioner av fristående)|
|ContainerNetworkSetup|bool, standard är FALSKT| Statisk |Om du vill konfigurera ett nätverk för behållaren.|
|ContainerNetworkName|String, standard är L ””| Statisk |Nätverksnamn ska användas när du konfigurerar ett nätverk för behållaren.|

### <a name="section-name-transactionalreplicator"></a>Avsnittsnamn: TransactionalReplicator
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| MaxCopyQueueSize |Uint, standard är 16384 | Statisk |Detta är högsta värdet definierar den ursprungliga storleken för kön som underhåller replikeringsåtgärder. Observera att det måste vara delbart med 2. Om körningsfel kön storlek i den här åtgärden kommer att begränsas mellan de primära och sekundära replikatörer. |
| BatchAcknowledgementInterval | Tid i sekunder är standardvärdet 0.015 | Statisk | Ange tidsintervall i sekunder. Anger hur lång tid som replikatorn väntar när du har fått en åtgärd innan en bekräftelse skickas tillbaka. Andra åtgärder som togs emot under den här tiden har sina bekräftelser skickas tillbaka i ett enda meddelande -> minska nätverkstrafiken men potentiellt minskar genomflödet av replikatorn. |
| MaxReplicationMessageSize |Uint, standard är 52428800 | Statisk | Maximal meddelandestorlek på replikeringsåtgärder. Standardvärdet är 50MB. |
| ReplicatorAddress |strängen, standard är ”localhost:0” | Statisk | Slutpunkt i form av en sträng-'IP:Port' som används av Windows Fabric replikatorn upprätta anslutningar till andra repliker för att kunna skicka och ta emot åtgärder. |
| InitialPrimaryReplicationQueueSize |Uint, standard är 64 | Statisk |Det här värdet anger den ursprungliga storleken för kön som underhåller replikeringsåtgärder på primärt. Observera att det måste vara delbart med 2.|
| MaxPrimaryReplicationQueueSize |Uint, standard är 8192 | Statisk |Detta är det maximala antalet åtgärder som kan finnas i kön primär replikering. Observera att det måste vara delbart med 2. |
| MaxPrimaryReplicationQueueMemorySize |Uint, standardvärdet är 0 | Statisk |Detta är det maximala värdet för den primära Replikeringskön i byte. |
| InitialSecondaryReplicationQueueSize |Uint, standard är 64 | Statisk |Det här värdet anger den ursprungliga storleken för kön som underhåller replikeringsåtgärder på sekundärt. Observera att det måste vara delbart med 2. |
| MaxSecondaryReplicationQueueSize |Uint, standard är 16384 | Statisk |Detta är det maximala antalet åtgärder som kan finnas i sekundära Replikeringskön. Observera att det måste vara delbart med 2. |
| MaxSecondaryReplicationQueueMemorySize |Uint, standardvärdet är 0 | Statisk |Detta är det maximala värdet för den sekundära Replikeringskön i byte. |
| SecondaryClearAcknowledgedOperations |Bool, standard är FALSKT | Statisk |Bool som styr om åtgärder på sekundär replikatorn är avmarkerad när de bekräftas till den primära servern (replikatorn tömmer till disken). Inställningar för den till TRUE kan medföra ytterligare Diskläsningar på den nya primärt när fånga repliker efter en redundansväxling. |
| MaxMetadataSizeInKB |Int, standardvärdet är 4 |Inte tillåten|Maximal storlek för log-metadata för dataströmmen. |
| MaxRecordSizeInKB |Uint, standard är 1024 |Inte tillåten| Maximal storlek för en loggpost för dataströmmen. |
| CheckpointThresholdInMB |Int, standardvärdet är 50 |Statisk|En kontrollpunkt påbörjas när loggen användningen överskrider detta värde. |
| MaxAccumulatedBackupLogSizeInMB |Int, är standard 800 |Statisk|Max ackumulerade storleken (i MB) för säkerhetskopiering loggar i en viss loggkedja. En inkrementell säkerhetskopiering begäranden misslyckas om den inkrementella säkerhetskopian skulle Generera en säkerhetskopiering logg som skulle orsaka de ackumulerade antalet säkerhetskopieringsloggar sedan den relevanta fullständiga säkerhetskopian ska vara större än den här storleken. I sådana fall måste användaren gör en fullständig säkerhetskopia. |
| MaxWriteQueueDepthInKB |Int, standardvärdet är 0 |Inte tillåten| Int för maximal skriva ködjup som core loggaren kan använda som anges i kilobyte för den logg som är associerad med den här repliken. Det här värdet är maximalt antal byte som kan vara utestående under viktiga uppdateringar av meddelandeloggfiler. Det kan vara 0 för kärnor loggaren att beräkna ett lämpligt värde eller en multipel av 4. |
| SharedLogId |Sträng |Inte tillåten|Delade identifierare. Detta är ett guid och måste vara unikt för varje delad logg. |
| SharedLogPath |Sträng |Inte tillåten|Sökvägen till delad loggen. Om det här värdet är tomt används standard delade loggen. |
| SlowApiMonitoringDuration |Tid i sekunder, standard är 300 |Statisk| Ange varaktighet för api innan varning hälsa händelsen utlöses.|
| MinLogSizeInMB |Int, standardvärdet är 0 |Statisk|Minsta storlek för transaktionell loggen. Loggen kommer inte att kunna trunkera till en storlek under den här inställningen. 0 anger att replikatorn avgör den minsta loggfilsstorleken enligt andra inställningar. Det här värdet ökar risken för att göra partiella kopior och inkrementella säkerhetskopieringar eftersom risken för relevanta loggposter trunkerades sänks. |

### <a name="section-name-fabricclient"></a>Avsnittsnamn: FabricClient
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| NodeAddresses |sträng, standardvärdet är ”” |Statisk|En samling av adresser (anslutningssträngar) på olika noder som kan användas för att kommunicera med den Naming Service. Först ansluter klienten att välja en av adresserna slumpmässigt. Om mer än en anslutningssträngen har angetts och en anslutning misslyckas på grund av en kommunikation eller timeout-fel. Klienten växlar att använda nästa adress sekventiellt. Se Naming serviceadressen försök information om återförsök semantik. |
| ConnectionInitializationTimeout |Tid i sekunder, är standardvärdet 2 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för anslutning för varje gång klienten försöker öppna en anslutning till gatewayen. |
| PartitionLocationCacheLimit |Int, standard är 100000 |Statisk|Antalet partitioner som cachelagrats för tjänsten upplösning (Ange 0 för ingen gräns). |
| ServiceChangePollInterval |Tid i sekunder, standardvärdet är 120 |Dynamisk|Ange tidsintervall i sekunder. Intervallet mellan på varandra följande avsökningar för tjänst ändras från klienten till gateway för registrerade tjänsten ändra meddelanden återanrop. |
| KeepAliveIntervalInSeconds |Int, standardvärdet är 20 |Statisk|Tidsintervall som FabricClient transporten skickar keep-alive-meddelanden till gatewayen. För 0; keepAlive är inaktiverad. Måste vara ett positivt värde. |
| HealthOperationTimeout |Tid i sekunder, standardvärdet är 120 |Dynamisk|Ange tidsintervall i sekunder. Timeout för en rapportmeddelande som skickas till Health Manager. |
| HealthReportSendInterval |Tid i sekunder, standardvärdet är 30 |Dynamisk|Ange tidsintervall i sekunder. Intervallet då reporting komponenten skickar ackumulerade hälsorapporter till Health Manager. |
| HealthReportRetrySendInterval |Tid i sekunder, standardvärdet är 30 |Dynamisk|Ange tidsintervall i sekunder. Intervallet då reporting komponenten igen skickar ackumulerade hälsorapporter till Health Manager. |
| RetryBackoffInterval |Tid i sekunder, standardinställningen är 3 |Dynamisk|Ange tidsintervall i sekunder. Inte intervallet innan du försöker igen. |
| MaxFileSenderThreads |Uint, standardvärdet är 10 |Statisk|Max antal filer som överförs parallellt. |

### <a name="section-name-common"></a>Avsnittsnamn: vanliga
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| PerfMonitorInterval |Tid i sekunder är standardvärdet 1 |Dynamisk|Ange tidsintervall i sekunder. Prestanda Övervakningsintervall. Inställningen till 0 eller negativt värde inaktiverar övervakning. |

### <a name="section-name-healthmanager"></a>Avsnittsnamn: HealthManager
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool, standard är FALSKT |Statisk|Klustret hälsoprincip för utvärdering: aktivera per program typen hälsoutvärderingen. |

### <a name="section-name-nodedomainids"></a>Avsnittsnamn: NodeDomainIds
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| UpgradeDomainId |sträng, standardvärdet är ”” |Statisk|Beskriver uppgraderingsdomänen som tillhör en nod. |
| PropertyGroup |NodeFaultDomainIdCollection |Statisk|Beskriver feldomäner som tillhör en nod. Feldomänen definieras via en URI som beskriver var noden i datacentret.  Fel domän URI: er är i formatet fd: / fd/följt av ett URI-sökvägssegment.|

### <a name="section-name-nodeproperties"></a>Avsnittsnamn: NodeProperties
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |Statisk|En samling sträng nyckel / värde-par för egenskaper för en nod. |

### <a name="section-name-nodecapacities"></a>Avsnittsnamn: NodeCapacities
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |Statisk|En samling av noden kapacitet för olika mått. |

### <a name="section-name-fabricnode"></a>Avsnittsnamn: FabricNode
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| StateTraceInterval |Tid i sekunder, standard är 300 |Statisk|Ange tidsintervall i sekunder. Intervall för att spåra nod status på varje nod och uppåt noder på FM/FMM. |
| StartApplicationPortRange |Int, standardvärdet är 0 |Statisk|Starta programmet portar som hanteras av värd undersystemet. Krävs om EndpointFilteringEnabled är true i värd. |
| EndApplicationPortRange |Int, standardvärdet är 0 |Statisk|Slut (inte inklusiva) program-portar som hanteras av värd undersystemet. Krävs om EndpointFilteringEnabled är true i värd. |
| ClusterX509StoreName |sträng, standardvärdet är ”Mina” |Dynamisk|Namnet på X.509-certifikatarkiv som innehåller klustret certifikat för säker kommunikation inom klustret. |
| ClusterX509FindType |strängen, standard är ”FindByThumbprint” |Dynamisk|Anger hur du söker efter klustret certifikat i arkivet som anges av ClusterX509StoreName stöds värdena: ”FindByThumbprint”; ”FindBySubjectName” med ”FindBySubjectName”; När det finns flera matchningar; med längst giltighetstid används. |
| ClusterX509FindValue |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta certifikat för klustret. |
| ClusterX509FindValueSecondary |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta certifikat för klustret. |
| ServerAuthX509StoreName |sträng, standardvärdet är ”Mina” |Dynamisk|Namnet på X.509-certifikatarkiv som innehåller servercertifikat för förrätter service. |
| ServerAuthX509FindType |strängen, standard är ”FindByThumbprint” |Dynamisk|Anger hur du söker efter servercertifikat i arkivet som anges av ServerAuthX509StoreName stöds värde: FindByThumbprint; FindBySubjectName. |
| ServerAuthX509FindValue |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta servercertifikat. |
| ServerAuthX509FindValueSecondary |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta servercertifikat. |
| ClientAuthX509StoreName |sträng, standardvärdet är ”Mina” |Dynamisk|Namnet på X.509-certifikatarkiv som innehåller standard administratörsroll FabricClient-certifikat. |
| ClientAuthX509FindType |strängen, standard är ”FindByThumbprint” |Dynamisk|Anger hur du söker efter certifikat i arkivet som anges av ClientAuthX509StoreName stöds värde: FindByThumbprint; FindBySubjectName. |
| ClientAuthX509FindValue |sträng, standardvärdet är ”” | Dynamisk|Sök filtervärdet som används för att hitta certifikatet för standard administratörsroll FabricClient. |
| ClientAuthX509FindValueSecondary |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta certifikatet för standard administratörsroll FabricClient. |
| UserRoleClientX509StoreName |sträng, standardvärdet är ”Mina” |Dynamisk|Namnet på X.509-certifikatarkiv som innehåller certifikat för standardanvändarrollen FabricClient. |
| UserRoleClientX509FindType |strängen, standard är ”FindByThumbprint” |Dynamisk|Anger hur du söker efter certifikat i arkivet som anges av UserRoleClientX509StoreName stöds värde: FindByThumbprint; FindBySubjectName. |
| UserRoleClientX509FindValue |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta certifikatet för standardanvändarrollen FabricClient. |
| UserRoleClientX509FindValueSecondary |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta certifikatet för standardanvändarrollen FabricClient. |

### <a name="section-name-paas"></a>Avsnittsnamn: Paas
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| ClusterId |sträng, standardvärdet är ”” |Inte tillåten|X509 certifikatarkivet användas av fabric för konfiguration av skydd. |

### <a name="section-name-fabrichost"></a>Avsnittsnamn: FabricHost
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| StopTimeout |Tid i sekunder, standard är 300 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för aktivering i värdtjänsten. Inaktivering och uppgradering. |
| StartTimeout |Tid i sekunder, standard är 300 |Dynamisk|Ange tidsintervall i sekunder. Timeout för fabricactivationmanager start. |
| ActivationRetryBackoffInterval |Tid i sekunder, standardvärdet är 5 |Dynamisk|Ange tidsintervall i sekunder. Backoff intervall på varje Aktiveringsfel; på varje kontinuerlig Aktiveringsfel systemet försöker aktivering för upp till MaxActivationFailureCount. Återförsöksintervall på varje försök är en produkt från kontinuerlig Aktiveringsfel och inte aktiveringsintervallet. |
| ActivationMaxRetryInterval |Tid i sekunder, standard är 300 |Dynamisk|Ange tidsintervall i sekunder. Max återförsöksintervall för aktivering. På varje kontinuerlig fel beräknas återförsöksintervall som Min (ActivationMaxRetryInterval; Kontinuerlig Felberäkning * ActivationRetryBackoffInterval). |
| ActivationMaxFailureCount |Int, standardvärdet är 10 |Dynamisk|Detta är det högsta antalet som systemet ska misslyckade försök aktivera igen innan den ger upp. |
| EnableServiceFabricAutomaticUpdates |Bool, standard är FALSKT |Dynamisk|Detta är att aktivera fabric automatiska uppdateringar via Windows Update. |
| EnableServiceFabricBaseUpgrade |Bool, standard är FALSKT |Dynamisk|Detta är att aktivera grundläggande uppdatering för servern. |
| EnableRestartManagement |Bool, standard är FALSKT |Dynamisk|Detta är att starta om servern. |


### <a name="section-name-failovermanager"></a>Avsnittsnamn: FailoverManager
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| UserReplicaRestartWaitDuration |Tid i sekunder är standard 60,0 * 30 |Dynamisk|Ange tidsintervall i sekunder. När en beständiga replik kraschar; Windows Fabric väntar varaktigheten för repliken för att gå tillbaka innan du skapar nya ersättning repliker (som kräver en kopia av tillståndet). |
| QuorumLossWaitDuration |Tid i sekunder är standardvärdet MaxValue |Dynamisk|Ange tidsintervall i sekunder. Det här är den maximala längd som tillåter vi en partition är i tillståndet förlorar kvorum. Om partitionen är fortfarande i kvorumförlust efter varaktigheten; partitionen återställs från förlorar kvorum genom att beakta nedåt replikerna som förlorad. Observera att detta potentiellt kan medföra dataförluster. |
| UserStandByReplicaKeepDuration |Tid i sekunder är standardvärdet 3600.0 * 24 * 7 |Dynamisk|Ange tidsintervall i sekunder. När en beständiga replik kommer tillbaka från ett nedåt tillstånd. den kan ha redan ersatts. Den här timern anger hur länge FM behåller vänteläge repliken innan du tar bort den. |
| UserMaxStandByReplicaCount |Int, standardvärdet är 1 |Dynamisk|Standard max antal StandBy-repliker som systemet håller för användaren tjänster. |
| ExpectedClusterSize|Int, standardvärdet är 1|Dynamisk|När klustret startas först. FM väntar på att detta många noder om du vill rapportera själva in innan den börjar placera andra tjänster. inklusive systemtjänster som namnges.  Det här värdet ökar den tid det tar ett kluster att starta. men förhindrar tidig noder från blir överbelastad och ytterligare flyttar som kommer att vara nödvändigt som fler noder är online.  Det här värdet ska normalt anges till en liten del av den första klusterstorleken. |
|ClusterPauseThreshold|Int, standardvärdet är 1|Dynamisk|Om antalet noder i systemet under detta värde än placering; belastningsutjämning; och växling vid fel har stoppats. |
|TargetReplicaSetSize|Int, standard är 7|Inte tillåten|Detta är antalet mål av FM repliker med Windows Fabric.  En hög siffra resulterar i högre tillförlitlighet för FM-data. med en liten prestanda kompromiss. |
|MinReplicaSetSize|Int, standardinställningen är 3|Inte tillåten|Det här är den minsta storleken på replikuppsättningen för FM.  Om antalet aktiva FM repliker hamnar under detta värde. FM avvisar ändringar i klustret tills minst min. antal repliker återställs |
|ReplicaRestartWaitDuration|TimeSpan, standard är Common::TimeSpan::FromSeconds(60.0 * 30)|Inte tillåten|Ange tidsintervall i sekunder. Det här är ReplicaRestartWaitDuration för FMService |
|StandByReplicaKeepDuration|TimeSpan, standard är Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Inte tillåten|Ange tidsintervall i sekunder. Det här är StandByReplicaKeepDuration för FMService |
|PlacementConstraints|String, standard är L ””|Inte tillåten|Alla placeringen för failover manager-repliker |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, standard är Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamisk|Ange tidsintervall i sekunder. Det här är den förväntade varaktigheten för en nod som ska uppgraderas under Windows Fabric-uppgraderingen. |
|ExpectedReplicaUpgradeDuration|TimeSpan, standard är Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamisk|Ange tidsintervall i sekunder. Det här är den förväntade varaktigheten för alla repliker som ska uppgraderas på en nod under uppgradering av programmet. |
|ExpectedNodeDeactivationDuration|TimeSpan, standard är Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamisk|Ange tidsintervall i sekunder. Det här är den förväntade varaktighet för en nod slutföra inaktiveringen i. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, standard är SANT|Dynamisk|Om värdet är true; repliker med ett mål för replikuppsättning 1 kommer att tillåtas att flytta under uppgraderingen. |
|ReconfigurationTimeLimit|TimeSpan, standard är Common::TimeSpan::FromSeconds(300)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för omkonfiguration; efter vilken en varning hälsorapport initieras |
|BuildReplicaTimeLimit|TimeSpan, standard är Common::TimeSpan::FromSeconds(3600)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att skapa en tillståndskänslig replik; efter vilken en varning hälsorapport initieras |
|CreateInstanceTimeLimit|TimeSpan, standard är Common::TimeSpan::FromSeconds(300)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att skapa en tillståndslös instans; efter vilken en varning hälsorapport initieras |
|PlacementTimeLimit|TimeSpan, standard är Common::TimeSpan::FromSeconds(600)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att nå målet replikantalet; efter vilken en varning hälsorapport initieras |

### <a name="section-name-namingservice"></a>Avsnittsnamn: NamingService
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, standard är 7 |Inte tillåten|Antal replik anger för varje partition för arkivet Naming Service. Öka antalet replikuppsättningar ger en högre tillförlitlighet för informationen i arkivet Naming; minska ändringen att informationen går förlorade på grund av nodfel; kostnad för ökad belastning på Windows Fabric och hur lång tid tar det för att utföra uppdateringar på namngivning data.|
|MinReplicaSetSize | Int, standardinställningen är 3 |Inte tillåten| Det minsta antalet namngivningstjänst repliker som krävs för att skriva till att slutföra en uppdatering. Om det finns färre repliker än det aktiva i systemet systemets tillförlitlighet nekar uppdateringar till arkivet Naming förrän replikeringarna har återställts. Det här värdet ska aldrig vara mer än TargetReplicaSetSize. |
|ReplicaRestartWaitDuration | Tid i sekunder är standardvärdet (60,0 * 30)|Inte tillåten| Ange tidsintervall i sekunder. När en replik för namngivningstjänst kraschar; den här timern startas.  När den upphör FM börjar ersätta repliker som är nere (den inte ännu anser tappas bort). |
|QuorumLossWaitDuration | Tid i sekunder är standardvärdet MaxValue |Inte tillåten| Ange tidsintervall i sekunder. När en Naming Service hämtar i kvorumförlust; den här timern startas.  När den upphör att gälla beaktas FM nedåt repliker som gå förlorad. och försöka att återställa kvorum. Inte som den här kan resultera i förlust av data. |
|StandByReplicaKeepDuration | Tid i sekunder är standard 3600.0 * 2 |Inte tillåten| Ange tidsintervall i sekunder. När en replik Naming Service kommer tillbaka från ett nedåt tillstånd. den kan ha redan ersatts.  Den här timern anger hur länge FM behåller vänteläge repliken innan du tar bort den. |
|PlacementConstraints | sträng, standardvärdet är ”” |Inte tillåten| Placering begränsningen för namngivning av tjänsten. |
|ServiceDescriptionCacheLimit | Int, standardvärdet är 0 |Statisk| Det maximala antalet poster som underhålls i LRU tjänstens beskrivning cache på lagringstjänst Naming (Ange 0 för ingen gräns). |
|RepairInterval | Tid i sekunder, standardvärdet är 5 |Statisk| Ange tidsintervall i sekunder. Intervall som reparera namngivning inkonsekvens mellan myndigheten ägare och namnägare startar. |
|MaxNamingServiceHealthReports | Int, standardvärdet är 10 |Dynamisk|Det maximala antalet långsam åtgärder som lagrar Naming service rapporter feltillstånd i taget. Om 0; alla åtgärder för långsam skickas. |
| MaxMessageSize |Int, standardvärdet är 4\*1024\*1024 |Statisk|Maximal storlek för noden klientkommunikation när du använder naming. DOS-attack lindra; Standardvärdet är 4MB. |
| MaxFileOperationTimeout |Tid i sekunder, standardvärdet är 30 |Dynamisk|Ange tidsintervall i sekunder. Maximal tidsgräns som tillåts för filåtgärd store-tjänsten. Ange en längre tidsgräns nekas. |
| MaxOperationTimeout |Tid i sekunder, standard är 600 |Dynamisk|Ange tidsintervall i sekunder. Maximal tidsgräns som tillåts för Klientåtgärder. Ange en längre tidsgräns nekas. |
| MaxClientConnections |Int, standardvärdet är 1000 |Dynamisk|Maximalt tillåtna antalet klientanslutningar per gateway. |
| ServiceNotificationTimeout |Tid i sekunder, standardvärdet är 30 |Dynamisk|Ange tidsintervall i sekunder. Den tidsgräns som används när tjänstmeddelanden levereras till klienten. |
| MaxOutstandingNotificationsPerClient |Int, standardvärdet är 1000 |Dynamisk|Det maximala antalet väntande meddelanden innan en klientregistrering stängs framtvingar av gateway. |
| MaxIndexedEmptyPartitions |Int, standardvärdet är 1000 |Dynamisk|Det maximala antalet tomma partitioner som ska vara indexera i notification-cache för att synkronisera återanslutning klienter. Eventuella tomma partitioner ovanför det här numret tas bort från indexet i stigande ordning för sökning version. Ansluta klienter kan fortfarande synkronisera och ta emot uppdateringar missade tom partition. men synkroniseringsprotokollet blir dyrare. |
| GatewayServiceDescriptionCacheLimit |Int, standardvärdet är 0 |Statisk|Det maximala antalet poster som underhålls i LRU tjänstens beskrivning cache på Namngivningsgateway (Ange 0 för ingen gräns). |
| PartitionCount |Int, standardinställningen är 3 |Inte tillåten|Antalet partitioner för tjänsten Naming lagra som ska skapas. Varje partition äger en enda partitionsnyckel som motsvarar dess index. så partitionsnycklar [0; PartitionCount) finns. Öka antalet namngivningstjänst partitioner ökar skalan som Naming Service kan utföra genom att minska den genomsnittliga mängden data från alla stödjande repliker ange; kostnad ökad användning av resurser (eftersom PartitionCount * ReplicaSetSize service repliker måste underhållas).|

### <a name="section-name-runas"></a>Avsnittsnamn: RunAs
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| RunAsAccountName |sträng, standardvärdet är ”” |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för ”DomainUser” eller ”ManagedServiceAccount” typen. Giltiga värden är ”domän\användare” eller ”user@domain”. |
|RunAsAccountType|sträng, standardvärdet är ”” |Dynamisk|Anger typen för RunAs-konto. Detta krävs för alla RunAs-avsnittet, giltiga värden är ”DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”.|
|Lösenord|sträng, standardvärdet är ”” |Dynamisk|Anger lösenordet för RunAs-konto. Detta krävs endast för typen ”DomainUser”. |

### <a name="section-name-runasfabric"></a>Avsnittsnamn: RunAs_Fabric
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| RunAsAccountName |sträng, standardvärdet är ”” |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för ”DomainUser” eller ”ManagedServiceAccount” typen. Giltiga värden är ”domän\användare” eller ”user@domain”. |
|RunAsAccountType|sträng, standardvärdet är ”” |Dynamisk|Anger typen för RunAs-konto. Detta krävs för alla RunAs-avsnittet, giltiga värden är ”Lokalanvändare/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”. |
|Lösenord|sträng, standardvärdet är ”” |Dynamisk|Anger lösenordet för RunAs-konto. Detta krävs endast för typen ”DomainUser”. |

### <a name="section-name-runashttpgateway"></a>Avsnittsnamn: RunAs_HttpGateway
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| RunAsAccountName |sträng, standardvärdet är ”” |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för ”DomainUser” eller ”ManagedServiceAccount” typen. Giltiga värden är ”domän\användare” eller ”user@domain”. |
|RunAsAccountType|sträng, standardvärdet är ”” |Dynamisk|Anger typen för RunAs-konto. Detta krävs för alla RunAs-avsnittet, giltiga värden är ”Lokalanvändare/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”. |
|Lösenord|sträng, standardvärdet är ”” |Dynamisk|Anger lösenordet för RunAs-konto. Detta krävs endast för typen ”DomainUser”. |

### <a name="section-name-runasdca"></a>Avsnittsnamn: RunAs_DCA
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| RunAsAccountName |sträng, standardvärdet är ”” |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för ”DomainUser” eller ”ManagedServiceAccount” typen. Giltiga värden är ”domän\användare” eller ”user@domain”. |
|RunAsAccountType|sträng, standardvärdet är ”” |Dynamisk|Anger typen för RunAs-konto. Detta krävs för alla RunAs-avsnittet, giltiga värden är ”Lokalanvändare/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”. |
|Lösenord|sträng, standardvärdet är ”” |Dynamisk|Anger lösenordet för RunAs-konto. Detta krävs endast för typen ”DomainUser”. |

### <a name="section-name-httpgateway"></a>Avsnittsnamn: HttpGateway
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|IsEnabled|Bool, standard är FALSKT |Statisk| Aktiverar eller inaktiverar HttpGateway. HttpGateway är inaktiverad som standard. |
|ActiveListeners |Uint, standardvärdet är 50 |Statisk| Antalet läsningar publicera till kön för http-servern. Detta anger antalet samtidiga begäranden som kan betjänas av HttpGateway. |
|MaxEntityBodySize |Uint, standardvärdet är 4194304 |Dynamisk|Ger den maximala storleken för brödtext som kan förväntas från en http-begäran. Standardvärdet är 4MB. Httpgateway misslyckas en begäran om den har en uppsättning storlek > det här värdet. Minsta skrivskyddade segmentstorleken är 4096 byte. Så här måste vara > = 4096. |
|HttpGatewayHealthReportSendInterval |Tid i sekunder, standardvärdet är 30 |Statisk|Ange tidsintervall i sekunder. Intervallet med vilken Http-Gateway skickar ackumulerade hälsa rapporterar till Health Manager. |

### <a name="section-name-ktllogger"></a>Avsnittsnamn: KtlLogger
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, standardvärdet är 1 |Dynamisk|Flagga som anger om minnesinställningarna ska automatiskt och dynamiskt konfigureras. Om noll sedan konfigurationsinställningarna för minne används direkt och ändra inte baserat på villkor som system. Om en sedan minnesinställningarna konfigureras automatiskt och kan ändras baserat på villkor som system. |
|WriteBufferMemoryPoolMinimumInKB |Int, standard är 8388608 |Dynamisk|Antal KB ursprungligen allokera för skrivning buffert minnespoolen. Använd 0 för att ange någon gräns standard ska stämma överens med SharedLogSizeInMB nedan. |
|WriteBufferMemoryPoolMaximumInKB | Int, standardvärdet är 0 |Dynamisk|Antal KB att skriva buffert minnespoolen att växa upp till. Använd 0 om du vill ange någon gräns. |
|MaximumDestagingWriteOutstandingInKB | Int, standardvärdet är 0 |Dynamisk|Antal KB så att delade loggen att Avancera i dedikerade loggen. Använd 0 om du vill ange någon gräns.
|SharedLogPath |sträng, standardvärdet är ”” |Statisk|Sökvägen och filnamnet för plats för delade loggen behållare. Använd ”” för att använda standardsökvägen under fabric-dataroten. |
|SharedLogId |sträng, standardvärdet är ”” |Statisk|Unikt guid för delade loggen behållare. Använd ”” om du använder standardsökvägen under fabric-dataroten. |
|SharedLogSizeInMB |Int, standard är 8192 |Statisk|Antal MB för att allokera i behållaren delade loggen. |

### <a name="section-name-applicationgatewayhttp"></a>Avsnittsnamn: ApplicationGateway/Http
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|IsEnabled |Bool, standard är FALSKT |Statisk| Aktiverar eller inaktiverar HttpApplicationGateway. HttpApplicationGateway är inaktiverad som standard och den här konfigurationen måste anges för att aktivera den. |
|NumberOfParallelOperations | Uint, standard är 5 000 |Statisk|Antalet läsningar publicera till kön för http-servern. Detta anger antalet samtidiga begäranden som kan betjänas av HttpGateway. |
|DefaultHttpRequestTimeout |Tid i sekunder. Standardvärdet är 120 |Dynamisk|Ange tidsintervall i sekunder.  Ger standardtimeout för begäranden för http-begäranden som bearbetas i HTTP-app gateway. |
|ResolveServiceBackoffInterval |Tid i sekunder, standardvärdet är 5 |Dynamisk|Ange tidsintervall i sekunder.  Ger inte standardintervallet innan du försöker en misslyckad lösa tjänståtgärden. |
|BodyChunkSize |Uint, standard är 16384 |Dynamisk| Anger storleken på för segmentet i byte som används för att läsa innehållet. |
|GatewayAuthCredentialType |String, standard är ”ingen” |Statisk| Anger typ av säkerhetsautentiseringsuppgifter som används på http-app gateway endpoint giltiga värden är ”ingen / X 509. |
|GatewayX509CertificateStoreName |sträng, standardvärdet är ”Mina” |Dynamisk| Namnet på X.509-certifikatarkiv som innehåller certifikat för HTTP-app gateway. |
|GatewayX509CertificateFindType |strängen, standard är ”FindByThumbprint” |Dynamisk| Anger hur du söker efter certifikat i arkivet som anges av GatewayX509CertificateStoreName stöds värde: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | sträng, standardvärdet är ”” |Dynamisk| Sök filtervärdet som används för att hitta gateway-certifikat för HTTP-app. Det här certifikatet är konfigurerad för https-slutpunkt och kan också användas för att kontrollera identiteten på appen om det behövs av tjänsterna. FindValue slås upp första; och om som inte finns; FindValueSecondary slås upp. |
|GatewayX509CertificateFindValueSecondary | sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta gateway-certifikat för HTTP-app. Det här certifikatet är konfigurerad för https-slutpunkt och kan också användas för att kontrollera identiteten på appen om det behövs av tjänsterna. FindValue slås upp första; och om som inte finns; FindValueSecondary slås upp.|
|HttpRequestConnectTimeout|TimeSpan, standard är Common::TimeSpan::FromSeconds(5)|Dynamisk|Ange tidsintervall i sekunder.  Ger connect timeout-värdet för http-begäranden som skickas från gateway för http-app.  |
|RemoveServiceResponseHeaders|String, standard är L ”datum. Server ”|Statisk|Semikolon / kommaavgränsad lista över svarshuvuden som tas bort från tjänsten svaret; innan den vidarebefordrar det till klienten. Om detta anges till en tom sträng; Skicka alla huvuden som returnerats av tjänsten som-är. engångsfaktorautentisering Skriv inte över datum och Server |
|ApplicationCertificateValidationPolicy|String, standard är L ”None”|Statisk| ApplicationCertificateValidationPolicy: Ingen: inte verifiera servercertifikat; begäran att lyckas. ServiceCertificateThumbprints: Avse config ServiceCertificateThumbprints kommaavgränsad lista över tumavtryck för fjärranslutna certifikat kan lita på omvänd proxy. ServiceCommonNameAndIssuer: Avse config ServiceCommonNameAndIssuer för ämne namn och utfärdaren tumavtrycket för fjärranslutna certifikat kan lita på omvänd proxy. |
|ServiceCertificateThumbprints|String, standard är L ””|Dynamisk| |
|CrlCheckingFlag|uint, standard är 0x40000000 |Dynamisk| Flaggorna för program/tjänst verifiering av certifikatkedjan; till exempel CRL-kontroll 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY inställningen till 0 inaktiverar CRL kontrollerar fullständig lista över värden som stöds är dokumenterade av dwFlags av CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|IgnoreCrlOfflineError|bool, standard är SANT|Dynamisk|Om du vill ignorera CRL offline fel för program/tjänst certifikatverifiering. |
|SecureOnlyMode|bool, standard är FALSKT|Dynamisk| SecureOnlyMode: true: omvänd Proxy vidarebefordrar bara till tjänster som publicerar säker slutpunkter. FALSKT: omvänd Proxy kan vidarebefordra begäran till secure/icke-säker slutpunkter.  |
|ForwardClientCertificate|bool, standard är FALSKT|Dynamisk| |

### <a name="section-name-applicationgatewayhttpservicecommonnameandissuer"></a>Avsnittsnamn: ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standard är ingen|Dynamisk|  |

### <a name="section-name-management"></a>Avsnittsnamn: Management
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| ImageStoreConnectionString |SecureString |Statisk|Anslutningssträngen till roten för Avbildningsarkiv. |
| ImageStoreMinimumTransferBPS | Int, standard är 1024 |Dynamisk|Den minsta överföringshastigheten mellan klustret och Avbildningsarkiv. Det här värdet används för att fastställa tidsgräns vid åtkomst till externa ImageStore. Ändra det här värdet bara om fördröjningen mellan klustret och Avbildningsarkiv är hög att ge mer tid för att klustret ska hämta från externa ImageStore. |
|AzureStorageMaxWorkerThreads | Int, standard är 25 |Dynamisk|Maximalt antal arbetstrådar parallellt. |
|AzureStorageMaxConnections | Int, standard är 5 000 |Dynamisk|Maximalt antal samtidiga anslutningar till azure-lagring. |
|AzureStorageOperationTimeout | Tid i sekunder är standardvärdet 6000 |Dynamisk|Ange tidsintervall i sekunder. Tidsgräns för xstore-åtgärd ska slutföras. |
|ImageCachingEnabled | Bool, standard är SANT |Statisk|Den här konfigurationen gör att vi kan aktivera eller inaktivera cachelagring. |
|DisableChecksumValidation | Bool, standard är FALSKT |Statisk| Den här konfigurationen gör att vi kan aktivera eller inaktivera validering av kontrollsumma under etableringen av programmet. |
|DisableServerSideCopy | Bool, standard är FALSKT |Statisk|Den här konfigurationen aktiverar eller inaktiverar serversidan kopia av programpaket på ImageStore under etableringen av programmet. |

### <a name="section-name-healthmanagerclusterhealthpolicy"></a>Avsnittsnamn: HealthManager/ClusterHealthPolicy
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| ConsiderWarningAsError |Bool, standard är FALSKT |Statisk|Klustret hälsoprincip för utvärdering: behandlas varningar som fel. |
| MaxPercentUnhealthyNodes | Int, standardvärdet är 0 |Statisk|Klustret hälsoprincip för utvärdering: Maxprocent felaktiga noder som tillåts för att klustret ska felfri. |
| MaxPercentUnhealthyApplications | Int, standardvärdet är 0 |Statisk|Klustret hälsoprincip för utvärdering: maximala procentandelen felaktiga program tillåts för att klustret ska felfritt. |

### <a name="section-name-healthmanagerclusterupgradehealthpolicy"></a>Avsnittsnamn: HealthManager/ClusterUpgradeHealthPolicy
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|Int, standardvärdet är 10|Statisk|Klustret uppgradera utvärdering hälsoprincip: Maxprocent delta felaktiga noder som tillåts för att klustret ska felfri |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|Int, standardvärdet är 15|Statisk|Klustret uppgradera utvärdering hälsoprincip: maximala procentandelen delta felaktiga noder i en uppgraderingsdomän tillåts för att klustret ska felfri |

### <a name="section-name-faultanalysisservice"></a>Avsnittsnamn: FaultAnalysisService
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, standardvärdet är 0 |Statisk|NOT_PLATFORM_UNIX_START TargetReplicaSetSize för FaultAnalysisService. |
| MinReplicaSetSize |Int, standardvärdet är 0 |Statisk|MinReplicaSetSize för FaultAnalysisService. |
| ReplicaRestartWaitDuration |Tid i sekunder, standardvärdet är 60 minuter|Statisk|Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för FaultAnalysisService. |
| QuorumLossWaitDuration | Tid i sekunder är standardvärdet MaxValue |Statisk|Ange tidsintervall i sekunder. QuorumLossWaitDuration för FaultAnalysisService. |
| StandByReplicaKeepDuration| Tid i sekunder är standardvärdet (60*24*7) minuter |Statisk|Ange tidsintervall i sekunder. StandByReplicaKeepDuration för FaultAnalysisService. |
| PlacementConstraints | sträng, standardvärdet är ””|Statisk| PlacementConstraints för FaultAnalysisService. |
| StoredActionCleanupIntervalInSeconds | Int, standard är 3 600 |Statisk|Detta är hur ofta arkivet rensas.  Endast åtgärder i ett avslutat tillstånd; och som slutförts minst CompletedActionKeepDurationInSeconds sedan kommer att tas bort. |
| CompletedActionKeepDurationInSeconds | Int, standard är 604800 |Statisk| Detta är ungefär hur lång tid att behålla åtgärder som är i ett avslutat tillstånd.  Detta beror också på StoredActionCleanupIntervalInSeconds; eftersom arbetet som rensning utförs endast på intervallet. 604800 är 7 dagar. |
| StoredChaosEventCleanupIntervalInSeconds | Int, standard är 3 600 |Statisk|Detta är hur ofta arkivet kommer att granskas för rensning; Om antalet händelser som är mer än 30000; rensningen kommer startar. |
|DataLossCheckWaitDurationInSeconds|Int, standard är 25|Statisk|Den totala mängden tid. i sekunder. att systemet väntar förlust av data ska ske.  Det här används internt när StartPartitionDataLossAsync() API: et anropas. |
|DataLossCheckPollIntervalInSeconds|int, standard är 5|Statisk|Detta är tiden mellan de kontroller som utförs under väntan på förlust av data ska ske.  Antalet gånger som data går förlorade numret kontrolleras per interna iteration är DataLossCheckWaitDurationInSeconds för detta. |
|ReplicaDropWaitDurationInSeconds|int, standard är 600|Statisk|Den här parametern används när data går förlorade api anropas.  Den styr hur länge systemet väntar på en replik hämta bort efter att ta bort replik anropas internt på den. |

### <a name="section-name-filestoreservice"></a>Avsnittsnamn: FileStoreService
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| NamingOperationTimeout |Tid i sekunder, standardvärdet är 60 |Dynamisk|Ange tidsintervall i sekunder. Tidsgräns för namngivning åtgärd utfördes. |
| QueryOperationTimeout | Tid i sekunder, standardvärdet är 60 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att utföra frågeåtgärden igen. |
| MaxCopyOperationThreads | Uint, standardvärdet är 0 |Dynamisk| Det maximala antalet parallella filer som andra kan kopiera från primära. '0' == antal kärnor. |
| MaxFileOperationThreads | Uint, standardvärdet är 100 |Statisk| Maximalt antal parallella trådar som får utföra FileOperations (kopiera/flytta) i den primära servern. '0' == antal kärnor. |
| MaxStoreOperations | Uint, standardvärdet är 4096 |Statisk|Maximalt antal tillåtna på den primära parallella store transaktion-åtgärder. '0' == antal kärnor. |
| MaxRequestProcessingThreads | Uint, standardinställningen är 200 |Statisk|Maximalt antal parallella trådar som tillåts att behandla begäranden i den primära servern. '0' == antal kärnor. |
| MaxSecondaryFileCopyFailureThreshold | Uint, standard är 25|Dynamisk|Det maximala antalet filen kopia på sekundärt innan den ger upp. |
| AnonymousAccessEnabled | Bool, standard är SANT |Statisk|Aktivera/inaktivera anonym åtkomst till FileStoreService resurser. |
| PrimaryAccountType | sträng, standardvärdet är ”” |Statisk|Primärt AccountType huvudkontots åtkomstkontrollistan i FileStoreService delar. |
| PrimaryAccountUserName | sträng, standardvärdet är ”” |Statisk|Det primära kontot användarnamnet för huvudnamn för ACL i FileStoreService delar. |
| PrimaryAccountUserPassword | SecureString, standard är tom |Statisk|Primär kontolösenordet huvudkontots åtkomstkontrollistan i FileStoreService delar. |
| PrimaryAccountNTLMPasswordSecret | SecureString, standard är tom |Statisk| Den hemlighet som lösenord som används som startvärde för samma lösenord när du använder NTLM-autentisering. |
| PrimaryAccountNTLMX509StoreLocation | strängen, standard är ”LocalMachine”|Statisk| Lagringsplatsen för X509 certifikat som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
| PrimaryAccountNTLMX509StoreName | strängen, standard är ”MY”|Statisk| Store-namnet på X509 certifikat som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
| PrimaryAccountNTLMX509Thumbprint | sträng, standardvärdet är ””|Statisk|Tumavtrycket för X509 certifikat som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
| SecondaryAccountType | sträng, standardvärdet är ””|Statisk| Sekundärt AccountType huvudkontots åtkomstkontrollistan i FileStoreService delar. |
| SecondaryAccountUserName | sträng, standardvärdet är ””| Statisk|Det sekundära kontot användarnamn huvudkontots åtkomstkontrollistan i FileStoreService delar. |
| SecondaryAccountUserPassword | SecureString, standard är tom |Statisk|Sekundär kontolösenordet huvudkontots åtkomstkontrollistan i FileStoreService delar.  |
| SecondaryAccountNTLMPasswordSecret | SecureString, standard är tom |Statisk| Den hemlighet som lösenord som används som startvärde för samma lösenord när du använder NTLM-autentisering. |
| SecondaryAccountNTLMX509StoreLocation | strängen, standard är ”LocalMachine” |Statisk|Lagringsplatsen för X509 certifikat som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
| SecondaryAccountNTLMX509StoreName | strängen, standard är ”MY” |Statisk|Store-namnet på X509 certifikat som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
| SecondaryAccountNTLMX509Thumbprint | sträng, standardvärdet är ””| Statisk|Tumavtrycket för X509 certifikat som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
|CommonNameNtlmPasswordSecret|SecureString, standard är Common::SecureString(L"")| Statisk|Den hemlighet som lösenord som används som startvärde för samma lösenord när du använder NTLM-autentisering |
|CommonName1Ntlmx509StoreLocation|String, standard är L ”LocalMachine”|Statisk|Lagringsplatsen för X509 certifikat som används för att generera HMAC på CommonName1NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonName1Ntlmx509StoreName|String, standard är L ”MY”| Statisk|Store-namnet på X509 certifikat som används för att generera HMAC på CommonName1NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonName1Ntlmx509CommonName|String, standard är L ””|Statisk| Namnet på X509 certifikat som används för att generera HMAC på CommonName1NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonName2Ntlmx509StoreLocation|String, standard är L ”LocalMachine”| Statisk|Lagringsplatsen för X509 certifikat som används för att generera HMAC på CommonName2NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonName2Ntlmx509StoreName|String, standard är L ”MY”|Statisk| Store-namnet på X509 certifikat som används för att generera HMAC på CommonName2NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonName2Ntlmx509CommonName|String, standard är L ””|Statisk|Namnet på X509 certifikat som används för att generera HMAC på CommonName2NtlmPasswordSecret när du använder NTLM-autentisering |
|GenerateV1CommonNameAccount| bool, standard är SANT|Statisk|Anger om du vill skapa ett konto med användaren namnet V1 generation algoritm. Från och med Service Fabric version 6.1; ett konto med v2 generation skapas alltid. V1-konto krävs för uppgradering från/till versioner som inte stöder generering av V2 (före 6.1).|

### <a name="section-name-imagestoreservice"></a>Avsnittsnamn: ImageStoreService
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| Enabled |Bool, standard är FALSKT |Statisk|Flaggan aktiverad för ImageStoreService. Standard: false |
| TargetReplicaSetSize | Int, standard är 7 |Statisk|TargetReplicaSetSize för ImageStoreService. |
| MinReplicaSetSize | Int, standardinställningen är 3 |Statisk|MinReplicaSetSize för ImageStoreService. |
| ReplicaRestartWaitDuration | Tid i sekunder är standard 60,0 * 30 |Statisk|Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för ImageStoreService. |
| QuorumLossWaitDuration | Tid i sekunder är standardvärdet MaxValue |Statisk| Ange tidsintervall i sekunder. QuorumLossWaitDuration för ImageStoreService. |
| StandByReplicaKeepDuration | Tid i sekunder är standard 3600.0 * 2 |Statisk| Ange tidsintervall i sekunder. StandByReplicaKeepDuration för ImageStoreService. |
| PlacementConstraints | sträng, standardvärdet är ”” |Statisk| PlacementConstraints för ImageStoreService. |

### <a name="section-name-imagestoreclient"></a>Avsnittsnamn: ImageStoreClient
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| ClientUploadTimeout |Tid i sekunder är standardvärdet 1 800 |Dynamisk|Ange tidsintervall i sekunder. Timeout-värde för översta överför begäran att Image Store-tjänsten. |
| ClientCopyTimeout | Tid i sekunder är standardvärdet 1 800 |Dynamisk| Ange tidsintervall i sekunder. Timeout-värde för översta copy-begäran att Image Store-tjänsten. |
|ClientDownloadTimeout | Tid i sekunder är standardvärdet 1 800 |Dynamisk| Ange tidsintervall i sekunder. Timeout-värde för översta hämtningsbegäran till Image Store-tjänsten. |
|ClientListTimeout | Tid i sekunder, standard är 600 |Dynamisk|Ange tidsintervall i sekunder. Timeout-värde för översta listan begäran att Image Store-tjänsten. |
|ClientDefaultTimeout | Tid i sekunder, standard är 180 |Dynamisk| Ange tidsintervall i sekunder. Timeout-värde för alla icke-överför/icke-hämtningsbegäranden (t.ex. finns och ta bort) att Image Store-tjänsten. |

### <a name="section-name-tokenvalidationservice"></a>Avsnittsnamn: TokenValidationService
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| Leverantörer |strängen, standard är ”DSTS” |Statisk|Kommaavgränsad lista över token validering leverantörer att aktivera (giltigt providers är: DSTS; AAD). För närvarande bara en enskild provider kan aktiveras när som helst. |

### <a name="section-name-upgradeorchestrationservice"></a>Avsnittsnamn: UpgradeOrchestrationService
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Int, standardvärdet är 0 |Statisk |TargetReplicaSetSize för UpgradeOrchestrationService. |
| MinReplicaSetSize |Int, standardvärdet är 0 |Statisk |MinReplicaSetSize för UpgradeOrchestrationService.
| ReplicaRestartWaitDuration | Tid i sekunder, standardvärdet är 60 minuter|Statisk| Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för UpgradeOrchestrationService. |
| QuorumLossWaitDuration | Tid i sekunder är standardvärdet MaxValue |Statisk| Ange tidsintervall i sekunder. QuorumLossWaitDuration för UpgradeOrchestrationService. |
| StandByReplicaKeepDuration | Tid i sekunder, standardvärdet är 60*24*7 minuter |Statisk| Ange tidsintervall i sekunder. StandByReplicaKeepDuration för UpgradeOrchestrationService. |
| PlacementConstraints | sträng, standardvärdet är ”” |Statisk| PlacementConstraints för UpgradeOrchestrationService. |
| AutoupgradeEnabled | Bool, standard är SANT |Statisk| Automatisk avsökning och uppgraderingsåtgärden baserat på mål-tillståndsfil. |
| UpgradeApprovalRequired | Bool, standard är FALSKT | Statisk|Ställa in att koden uppgradering kräver administratörsgodkännande innan du fortsätter. |

### <a name="section-name-upgradeservice"></a>Avsnittsnamn: UpgradeService
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| PlacementConstraints |sträng, standardvärdet är ”” |Inte tillåten|PlacementConstraints för uppgradering. |
| TargetReplicaSetSize | Int, standardinställningen är 3 |Inte tillåten| TargetReplicaSetSize för UpgradeService. |
| MinReplicaSetSize | Int, standardvärdet är 2 |Inte tillåten| MinReplicaSetSize för UpgradeService. |
| CoordinatorType | strängen, standard är ”WUTest”|Inte tillåten|CoordinatorType för UpgradeService. |
| BaseUrl | sträng, standardvärdet är ”” |Statisk|BaseUrl för UpgradeService. |
| ClusterId | sträng, standardvärdet är ”” |Statisk|ClusterId för UpgradeService. |
| X509StoreName | sträng, standardvärdet är ”Mina”|Dynamisk|X509StoreName för UpgradeService. |
| X509StoreLocation | sträng, standardvärdet är ”” |Dynamisk| X509StoreLocation för UpgradeService. |
| X509FindType | sträng, standardvärdet är ””|Dynamisk| X509FindType för UpgradeService. |
| X509FindValue | sträng, standardvärdet är ”” |Dynamisk| X509FindValue för UpgradeService. |
| X509SecondaryFindValue | sträng, standardvärdet är ”” |Dynamisk| X509SecondaryFindValue för UpgradeService. |
| OnlyBaseUpgrade | Bool, standard är FALSKT |Dynamisk|OnlyBaseUpgrade för UpgradeService. |
| TestCabFolder | sträng, standardvärdet är ”” |Statisk| TestCabFolder för UpgradeService. |

### <a name="section-name-security"></a>Avsnittsnamn: säkerhet
| **Parametern** | **Tillåtna värden** |**Princip för versionsuppgradering**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ClusterCredentialType|String, standard är L ”None”|Inte tillåten|Anger vilken typ av säkerhetsreferenser som ska användas för att skydda klustret. Giltiga värden är ”ingen/X509/Windows” |
|ServerAuthCredentialType|String, standard är L ”None”|Statisk|Anger vilken typ av säkerhetsreferenser som ska användas för att skydda kommunikationen mellan FabricClient och klustret. Giltiga värden är ”ingen/X509/Windows” |
|ClientRoleEnabled|bool, standard är FALSKT|Statisk|Anger om klientrollen är aktiverad; Om värdet är true; klienter tilldelas roller baserat på deras identiteter. För V2; aktivera detta innebär att klienten inte i AdminClientCommonNames/AdminClientIdentities kan endast köra skrivskyddade åtgärder. |
|ClusterCertThumbprints|String, standard är L ””|Dynamisk|Tumavtryck för certifikat som tillåts att ansluta klusternoderna. en kommaavgränsad lista med. |
|ServerCertThumbprints|String, standard är L ””|Dynamisk|Tumavtryck för certifikat används av klustret för att kommunicera med klienter. klienter använder detta för att autentisera till klustret. Det är en kommaavgränsad lista med. |
|ClientCertThumbprints|String, standard är L ””|Dynamisk|Tumavtryck för certifikat som används av klienter för att kommunicera klusternoderna. klustret använder det tillåter inkommande anslutning. Det är en kommaavgränsad lista med. |
|AdminClientCertThumbprints|String, standard är L ””|Dynamisk|Tumavtryck för certifikat som används av klienter i rollen Administratör. Det är en kommaavgränsad lista med. |
|CrlCheckingFlag|uint, standard är 0x40000000|Dynamisk|Standard certifikatkedjan validering flaggan; kan åsidosättas av komponenten-specifika flaggan; t.ex. Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ ENDAST inställningen till 0 inaktiveras CRL kontrollerar fullständig lista över värden som stöds är dokumenterade av dwFlags av CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|IgnoreCrlOfflineError|bool, standard är FALSKT|Dynamisk|Om du vill ignorera offline CRL-fel när servern verifierar inkommande klientcertifikat |
|IgnoreSvrCrlOfflineError|bool, standard är SANT|Dynamisk|Om du vill ignorera offline CRL-fel när klienten verifierar inkommande servercertifikat; som standard till true. Attacker med återkallade certifikat kräver att kompromettera DNS; svårare än med återkallade certifikat. |
|CrlDisablePeriod|TimeSpan, standard är Common::TimeSpan::FromMinutes(15)|Dynamisk|Ange tidsintervall i sekunder. Hur länge CRL-kontroll är inaktiverad för ett visst certifikat efter offline fel. Om CRL offline fel kan ignoreras. |
|CrlOfflineHealthReportTtl|TimeSpan, standard är Common::TimeSpan::FromMinutes(1440)|Dynamisk|Ange tidsintervall i sekunder. |
|CertificateHealthReportingInterval|TimeSpan, standard är Common::TimeSpan::FromSeconds(3600 * 8)|Statisk|Ange tidsintervall i sekunder. Ange intervall för certifikatet hälsa rapportering. standard till 8 timmar. inställningen värdet 0 inaktiverar certifikat hälsa reporting |
|CertificateExpirySafetyMargin|TimeSpan, standard är Common::TimeSpan::FromMinutes(43200)|Statisk|Ange tidsintervall i sekunder. Säkerhetsmarginal för certifikatets förfallodatum; certifikatet hälsa Rapportstatus ändras till varning från OK när förfallodatum är närmare än så. Standardvärdet är 30 dagar. |
|ClientClaimAuthEnabled|bool, standard är FALSKT|Statisk|Anger om Anspråksbaserad autentisering har aktiverats på klienter. Anger det här värdet är true implicit anger ClientRoleEnabled. |
|ClientClaims|String, standard är L ””|Dynamisk|Alla möjliga anspråk som förväntas från klienter för att ansluta till gatewayen. Detta är en lista med 'Eller': ClaimsEntry || ClaimsEntry || ClaimsEntry... varje ClaimsEntry är en ”AND”-lista: ClaimType = ClaimValue & & ClaimType = ClaimValue & & ClaimType = ClaimValue... |
|AdminClientClaims|String, standard är L ””|Dynamisk|Alla möjliga anspråk som förväntas från admin-klienter. samma format som ClientClaims; den här listan läggs internt till ClientClaims; Därför behöver du också lägga till samma poster i ClientClaims. |
|ClusterSpn|String, standard är L ””|Inte tillåten|Tjänstens huvudnamn klusternoderna. När fabric körs som en enda domänanvändare (gMSA/domänanvändarkonto). Det är SPN för lånet lyssnare och lyssnare i fabric.exe: federation lyssnare; internt replikering lyssnare; Runtime service lyssnare och namngivning gateway-lyssnare. Detta ska vara tomt när fabric körs som datorkonton; i så fall ansluter sida för att beräkna lyssnare SPN från lyssnare transportadress. |
|ClusterIdentities|String, standard är L ””|Dynamisk|Windows-identiteten för noder i klustret. används för klustret medlemskap auktorisering. Det är en kommaavgränsad lista. varje post är ett namn på ett domänkonto eller gruppnamn |
|ClientIdentities|String, standard är L ””|Dynamisk|Windows-identiteten för FabricClient; namngivning gateway använder dem för att tillåta inkommande anslutningar. Det är en kommaavgränsad lista. varje post är ett namn på ett domänkonto eller gruppnamn. För bekvämlighet. det konto som kör fabric.exe tillåts automatiskt; Därför är grupp ServiceFabricAllowedUsers och ServiceFabricAdministrators. |
|AdminClientIdentities|String, standard är L ””|Dynamisk|Windows-identiteten för fabric-klienter i rollen Administratör; används för att auktorisera Privilegierade fabric-åtgärder. Det är en kommaavgränsad lista. varje post är ett namn på ett domänkonto eller gruppnamn. För bekvämlighet. det konto som kör fabric.exe tilldelas automatiskt administratörsroll; Därför är gruppera ServiceFabricAdministrators. |
|AADTenantId|String, standard är L ””|Statisk|Klient-ID (GUID) |
|AADClusterApplication|String, standard är L ””|Statisk|Namnet på webbprogrammet API eller ID som representerar klustret |
|AADClientApplication|String, standard är L ””|Statisk|Native Client programnamnet eller ID som representerar Fabric-klienter |
|X509Folder|strängen, standard är /var/lib/waagent|Statisk|Mappen där X509 certifikat och privata nycklar finns |
|FabricHostSpn| String, standard är L ”” |Statisk| Tjänstens huvudnamn FabricHost; När fabric körs som en enda domänanvändare (gMSA/domänanvändarkonto) och FabricHost körs under datorkontot. Det är SPN IPC-lyssnaren för FabricHost; som standard ska lämnas tomt eftersom FabricHost körs under datorkontot |
|DisableFirewallRuleForPublicProfile| bool, standard är SANT | Statisk|Anger om brandväggsregel inte ska aktiveras för offentlig profil |
|DisableFirewallRuleForPrivateProfile| bool, standard är SANT |Statisk| Anger om brandväggsregel inte ska aktiveras för privat profil | 
|DisableFirewallRuleForDomainProfile| bool, standard är SANT |Statisk| Anger om brandväggsregel inte ska aktiveras för domänprofilen |
|SettingsX509StoreName| String, standard är L ”MY”| Dynamisk|X509 certifikatarkivet som används av infrastrukturresurser för skyddskonfigurationen |

### <a name="section-name-securityadminclientx509names"></a>Avsnittsnamn: Säkerhet/AdminClientX509Names
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standard är ingen|Dynamisk| |

### <a name="section-name-securityclientx509names"></a>Avsnittsnamn: Säkerhet/ClientX509Names
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standard är ingen|Dynamisk| |

### <a name="section-name-securityclusterx509names"></a>Avsnittsnamn: Säkerhet/ClusterX509Names
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standard är ingen|Dynamisk| |

### <a name="section-name-securityserverx509names"></a>Avsnittsnamn: Säkerhet/ServerX509Names
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standard är ingen|Dynamisk| |

### <a name="section-name-securityclientcertificateissuerstores"></a>Avsnittsnamn: Säkerhet/ClientCertificateIssuerStores
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standard är ingen |Dynamisk|X509 utfärdaren certifikatarkiv för klientcertifikat; Name = clientIssuerCN; Värde = kommaavgränsad lista över lager |

### <a name="section-name-securityclustercertificateissuerstores"></a>Avsnittsnamn: Säkerhet/ClusterCertificateIssuerStores
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standard är ingen |Dynamisk|X509 utfärdaren certifikatarkiv för klustercertifikat; Name = clusterIssuerCN; Värde = kommaavgränsad lista över lager |

### <a name="section-name-securityservercertificateissuerstores"></a>Avsnittsnamn: Säkerhet/ServerCertificateIssuerStores
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standard är ingen |Dynamisk|X509 utfärdaren certifikatarkiv för servercertifikat; Name = serverIssuerCN; Värde = kommaavgränsad lista över lager |

### <a name="section-name-securityclientaccess"></a>Avsnittsnamn: Säkerhet/ClientAccess
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| CreateName |String, standard är ”Admin” |Dynamisk|Konfiguration för skapande av namngivnings-URI. |
| DeleteName |String, standard är ”Admin” |Dynamisk|Säkerhetskonfiguration för borttagning av namngivnings-URI. |
| PropertyWriteBatch |String, standard är ”Admin” |Dynamisk|Säkerhetsinställningar för Naming egenskapen skrivåtgärder. |
| CreateService |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för att skapa tjänster. |
| CreateServiceFromTemplate |String, standard är ”Admin” |Dynamisk|Säkerhetskonfiguration för att skapa från mall. |
| UpdateService |String, standard är ”Admin” |Dynamisk|Säkerhetskonfiguration för uppdateringar av tjänsten. |
| DeleteService  |String, standard är ”Admin” |Dynamisk|Säkerhetskonfiguration för borttagning av tjänsten. |
| ProvisionApplicationType |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för programmet typen etablering. |
| CreateApplication |String, standard är ”Admin” | Dynamisk|Konfiguration för skapande av programmet. |
| DeleteApplication |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för borttagning av programmet. |
| UpgradeApplication |String, standard är ”Admin” |Dynamisk| Under start eller att avbryta programuppgraderingar säkerhetskonfiguration. |
| RollbackApplicationUpgrade |String, standard är ”Admin” |Dynamisk| För att återställa programuppgraderingar säkerhetskonfiguration. |
| UnprovisionApplicationType |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för programmet typen avetablering. |
| MoveNextUpgradeDomain |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för återupptar programuppgraderingar med en explicit uppgradera domän. |
| ReportUpgradeHealth |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för återupptar programuppgraderingar med den aktuella uppgraderingen pågår. |
| ReportHealth |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för rapportering hälsa. |
| ProvisionFabric |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för MSI och/eller klusternamnresursen manifestet etablering. |
| UpgradeFabric |String, standard är ”Admin” |Dynamisk| För att starta klusteruppgradering säkerhetskonfiguration. |
| RollbackFabricUpgrade |String, standard är ”Admin” |Dynamisk| För att återställa klusteruppgradering säkerhetskonfiguration. |
| UnprovisionFabric |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för MSI och/eller klusternamnresursen manifestet avetablering. |
| MoveNextFabricUpgradeDomain |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för återupptar klusteruppgradering med en explicit uppgraderingen domän. |
| ReportFabricUpgradeHealth |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för återupptar klusteruppgradering med den aktuella uppgraderingen pågår. |
| StartInfrastructureTask |String, standard är ”Admin” | Dynamisk|Säkerhetskonfiguration för start av infrastrukturen. |
| FinishInfrastructureTask |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för färdigställa infrastruktur uppgifter. |
| ActivateNode |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för aktivering med en nod. |
| DeactivateNode |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för en nod. |
| DeactivateNodesBatch |String, standard är ”Admin” |Dynamisk| Konfiguration för flera noder. |
| RemoveNodeDeactivations |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för Återför inaktivering på flera noder. |
| GetNodeDeactivationStatus |String, standard är ”Admin” |Dynamisk| För att kontrollera status för inaktivering av säkerhetskonfiguration. |
| NodeStateRemoved |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för rapportering nodens tillstånd tas bort. |
| RecoverPartition |String, standard är ”Admin” | Dynamisk|Säkerhetskonfiguration för återställning av en partition. |
| RecoverPartitions |String, standard är ”Admin” | Dynamisk|Säkerhetskonfiguration för återställning av partitioner. |
| RecoverServicePartitions |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för att återskapa partitioner för tjänsten. |
| RecoverSystemPartitions |String, standard är ”Admin” |Dynamisk| För att återställa tjänsten systempartitionerna säkerhetskonfiguration. |
| ReportFault |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration som rapporterade fel. |
| InvokeInfrastructureCommand |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för kommandon för hantering av infrastruktur-aktivitet. |
| FileContent |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för avbildningen lagra klienten filöverföring (extern till klustret). |
| FileDownload |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för image store klienten filen download inleda (extern till klustret). |
| InternalList |String, standard är ”Admin” | Dynamisk|Säkerhetskonfiguration för avbildningen lagra filen listan klientåtgärden (intern). |
| Ta bort |String, standard är ”Admin” |Dynamisk| Säkerhetskonfigurationer för avbildningen lagra ta bort klientåtgärden. |
| Ladda upp |String, standard är ”Admin” | Dynamisk|Säkerhetskonfiguration för avbildningen lagra Överföringsåtgärden för klienten. |
| GetStagingLocation |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för avbildningen lagra klienten mellanlagring plats hämtning. |
| GetStoreLocation |String, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för avbildningen lagra klienten store plats hämtning. |
| NodeControl |String, standard är ”Admin” |Dynamisk| Konfiguration för att starta; Stoppa; och starta om noder. |
| CodePackageControl |String, standard är ”Admin” |Dynamisk| Konfiguration för att starta om koden paket. |
| UnreliableTransportControl |String, standard är ”Admin” |Dynamisk| Instabilt Transport för att lägga till och ta bort beteenden. |
| MoveReplicaControl |String, standard är ”Admin” | Dynamisk|Flytta replik. |
| PredeployPackageToNode |String, standard är ”Admin” |Dynamisk| Hjälp av noggrann api. |
| StartPartitionDataLoss |String, standard är ”Admin” |Dynamisk| Startar förlust av data på en partition. |
| StartPartitionQuorumLoss |String, standard är ”Admin” |Dynamisk| Startar förlorar kvorum på en partition. |
| StartPartitionRestart |String, standard är ”Admin” |Dynamisk| Startar samtidigt om vissa eller alla repliker för en partition. |
| CancelTestCommand |String, standard är ”Admin” |Dynamisk| Avbryter en specifik TestCommand - om det är aktiverade. |
| StartChaos |String, standard är ”Admin” |Dynamisk| Startar Chaos - om det inte redan har startats. |
| StopChaos |String, standard är ”Admin” |Dynamisk| Stoppar Chaos - om den har startats. |
| StartNodeTransition |String, standard är ”Admin” |Dynamisk| Konfiguration för att starta en nod övergång. |
| StartClusterConfigurationUpgrade |String, standard är ”Admin” |Dynamisk| Startar StartClusterConfigurationUpgrade på en partition. |
| GetUpgradesPendingApproval |String, standard är ”Admin” |Dynamisk| Startar GetUpgradesPendingApproval på en partition. |
| StartApprovedUpgrades |String, standard är ”Admin” |Dynamisk| Startar StartApprovedUpgrades på en partition. |
| Pinga |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för klienten ping. |
| Fråga |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för frågor. |
| NameExists |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Säkerhetskonfiguration namngivnings-URI befintliga kontrollerar. |
| EnumerateSubnames |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för namngivnings-URI-uppräkningen. |
| EnumerateProperties |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Säkerhetskonfiguration för namngivning av egenskapen uppräkningen. |
| PropertyReadBatch |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för Naming egenskapen läsåtgärder. |
| GetServiceDescription |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för lång avsökning tjänstmeddelanden och läsa beskrivningar. |
| ResolveService |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för matchning av klagomål-baserad tjänst. |
| ResolveNameOwner |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Säkerhetskonfiguration för att lösa namngivnings-URI-ägare. |
| ResolvePartition |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Säkerhetskonfiguration för att lösa systemtjänster. |
| ServiceNotifications |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för händelsebaserat tjänstmeddelanden. |
| PrefixResolveService |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för matchning av klagomål-baserad tjänst prefix. |
| GetUpgradeStatus |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för avsökning av uppgraderingsstatus för programmet. |
| GetFabricUpgradeStatus |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för avsökning av uppgraderingsstatus för klustret. |
| InvokeInfrastructureQuery |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Säkerhetskonfiguration för frågor infrastrukturen. |
| Visa lista |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Säkerhetskonfiguration för avbildningen lagra klientåtgärden filen lista. |
| ResetPartitionLoad |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för återställning för en failoverUnit. |
| ToggleVerboseServicePlacementHealthReporting | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för växla utförlig ServicePlacement HealthReporting. |
| GetPartitionDataLossProgress | sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Hämtar status för en api-anrop invoke data går förlorade. |
| GetPartitionQuorumLossProgress | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Hämtar status för ett invoke kvorum förlust api-anrop. |
| GetPartitionRestartProgress | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Hämtar status för en omstart partition api-anrop. |
| GetChaosReport | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Hämtar status för Chaos inom ett angivet tidsintervall. |
| GetNodeTransitionProgress | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för att hämta pågår på en nod övergång. |
| GetClusterConfigurationUpgradeStatus | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Startar GetClusterConfigurationUpgradeStatus på en partition. |
| GetClusterConfiguration | sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Startar GetClusterConfiguration på en partition. |
|CreateComposeDeployment|String, standard är L ”Admin”| Dynamisk|Skapar en Skriv-distribution som beskrivs genom att skriva filer |
|DeleteComposeDeployment|String, standard är L ”Admin”| Dynamisk|Tar bort Skriv-distribution |
|UpgradeComposeDeployment|String, standard är L ”Admin”| Dynamisk|Uppgraderar Skriv-distribution |
|ResolveSystemService|String, standard är L ”Admin\|\|användare”|Dynamisk| Konfiguration för att lösa systemtjänster |
|GetUpgradeOrchestrationServiceState|String, standard är L ”Admin”| Dynamisk|Startar GetUpgradeOrchestrationServiceState på en partition |
|SetUpgradeOrchestrationServiceState|String, standard är L ”Admin”| Dynamisk|Startar SetUpgradeOrchestrationServiceState på en partition |

### <a name="section-name-reconfigurationagent"></a>Avsnittsnamn: ReconfigurationAgent
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | Tid i sekunder är standardvärdet 900 |Dynamisk|Ange tidsintervall i sekunder. Den varaktighet som systemet ska vänta innan försöket avbryts värdar som har repliker som fastnat i Stäng under uppgradering av programmet.|
| ServiceApiHealthDuration | Tid i sekunder, standardinställningen är 30 minuter |Dynamisk| Ange tidsintervall i sekunder. ServiceApiHealthDuration definierar hur länge vi vänta på en API för tjänsten ska köras innan vi rapporterar den feltillstånd. |
| ServiceReconfigurationApiHealthDuration | Tid i sekunder, standardvärdet är 30 |Dynamisk| Ange tidsintervall i sekunder. ServiceReconfigurationApiHealthDuration definierar hur länge vi vänta på en API för tjänsten ska köras innan vi rapporterar feltillstånd. Detta gäller för API-anrop som kan påverka tillgängligheten.|
| PeriodicApiSlowTraceInterval | Tid i sekunder, standardvärdet är 5 minuter |Dynamisk| Ange tidsintervall i sekunder. PeriodicApiSlowTraceInterval definierar intervallet över vilket långsam API-anrop ska fångas av API-Övervakare. |
| NodeDeactivationMaxReplicaCloseDuration | Tid i sekunder är standardvärdet 900 |Dynamisk|Ange tidsintervall i sekunder. Den varaktighet som systemet ska vänta innan försöket avbryts värdar som har repliker som fastnat i Stäng under nodinaktiveringen. |
| FabricUpgradeMaxReplicaCloseDuration | Tid i sekunder är standardvärdet 900 |Dynamisk| Ange tidsintervall i sekunder. Den varaktighet som systemet ska vänta innan försöket avbryts värdar som har repliker som fastnat i Stäng under fabric-uppgraderingen. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, standard är Common::TimeSpan::FromSeconds(120)|Dynamisk|Ange tidsintervall i sekunder. Den varaktighet som systemet ska vänta innan försöket avbryts värdar som har repliker som fastnat i Stäng. Om det här värdet anges till 0 instrueras repliker inte att stänga.|
|ReplicaChangeRoleFailureRestartThreshold|Int, standardvärdet är 10|Dynamisk| Heltal. Ange antalet API-fel under primära befordran efter vilken åtgärd för automatisk lösning (replik omstart) kommer att tillämpas. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, standardvärdet är 2147483647|Dynamisk| Heltal. Ange antalet API-fel under primära befordran efter vilken varning hälsorapport aktiveras.|

### <a name="section-name-placementandloadbalancing"></a>Avsnittsnamn: PlacementAndLoadBalancing
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| TraceCRMReasons |Bool, standard är SANT |Dynamisk|Anger om du vill spåra orsakerna till CRM utfärdat förflyttningar operativa händelser-kanalen. |
| ValidatePlacementConstraint | Bool, standard är SANT |Dynamisk| Anger huruvida PlacementConstraint-uttrycket för en tjänst verifieras när en tjänst ServiceDescription uppdateras. |
| PlacementConstraintValidationCacheSize | Int, standard är 10 000 |Dynamisk| Begränsar storleken på den tabell som används för snabb validering och cachelagring av placering av begränsningsuttryck. |
|VerboseHealthReportLimit | Int, standardvärdet är 20 | Dynamisk|Definierar hur många gånger som en replik har gå Ej placerade innan en varning om hälsotillstånd rapporteras för det (om utförlig hälsa reporting är aktiverad). |
|ConstraintViolationHealthReportLimit | Int, standardvärdet är 50 |Dynamisk| Definierar hur många gånger som villkoret brott mot replik måste vara beständigt olöst innan diagnostik utförs och hälsorapporter släpps. |
|DetailedConstraintViolationHealthReportLimit | Int, standardinställningen är 200 |Dynamisk| Definierar hur många gånger som villkoret brott mot replik måste beständigt olöst innan diagnostik utförs och detaljerade rapporter släpps hälsa. |
|DetailedVerboseHealthReportLimit | Int, standardinställningen är 200 | Dynamisk|Definierar hur många gånger som en replik av en Ej placerade måste beständigt Ej placerade innan detaljerad hälsorapporter släpps. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, standardvärdet är 20 | Dynamisk|Definierar hur många gånger i följd som utfärdats ResourceBalancer förflyttningar ignoreras innan diagnostik utförs och hälsovarningar släpps. Negativt: Inga varningar orsakat under det här villkoret. |
|DetailedNodeListLimit | Int, standardvärdet är 15 |Dynamisk| Definierar antalet noder per begränsning för att inkludera innan trunkering i rapporter om Ej placerade replik. |
|DetailedPartitionListLimit | Int, standardvärdet är 15 |Dynamisk| Definierar antalet partitioner per diagnostiska post för en begränsning att inkludera innan trunkering i diagnostik. |
|DetailedDiagnosticsInfoListLimit | Int, standardvärdet är 15 |Dynamisk| Anger antal diagnostikposter (med detaljerad information) per begränsning för att inkludera innan trunkering i diagnostik.|
|PLBRefreshGap | Tid i sekunder är standardvärdet 1 |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta mängden som måste passera innan PLB uppdaterar tillstånd igen. |
|MinPlacementInterval | Tid i sekunder är standardvärdet 1 |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta mängden som måste passera innan två på varandra följande placering Avrundar. |
|MinConstraintCheckInterval | Tid i sekunder är standardvärdet 1 |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta mängden som måste passera innan två på varandra följande begränsningen Kontrollera Avrundar. |
|MinLoadBalancingInterval | Tid i sekunder, standardvärdet är 5 |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta mängden som måste passera innan två på varandra följande belastningsutjämning Avrundar. |
|BalancingDelayAfterNodeDown | Tid i sekunder, standardvärdet är 120 |Dynamisk|Ange tidsintervall i sekunder. Starta inte NLB aktiviteter inom denna period efter en nod av händelsen. |
|BalancingDelayAfterNewNode | Tid i sekunder, standardvärdet är 120 |Dynamisk|Ange tidsintervall i sekunder. Starta inte NLB aktiviteter inom den tiden när du lägger till en ny nod. |
|ConstraintFixPartialDelayAfterNodeDown | Tid i sekunder, standardvärdet är 120 |Dynamisk| Ange tidsintervall i sekunder. Gör inte åtgärda FaultDomain och UpgradeDomain begränsningen överträdelser inom denna period efter en nod av händelsen. |
|ConstraintFixPartialDelayAfterNewNode | Tid i sekunder, standardvärdet är 120 |Dynamisk| Ange tidsintervall i sekunder. DDo inte åtgärda FaultDomain och UpgradeDomain begränsningen överträdelser inom den tiden när du lägger till en ny nod. |
|GlobalMovementThrottleThreshold | Uint, standardvärdet är 1000 |Dynamisk| Maximalt antal förflyttningar tillåten i fasen belastningsutjämning i det senaste intervall som anges av GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForPlacement | Uint, standardvärdet är 0 |Dynamisk| Maximalt antal förflyttningar tillåten i placering fas i det senaste intervall som anges av GlobalMovementThrottleCountingInterval.0 anger att ingen gräns.|
|GlobalMovementThrottleThresholdForBalancing | Uint, standardvärdet är 0 | Dynamisk|Maximalt antal förflyttningar tillåten i NLB fas i det senaste intervall som anges av GlobalMovementThrottleCountingInterval. 0 anger att ingen gräns. |
|GlobalMovementThrottleCountingInterval | Tid i sekunder, standard är 600 |Statisk| Ange tidsintervall i sekunder. Ange längden på den senaste intervall som du vill spåra per domän replik förflyttningar (används tillsammans med GlobalMovementThrottleThreshold). Kan anges till 0 för att ignorera global begränsning helt och hållet. |
|MovementPerPartitionThrottleThreshold | Uint, standardvärdet är 50 |Dynamisk| Inga NLB-relaterade förflyttning utförs för en partition om antalet NLB relaterade förflyttningar för repliker av den aktuella partitionen har nått eller överskridit MovementPerFailoverUnitThrottleThreshold i det senaste intervall som anges av MovementPerPartitionThrottleCountingInterval. |
|MovementPerPartitionThrottleCountingInterval | Tid i sekunder, standard är 600 |Statisk| Ange tidsintervall i sekunder. Ange längden på den senaste intervall som du vill spåra replik förflyttningar för varje partition (används tillsammans med MovementPerPartitionThrottleThreshold). |
|PlacementSearchTimeout | Tid i sekunder, standardvärdet är 0,5 |Dynamisk| Ange tidsintervall i sekunder. När du monterar tjänster. Sök efter högst i långt innan ett resultat returneras. |
|UseMoveCostReports | Bool, standard är FALSKT | Dynamisk|Instruerar LB att ignorera elementet kostnaden för funktionen bedömningsprofil; resulterande potentiellt stora antal flyttar för bättre belastningsutjämnade placering. |
|PreventTransientOvercommit | Bool, standard är FALSKT | Dynamisk|Anger bör PLB omedelbart förlita dig på resurser som kan frigöras genom initierade flyttar. Som standard. PLB initiera flytta ut och flytta i på samma nod som kan skapa tillfälliga överanstränga. Om den här parametern anges till true hindrar dessa typer av overcommits och på begäran defragmentering (aka placementWithMove) kommer att inaktiveras. |
|InBuildThrottlingEnabled | Bool, standard är FALSKT |Dynamisk| Bestämma om i build-begränsning är aktiverad. |
|InBuildThrottlingAssociatedMetric | sträng, standardvärdet är ”” |Statisk| Det associerade måttnamnet för denna begränsning. |
|InBuildThrottlingGlobalMaxValue | Int, standardvärdet är 0 |Dynamisk|Maxantalet i skapa-repliker tillåts globalt. |
|SwapPrimaryThrottlingEnabled | Bool, standard är FALSKT|Dynamisk| Bestämma om byte-primära begränsningen är aktiverad. |
|SwapPrimaryThrottlingAssociatedMetric | sträng, standardvärdet är ””|Statisk| Det associerade måttnamnet för denna begränsning. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, standardvärdet är 0 |Dynamisk| Maxantalet swap-primära repliker tillåts globalt. |
|PlacementConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för placering begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|PreferredLocationConstraintPriority | Int, standardvärdet är 2| Dynamisk|Anger prioriteten för önskade platsbegränsningen: 0: hårda; 1: mjuk; 2: optimering; negativt: ignorera |
|CapacityConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för kapacitet begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|AffinityConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för mappning mellan begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|FaultDomainConstraintPriority | Int, standardvärdet är 0 |Dynamisk| Anger prioriteten för domänbegränsningar fel: 0: hårda; 1: mjuk; negativt: ignorera. |
|UpgradeDomainConstraintPriority | Int, standardvärdet är 1| Dynamisk|Anger prioriteten för uppgraderingsdomänen begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|ScaleoutCountConstraintPriority | Int, standardvärdet är 0 |Dynamisk| Anger prioriteten för scaleout antal begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|ApplicationCapacityConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för kapacitet begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|MoveParentToFixAffinityViolation | Bool, standard är FALSKT |Dynamisk| Inställning som avgör om överordnade repliker kan flyttas till åtgärda tillhörighet begränsningar.|
|MoveExistingReplicaForPlacement | Bool, standard är SANT |Dynamisk|Inställningen som bestämmer om att flytta befintliga repliken under placeringen. |
|UseSeparateSecondaryLoad | Bool, standard är SANT | Dynamisk|Inställningen som bestämmer om Använd olika sekundära belastningen. |
|PlaceChildWithoutParent | Bool, standard är SANT | Dynamisk|Inställning som anger om underordnade tjänsten replik kan placeras om ingen överordnad replik är igång. |
|PartiallyPlaceServices | Bool, standard är SANT |Dynamisk| Anger om alla repliker för tjänsten i klustret kommer att placeras ”allt eller inget” angivna begränsad lämplig noder för för dessa.|
|InterruptBalancingForAllFailoverUnitUpdates | Bool, standard är FALSKT | Dynamisk|Anger om någon typ av failover unit update ska avbryta snabb eller långsam NLB kör. Med angivna ”false” NLB kör avbryts om FailoverUnit: är skapas eller tas bort; saknar repliker; ändra platsen för primära repliken eller ändrade antal repliker. NLB kör avbryts inte i andra fall - om FailoverUnit: har extra repliker; Ändra alla replik flagga; Ändra bara partition version eller annan. |
|GlobalMovementThrottleThresholdPercentage|Double, är standardvärdet 0|Dynamisk|Maximalt antal totala förflyttningar tillåten i faser för belastningsutjämning och placering (uttryckt i procent av totalt antal repliker i klustret) i det senaste intervall som anges av GlobalMovementThrottleCountingInterval. 0 anger att ingen gräns. Om både den här och GlobalMovementThrottleThreshold anges; sedan används mer konservativ gränsen.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double, är standardvärdet 0|Dynamisk|Maximalt antal förflyttningar tillåts i NLB fasen (uttryckt i procent av totalt antal repliker i PLB) i det senaste intervall som anges av GlobalMovementThrottleCountingInterval. 0 anger att ingen gräns. Om både den här och GlobalMovementThrottleThresholdForBalancing anges; sedan används mer konservativ gränsen.|
|AutoDetectAvailableResources|bool, standard är SANT|Statisk|Den här konfigurationen utlöser automatisk identifiering av tillgängliga resurser på noden (processor och minne) när den här konfigurationen är inställd på true - kommer att läsa verkliga kapacitet och korrigera dem om användaren anges felaktiga noden kapacitet eller inte har definierat dem alls om den här konfigurationen är inställd på false - kommer vi  spåra en varning som användaren angett felaktiga noden kapacitet; men vi kan inte korrigera dem. vilket innebär att användaren vill ha de kapacitet som angetts som > än verkligen har noden eller om de kapaciteter som är odefinierad; den antar obegränsad kapacitet |

### <a name="section-name-hosting"></a>Avsnittsnamn: värd
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| ServiceTypeRegistrationTimeout |Tid i sekunder, standard är 300 |Dynamisk|Högsta tillåtna tiden för ServiceType registreras med fabric |
| ServiceTypeDisableFailureThreshold |Heltal, standard är 1 |Dynamisk|Detta är tröskelvärdet för antal fel som efter vilket meddelas FailoverManager (FM) att inaktivera tjänsttypen på noden och prova en annan nod för placering. |
| ActivationRetryBackoffInterval |Tid i sekunder, standardvärdet är 5 |Dynamisk|Backoff intervall på varje Aktiveringsfel; På varje kontinuerlig Aktiveringsfel försöker systemet aktivering för upp till MaxActivationFailureCount. Återförsöksintervall på varje försök är en produkt från kontinuerlig Aktiveringsfel och inte aktiveringsintervallet. |
| ActivationMaxRetryInterval |Tid i sekunder, standard är 300 |Dynamisk|På varje kontinuerlig Aktiveringsfel försöker systemet aktivering för upp till ActivationMaxFailureCount. ActivationMaxRetryInterval Anger tidsintervall för vänta innan ett återförsök görs efter varje Aktiveringsfel |
| ActivationMaxFailureCount |Heltal, standard är 10 |Dynamisk|Antal gånger som system för nya försök misslyckats aktivering innan den ger upp |
|ActivationTimeout| TimeSpan, standard är Common::TimeSpan::FromSeconds(180)|Dynamisk| Ange tidsintervall i sekunder. Tidsgränsen för aktivering av programmet. Inaktivering och uppgradering. |
|ApplicationHostCloseTimeout| TimeSpan, standard är Common::TimeSpan::FromSeconds(120)|Dynamisk| Ange tidsintervall i sekunder. När Fabric avsluta identifieras i ett självsignerat aktiverat processer. FabricRuntime stänger alla repliker i användarens värdprocess (programvärd). Detta är tidsgränsen för åtgärden Stäng. |
|ApplicationUpgradeTimeout| TimeSpan, standard är Common::TimeSpan::FromSeconds(360)|Dynamisk| Ange tidsintervall i sekunder. Tidsgränsen för uppgradering av programmet. Om tidsgränsen är mindre än ”ActivationTimeout” distributionen misslyckas. |
|CreateFabricRuntimeTimeout|TimeSpan, standard är Common::TimeSpan::FromSeconds(120)|Dynamisk| Ange tidsintervall i sekunder. Timeout-värdet för synkronisering FabricCreateRuntime anropa |
|DeploymentMaxFailureCount|Int, standardvärdet är 20| Dynamisk|Programdistribution provas för DeploymentMaxFailureCount gånger innan distributionen av programmet på noden.| 
|DeploymentMaxRetryInterval| TimeSpan, standard är Common::TimeSpan::FromSeconds(3600)|Dynamisk| Ange tidsintervall i sekunder. Max återförsöksintervall för distributionen. På varje kontinuerlig fel beräknas återförsöksintervall som Min (DeploymentMaxRetryInterval; Kontinuerlig Felberäkning * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, standard är Common::TimeSpan::FromSeconds(10)|Dynamisk|Ange tidsintervall i sekunder. Inte intervall för distributionen misslyckades. På varje kontinuerlig distributionsfel försöker systemet distributionen för upp till MaxDeploymentFailureCount. Återförsöksintervall är en produkt för kontinuerlig distribution felet och distribution backoff intervall. |
|EnableActivateNoWindow| bool, standard är FALSKT|Dynamisk| Aktiverad process skapas i bakgrunden utan någon konsol. |
|EnableProcessDebugging|bool, standard är FALSKT|Dynamisk| Aktiverar starta programmet värdar under felsökare |
|EndpointProviderEnabled| bool, standard är FALSKT|Statisk| Möjliggör hantering av slutpunkten resurser av Fabric. Kräver att start- och slutdatum programmet portintervall i FabricNode. |
|FabricContainerAppsEnabled| bool, standard är FALSKT|Statisk| |
|FirewallPolicyEnabled|bool, standard är FALSKT|Statisk| Aktiverar öppna portar i brandväggen för Endpoint resurser med explicit portar som angetts i ServiceManifest |
|GetCodePackageActivationContextTimeout|TimeSpan, standard är Common::TimeSpan::FromSeconds(120)|Dynamisk|Ange tidsintervall i sekunder. Timeout-värdet för CodePackageActivationContext-anrop. Detta gäller inte för ad hoc-tjänster. |
|IPProviderEnabled|bool, standard är FALSKT|Statisk|Möjliggör hantering av IP-adresser. |
|NTLMAuthenticationEnabled|bool, standard är FALSKT|Statisk| Aktiverar stöd för att använda NTLM av koden paket som kör andra användare så att processerna mellan datorer kan kommunicera på ett säkert sätt. |
|NTLMAuthenticationPasswordSecret|SecureString, standard är Common::SecureString(L"")|Statisk|Har ett krypterat som används för att generera lösenordet för NTLM-användare. Måste anges om NTLMAuthenticationEnabled är true. Validerad av distributionen. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, standard är Common::TimeSpan::FromMinutes(3)|Dynamisk|Ange tidsintervall i sekunder. Miljö-specifika inställningar regelbundna intervall på vilken värd söker efter nya certifikat som ska användas för FileStoreService NTLM-konfiguration. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, standard är Common::TimeSpan::FromMinutes(4)|Dynamisk| Ange tidsintervall i sekunder. Tidsgränsen för att konfigurera NTLM-användare med vanliga namn för certifikatet. NTLM-användare krävs för FileStoreService resurser. |
|RegisterCodePackageHostTimeout|TimeSpan, standard är Common::TimeSpan::FromSeconds(120)|Dynamisk| Ange tidsintervall i sekunder. Timeout-värdet för anropet FabricRegisterCodePackageHost synkronisering. Detta gäller endast multi kod paketet programvärdar som FWP |
|RequestTimeout|TimeSpan, standard är Common::TimeSpan::FromSeconds(30)|Dynamisk| Ange tidsintervall i sekunder. Representerar tidsgränsen för kommunikation mellan användarens programvärd och Fabric-processen för olika värd relaterade åtgärder, till exempel factory registreringen. registrering av körningsmiljön. |
|RunAsPolicyEnabled| bool, standard är FALSKT|Statisk| Gör det möjligt att köra kod paket som lokala användare än den processen körs under vilka fabric. För att aktivera den här principen måste Fabric köras som SYSTEM eller användare som har SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, standard är Common::TimeSpan::FromSeconds(120)|Dynamisk|Ange tidsintervall i sekunder. Timeout-värdet för synkronisering Register (Stateless/Stateful) ServiceFactory-anrop |
|ServiceTypeDisableGraceInterval|TimeSpan, standard är Common::TimeSpan::FromSeconds(30)|Dynamisk|Ange tidsintervall i sekunder. Tidsintervall efter vilket tjänsttypen kan inaktiveras |
|EnableDockerHealthCheckIntegration|bool, standard är SANT|Statisk|Möjliggör integrering av docker-hälsokontroll händelser med hälsorapport för Service Fabric-system |

### <a name="section-name-federation"></a>Avsnittsnamn: Federation
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| LeaseDuration |Tid i sekunder, standardvärdet är 30 |Dynamisk|Varaktighet som ett lån pågår mellan en nod och sina grannar. |
| LeaseDurationAcrossFaultDomain |Tid i sekunder, standardvärdet är 30 |Dynamisk|Varaktighet som ett lån pågår mellan en nod och sina grannar över feldomäner. |

### <a name="section-name-clustermanager"></a>Avsnittsnamn: ClusterManager
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| UpgradeStatusPollInterval |Tid i sekunder, standardvärdet är 60 |Dynamisk|Frekvens för avsökning för uppgraderingsstatus för programmet. Det här värdet fastställer mängden uppdatering för alla GetApplicationUpgradeProgress-anrop |
| UpgradeHealthCheckInterval |Tid i sekunder, standardvärdet är 60 |Dynamisk|Frekvensen av hälsostatus kontrollerar under en uppgradering för övervakade program |
| FabricUpgradeStatusPollInterval |Tid i sekunder, standardvärdet är 60 |Dynamisk|Frekvens för avsökning för Fabric uppgraderingsstatus. Det här värdet fastställer mängden uppdatering för alla GetFabricUpgradeProgress-anrop |
| FabricUpgradeHealthCheckInterval |Tid i sekunder, standardvärdet är 60 |Dynamisk|Frekvensen av hälsostatus Kontrollera under en övervakad Fabric-uppgradering |
|InfrastructureTaskProcessingInterval | Tid i sekunder är standard 10 |Dynamisk|Ange tidsintervall i sekunder. Det intervall som används av aktiviteten infrastruktur för bearbetning av datorn. |
|TargetReplicaSetSize |Int, standard är 7 |Inte tillåten|TargetReplicaSetSize för ClusterManager. |
|MinReplicaSetSize |Int, standardinställningen är 3 |Inte tillåten|MinReplicaSetSize för ClusterManager. |
|ReplicaRestartWaitDuration |Tid i sekunder är standardvärdet (60,0 * 30)|Inte tillåten|Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för ClusterManager. |
|QuorumLossWaitDuration |Tid i sekunder är standardvärdet MaxValue |Inte tillåten| Ange tidsintervall i sekunder. QuorumLossWaitDuration för ClusterManager. |
|StandByReplicaKeepDuration | Tid i sekunder är standardvärdet (3600.0 * 2)|Inte tillåten|Ange tidsintervall i sekunder. StandByReplicaKeepDuration för ClusterManager. |
|PlacementConstraints | sträng, standardvärdet är ”” |Inte tillåten|PlacementConstraints för ClusterManager. |
|SkipRollbackUpdateDefaultService | Bool, standard är FALSKT |Dynamisk|CM hoppar över Återför uppdaterade standardtjänster under uppgraderingen återställningen av programmet. |
|EnableDefaultServicesUpgrade | Bool, standard är FALSKT |Dynamisk|Aktivera uppgradering standardtjänster under uppgradering av programmet. Standardtjänsterna skulle skrivas efter uppgraderingen. |
|InfrastructureTaskHealthCheckWaitDuration |Tid i sekunder, standardvärdet är 0|Dynamisk| Ange tidsintervall i sekunder. Mängden tid som ska förflyta innan du startar hälsokontroller när aktiviteten en infrastruktur för efterbearbetning. |
|InfrastructureTaskHealthCheckStableDuration | Tid i sekunder, standardvärdet är 0|Dynamisk| Ange tidsintervall i sekunder. Hur lång tid att se på varandra följande skickade hälsokontroller innan efter bearbetning av en infrastruktur-aktivitet har slutförts. Sett misslyckade hälsokontrollen återställs den här timern. |
|InfrastructureTaskHealthCheckRetryTimeout | Tid i sekunder, standardvärdet är 60 |Dynamisk|Ange tidsintervall i sekunder. Det gick inte att hälsokontroller när aktiviteten en infrastruktur för efterbearbetning mängden tid för försöker igen. Sett skickade hälsokontrollen återställs den här timern. |
|ImageBuilderTimeoutBuffer |Tid i sekunder, standardinställningen är 3 |Dynamisk|Ange tidsintervall i sekunder. Hur lång tid för Image Builder specifika timeout-fel att återgå till klienten. Om den här bufferten är för liten. sedan klienten timeout innan servern och hämtar en allmän timeout-fel. |
|MinOperationTimeout | Tid i sekunder, standardvärdet är 60 |Dynamisk|Ange tidsintervall i sekunder. Den minsta globala tidsgränsen för internt bearbetning vid ClusterManager. |
|MaxOperationTimeout |Tid i sekunder är standardvärdet MaxValue |Dynamisk| Ange tidsintervall i sekunder. Den maximala globala tidsgränsen för internt bearbetning vid ClusterManager. |
|MaxTimeoutRetryBuffer | Tid i sekunder, standard är 600 |Dynamisk|Ange tidsintervall i sekunder. Tidsgräns för maximal åtgärd när du startar om internt på grund av timeout är <Original Time out>  +  <MaxTimeoutRetryBuffer>. Ytterligare timeout har lagts till i steg om MinOperationTimeout. |
|MaxCommunicationTimeout |Tid i sekunder, standard är 600 |Dynamisk|Ange tidsintervall i sekunder. Maximal tidsgräns för intern kommunikation mellan andra system och ClusterManager tjänster (d.v.s.; Namntjänst; Failover Manager och osv.). Det här bör vara mindre än globala MaxOperationTimeout (eftersom det kan finnas flera kommunikation mellan komponenter för varje klientåtgärd). |
|MaxDataMigrationTimeout |Tid i sekunder, standard är 600 |Dynamisk|Ange tidsintervall i sekunder. Maximal tidsgräns för data migrering återställningsåtgärder när en Fabric-uppgraderingen har ägt rum. |
|MaxOperationRetryDelay |Tid i sekunder, standardvärdet är 5|Dynamisk| Ange tidsintervall i sekunder. Maximal fördröjning för interna återförsök när fel uppstår. |
|ReplicaSetCheckTimeoutRollbackOverride |Tid i sekunder är standardvärdet 1200 |Dynamisk| Ange tidsintervall i sekunder. Om ReplicaSetCheckTimeout är det maximala värdet för DWORD; sedan är den åsidosättas med värdet för den här konfigurationen för återställning. Det värde som används för sammanslagning vanlig åsidosätts aldrig. |

### <a name="section-name-defragmentationemptynodedistributionpolicy"></a>Avsnittsnamn: DefragmentationEmptyNodeDistributionPolicy
| **Parametern** | **Tillåtna värden** |**Princip för versionsuppgradering**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, standard är ingen|Dynamisk|Anger principen defragmenteringen följer vid tömma noder. För ett visst mått anger 0 att SA ska försöka defragmentera noder jämnt över UDs och FDs; 1 anger bara att noder måste defragmenteras |

### <a name="section-name-defragmentationmetrics"></a>Avsnittsnamn: DefragmentationMetrics
| **Parametern** | **Tillåtna värden** |**Princip för versionsuppgradering**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, standard är ingen|Dynamisk|Anger uppsättningen mått som ska användas för defragmentering och inte för belastningsutjämning. |

### <a name="section-name-defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>Avsnittsnamn: DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametern** | **Tillåtna värden** |**Princip för versionsuppgradering**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standard är ingen|Dynamisk|Anger antalet noder – kostnadsfria som behövs för att överväga klustret defragmenteras genom att ange antingen procent i intervallet [0.0-1.0) eller antalet tom noder som tal > = 1.0 |

### <a name="section-name-dnsservice"></a>Avsnittsnamn: DnsService
| **Parametern** | **Tillåtna värden** |**Princip för versionsuppgradering**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|IsEnabled|bool, standard är FALSKT|Statisk| |
|InstanceCount|int, standard är-1|Statisk|  |

### <a name="section-name-metricactivitythresholds"></a>Avsnittsnamn: MetricActivityThresholds
| **Parametern** | **Tillåtna värden** |**Princip för versionsuppgradering**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, standard är ingen|Dynamisk|Anger uppsättningen MetricActivityThresholds för mätvärdena i klustret. Belastningsutjämning fungerar om maxNodeLoad är större än MetricActivityThresholds. Defragmenterar mått för den definierar mängden belastningen är lika med eller lägre än som Service Fabric beaktas noden tom |

### <a name="section-name-metricbalancingthresholds"></a>Avsnittsnamn: MetricBalancingThresholds
| **Parametern** | **Tillåtna värden** |**Princip för versionsuppgradering**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standard är ingen|Dynamisk|Anger uppsättningen MetricBalancingThresholds för mätvärdena i klustret. Belastningsutjämning fungerar om maxNodeLoad/minNodeLoad är större än MetricBalancingThresholds. Defragmentering fungerar om maxNodeLoad/minNodeLoad i minst en FD eller UD är mindre än MetricBalancingThresholds. |

### <a name="section-name-nodebufferpercentage"></a>Avsnittsnamn: NodeBufferPercentage
| **Parametern** | **Tillåtna värden** |**Princip för versionsuppgradering**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standard är ingen|Dynamisk|Noden nätverkskapaciteten i procent per måttnamnet; används som en buffert för att skydda vissa ledigt plats på en nod för växling vid fel fall. |

### <a name="section-name-replication"></a>Avsnittsnamn: replikering
| **Parametern** | **Tillåtna värden** | **Princip för versionsuppgradering**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|MaxCopyQueueSize|Uint, standard är 1024|Statisk|Detta är högsta värdet definierar den ursprungliga storleken för kön som underhåller replikeringsåtgärder.  Observera att det måste vara delbart med 2.  Om körningsfel kön storlek i den här åtgärden kommer att begränsas mellan de primära och sekundära replikatörer.|
|BatchAcknowledgementInterval|TimeSpan, standard är Common::TimeSpan::FromMilliseconds(15)|Statisk|Ange tidsintervall i sekunder. Anger hur lång tid som replikatorn väntar när du har fått en åtgärd innan en bekräftelse skickas tillbaka. Andra åtgärder som togs emot under den här tiden har sina bekräftelser skickas tillbaka i ett enda meddelande -> minska nätverkstrafiken men potentiellt minskar genomflödet av replikatorn.|
|MaxReplicationMessageSize|Uint, standard är 52428800|Statisk|Maximal meddelandestorlek på replikeringsåtgärder. Standardvärdet är 50MB.|
|ReplicatorAddress|String, standard är L ”localhost:0”|Statisk|Slutpunkt i form av en sträng-'IP:Port' som används av Windows Fabric replikatorn upprätta anslutningar till andra repliker för att kunna skicka och ta emot åtgärder.|
|ReplicatorListenAddress|String, standard är L ”localhost:0”|Statisk|Slutpunkt i form av en sträng-'IP:Port' som används av Windows Fabric replikatorn tar emot åtgärder från andra repliker.|
|ReplicatorPublishAddress|String, standard är L ”localhost:0”|Statisk|Slutpunkt i form av en sträng-'IP:Port' som används av Windows Fabric replikatorn för att skicka-åtgärder till andra repliker.|
|MaxPrimaryReplicationQueueSize|Uint, standard är 1024|Statisk|Detta är det maximala antalet åtgärder som kan finnas i kön primär replikering. Observera att det måste vara delbart med 2.|
|MaxPrimaryReplicationQueueMemorySize|Uint, standardvärdet är 0|Statisk|Detta är det maximala värdet för den primära Replikeringskön i byte.|
|MaxSecondaryReplicationQueueSize|uint, standard är 2048|Statisk|Detta är det maximala antalet åtgärder som kan finnas i sekundära Replikeringskön. Observera att det måste vara delbart med 2.|
|MaxSecondaryReplicationQueueMemorySize|Uint, standardvärdet är 0|Statisk|Detta är det maximala värdet för den sekundära Replikeringskön i byte.|
|QueueHealthMonitoringInterval|TimeSpan, standard är Common::TimeSpan::FromSeconds(30)|Statisk|Ange tidsintervall i sekunder. Det här värdet anger hur lång tid som används av replikatorn för att övervaka varning hälsa händelser i replikeringsköer för åtgärden. Värdet '0' inaktiverar övervakning av hälsotillstånd |
|QueueHealthWarningAtUsagePercent|uint, standard är 80|Statisk|Det här värdet avgör hur replikering kön (i procent) efter vilken vi rapporterar varning om hög användning. Vi kan göra det efter ett QueueHealthMonitoringInterval grace tidsintervall. Om kön användning understiger procentsatsen i den grace-period|
|RetryInterval|TimeSpan, standard är Common::TimeSpan::FromSeconds(5)|Statisk|Ange tidsintervall i sekunder. När en åtgärd går förlorad eller avvisa den här timern avgör hur ofta replikatorn försöker skicka igen.|

### <a name="section-name-transport"></a>Avsnittsnamn: Transport
| **Parametern** | **Tillåtna värden** |**Princip för versionsuppgradering** |**Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ResolveOption|sträng, standard är L ”okänt”|Statisk|Anger hur FQDN är löst.  Giltiga värden är ”ospecificerade/ipv4/ipv6-”. |
|ConnectionOpenTimeout|TimeSpan, standard är Common::TimeSpan::FromSeconds(60)|Statisk|Ange tidsintervall i sekunder. Timeout för anslutning inställningen på både inkommande och accepterar sida (inklusive säkerhetsförhandlingen i säkert läge) |

## <a name="next-steps"></a>Nästa steg
Läs artiklarna mer information om hantering av kluster:

[Lägga till, rulla över, ta bort certifikat från ditt Azure-kluster ](service-fabric-cluster-security-update-certs-azure.md) 

