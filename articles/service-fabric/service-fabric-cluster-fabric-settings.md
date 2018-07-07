---
title: Ändra inställningarna för Azure Service Fabric-kluster | Microsoft Docs
description: Den här artikeln beskriver fabric-inställningar och fabric uppgradera principerna som du kan anpassa.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/27/2018
ms.author: aljo
ms.openlocfilehash: 499c7182fba9d8efeebfb22e22a692d431dcb7ac
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888661"
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Anpassa Service Fabric-klusterinställningar och uppgraderingsprincip för infrastruktur
Det här dokumentet får du lära dig att anpassa olika infrastrukturinställningarna och infrastrukturen uppgraderingsprincip för Service Fabric-klustret. Du kan anpassa dem via den [Azure-portalen](https://portal.azure.com) eller med en Azure Resource Manager-mall.

> [!NOTE]
> Alla inställningar är inte tillgänglig i portalen. Om en inställning som anges nedan inte är tillgänglig via portalen anpassar du den med en Azure Resource Manager-mall.
> 

## <a name="description-of-the-different-upgrade-policies"></a>Beskrivning av olika uppgradera principer

- **Dynamisk** – ändringar i en dynamisk konfiguration inte orsakar några omstart av Service Fabric-processer eller din tjänst värdprocesser. 
- **Statisk** – ändringar i en statisk konfiguration leder till Service Fabric-noden startas om för att kunna använda ändringen. Tjänster på noderna kommer att startas om.
- **NotAllowed** – de här inställningarna kan inte ändras. Om du ändrar dessa inställningar kräver att klustret förstöras och ett nytt kluster skapas. 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Anpassa inställningar för klustret med hjälp av Resource Manager-mallar
Stegen nedan visar hur du lägger till en ny inställning *MaxDiskQuotaInMB* till den *diagnostik* avsnittet.

1. Gå till https://resources.azure.com
2. Gå till din prenumeration genom att expandera **prenumerationer** -> **\<din prenumeration >** -> **resourceGroups**  ->   **\<Din resursgrupp >** -> **providers** -> **Microsoft.ServiceFabric**  ->  **kluster** -> **\<Your klustrets namn >**
3. I övre högra hörnet väljer **Läs/Skriv.**
4. Välj **redigera** och uppdatera den `fabricSettings` JSON-element och Lägg till ett nytt element:

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

Följande är en lista över Fabric inställningar som du kan anpassa, ordnade efter avsnittet.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|sträng, standard är L ”None”|Statisk| ApplicationCertificateValidationPolicy: Ingen: inte verifiera servercertifikat; lyckas begäran. ServiceCertificateThumbprints: Avse config ServiceCertificateThumbprints kommaavgränsad lista över tumavtrycken för de fjärranslutna certifikat som kan lita på den omvända proxyn. ServiceCommonNameAndIssuer: Avse config ServiceCommonNameAndIssuer för ämne namn och tumavtrycket för fjärr-certifikat som kan lita på den omvända proxyn. |
|BodyChunkSize |Uint, standardvärdet är 16384 |Dynamisk| Anger storleken på för segmentet i byte som används för att läsa innehållet. |
|CrlCheckingFlag|uint, standard är 0x40000000 |Dynamisk| Flaggor för program/tjänst verifiering av certifikatkedjan; t.ex. CRL-kontroll 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY inställningen till 0 inaktiverar CRL kontrollerar fullständig lista över värden som stöds är dokumenterats av dwFlags av CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Tid i sekunder. Standardvärdet är 120 |Dynamisk|Ange tidsintervall i sekunder.  Ger timeout för standard-begäranden för http-begäranden som bearbetas i app-gateway http. |
|ForwardClientCertificate|bool, standard är FALSKT|Dynamisk| |
|GatewayAuthCredentialType |sträng, standard är ”ingen” |Statisk| Anger de autentiseringsuppgifter du använder på http-app gateway-slutpunkten giltiga värden är ”ingen / X 509. |
|GatewayX509CertificateFindType |sträng, standard är ”FindByThumbprint” |Dynamisk| Anger hur du söker efter certifikat i arkivet som anges av GatewayX509CertificateStoreName stöds värde: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | sträng, standardvärdet är ”” |Dynamisk| Sök filtervärdet som används för att hitta http app gateway-certifikatet. Det här certifikatet har konfigurerats på https-slutpunkten och kan också användas för att verifiera identiteten för appen vid behov av tjänsterna. Först; söks FindValue och om som inte finns; FindValueSecondary slås upp. |
|GatewayX509CertificateFindValueSecondary | sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta http app gateway-certifikatet. Det här certifikatet har konfigurerats på https-slutpunkten och kan också användas för att verifiera identiteten för appen vid behov av tjänsterna. Först; söks FindValue och om som inte finns; FindValueSecondary slås upp.|
|GatewayX509CertificateStoreName |sträng, standardvärdet är ”My” |Dynamisk| Namnet på X.509-certifikatarkiv som innehåller certifikat för HTTP-app gateway. |
|HttpRequestConnectTimeout|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(5)|Dynamisk|Ange tidsintervall i sekunder.  Ger connect timeout-värdet för http-begäranden som skickas från app-gateway http.  |
|IgnoreCrlOfflineError|bool, standard är SANT|Dynamisk|Om du vill ignorera CRL offline fel för program/tjänst certifikatverifiering. |
|IsEnabled |Bool, standard är FALSKT |Statisk| Aktiverar eller inaktiverar HttpApplicationGateway. HttpApplicationGateway är inaktiverad som standard och den här konfigurationen måste anges för att aktivera den. |
|NumberOfParallelOperations | Uint, standardvärdet är 5000 |Statisk|Antalet läsningar att publicera till kön för http-server. Detta styr antalet samtidiga begäranden som kan betjänas av HttpGateway. |
|RemoveServiceResponseHeaders|sträng, standard är L ”datum. Server ”|Statisk|Semikolonseparerade / kommaavgränsad lista över svarshuvuden som tas bort från tjänstsvaret; innan den vidarebefordrar det till klienten. Om detta är inställt på tom sträng; Skicka alla rubriker som returneras av tjänsten som – är. dvs Skriv inte över datum och Server |
|ResolveServiceBackoffInterval |Tid i sekunder, standardvärdet är 5 |Dynamisk|Ange tidsintervall i sekunder.  Ger lösa backoff standardintervallet innan du försöker utföra en tjänståtgärd. |
|SecureOnlyMode|bool, standard är FALSKT|Dynamisk| SecureOnlyMode: true: omvänd Proxy endast vidarebefordrar till tjänster som publicerar säker slutpunkter. FALSKT: omvänd Proxy kan vidarebefordra begäranden till secure/icke-säker slutpunkter.  |
|ServiceCertificateThumbprints|sträng, standard är L ””|Dynamisk| |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standardvärdet är ingen|Dynamisk|  |

## <a name="backuprestoreservice"></a>BackupRestoreService
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, standardvärdet är 0|Statisk|MinReplicaSetSize för BackupRestoreService |
|PlacementConstraints|wstring, standard är L ””|Statisk| PlacementConstraints för BackupRestore-tjänsten |
|SecretEncryptionCertThumbprint|wstring, standard är L ””|Dynamisk|Kryptering med hemliga X509 certifikatets tumavtryck |
|SecretEncryptionCertX509StoreName|wstring, standard är L ”My”|  Dynamisk|    Detta anger certifikatet som ska användas för kryptering och dekryptering av inloggningsuppgifter namn för X.509-certifikatarkiv som används för att kryptera dekrypteringen store-autentiseringsuppgifter som används av tjänsten Backup Restore |
|TargetReplicaSetSize|int, standardvärdet är 0|Statisk| TargetReplicaSetSize för BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|EnableDefaultServicesUpgrade | Bool, standard är FALSKT |Dynamisk|Aktivera uppgradering standardtjänster under uppgradering av programmet. Kommer att skrivas över standardtjänstbeskrivningar efter uppgraderingen. |
|FabricUpgradeHealthCheckInterval |Tid i sekunder, standardvärdet är 60 |Dynamisk|Frekvensen för hälsostatus Kontrollera vid en uppgradering av övervakade Fabric |
|FabricUpgradeStatusPollInterval |Tid i sekunder, standardvärdet är 60 |Dynamisk|Frekvensen för avsökning för Fabric uppgradera status. Det här värdet fastställer mängden uppdatering för ett GetFabricUpgradeProgress-anrop |
|ImageBuilderTimeoutBuffer |Tid i sekunder, standard är 3 |Dynamisk|Ange tidsintervall i sekunder. Hur lång tid för Image Builder specifika timeout-fel att återgå till klienten. Om den här bufferten är för liten. sedan klienten når sin tidsgräns innan servern och hämtar en allmän timeout-fel. |
|InfrastructureTaskHealthCheckRetryTimeout | Tid i sekunder, standardvärdet är 60 |Dynamisk|Ange tidsintervall i sekunder. Det gick inte att hälsokontroller vid bearbetning av en infrastruktur-uppgift efter hur lång tid att spendera försöker igen. Får en skickade hälsokontroll återställer timern. |
|InfrastructureTaskHealthCheckStableDuration | Tid i sekunder, standardvärdet är 0|Dynamisk| Ange tidsintervall i sekunder. Hur lång tid att Observera efterföljande skickade hälsokontroller innan efter bearbetning av en infrastruktur-uppgift har slutförts. Den här timern återställs om du får en misslyckad hälsokontroll. |
|InfrastructureTaskHealthCheckWaitDuration |Tid i sekunder, standardvärdet är 0|Dynamisk| Ange tidsintervall i sekunder. Hur lång tid att vänta innan du startar hälsokontroller efter efterbearbeta för ändring av en infrastruktur-uppgift. |
|InfrastructureTaskProcessingInterval | Tid i sekunder, standarden är 10 |Dynamisk|Ange tidsintervall i sekunder. Det intervall som används av aktiviteten infrastruktur för bearbetning av datorn. |
|MaxCommunicationTimeout |Tid i sekunder, standardvärdet är 600 |Dynamisk|Ange tidsintervall i sekunder. Den maximala tidsgränsen för intern kommunikation mellan ClusterManager och andra tjänster (d.v.s.; Namntjänst; Redundanshanteraren och osv.). Den här timeouten bör vara mindre än globala MaxOperationTimeout (eftersom det kan finnas flera kommunikation mellan komponenter för varje klientåtgärd). |
|MaxDataMigrationTimeout |Tid i sekunder, standardvärdet är 600 |Dynamisk|Ange tidsintervall i sekunder. Den maximala tidsgränsen för data migration-återställningsåtgärder efter en uppgradering av Fabric har ägt rum. |
|MaxOperationRetryDelay |Tid i sekunder, standardvärdet är 5|Dynamisk| Ange tidsintervall i sekunder. Maximal fördröjning för interna återförsök när fel uppstår. |
|MaxOperationTimeout |Tid i sekunder, är standardvärdet MaxValue |Dynamisk| Ange tidsintervall i sekunder. Den maximala globala tidsgränsen för internt bearbetningsåtgärder på ClusterManager. |
|MaxTimeoutRetryBuffer | Tid i sekunder, standardvärdet är 600 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för maximal åtgärden när återförsök internt på grund av timeout-fel är <Original Time out>  +  <MaxTimeoutRetryBuffer>. Ytterligare tidsgräns har lagts till i steg om MinOperationTimeout. |
|MinOperationTimeout | Tid i sekunder, standardvärdet är 60 |Dynamisk|Ange tidsintervall i sekunder. Den minsta globala tidsgränsen för internt bearbetningsåtgärder på ClusterManager. |
|MinReplicaSetSize |Int, standard är 3 |Inte tillåten|MinReplicaSetSize för ClusterManager. |
|PlacementConstraints | sträng, standardvärdet är ”” |Inte tillåten|PlacementConstraints för ClusterManager. |
|QuorumLossWaitDuration |Tid i sekunder, är standardvärdet MaxValue |Inte tillåten| Ange tidsintervall i sekunder. QuorumLossWaitDuration för ClusterManager. |
|ReplicaRestartWaitDuration |Tid i sekunder, är standardvärdet (60,0 * 30)|Inte tillåten|Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för ClusterManager. |
|Detta är att starta om servern. |Detta är att aktivera fabric automatiska uppdateringar via Windows Update. |Dynamisk| Ange tidsintervall i sekunder. Detta är att aktivera grundläggande uppdatering för server. StartTimeout |
|Timeout för fabricactivationmanager start. | Bool, standard är FALSKT |Dynamisk|StopTimeout |
|Tidsgränsen för värdtjänsten-aktivering. Inaktivering och uppgradering. | FabricNode|Inte tillåten|Ange tidsintervall i sekunder. ClientAuthX509FindType |
|TargetReplicaSetSize |Anger hur du söker efter certifikat i arkivet som anges av ClientAuthX509StoreName stöds värde: FindByThumbprint; FindBySubjectName. |Inte tillåten|Sök filtervärdet som används för att hitta certifikatet för standard-administratörsroll FabricClient. |
|Namnet på det X.509-certifikatarkivet som innehåller certifikatet för standard-administratörsroll FabricClient. |Tid i sekunder, standardvärdet är 60 |Dynamisk|ClusterX509FindType |
|UpgradeStatusPollInterval |Tid i sekunder, standardvärdet är 60 |Dynamisk|Anger hur du söker efter klustercertifikat i arkivet som anges av ClusterX509StoreName stöds värdena: ”FindByThumbprint”; ”FindBySubjectName” med ”FindBySubjectName”; När det finns flera matchningar; Det sista upphör används. ClusterX509FindValue |

## <a name="common"></a>Common
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|Sök filtervärdet som används för att hitta klustercertifikat. |ClusterX509FindValueSecondary |Dynamisk|Ange tidsintervall i sekunder. Prestanda Övervakningsintervall. Inställningen till 0 eller negativt värde inaktiverar övervakning. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parametern** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, standardvärdet är ingen|Dynamisk|Anger principen defragmenteringen följer när du tömmer noderna. För ett visst mått anger 0 att SF bör försöka defragmentera noder jämnt mellan Uppdateringsdomäner och Feldomäner; 1 anger bara att noderna måste defragmenteras |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parametern** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, standardvärdet är ingen|Dynamisk|Anger uppsättningen mått som ska användas för defragmentering och inte för Utjämning av nätverksbelastning. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametern** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standardvärdet är ingen|Dynamisk|Anger hur många kostnadsfria noder som behövs för att tänka på klustret defragmenteras genom att ange antingen procent i intervallet [0.0-1.0) eller antalet noder som tom as-nummer > = 1.0 |

## <a name="diagnostics"></a>Diagnostik
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, standard är SANT | Dynamisk |Personifiering krävs om huruvida när komma åt diagnostisk lagrar åt programmet. |
|AppEtwTraceDeletionAgeInDays |Int, standard är 3 | Dynamisk |Antal dagar efter vilken vi ta bort gamla ETL-filer som innehåller programmet ETW-spårning. |
|ApplicationLogsFormatVersion |Int, standardvärdet är 0 | Dynamisk |Version för program loggar format. Värden som stöds är 0 och 1. Version 1 innehåller flera fält från händelseposten ETW än version 0. |
|ClusterId |Sträng | Dynamisk |Unikt id för klustret. Detta skapas när klustret har skapats. |
|ConsumerInstances |Sträng | Dynamisk |Lista över DCA konsumentinstanserna. |
|DiskFullSafetySpaceInMB |Int, standardvärdet är 1024 | Dynamisk |Återstående diskutrymme i MB för att skydda mot av DCA. |
|EnableCircularTraceSession |Bool, standard är FALSKT | Statisk |Flagga som anger om cirkulär spårningssessioner ska användas. |
|Konfigurationsparametern |Bool, standard är SANT | Dynamisk |Detta kommer att aktivera eller inaktivera telemetri. |
|MaxDiskQuotaInMB |Int, standardvärdet är 65536 | Dynamisk |Diskkvot i MB för Windows Fabric-loggfiler. |
|ProducerInstances |Sträng | Dynamisk |Lista över DCA producent instanser. |

## <a name="dnsservice"></a>DnsService
| **Parametern** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|IsEnabled|bool, standard är FALSKT|Statisk| |
|InstanceCount|int, standard är-1|Statisk|  |

## <a name="fabricclient"></a>FabricClient
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Tid i sekunder, är standardvärdet 2 |Dynamisk|Ange tidsintervall i sekunder. Anslutningen timeout-intervall för varje gång klienten försöker öppna en anslutning till gatewayen.|
|HealthOperationTimeout |Tid i sekunder, standardvärdet är 120 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för en rapport-meddelanden som skickas till Health Manager. |
|HealthReportRetrySendInterval |Tid i sekunder, standardvärdet är 30 |Dynamisk|Ange tidsintervall i sekunder. Intervallet då reporting komponenten igen skickar ackumulerade hälsorapporter till Health Manager. |
|HealthReportSendInterval |Tid i sekunder, standardvärdet är 30 |Dynamisk|Ange tidsintervall i sekunder. Intervallet då reporting komponenten skickar ackumulerade hälsorapporter till Health Manager. |
|KeepAliveIntervalInSeconds |Int, standardvärdet är 20 |Statisk|Intervallet då FabricClient-transport skickar keep-alive-meddelanden till gatewayen. För 0. keepAlive är inaktiverad. Måste vara ett positivt värde. |
|MaxFileSenderThreads |Uint, standarden är 10 |Statisk|Max antal filer som överförs parallellt. |
|NodeAddresses |sträng, standardvärdet är ”” |Statisk|En uppsättning adresser (anslutningssträngar) på olika noder som kan användas för att kommunicera med Namngivningstjänsten. Först ansluter klienten att välja en av adresserna slumpmässigt. Om mer än en anslutningssträng har angetts och en anslutning misslyckas på grund av en kommunikation eller tidsgränsfel; Klienten växlar du använder nästa adress sekventiellt. Se tjänstadressen Naming försök avsnitt finns information om återförsök semantik. |
|PartitionLocationCacheLimit |Int, standard är 100 000 |Statisk|Antalet partitioner som cachelagras för tjänst-lösning (värdet 0 för ingen begränsning). |
|RetryBackoffInterval |Tid i sekunder, standard är 3 |Dynamisk|Ange tidsintervall i sekunder. Backoffintervall innan du försöker igen. |
|ServiceChangePollInterval |Tid i sekunder, standardvärdet är 120 |Dynamisk|Ange tidsintervall i sekunder. Intervallet mellan på varandra följande enkäter för tjänsten ändras från klienten till gatewayen för registrerade tjänsten ändra meddelanden återanrop. |

## <a name="fabrichost"></a>FabricHost
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, standarden är 10 |Dynamisk|Det här är det maximala antalet som systemet kommer misslyckade försök aktivera igen innan den ger upp. |
|ActivationMaxRetryInterval |Tid i sekunder, är standardvärdet 300 |Dynamisk|Ange tidsintervall i sekunder. Max återförsöksintervallet för aktivering. Vid varje kontinuerlig fel beräknas återförsöksintervallet som Min (ActivationMaxRetryInterval; Kontinuerlig Felberäkning * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tid i sekunder, standardvärdet är 5 |Dynamisk|Ange tidsintervall i sekunder. Backoff intervall på varje Aktiveringsfel; på varje kontinuerlig Aktiveringsfel systemet försöker aktivering för upp till MaxActivationFailureCount. Återförsöksintervall på varje försök är en produkt av kontinuerlig Aktiveringsfel och aktivering backoffintervall. |
|EnableRestartManagement |Bool, standard är FALSKT |Dynamisk|Detta är att starta om servern. |
|EnableServiceFabricAutomaticUpdates |Bool, standard är FALSKT |Dynamisk|Detta är att aktivera fabric automatiska uppdateringar via Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, standard är FALSKT |Dynamisk|Detta är att aktivera grundläggande uppdatering för server. |
|StartTimeout |Tid i sekunder, är standardvärdet 300 |Dynamisk|Ange tidsintervall i sekunder. Timeout för fabricactivationmanager start. |
|StopTimeout |Tid i sekunder, är standardvärdet 300 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för värdtjänsten-aktivering. Inaktivering och uppgradering. |

## <a name="fabricnode"></a>FabricNode
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |sträng, standard är ”FindByThumbprint” |Dynamisk|Anger hur du söker efter certifikat i arkivet som anges av ClientAuthX509StoreName stöds värde: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |sträng, standardvärdet är ”” | Dynamisk|Sök filtervärdet som används för att hitta certifikatet för standard-administratörsroll FabricClient. |
|ClientAuthX509FindValueSecondary |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta certifikatet för standard-administratörsroll FabricClient. |
|ClientAuthX509StoreName |sträng, standardvärdet är ”My” |Dynamisk|Namnet på det X.509-certifikatarkivet som innehåller certifikatet för standard-administratörsroll FabricClient. |
|ClusterX509FindType |sträng, standard är ”FindByThumbprint” |Dynamisk|Anger hur du söker efter klustercertifikat i arkivet som anges av ClusterX509StoreName stöds värdena: ”FindByThumbprint”; ”FindBySubjectName” med ”FindBySubjectName”; När det finns flera matchningar; Det sista upphör används. |
|ClusterX509FindValue |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta klustercertifikat. |
|ClusterX509FindValueSecondary |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta klustercertifikat. |
|ClusterX509StoreName |sträng, standardvärdet är ”My” |Dynamisk|Namnet på X.509-certifikatarkiv som innehåller klustret certifikat för säker kommunikation inom ett kluster. |
|EndApplicationPortRange |Int, standardvärdet är 0 |Statisk|Slut (inte inkluderande) programportar som hanteras av som är värd för undersystem. Krävs om EndpointFilteringEnabled är true i Hosting. |
|ServerAuthX509FindType |sträng, standard är ”FindByThumbprint” |Dynamisk|Anger hur du söker efter certifikat i arkivet som anges av ServerAuthX509StoreName stöds värde: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta servercertifikatet. |
|ServerAuthX509FindValueSecondary |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta servercertifikatet. |
|ServerAuthX509StoreName |sträng, standardvärdet är ”My” |Dynamisk|Namnet på X.509-certifikatarkiv som innehåller servercertifikatets för ingång-tjänsten. |
|StartApplicationPortRange |Int, standardvärdet är 0 |Statisk|Start av program-portarna som hanteras av som är värd för undersystem. Krävs om EndpointFilteringEnabled är true i Hosting. |
|StateTraceInterval |Tid i sekunder, är standardvärdet 300 |Statisk|Ange tidsintervall i sekunder. Intervall för att spåra nodstatus på varje nod och senare noder i FM/fmm-tjänsten. |
|UserRoleClientX509FindType |sträng, standard är ”FindByThumbprint” |Dynamisk|Anger hur du söker efter certifikat i arkivet som anges av UserRoleClientX509StoreName stöds värde: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta certifikatet för standard-användarrollen FabricClient. |
|UserRoleClientX509FindValueSecondary |sträng, standardvärdet är ”” |Dynamisk|Sök filtervärdet som används för att hitta certifikatet för standard-användarrollen FabricClient. |
|UserRoleClientX509StoreName |sträng, standardvärdet är ”My” |Dynamisk|Namnet på X.509-certifikatarkiv som innehåller certifikatet för standard-användarrollen FabricClient. |

## <a name="failovermanager"></a>FailoverManager
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|BuildReplicaTimeLimit|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(3600)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att skapa en tillståndskänslig replik; därefter initieras en varning hälsorapport |
|ClusterPauseThreshold|int, standard är 1|Dynamisk|Om antalet noder i systemet går under detta värde sedan placering; belastningsutjämning; och redundans har stoppats. |
|CreateInstanceTimeLimit|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(300)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att skapa en tillståndslös instans; därefter initieras en varning hälsorapport |
|ExpectedClusterSize|int, standard är 1|Dynamisk|När klustret startas först. FM väntar på att detta många noder om du vill rapportera själva in innan den börjar att placera andra tjänster. inklusive systemtjänster som att döpa. Det här värdet ökas ökar den tid det tar ett kluster att starta. men förhindrar tidig noderna från blir överbelastad och ytterligare flyttar som kommer att vara nödvändigt, eftersom mer noderna är online. Det här värdet sättas vanligtvis till en liten del av en inledande klusterstorleken. |
|ExpectedNodeDeactivationDuration|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamisk|Ange tidsintervall i sekunder. Det här är den förväntade tiden för en nod att slutföra inaktivering i. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamisk|Ange tidsintervall i sekunder. Det här är den förväntade tiden för en nod som ska uppgraderas under Windows Fabric-uppgraderingen. |
|ExpectedReplicaUpgradeDuration|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamisk|Ange tidsintervall i sekunder. Det här är den förväntade tiden för alla repliker som ska uppgraderas på en nod under uppgradering av programmet. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, standard är SANT|Dynamisk|Om värdet är true; repliker med ett mål för replikuppsättning 1 tillåts att flytta under uppgraderingen. |
|MinReplicaSetSize|int, standard är 3|Inte tillåten|Det här är den minsta storleken för FM. Om antalet aktiva FM repliker hamnar under detta värde; FM avvisar ändringar i klustret tills minst det minsta antalet repliker återställs |
|PlacementConstraints|sträng, standard är L ””|Inte tillåten|Alla placeringsbegränsningar för redundans manager repliker |
|PlacementTimeLimit|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(600)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att nå målet replikantal; därefter initieras en varning hälsorapport |
|QuorumLossWaitDuration |Tid i sekunder, är standardvärdet MaxValue |Dynamisk|Ange tidsintervall i sekunder. Det här är den maximala längd som tillåter vi en partition ska vara i tillståndet hos förlorar kvorum. Om partitionen är fortfarande i förlorar kvorum efter varaktigheten; partitionen återställs från förlorar kvorum genom att tänka på replikerna som förlorad. Observera att detta kan potentiellt medföra dataförlust. |
|ReconfigurationTimeLimit|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(300)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för omkonfiguration; därefter initieras en varning hälsorapport |
|ReplicaRestartWaitDuration|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(60.0 * 30)|Inte tillåten|Ange tidsintervall i sekunder. Det här är ReplicaRestartWaitDuration för FMService |
|Tidsgränsen för värdtjänsten-aktivering. Inaktivering och uppgradering.|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Inte tillåten|Ange tidsintervall i sekunder. Det här är StandByReplicaKeepDuration för FMService |
|TargetReplicaSetSize|int, standard är 7|Inte tillåten|Detta är antalet FM repliker som underhåller Windows Fabric. En hög siffra leder till högre tillförlitlighet FM; med en liten prestanda kompromiss. |
|UserMaxStandByReplicaCount |Int, standardvärdet är 1 |Dynamisk|Max standardantalet StandBy-repliker som systemet håller för tjänster. |
|UserReplicaRestartWaitDuration |Tid i sekunder är standard 60,0 * 30 |Dynamisk|Ange tidsintervall i sekunder. När en bestående replik kraschar; Windows Fabric väntar för den här perioden för repliken för att gå tillbaka innan du skapar nya ersättning repliker (som kräver en kopia av tillståndet). |
|UserStandByReplicaKeepDuration |Tid i sekunder, standardvärdet är 7 * 24 * 3600.0 |Dynamisk|Ange tidsintervall i sekunder. När en bestående replik kommer tillbaka från tillståndet på; den kan ha redan ersatts. Den här timern anger hur länge FM behåller vänteläge repliken innan du tar bort den. |

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, standardvärdet är 604800 |Statisk| Det här är ungefär hur lång tid att behålla åtgärder som är i ett avslutat tillstånd. Detta beror också på StoredActionCleanupIntervalInSeconds; eftersom arbete att rensa utförs endast i det här intervallet. 604800 är 7 dagar. |
|DataLossCheckPollIntervalInSeconds|int, standard är 5|Statisk|Det här är tiden mellan de kontroller som utförs under väntan på att data går förlorade ske. Hur många gånger data går förlorade numret kontrolleras per interna iteration är DataLossCheckWaitDurationInSeconds/detta. |
|DataLossCheckWaitDurationInSeconds|int, standard är 25|Statisk|Den totala mängden tid. i sekunder. att systemet väntar för förlust av data ska ske. Det här används internt när StartPartitionDataLossAsync() API: et anropas. |
|MinReplicaSetSize |Int, standardvärdet är 0 |Statisk|MinReplicaSetSize för FaultAnalysisService. |
|PlacementConstraints | sträng, standardvärdet är ””|Statisk| PlacementConstraints för FaultAnalysisService. |
|QuorumLossWaitDuration | Tid i sekunder, är standardvärdet MaxValue |Statisk|Ange tidsintervall i sekunder. QuorumLossWaitDuration för FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, standard är 600|Statisk|Den här parametern används när data går förlorade api anropas. Den kontrollerar hur länge systemet väntar på en replik ska tas bort efter att ta bort repliken anropas internt på den. |
|ReplicaRestartWaitDuration |Tid i sekunder, standardvärdet är 60 minuter|Statisk|Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för FaultAnalysisService. |
|Tidsgränsen för värdtjänsten-aktivering. Inaktivering och uppgradering.| Tid i sekunder, är standardvärdet (60*24*7) minuter |Statisk|Ange tidsintervall i sekunder. StandByReplicaKeepDuration för FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, standard är 3 600 |Statisk|Detta är hur ofta arkivet rensas. Endast åtgärder i ett avslutat tillstånd; och som slutförts minst CompletedActionKeepDurationInSeconds sedan kommer att tas bort. |
|StoredChaosEventCleanupIntervalInSeconds | Int, standard är 3 600 |Statisk|Detta är hur ofta arkivet kommer att granskas för rensning; Om antalet händelser som är mer än 30000; rensningen kommer igång. |
|TargetReplicaSetSize |Int, standardvärdet är 0 |Statisk|NOT_PLATFORM_UNIX_START TargetReplicaSetSize för FaultAnalysisService. |

## <a name="federation"></a>Federation
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|LeaseDuration |Tid i sekunder, standardvärdet är 30 |Dynamisk|Tiden då ett lån varar mellan en nod och sina grannar. |
|LeaseDurationAcrossFaultDomain |Tid i sekunder, standardvärdet är 30 |Dynamisk|Tiden då ett lån varar mellan en nod och dess grannar över feldomäner. |

## <a name="filestoreservice"></a>FileStoreService
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|AnonymousAccessEnabled | Bool, standard är SANT |Statisk|Aktivera/inaktivera anonym åtkomst till FileStoreService-resurser. |
|CommonName1Ntlmx509CommonName|sträng, standard är L ””|Statisk| Namnet på X509 certifikatet som används för att generera HMAC på CommonName1NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonName1Ntlmx509StoreLocation|sträng, standard är L ”LocalMachine”|Statisk|Lagringsplatsen för X509 certifikatet som används för att generera HMAC på CommonName1NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonName1Ntlmx509StoreName|sträng, standard är L ”MY”| Statisk|Store-namnet på X509 certifikatet som används för att generera HMAC på CommonName1NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonName2Ntlmx509CommonName|sträng, standard är L ””|Statisk|Namnet på X509 certifikatet som används för att generera HMAC på CommonName2NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonName2Ntlmx509StoreLocation|sträng, standard är L ”LocalMachine”| Statisk|Lagringsplatsen för X509 certifikatet som används för att generera HMAC på CommonName2NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonName2Ntlmx509StoreName|sträng, standard är L ”MY”|Statisk| Store-namnet på X509 certifikatet som används för att generera HMAC på CommonName2NtlmPasswordSecret när du använder NTLM-autentisering |
|CommonNameNtlmPasswordSecret|SecureString, standardvärdet är Common::SecureString(L"")| Statisk|Lösenord-hemlighet som används som startvärde för genererade samma lösenord när du använder NTLM-autentisering |
|GenerateV1CommonNameAccount| bool, standard är SANT|Statisk|Anger om du vill skapa ett konto med användaren namnet V1-generationsalgoritm. Från och med Service Fabric version 6.1; ett konto med v2-generation skapas alltid. V1-konto krävs för uppgradering från/till versioner som inte har stöd för V2-generation (före 6.1).|
|MaxCopyOperationThreads | Uint, standardvärdet är 0 |Dynamisk| Det maximala antalet parallella filer som sekundär kan kopiera från primära. ”0” == antalet kärnor. |
|MaxFileOperationThreads | Uint, standardvärdet är 100 |Statisk| Det maximala antalet parallella trådar som får utföra FileOperations (kopiera/flytta) i primärt. ”0” == antalet kärnor. |
|MaxRequestProcessingThreads | Uint, standardinställningen är 200 |Statisk|Det maximala antalet parallella trådar som tillåts att behandla begäranden i primärt. ”0” == antalet kärnor. |
|MaxSecondaryFileCopyFailureThreshold | Uint, standardvärdet är 25|Dynamisk|Det maximala antalet återförsök för fil-kopia på sekundärt innan den ger upp. |
|MaxStoreOperations | Uint, standardvärdet är 4096 |Statisk|Det maximala antalet tillåtna på primära parallella store transaktion-åtgärder. ”0” == antalet kärnor. |
|NamingOperationTimeout |Tid i sekunder, standardvärdet är 60 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att utföra åtgärden för namngivning. |
|PrimaryAccountNTLMPasswordSecret | SecureString, standard är tom |Statisk| Hemligheten för lösenord som används som startvärde för genererade samma lösenord när du använder NTLM-autentisering. |
|PrimaryAccountNTLMX509StoreLocation | sträng, standard är ”LocalMachine”|Statisk| Lagringsplatsen för X509 certifikatet som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
|PrimaryAccountNTLMX509StoreName | sträng, standard är ”MY”|Statisk| Store-namnet på X509 certifikatet som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
|PrimaryAccountNTLMX509Thumbprint | sträng, standardvärdet är ””|Statisk|Tumavtrycket för X509 certifikatet som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
|PrimaryAccountType | sträng, standardvärdet är ”” |Statisk|Primärt AccountType för huvudnamn åtkomstkontrollistan i FileStoreService delar. |
|PrimaryAccountUserName | sträng, standardvärdet är ”” |Statisk|Det primära kontot användarnamn för huvudnamn åtkomstkontrollistan i FileStoreService delar. |
|PrimaryAccountUserPassword | SecureString, standard är tom |Statisk|Primär kontolösenordet för huvudnamn åtkomstkontrollistan i FileStoreService delar. |
|QueryOperationTimeout | Tid i sekunder, standardvärdet är 60 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att utföra frågeåtgärden igen. |
|SecondaryAccountNTLMPasswordSecret | SecureString, standard är tom |Statisk| Hemligheten för lösenord som används som startvärde för genererade samma lösenord när du använder NTLM-autentisering. |
|SecondaryAccountNTLMX509StoreLocation | sträng, standard är ”LocalMachine” |Statisk|Lagringsplatsen för X509 certifikatet som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
|SecondaryAccountNTLMX509StoreName | sträng, standard är ”MY” |Statisk|Store-namnet på X509 certifikatet som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
|SecondaryAccountNTLMX509Thumbprint | sträng, standardvärdet är ””| Statisk|Tumavtrycket för X509 certifikatet som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när du använder NTLM-autentisering. |
|SecondaryAccountType | sträng, standardvärdet är ””|Statisk| Sekundärt AccountType för huvudnamn åtkomstkontrollistan i FileStoreService delar. |
|SecondaryAccountUserName | sträng, standardvärdet är ””| Statisk|Sekundär kontot användarnamn för huvudnamn åtkomstkontrollistan i FileStoreService delar. |
|SecondaryAccountUserPassword | SecureString, standard är tom |Statisk|Sekundär kontolösenordet för huvudnamn åtkomstkontrollistan i FileStoreService delar. |

## <a name="healthmanager"></a>HealthManager
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool, standard är FALSKT |Statisk|Kluster-utvärderingen hälsoprincip: aktivera per programutvärderingar typ hälsotillstånd. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, standard är FALSKT |Statisk|Kluster-utvärderingen hälsoprincip: varningar behandlas som fel. |
|MaxPercentUnhealthyApplications | Int, standardvärdet är 0 |Statisk|Kluster-utvärderingen hälsoprincip: Maxprocent av felaktiga program som tillåts för klustret är felfritt. |
|MaxPercentUnhealthyNodes | Int, standardvärdet är 0 |Statisk|Kluster-utvärderingen hälsoprincip: Maxprocent av defekta noder som tillåts för klustret är felfritt. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, standardvärdet är 10|Statisk|Uppgradera hälsoprincip för utvärdering av kluster: Maxprocent av förändrade defekta noder som tillåts för klustret är felfritt |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, standardvärdet är 15|Statisk|Uppgradera hälsoprincip för utvärdering av kluster: Maxprocent av förändrade defekta noder i en uppgraderingsdomän som tillåts för klustret är felfritt |

## <a name="hosting"></a>Som är värd för
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Heltal, standardvärdet är 10 |Dynamisk|Antal gånger som nya försök system misslyckades aktivering innan den ger upp |
|ActivationMaxRetryInterval |Tid i sekunder, är standardvärdet 300 |Dynamisk|På varje kontinuerlig Aktiveringsfel försöker systemet aktivering för upp till ActivationMaxFailureCount. ActivationMaxRetryInterval Anger tidsintervall för vänta innan ett återförsök görs efter varje Aktiveringsfel |
|ActivationRetryBackoffInterval |Tid i sekunder, standardvärdet är 5 |Dynamisk|Backoff intervallet på varje Aktiveringsfel; På varje kontinuerlig Aktiveringsfel försöker systemet aktivering för upp till MaxActivationFailureCount. Återförsöksintervall på varje försök är en produkt av kontinuerlig Aktiveringsfel och aktivering backoffintervall. |
|ActivationTimeout| TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(180)|Dynamisk| Ange tidsintervall i sekunder. Tidsgränsen för aktivering av programmet. Inaktivering och uppgradering. |
|ApplicationHostCloseTimeout| TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(120)|Dynamisk| Ange tidsintervall i sekunder. När Fabric avsluta har identifierats för ett självsignerat aktiverat processer. FabricRuntime stänger alla repliker i användarens värdprocess (programvärden). Det här är tidsgränsen för åtgärden Stäng. |
|ApplicationUpgradeTimeout| TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(360)|Dynamisk| Ange tidsintervall i sekunder. Tidsgränsen för uppgradering av programmet. Om tidsgränsen är mindre än ”ActivationTimeout” deployer misslyckas. |
|ContainerServiceArguments|wstring, standard är L ”-H localhost: 2375 -H npipe: / /”|Statisk|Service Fabric (SA) hanterar docker-daemon (förutom på windows-klientdatorer som Win10). Den här konfigurationen gör att användaren anger anpassade argument som ska skickas till docker-daemon när du startar den. När anpassade argument, Service Fabric inte skickar några andra argument till Docker-motorn, förutom ”--pidfile' argumentet. Användare bör därför inte ange '--pidfile' argument som en del av deras kund-argument. De anpassade argumenten ska kontrollera också att daemon lyssnar på pipen med standardnamnet på Windows (eller Unix-domänsocket på Linux) för Service Fabric ska kunna kommunicera med den.|
|CreateFabricRuntimeTimeout|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(120)|Dynamisk| Ange tidsintervall i sekunder. Timeout-värdet för synkronisering FabricCreateRuntime anropa |
|DeploymentMaxFailureCount|int, standard är 20| Dynamisk|Programdistribution görs DeploymentMaxFailureCount gånger innan distributionen av programmet på noden.| 
|DeploymentMaxRetryInterval| TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(3600)|Dynamisk| Ange tidsintervall i sekunder. Max återförsöksintervallet för distributionen. Vid varje kontinuerlig fel beräknas återförsöksintervallet som Min (DeploymentMaxRetryInterval; Kontinuerlig Felberäkning * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(10)|Dynamisk|Ange tidsintervall i sekunder. Backoffintervall för distributionsfel. På varje kontinuerlig distributionsfel försöker systemet distributionen för upp till MaxDeploymentFailureCount. Intervallet är en produkt för misslyckad kontinuerlig distribution och distribution backoff intervall. |
|EnableActivateNoWindow| bool, standard är FALSKT|Dynamisk| Aktiverad process skapas i bakgrunden utan någon konsol. |
|EnableDockerHealthCheckIntegration|bool, standard är SANT|Statisk|Låter dig integrera docker HEALTHCHECK händelser med Service Fabric systemets hälsorapport |
|EnableProcessDebugging|bool, standard är FALSKT|Dynamisk| Aktiverar startar programmet värdar under felsökare |
|EndpointProviderEnabled| bool, standard är FALSKT|Statisk| Möjliggör hantering av slutpunkten resurser av Fabric. Kräver att start- och portintervall för program i FabricNode. |
|FabricContainerAppsEnabled| bool, standard är FALSKT|Statisk| |
|FirewallPolicyEnabled|bool, standard är FALSKT|Statisk| Gör det möjligt att öppna portar i brandväggen för Endpoint resurser med explicita portar som anges i ServiceManifest |
|GetCodePackageActivationContextTimeout|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(120)|Dynamisk|Ange tidsintervall i sekunder. Timeout-värdet för CodePackageActivationContext-anrop. Detta gäller inte för ad hoc-tjänster. |
|IPProviderEnabled|bool, standard är FALSKT|Statisk|Möjliggör hantering av IP-adresser. |
|LinuxExternalExecutablePath|wstring, standard är L ”/ usr/bin /” |Statisk|Den primära katalogen för externa körbara kommandon på noden.|
|NTLMAuthenticationEnabled|bool, standard är FALSKT|Statisk| Aktiverar stöd för att använda NTLM av kodpaket som är igång som andra användare så att processerna över datorer kan kommunicera på ett säkert sätt. |
|NTLMAuthenticationPasswordSecret|SecureString, standardvärdet är Common::SecureString(L"")|Statisk|Är ett krypterat har som används för att generera lösenordet för NTLM-användare. Måste anges om NTLMAuthenticationEnabled är sant. Godkänt distribueraren. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, standardvärdet är Common::TimeSpan::FromMinutes(3)|Dynamisk|Ange tidsintervall i sekunder. Miljö-specifika inställningar med vilka Hosting vilket regelbundna intervall söker efter nya certifikat som ska användas för konfiguration av FileStoreService NTLM. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, standardvärdet är Common::TimeSpan::FromMinutes(4)|Dynamisk| Ange tidsintervall i sekunder. Tidsgränsen för att konfigurera NTLM-användare som använder vanliga namn för certifikatet. NTLM-användare krävs för FileStoreService resurser. |
|RegisterCodePackageHostTimeout|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(120)|Dynamisk| Ange tidsintervall i sekunder. Timeout-värdet för anropet FabricRegisterCodePackageHost synkronisering. Detta gäller endast med flera kod paketet programvärd som FWP |
|RequestTimeout|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(30)|Dynamisk| Ange tidsintervall i sekunder. Detta representerar tidsgränsen för kommunikation mellan användarens programvärd och Fabric-processen för olika värdbaserade relaterade åtgärder, till exempel factory registreringen. Runtime-registrering. |
|RunAsPolicyEnabled| bool, standard är FALSKT|Statisk| Gör det möjligt att köra kodpaket som lokala användare än den processen körs under vilka fabric. För att kunna aktivera den här principen måste du köra Fabric som SYSTEM eller användare som har SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(120)|Dynamisk|Ange tidsintervall i sekunder. Timeout-värdet för synkronisering Register (Stateless/tillståndskänslig) ServiceFactory anrop |
|ServiceTypeDisableFailureThreshold |Heltal, standard är 1 |Dynamisk|Det här är tröskelvärdet för antalet fel efter vilken FailoverManager (FM) meddelas att inaktivera tjänsttypen på noden och prova en annan nod för placering. |
|ServiceTypeDisableGraceInterval|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(30)|Dynamisk|Ange tidsintervall i sekunder. Tidsintervall efter vilket tjänsttypen kan inaktiveras |
|ServiceTypeRegistrationTimeout |Tid i sekunder, är standardvärdet 300 |Dynamisk|Längsta tid som tillåts för ServiceType registreras med fabric |
|UseContainerServiceArguments|bool, standard är SANT|Statisk|Den här konfigurationen meddelar som är värd för att hoppa över skicka argument (anges i config ContainerServiceArguments) till docker-daemon.|

## <a name="httpgateway"></a>HttpGateway
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, standardvärdet är 50 |Statisk| Antalet läsningar att publicera till kön för http-server. Detta styr antalet samtidiga begäranden som kan betjänas av HttpGateway. |
|HttpGatewayHealthReportSendInterval |Tid i sekunder, standardvärdet är 30 |Statisk|Ange tidsintervall i sekunder. Intervallet som Http-Gateway skickar ackumulerade hälsotillstånd rapporterar till Health Manager. |
|IsEnabled|Bool, standard är FALSKT |Statisk| Aktiverar eller inaktiverar HttpGateway. HttpGateway är inaktiverad som standard. |
|MaxEntityBodySize |Uint, standardvärdet är 4194304 |Dynamisk|Ger den maximala storleken för brödtexten som kan förväntas från en http-begäran. Standardvärdet är 4MB. Httpgateway misslyckas en begäran om den har en mängd storlek > det här värdet. Minsta Läs segmentstorleken är 4096 byte. Så här måste vara > = 4096. |

## <a name="imagestoreclient"></a>ImageStoreClient
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ClientCopyTimeout | Tid i sekunder, standardvärdet är 1800 |Dynamisk| Ange tidsintervall i sekunder. Timeoutvärdet för översta kopia begäran till avbildningen Store-tjänsten. |
|ClientDefaultTimeout | Tid i sekunder, standardvärdet är 180 |Dynamisk| Ange tidsintervall i sekunder. Timeoutvärdet för alla icke-uppladdning/icke-nedladdning förfrågningar (t.ex. finns och ta bort) till avbildningen Store-tjänsten. |
|ClientDownloadTimeout | Tid i sekunder, standardvärdet är 1800 |Dynamisk| Ange tidsintervall i sekunder. Timeoutvärdet för översta hämtningsbegäran till avbildningen Store-tjänsten. |
|ClientListTimeout | Tid i sekunder, standardvärdet är 600 |Dynamisk|Ange tidsintervall i sekunder. Timeoutvärdet för översta lista begäran till avbildningen Store-tjänsten. |
|ClientUploadTimeout |Tid i sekunder, standardvärdet är 1800 |Dynamisk|Ange tidsintervall i sekunder. Timeoutvärdet för översta överföringsförfrågan till avbildningen Store-tjänsten. |

## <a name="imagestoreservice"></a>ImageStoreService
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|Enabled |Bool, standard är FALSKT |Statisk|Aktiverad flagga för ImageStoreService. Standard: FALSKT |
|MinReplicaSetSize | Int, standard är 3 |Statisk|MinReplicaSetSize för ImageStoreService. |
|PlacementConstraints | sträng, standardvärdet är ”” |Statisk| PlacementConstraints för ImageStoreService. |
|QuorumLossWaitDuration | Tid i sekunder, är standardvärdet MaxValue |Statisk| Ange tidsintervall i sekunder. QuorumLossWaitDuration för ImageStoreService. |
|ReplicaRestartWaitDuration | Tid i sekunder är standard 60,0 * 30 |Statisk|Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för ImageStoreService. |
|Tidsgränsen för värdtjänsten-aktivering. Inaktivering och uppgradering. | Tid i sekunder är standard 3600.0 * 2 |Statisk| Ange tidsintervall i sekunder. StandByReplicaKeepDuration för ImageStoreService. |
|TargetReplicaSetSize | Anger hur du söker efter certifikat i arkivet som anges av ClientAuthX509StoreName stöds värde: FindByThumbprint; FindBySubjectName. |Statisk|TargetReplicaSetSize för ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, standardvärdet är 1 |Dynamisk|Flagga som anger om minnesinställningarna automatiskt och dynamiskt konfigureras. Om noll sedan konfigurationsinställningarna för minne används direkt och ändra inte baserat på system-villkor. Om en sedan minnesinställningarna konfigureras automatiskt och kan ändras baserat på system-villkor. |
|MaximumDestagingWriteOutstandingInKB | Int, standardvärdet är 0 |Dynamisk|Antal KB så att delade loggen att gå vidare före dedikerade loggen. Använd 0 för att ange någon gräns.
|SharedLogId |sträng, standardvärdet är ”” |Statisk|Unikt guid för delade log-behållare. Använd ”” om du använder standardsökvägen under fabric-dataroten. |
|SharedLogPath |sträng, standardvärdet är ”” |Statisk|Sökvägen och filnamnet för plats för delade log behållare. Använd ”” för att använda standardsökvägen under fabric-dataroten. |
|SharedLogSizeInMB |Int, standardvärdet är 8192 |Statisk|Antal MB för att allokera i delade log-behållaren. |
|WriteBufferMemoryPoolMaximumInKB | Int, standardvärdet är 0 |Dynamisk|Antal KB att tillåta skrivna minne buffertpoolen att växa upp till. Använd 0 för att ange någon gräns. |
|WriteBufferMemoryPoolMinimumInKB |Int, standard är 8388608 |Dynamisk|Antal KB tilldelas inledningsvis för skrivna minne buffertpooltillägget. Använd 0 för att visa obegränsat standard bör överensstämma med SharedLogSizeInMB nedan. |

## <a name="management"></a>Hantering
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|AzureStorageMaxConnections | Int, standardvärdet är 5000 |Dynamisk|Det maximala antalet samtidiga anslutningar till azure storage. |
|AzureStorageMaxWorkerThreads | Int, standardvärdet är 25 |Dynamisk|Det maximala antalet trådar parallellt. |
|AzureStorageOperationTimeout | Tid i sekunder, är standardvärdet 6000 |Dynamisk|Ange tidsintervall i sekunder. Tidsgräns för xstore åtgärden har slutförts. |
|CleanupApplicationPackageOnProvisionSuccess|bool, standard är FALSKT |Dynamisk|Den här konfigurationen aktiverar eller inaktiverar automatisk rensning av programpaket på lyckad etablering. |
|DisableChecksumValidation | Bool, standard är FALSKT |Statisk| Den här konfigurationen gör att vi kan aktivera eller inaktivera validering av kontrollsumma vid etableringen av programmet. |
|DisableServerSideCopy | Bool, standard är FALSKT |Statisk|Den här konfigurationen aktiverar eller inaktiverar serversidan kopia av programpaket på ImageStore vid etableringen av programmet. |
|ImageCachingEnabled | Bool, standard är SANT |Statisk|Den här konfigurationen gör att vi kan aktivera eller inaktivera cachelagring. |
|ImageStoreConnectionString |SecureString |Statisk|Anslutningssträng till roten för ImageStore. |
|ImageStoreMinimumTransferBPS | Int, standardvärdet är 1024 |Dynamisk|Den minsta överföringshastigheten mellan kluster och ImageStore. Det här värdet används för att fastställa tidsgräns vid åtkomst till externa ImageStore. Ändra det här värdet bara om fördröjningen mellan kluster och ImageStore är högt för att ge mer tid för klustret för att ladda ned från externa ImageStore. |

## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parametern** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, standardvärdet är ingen|Dynamisk|Anger uppsättningen MetricActivityThresholds för mått i klustret. Belastningsutjämning fungerar om maxNodeLoad är större än MetricActivityThresholds. Defragmenterar mått för den definierar mängden belastningen lika med eller lägre än den som Service Fabric betraktar noden tom |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parametern** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standardvärdet är ingen|Dynamisk|Anger uppsättningen MetricBalancingThresholds för mått i klustret. Belastningsutjämning fungerar om maxNodeLoad/minNodeLoad är större än MetricBalancingThresholds. Defragmentering fungerar om maxNodeLoad/minNodeLoad i minst en FD och UD är mindre än MetricBalancingThresholds. |

## <a name="namingservice"></a>NamingService
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, standardvärdet är 0 |Statisk|Det maximala antalet poster i LRU-tjänst beskrivning cache vid namngivning av Gateway (värdet 0 för ingen begränsning). |
|MaxClientConnections |Int, standard är 1 000 |Dynamisk|Högsta tillåtna antalet klientanslutningar per gateway. |
|MaxFileOperationTimeout |Tid i sekunder, standardvärdet är 30 |Dynamisk|Ange tidsintervall i sekunder. Den maximala tidsgränsen som tillåts för åtgärden för filen store-tjänsten. Med en tidsgräns som är större nekas. |
|MaxIndexedEmptyPartitions |Int, standard är 1 000 |Dynamisk|Det maximala antalet tomma partitioner som är kvar indexeras i cacheminnet meddelande för att synkronisera återanslutning klienter. Eventuella tomma partitioner ovanför det här talet tas bort från indexet i stigande ordning för lookup-version. Ansluta klienter kan fortfarande synkronisera och ta emot uppdateringar för missade tom partition. men synkroniseringsprotokollet blir dyrare. |
|MaxMessageSize |Int, standardvärdet är 4\*1024\*1024 |Statisk|Den maximala meddelandestorleken för noden klientkommunikation när du använder naming. DOS-attack lindra; Standardvärdet är 4MB. |
|MaxNamingServiceHealthReports | Int, standarden är 10 |Dynamisk|Det maximala antalet långsamma åtgärder som namngivning lagrar tjänsten defekta rapporter i taget. Om 0; alla åtgärder för långsam skickas. |
|MaxOperationTimeout |Tid i sekunder, standardvärdet är 600 |Dynamisk|Ange tidsintervall i sekunder. Den maximala tidsgränsen som tillåts för Klientåtgärder. Med en tidsgräns som är större nekas. |
|MaxOutstandingNotificationsPerClient |Int, standard är 1 000 |Dynamisk|Det maximala antalet utestående meddelanden innan en klientregistrering är stängde av gateway. |
|MinReplicaSetSize | Int, standard är 3 |Inte tillåten| Det minsta antalet Namngivningstjänsten repliker som krävs för att skriva till att slutföra en uppdatering. Om det finns färre repliker än det aktiva i systemet tillförlitlighet systemet nekar uppdateringar till Naming Service Store förrän repliker har återställts. Det här värdet ska aldrig vara mer än TargetReplicaSetSize. |
|PartitionCount |Int, standard är 3 |Inte tillåten|Antalet partitioner för namngivning av tjänsten lagra som ska skapas. Varje partition äger en enda partitionsnyckel som motsvarar dess index. så partitionsnycklar [0. PartitionCount) finns. Öka antalet Namngivningstjänsten partitioner ökar skalan som tjänsten Naming kan utföra på genom att minska den genomsnittliga mängden data från valfri replik för säkerhetskopiering ange; till en kostnad av ökad användning av resurser (eftersom PartitionCount * ReplicaSetSize service repliker måste underhållas).|
|PlacementConstraints | sträng, standardvärdet är ”” |Inte tillåten| Placering av begränsning för namngivning av tjänsten. |
|QuorumLossWaitDuration | Tid i sekunder, är standardvärdet MaxValue |Inte tillåten| Ange tidsintervall i sekunder. När en Namngivningstjänsten kommer till förlorar kvorum; den här timern startar. När den upphör att gälla betraktar FM på repliker som gå förlorad. och försöka att återställa kvorum. Inte som det kan resultera i dataförlust. |
|RepairInterval | Tid i sekunder, standardvärdet är 5 |Statisk| Ange tidsintervall i sekunder. Intervall som reparera namngivning inkonsekvens mellan utfärdare av ägaren och ägare startar. |
|ReplicaRestartWaitDuration | Tid i sekunder, är standardvärdet (60,0 * 30)|Inte tillåten| Ange tidsintervall i sekunder. När en replik Namngivningstjänsten kraschar; den här timern startar. När den upphör att gälla FM börjar att ersätta repliker som är nere (det inte ännu anser tappas bort). |
|ServiceDescriptionCacheLimit | Int, standardvärdet är 0 |Statisk| Det maximala antalet poster i beskrivning cache för LRU-tjänst på Store Naming-tjänsten (värdet 0 för ingen begränsning). |
|ServiceNotificationTimeout |Tid i sekunder, standardvärdet är 30 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen används när du levererar tjänstmeddelanden till klienten. |
|Tidsgränsen för värdtjänsten-aktivering. Inaktivering och uppgradering. | Tid i sekunder är standard 3600.0 * 2 |Inte tillåten| Ange tidsintervall i sekunder. När en replik Namngivningstjänsten kommer tillbaka från tillståndet på; den kan ha redan ersatts. Den här timern anger hur länge FM behåller vänteläge repliken innan du tar bort den. |
|TargetReplicaSetSize |Anger hur du söker efter certifikat i arkivet som anges av ClientAuthX509StoreName stöds värde: FindByThumbprint; FindBySubjectName. |Inte tillåten|Antal replik anger för varje partition för namngivning av tjänstens Arkiv. Öka antalet replikuppsättningar ökar tillförlitligheten hos informationen i Naming Service Store; minska ändringen att informationen kommer att gå förlorade på grund av nodfel; till en kostnad av ökad belastning på Windows Fabric och hur lång tid tar det för att utföra uppdateringar på namngivning data.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parametern** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, standardvärdet är ingen|Dynamisk|Noden nätverkskapaciteten i procent per Måttnamn; används som en buffert för att skydda någon kostnadsfri plats på en nod för redundans. |

## <a name="nodecapacities"></a>NodeCapacities
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Statisk|En samling av nodkapaciteterna för olika mått. |

## <a name="nodedomainids"></a>NodeDomainIds
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Statisk|Beskriver feldomäner som en nod som hör till. Feldomänen definieras via en URI som beskriver platsen för noden i datacentret.  Fel domän URI: er är i formatet fd: / fd/följt av ett vägsegment för URI: N.|
|UpgradeDomainId |sträng, standardvärdet är ”” |Statisk|Beskriver uppgraderingsdomän som en nod som hör till. |

## <a name="nodeproperties"></a>NodeProperties
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Statisk|En samling nyckel / värde-strängpar för nodegenskaper. |

## <a name="paas"></a>Paas
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ClusterId |sträng, standardvärdet är ”” |Inte tillåten|X509 certifikatarkivet som används av infrastruktur för skyddskonfigurationen. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|Räknare |Sträng | Dynamisk |Kommaavgränsad lista över prestandaräknare för att samla in. |
|IsEnabled |Bool, standard är SANT | Dynamisk |Flagga som anger om samling med prestandaräknare på lokal nod har aktiverats. |
|MaxCounterBinaryFileSizeInMB |Int, standardvärdet är 1 | Dynamisk |Maximal storlek (i MB) för varje prestandaräknaren binär fil. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, standarden är 10 | Dynamisk |Maximalt intervall (i sekunder) efter vilken en ny prestandaräknaren binär fil har skapats. |
|SamplingIntervalInSeconds |Int, standardvärdet är 60 | Dynamisk |Exempelintervall för prestandaräknare som samlas in. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för mappning mellan och begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|ApplicationCapacityConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för kapacitet begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|AutoDetectAvailableResources|bool, standard är SANT|Statisk|Den här konfigurationen utlöser automatisk identifiering av tillgängliga resurser på noden (processor och minne) när den här konfigurationen är inställd på true – vi läsa verkliga kapaciteter och korrigera dem om användaren har angetts felaktigt nodkapaciteterna eller inte har definierat dem alls om den här konfigurationen är inställd på false - ska vi  spåra en varning som användaren angett felaktig nodkapaciteterna; men vi korrigerar inte dem. vilket innebär att användaren vill ha de kapacitet som angetts som > än noden har verkligen eller om de kapaciteter som är odefinierad; den antar obegränsad kapacitet |
|BalancingDelayAfterNewNode | Tid i sekunder, standardvärdet är 120 |Dynamisk|Ange tidsintervall i sekunder. Starta inte belastningsutjämning aktiviteter inom denna period när du lägger till en ny nod. |
|BalancingDelayAfterNodeDown | Tid i sekunder, standardvärdet är 120 |Dynamisk|Ange tidsintervall i sekunder. Starta inte belastningsutjämning aktiviteter inom denna period efter en nod av händelsen. |
|CapacityConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för kapacitet begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, standardvärdet är 20 | Dynamisk|Anger hur många gånger i rad som ResourceBalancer-utfärdade förflyttningar ignoreras innan diagnostik utförs och hälsotillståndsvarningar genereras. Negativt: Inga varningar genereras under det här tillståndet. |
|ConstraintFixPartialDelayAfterNewNode | Tid i sekunder, standardvärdet är 120 |Dynamisk| Ange tidsintervall i sekunder. DDo inte åtgärda FaultDomain och UpgradeDomain begränsningen överträdelser inom denna period när du lägger till en ny nod. |
|ConstraintFixPartialDelayAfterNodeDown | Tid i sekunder, standardvärdet är 120 |Dynamisk| Ange tidsintervall i sekunder. Gör inte åtgärda FaultDomain och UpgradeDomain begränsningen överträdelser inom denna period efter en nod av händelsen. |
|ConstraintViolationHealthReportLimit | Int, standardvärdet är 50 |Dynamisk| Definierar hur många gånger begränsningen innebär ett brott mot repliken måste vara beständigt olöst innan diagnostik utförs och systemhälsorapporter genereras. |
|DetailedConstraintViolationHealthReportLimit | Int, standardinställningen är 200 |Dynamisk| Definierar hur många gånger begränsningen innebär ett brott mot repliken måste vara beständigt olöst innan diagnostik utförs och detaljerade rapporter genereras hälsotillstånd. |
|DetailedDiagnosticsInfoListLimit | Int, standardvärdet är 15 |Dynamisk| Definierar antal diagnostikposter (med detaljerad information) per begränsningen att inkludera innan trunkering i diagnostik.|
|DetailedNodeListLimit | Int, standardvärdet är 15 |Dynamisk| Definierar antalet noder per begränsningen att inkludera innan trunkering i rapporter om Ej placerade repliken. |
|DetailedPartitionListLimit | Int, standardvärdet är 15 |Dynamisk| Definierar hur många partitioner per diagnostiska post för ett villkor att inkludera innan trunkering i diagnostik. |
|DetailedVerboseHealthReportLimit | Int, standardinställningen är 200 | Dynamisk|Definierar hur många gånger som en Ej placerade replik måste vara beständigt Ej placerade innan detaljerad hälsorapporter genereras. |
|FaultDomainConstraintPriority | Int, standardvärdet är 0 |Dynamisk| Anger prioriteten för domänbegränsningar för fel: 0: hårda; 1: mjuk; negativt: ignorera. |
|GlobalMovementThrottleCountingInterval | Tid i sekunder, standardvärdet är 600 |Statisk| Ange tidsintervall i sekunder. Ange längden på den senaste intervall som du vill spåra per domän repliken förflyttningar (används tillsammans med GlobalMovementThrottleThreshold). Kan anges till 0 för att ignorera global begränsning helt och hållet. |
|GlobalMovementThrottleThreshold | Uint, standard är 1 000 |Dynamisk| Maximalt antal förflyttningar som tillåts i fasen belastningsutjämning i den senaste intervall som anges av GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, standardvärdet är 0 | Dynamisk|Maximalt antal tillåtna i nätverksbelastning fas i den senaste intervall som anges av GlobalMovementThrottleCountingInterval förflyttningar. 0 anger att ingen gräns. |
|GlobalMovementThrottleThresholdForPlacement | Uint, standardvärdet är 0 |Dynamisk| Maximalt antal tillåtna i placering fas i den senaste intervall som anges av GlobalMovementThrottleCountingInterval.0 förflyttningar anger att ingen gräns.|
|GlobalMovementThrottleThresholdPercentage|Double, är standardvärdet 0|Dynamisk|Maximalt antal totala förflyttningar som tillåts i belastningsutjämning och placering faser (uttryckt i procent av totalt antal repliker i klustret) i den senaste intervall som anges av GlobalMovementThrottleCountingInterval. 0 anger att ingen gräns. Om både den här och GlobalMovementThrottleThreshold anges; sedan används mer konservativ gränsen.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double, är standardvärdet 0|Dynamisk|Maximalt antal tillåtna i nätverksbelastning fasen (uttryckt i procent av totalt antal repliker i PLB) i den senaste intervall som anges av GlobalMovementThrottleCountingInterval förflyttningar. 0 anger att ingen gräns. Om både den här och GlobalMovementThrottleThresholdForBalancing anges; sedan används mer konservativ gränsen.|
|InBuildThrottlingAssociatedMetric | sträng, standardvärdet är ”” |Statisk| Det associerade måttnamnet för den här begränsningen. |
|InBuildThrottlingEnabled | Bool, standard är FALSKT |Dynamisk| Avgör om i build-begränsning har aktiverats. |
|InBuildThrottlingGlobalMaxValue | Int, standardvärdet är 0 |Dynamisk|Maximal antalet i skapa repliker tillåts globalt. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, standard är FALSKT | Dynamisk|Anger om alla typer av redundans enhet uppdatering ska avbryta snabb eller långsam belastningsutjämning kör. Anges med ”false” belastningsutjämning kör avbryts om FailoverUnit: skapade/utgå. Det saknas replikeringar; Ändra plats för primära repliken eller ändrade antal repliker. Belastningsutjämning kör inte avbryts i andra fall - om FailoverUnit: har extra repliker; Ändra flaggan valfri replik; Ändra bara partition versionen eller annan. |
|MinConstraintCheckInterval | ClusterX509FindValueSecondary |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta mängden tid som måste passera innan två på varandra följande begränsningen Kontrollera Avrundar. |
|MinLoadBalancingInterval | Tid i sekunder, standardvärdet är 5 |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta mängden tid som måste passera innan två på varandra följande belastningsutjämning Avrundar. |
|MinPlacementInterval | ClusterX509FindValueSecondary |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta mängden tid som måste passera innan två på varandra följande placering Avrundar. |
|MoveExistingReplicaForPlacement | Bool, standard är SANT |Dynamisk|Ange som bestämmer om att flytta befintliga repliken under placeringen. |
|MovementPerPartitionThrottleCountingInterval | Tid i sekunder, standardvärdet är 600 |Statisk| Ange tidsintervall i sekunder. Ange längden på den senaste intervall som du vill spåra repliken förflyttningar för varje partition (används tillsammans med MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, standardvärdet är 50 |Dynamisk| Ingen belastningsutjämning-relaterade förflyttning utförs för en partition om antalet belastningsutjämning relaterade förflyttningar för repliker av den partitionen har nått eller överskridit MovementPerFailoverUnitThrottleThreshold under den senaste tidsperioden som anges av MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, standard är FALSKT |Dynamisk| Inställning som avgör om överordnade repliker kan flyttas till åtgärda tillhörighet begränsningar.|
|PartiallyPlaceServices | Bool, standard är SANT |Dynamisk| Anger om alla repliker som tjänst i klustret ska placeras ”allt eller inget” angivna begränsad lämplig noder för de.|
|PlaceChildWithoutParent | Bool, standard är SANT | Dynamisk|Inställning som bestämmer om underordnade tjänsten repliken kan placeras om ingen överordnad replik är igång. |
|PlacementConstraintPriority | Int, standardvärdet är 0 | Dynamisk|Anger prioriteten för placering av begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|PlacementConstraintValidationCacheSize | Int, standardvärdet är 10 000 |Dynamisk| Begränsar storleken på tabellen används för snabb verifiering och cachelagring av placering begränsningsuttryck. |
|PlacementSearchTimeout | Tid i sekunder, standardvärdet är 0,5 |Dynamisk| Ange tidsintervall i sekunder. När du monterar tjänster. Sök efter högst i långa innan resultatet returneras. |
|PLBRefreshGap | ClusterX509FindValueSecondary |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta mängden tid som måste passera innan PLB uppdaterar tillstånd igen. |
|PreferredLocationConstraintPriority | Int, standardvärdet är 2| Dynamisk|Anger prioriteten för önskade platsbegränsningen: 0: hårda; 1: mjuk; 2: optimering; negativt: ignorera |
|PreventTransientOvercommit | Bool, standard är FALSKT | Dynamisk|Anger bör PLB omedelbart kan räkna med resurser som ska frigöras genom initierad flyttar. Som standard. PLB kan initiera flytta bort och flytta i på samma nod som kan skapa tillfälliga överanstränga. Ställa in den här parametern SANT förhindrar dessa typer av overcommits och på begäran defragmentering (även kallat placementWithMove) kommer att inaktiveras. |
|ScaleoutCountConstraintPriority | Int, standardvärdet är 0 |Dynamisk| Anger prioriteten för skalning antal begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|SwapPrimaryThrottlingAssociatedMetric | sträng, standardvärdet är ””|Statisk| Det associerade måttnamnet för den här begränsningen. |
|SwapPrimaryThrottlingEnabled | Bool, standard är FALSKT|Dynamisk| Avgör om swap-primary-begränsning har aktiverats. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, standardvärdet är 0 |Dynamisk| Maximal antalet swap-primära repliker tillåts globalt. |
|TraceCRMReasons |Bool, standard är SANT |Dynamisk|Anger om du vill spåra orsakerna till CRM utfärdat förflyttningar till kanalen operativa händelser. |
|UpgradeDomainConstraintPriority | Int, standardvärdet är 1| Dynamisk|Anger prioriteten för uppgraderingsdomän begränsning: 0: hårda; 1: mjuk; negativt: ignorera. |
|UseMoveCostReports | Bool, standard är FALSKT | Dynamisk|Instruerar LB att ignorera kostnadselement bedömnings fungerande. resulterande potentiellt stort antal flyttar för bättre belastningsutjämnade placering. |
|UseSeparateSecondaryLoad | Bool, standard är SANT | Dynamisk|Inställning som anger om använder olika sekundära belastningen. |
|ValidatePlacementConstraint | Bool, standard är SANT |Dynamisk| Anger huruvida PlacementConstraint-uttrycket för en tjänst har verifierats när en tjänst ServiceDescription uppdateras. |
|VerboseHealthReportLimit | Int, standardvärdet är 20 | Dynamisk|Definierar hur många gånger som en replik måste gå Ej placerade innan en varning om hälsotillståndet rapporteras för det (om utförlig tillståndsrapportering har aktiverats). |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Tid i sekunder, standardvärdet är 900 |Dynamisk|Ange tidsintervall i sekunder. Den tid som systemet väntar innan försöket avbryts värdar för tjänster som har repliker som har fastnat i Stäng under uppgradering av programmet.|
|FabricUpgradeMaxReplicaCloseDuration | Tid i sekunder, standardvärdet är 900 |Dynamisk| Ange tidsintervall i sekunder. Den tid som systemet väntar innan försöket avbryts värdar för tjänster som har repliker som har fastnat i Stäng under uppgraderingen av fabric. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(120)|Dynamisk|Ange tidsintervall i sekunder. Den tid som systemet väntar innan försöket avbryts värdar för tjänster som har repliker som har fastnat i Stäng. Om det här värdet anges till 0, kommer inte repliker instrueras att Stäng.|
|NodeDeactivationMaxReplicaCloseDuration | Tid i sekunder, standardvärdet är 900 |Dynamisk|Ange tidsintervall i sekunder. Den tid som systemet väntar innan försöket avbryts värdar för tjänster som har repliker som har fastnat i Stäng under noden inaktivering. |
|PeriodicApiSlowTraceInterval | Tid i sekunder, standardvärdet är 5 minuter |Dynamisk| Ange tidsintervall i sekunder. PeriodicApiSlowTraceInterval definierar intervallet över vilket långsam API-anrop ska fångas av API-Övervakare. |
|ReplicaChangeRoleFailureRestartThreshold|int, standardvärdet är 10|Dynamisk| Heltal. Ange antalet API-fel under primära befordran efter vilken åtgärd för automatisk riskreducering (replik omstart) kommer att tillämpas. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, standard är 2147483647|Dynamisk| Heltal. Ange antalet API-fel under primära befordran efter vilken varning hälsorapport aktiveras.|
|ServiceApiHealthDuration | Tid i sekunder, standardvärdet är 30 minuter |Dynamisk| Ange tidsintervall i sekunder. ServiceApiHealthDuration definierar hur länge vi väntar ett API ska köras innan vi rapporterar det feltillstånd. |
|ServiceReconfigurationApiHealthDuration | Tid i sekunder, standardvärdet är 30 |Dynamisk| Ange tidsintervall i sekunder. ServiceReconfigurationApiHealthDuration definierar hur länge vi väntar ett API ska köras innan vi rapporterar defekta. Detta gäller för API-anrop som påverkar tillgänglighet.|

## <a name="replication"></a>Replikering
| **Parametern** | **Tillåtna värden** | **Uppgradera princip**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, standardvärdet är Common::TimeSpan::FromMilliseconds(15)|Statisk|Ange tidsintervall i sekunder. Anger hur lång tid som replikatorn väntar efter att ha fått en åtgärd innan du skickar tillbaka en bekräftelse. Andra åtgärder som togs emot under denna tidsperiod kommer att ha sina bekräftelser skickas tillbaka i ett enda meddelande -> minskar nätverkstrafiken men potentiellt minska genomflödet av replikatorn.|
|MaxCopyQueueSize|uint, standard är 1024|Statisk|Detta är maximalt värdet definierar den ursprungliga storleken för kön som innehåller replikeringsåtgärder. Observera att det måste vara delbart med 2. Om åtgärden kommer att begränsas mellan primära och sekundära replikatörer under körning kön växer till den här storleken.|
|MaxPrimaryReplicationQueueMemorySize|uint, standardvärdet är 0|Statisk|Det här är det högsta värdet för den primära Replikeringskön i byte.|
|MaxPrimaryReplicationQueueSize|uint, standard är 1024|Statisk|Det här är det maximala antalet åtgärder som kan finnas i den primära Replikeringskön. Observera att det måste vara delbart med 2.|
|MaxReplicationMessageSize|uint, standard är 52428800|Statisk|Maximal meddelandestorlek på replikeringsåtgärder. Standardvärdet är 50MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, standardvärdet är 0|Statisk|Det här är det högsta värdet för den sekundära Replikeringskön i byte.|
|MaxSecondaryReplicationQueueSize|uint, standard är 2048|Statisk|Det här är det maximala antalet åtgärder som kan finnas i sekundära Replikeringskön. Observera att det måste vara delbart med 2.|
|QueueHealthMonitoringInterval|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(30)|Statisk|Ange tidsintervall i sekunder. Det här värdet anger hur lång tid som används av replikatorn för att övervaka alla varning hälsohändelser i replikeringsköer för åtgärden. Värdet '0' inaktiverar hälsoövervakning |
|QueueHealthWarningAtUsagePercent|uint, standard är 80|Statisk|Det här värdet fastställer den användning av replikeringskö (i procent) efter vilken vi rapporterar varning om hög kö användning. Vi kan göra det när du har en respitperiod intervall på QueueHealthMonitoringInterval. Om användning av replikeringskö hamnar under denna procentandel i grace-intervall|
|ReplicatorAddress|sträng, standard är L ”localhost:0”|Statisk|Slutpunkt i form av en sträng-'IP:Port' som används av Windows Fabric replikatorn för att upprätta anslutningar med andra repliker för att kunna skicka och ta emot åtgärder.|
|ReplicatorListenAddress|sträng, standard är L ”localhost:0”|Statisk|Slutpunkt i form av en sträng-'IP:Port' som används av Windows Fabric replikatorn för att ta emot från andra repliker.|
|ReplicatorPublishAddress|sträng, standard är L ”localhost:0”|Statisk|Slutpunkt i form av en sträng-'IP:Port' som används av Windows Fabric replikatorn skicka åtgärder till andra repliker.|
|RetryInterval|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(5)|Statisk|Ange tidsintervall i sekunder. När en åtgärd går förlorad eller avvisas timern anger du hur ofta replikatorn försöker skicka om åtgärden.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parametern** | **Tillåtna värden** | **Uppgradera princip**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|IsEnabled|bool, standard är FALSKT |Statisk|Styr om tjänsten är aktiverad i klustret eller inte. |

## <a name="runas"></a>RunAs
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, standardvärdet är ”” |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för ”domänanvändare” eller ”ManagedServiceAccount” typen. Giltiga värden är ”domän\användare” eller ”user@domain”. |
|RunAsAccountType|sträng, standardvärdet är ”” |Dynamisk|Anger typ av RunAs-konto. Detta krävs för alla RunAs-avsnittet, giltiga värden är ”domänanvändare/NetworkService/ManagedServiceAccount/LocalSystem”.|
|Lösenord|sträng, standardvärdet är ”” |Dynamisk|Anger lösenordet för RunAs-konto. Detta krävs endast för typen ”domänanvändare”. |

## <a name="runasdca"></a>RunAs_DCA
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, standardvärdet är ”” |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för ”domänanvändare” eller ”ManagedServiceAccount” typen. Giltiga värden är ”domän\användare” eller ”user@domain”. |
|RunAsAccountType|sträng, standardvärdet är ”” |Dynamisk|Anger typ av RunAs-konto. Detta krävs för alla RunAs-avsnittet, giltiga värden är ”Lokalanvändare/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”. |
|Lösenord|sträng, standardvärdet är ”” |Dynamisk|Anger lösenordet för RunAs-konto. Detta krävs endast för typen ”domänanvändare”. |

## <a name="runasfabric"></a>RunAs_Fabric
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, standardvärdet är ”” |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för ”domänanvändare” eller ”ManagedServiceAccount” typen. Giltiga värden är ”domän\användare” eller ”user@domain”. |
|RunAsAccountType|sträng, standardvärdet är ”” |Dynamisk|Anger typ av RunAs-konto. Detta krävs för alla RunAs-avsnittet, giltiga värden är ”Lokalanvändare/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”. |
|Lösenord|sträng, standardvärdet är ”” |Dynamisk|Anger lösenordet för RunAs-konto. Detta krävs endast för typen ”domänanvändare”. |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, standardvärdet är ”” |Dynamisk|Anger namnet på RunAs-kontot. Detta krävs endast för ”domänanvändare” eller ”ManagedServiceAccount” typen. Giltiga värden är ”domän\användare” eller ”user@domain”. |
|RunAsAccountType|sträng, standardvärdet är ”” |Dynamisk|Anger typ av RunAs-konto. Detta krävs för alla RunAs-avsnittet, giltiga värden är ”Lokalanvändare/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”. |
|Lösenord|sträng, standardvärdet är ”” |Dynamisk|Anger lösenordet för RunAs-konto. Detta krävs endast för typen ”domänanvändare”. |

## <a name="security"></a>Säkerhet
| **Parametern** | **Tillåtna värden** |**Uppgradera princip**| **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|AADClientApplication|sträng, standard är L ””|Statisk|Native Client-programnamn eller ID som representerar Fabric klienter |
|AADClusterApplication|sträng, standard är L ””|Statisk|Webb-API-programnamn eller ID som representerar klustret |
|AADTenantId|sträng, standard är L ””|Statisk|Klient-ID (GUID) |
|AdminClientCertThumbprints|sträng, standard är L ””|Dynamisk|Tumavtryck för certifikat som används av klienter i rollen som administratör. Det är en kommaavgränsad lista med namn på. |
|AdminClientClaims|sträng, standard är L ””|Dynamisk|Alla möjliga anspråk förväntades från admin-klienter. samma format som ClientClaims; den här listan läggs internt till ClientClaims; så du behöver inte att lägga till samma poster till ClientClaims. |
|AdminClientIdentities|sträng, standard är L ””|Dynamisk|Windows-identiteter för fabric klienter administratörsroll; används för att auktorisera Privilegierade fabric åtgärder. Det är en kommaavgränsad lista. varje post är ett Domänkontonamn eller gruppnamn. För att underlätta; det konto som kör fabric.exe tilldelas automatiskt administratörsroll; så är gruppera ServiceFabricAdministrators. |
|CertificateExpirySafetyMargin|TimeSpan, standardvärdet är Common::TimeSpan::FromMinutes(43200)|Statisk|Ange tidsintervall i sekunder. Säkerhetsmarginal för förfallodatum för certifikat; certifikatet hälsotillstånd Rapportstatus ändras till varning från OK när förfallotid är närmare än så. Standardvärdet är 30 dagar. |
|CertificateHealthReportingInterval|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(3600 * 8)|Statisk|Ange tidsintervall i sekunder. Ange intervall för certifikatet tillståndsrapportering; standard till åtta timmar. inställningen till 0 inaktiverar certifikat tillståndsrapportering |
|ClientCertThumbprints|sträng, standard är L ””|Dynamisk|Tumavtryck för certifikat som används av klienter för att kommunicera med klusternoderna. klustret använder det här auktorisera inkommande anslutning. Det är en kommaavgränsad lista med namn på. |
|ClientClaimAuthEnabled|bool, standard är FALSKT|Statisk|Anger om Anspråksbaserad autentisering har aktiverats på klienter. Om du anger detta SANT implicit anger ClientRoleEnabled. |
|ClientClaims|sträng, standard är L ””|Dynamisk|Alla möjliga anspråk förväntades från klienter för att ansluta till gatewayen. Detta är en ”OR”-lista: ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... varje ClaimsEntry är en ”AND”-lista: ClaimType = ClaimValue & & ClaimType = ClaimValue & & ClaimType = ClaimValue... |
|ClientIdentities|sträng, standard är L ””|Dynamisk|Windows-identiteter för FabricClient; Naming gateway använder dem för att tillåta inkommande anslutningar. Det är en kommaavgränsad lista. varje post är ett Domänkontonamn eller gruppnamn. För att underlätta; det konto som kör fabric.exe tillåts automatiskt; så är grupp ServiceFabricAllowedUsers och ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, standard är FALSKT|Statisk|Anger om klientrollen är aktiverad; Om värdet är true; klienter tilldelas roller baserat på deras identiteter. För V2; Aktivera det innebär att klienten inte i AdminClientCommonNames/AdminClientIdentities kan endast köra skrivskyddade åtgärder. |
|ClusterCertThumbprints|sträng, standard är L ””|Dynamisk|Tumavtryck för certifikat som tillåts att ansluta klusternoderna. en namnlista med kommaavgränsade. |
|ClusterCredentialType|sträng, standard är L ”None”|Inte tillåten|Anger vilken typ av säkerhetsautentiseringsuppgifter som ska användas för att skydda klustret. Giltiga värden är ”ingen/X509/Windows” |
|ClusterIdentities|sträng, standard är L ””|Dynamisk|Windows-identiteter för noder i klustret. används för klustret medlemskap auktorisering. Det är en kommaavgränsad lista. varje post är ett Domänkontonamn eller gruppnamn |
|ClusterSpn|sträng, standard är L ””|Inte tillåten|Tjänstens huvudnamn klusternoderna. När infrastrukturen körs som en enda domänanvändare (gMSA/domän-användarkonto). Det är SPN för lånet lyssnare och lyssnare i fabric.exe: federation lyssnare; intern replikeringsprocedur lyssnare; Runtime-tjänsten lyssnare och naming gateway-lyssnare. Detta bör lämnas tom när fabric körs som datorkonton; i så fall ansluter sida compute-lyssnaren SPN från lyssnare transport-adress. |
|CrlCheckingFlag|uint, standard är 0x40000000|Dynamisk|Standard certifikatkedja verifiering flaggan; kan åsidosättas av komponentspecifika flaggan; t.ex. Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ ENDAST inställningen till 0 inaktiveras listan över återkallade certifikat kontrollerar fullständig lista över värden som stöds är dokumenterats av dwFlags av CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, standardvärdet är Common::TimeSpan::FromMinutes(15)|Dynamisk|Ange tidsintervall i sekunder. Hur länge CRL-kontroll är inaktiverad för ett visst certifikat efter offline fel; Om listan över återkallade certifikat offline fel kan ignoreras. |
|CrlOfflineHealthReportTtl|TimeSpan, standardvärdet är Common::TimeSpan::FromMinutes(1440)|Dynamisk|Ange tidsintervall i sekunder. |
|DisableFirewallRuleForDomainProfile| bool, standard är SANT |Statisk| Anger om brandväggsregel inte ska vara aktiverad för domänprofilen |
|DisableFirewallRuleForPrivateProfile| bool, standard är SANT |Statisk| Anger om brandväggsregel inte ska vara aktiverad för privat profil | 
|DisableFirewallRuleForPublicProfile| bool, standard är SANT | Statisk|Anger om brandväggsregel inte ska vara aktiverad för offentliga profil |
|FabricHostSpn| sträng, standard är L ”” |Statisk| Tjänstens huvudnamn FabricHost; När infrastrukturen körs som en enda domänanvändare (gMSA/domän-användarkonto) och FabricHost körs under datorkontot. Det är SPN IPC-lyssnaren för FabricHost; som standard bör lämnas tom eftersom FabricHost körs under datorkontot |
|IgnoreCrlOfflineError|bool, standard är FALSKT|Dynamisk|Om du vill ignorera CRL offline fel när serversidan verifierar inkommande klientcertifikat |
|IgnoreSvrCrlOfflineError|bool, standard är SANT|Dynamisk|Om du vill ignorera CRL offline fel när klienten verifierar inkommande servercertifikat; som standard true. Attacker med återkallade servercertifikat kräver att kompromissa med DNS; svårare än med återkallade klientcertifikat. |
|ServerAuthCredentialType|sträng, standard är L ”None”|Statisk|Anger vilken typ av säkerhetsautentiseringsuppgifter som ska användas för att säkra kommunikationen mellan FabricClient och klustret. Giltiga värden är ”ingen/X509/Windows” |
|ServerCertThumbprints|sträng, standard är L ””|Dynamisk|Tumavtryck för servercertifikat som används av klustret för att kommunicera med klienter. klienter använder detta för att autentisera klustret. Det är en kommaavgränsad lista med namn på. |
|SettingsX509StoreName| sträng, standard är L ”MY”| Dynamisk|X509 certifikatarkivet som används av infrastruktur för skyddskonfigurationen |
|UseClusterCertForIpcServerTlsSecurity|bool, standard är FALSKT|Statisk|Om du vill använda klustercertifikatet för att skydda IPC Server TLS transport enhet |
|X509Folder|sträng, standard är /var/lib/waagent|Statisk|Mapp där X509 certifikat och privata nycklar finns |

## <a name="securityadminclientx509names"></a>Säkerhet/AdminClientX509Names
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standardvärdet är ingen|Dynamisk| |

## <a name="securityclientaccess"></a>Säkerhet/ClientAccess
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ActivateNode |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för aktivering av en nod. |
|CancelTestCommand |sträng, standard är ”Admin” |Dynamisk| Avbryter en specifik TestCommand - om det är under flygning. |
|CodePackageControl |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att starta om kodpaket. |
|CreateApplication |sträng, standard är ”Admin” | Dynamisk|Konfiguration för skapande av programmet. |
|CreateComposeDeployment|sträng, standard är L ”Admin”| Dynamisk|Skapar en Skriv-distribution som beskrivs av Skriv filer |
|CreateName |sträng, standard är ”Admin” |Dynamisk|Konfiguration för skapande av namngivnings-URI. |
|CreateService |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att skapa tjänster. |
|CreateServiceFromTemplate |sträng, standard är ”Admin” |Dynamisk|Konfiguration för att skapa tjänster från mallen. |
|DeactivateNode |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för en nod. |
|DeactivateNodesBatch |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för inaktivering av flera noder. |
|Ta bort |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfigurationer för avbildningen lagra ta bort klientåtgärden. |
|DeleteApplication |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för borttagning av programmet. |
|DeleteComposeDeployment|sträng, standard är L ”Admin”| Dynamisk|Tar bort compose-distributionen |
|DeleteName |sträng, standard är ”Admin” |Dynamisk|Säkerhetskonfiguration för borttagning av namngivnings-URI. |
|DeleteService |sträng, standard är ”Admin” |Dynamisk|Säkerhetskonfiguration för borttagning av. |
|EnumerateProperties |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Säkerhetskonfiguration för namngivning av uppräkning. |
|EnumerateSubnames |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för namngivnings-URI-uppräkningen. |
|FileContent |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för avbildningen lagra klienten filöverföring (extern till klustret). |
|FileDownload |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för avbildning store klienten filen download motivera (extern till klustret). |
|FinishInfrastructureTask |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för infrastruktursuppgifter som slutförs. |
|GetChaosReport | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Hämtar status för Chaos inom ett visst tidsintervall. |
|GetClusterConfiguration | sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Startar GetClusterConfiguration på en partition. |
|GetClusterConfigurationUpgradeStatus | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Startar GetClusterConfigurationUpgradeStatus på en partition. |
|GetFabricUpgradeStatus |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för avsökning av uppgraderingsstatus för klustret. |
|GetNodeDeactivationStatus |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att kontrollera inaktiveringsstatus. |
|GetNodeTransitionProgress | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Konfiguration för att hämta pågår på en övergång node-kommandot. |
|GetPartitionDataLossProgress | sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Hämtar förloppet för ett api-anrop invoke data går förlorade. |
|GetPartitionQuorumLossProgress | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Hämtar förloppet för ett invoke kvorum förlust api-anrop. |
|GetPartitionRestartProgress | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Hämtar förloppet för en api-anrop för omstart av partition. |
|GetServiceDescription |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för långa avsökning tjänstmeddelanden och läsa tjänstbeskrivningar. |
|GetStagingLocation |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för avbildningen lagra klienten mellanlagring plats hämtning. |
|GetStoreLocation |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för avbildningen lagra klienten store plats hämtning. |
|GetUpgradeOrchestrationServiceState|sträng, standard är L ”Admin”| Dynamisk|Startar GetUpgradeOrchestrationServiceState på en partition |
|GetUpgradesPendingApproval |sträng, standard är ”Admin” |Dynamisk| Startar GetUpgradesPendingApproval på en partition. |
|GetUpgradeStatus |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för avsökning av status för uppgraderingen. |
|InternalList |sträng, standard är ”Admin” | Dynamisk|Säkerhetskonfiguration för avbildningen lagra filen listan klientåtgärden (internt). |
|InvokeContainerApi|wstring, standard är L ”Admin”|Dynamisk|Anropa API för behållare |
|InvokeInfrastructureCommand |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för kommandon för hantering av infrastruktur-uppgiften. |
|InvokeInfrastructureQuery |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Säkerhetskonfiguration för frågor till infrastrukturen. |
|Visa lista |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Säkerhetskonfiguration för avbildningen lagra klientåtgärden fil lista. |
|MoveNextFabricUpgradeDomain |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att kunna återuppta klusteruppgradering med en explicit Uppgraderingsdomänen. |
|MoveNextUpgradeDomain |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att kunna återuppta programuppgraderingar med en explicit uppgradera domän. |
|MoveReplicaControl |sträng, standard är ”Admin” | Dynamisk|Flytta replik. |
|NameExists |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Säkerhetskonfiguration om namngivnings-URI finns kontrollerar. |
|NodeControl |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att starta; Stoppa; och starta om noderna. |
|NodeStateRemoved |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för rapportering Nodtillstånd tas bort. |
|Pinga |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för klienten ping. |
|PredeployPackageToNode |sträng, standard är ”Admin” |Dynamisk| Fördistribution api. |
|PrefixResolveService |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för matchning av klagomål-baserad tjänst prefix. |
|PropertyReadBatch |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för namngivning av egenskapen läsåtgärder. |
|PropertyWriteBatch |sträng, standard är ”Admin” |Dynamisk|Säkerhetskonfigurationer för namngivning av egenskapen skrivåtgärder. |
|ProvisionApplicationType |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för programetablering typen. |
|ProvisionFabric |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för MSI och/eller kluster Manifest etablering. |
|Fråga |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för frågor. |
|RecoverPartition |sträng, standard är ”Admin” | Dynamisk|Säkerhetskonfiguration för återställning av en partition. |
|RecoverPartitions |sträng, standard är ”Admin” | Dynamisk|Säkerhetskonfiguration för återställning av partitioner. |
|RecoverServicePartitions |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att återställa tjänstpartitioner. |
|RecoverSystemPartitions |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att återställa systempartitioner för tjänsten. |
|RemoveNodeDeactivations |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för återställer inaktivering på flera noder. |
|ReportFabricUpgradeHealth |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att kunna återuppta klusteruppgradering med aktuella uppgraderingsförloppet. |
|ReportFault |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration som rapporterade fel. |
|ReportHealth |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för rapportering hälsotillstånd. |
|ReportUpgradeHealth |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att kunna återuppta programuppgraderingar med aktuella uppgraderingsförloppet. |
|ResetPartitionLoad |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för återställning för en failoverUnit. |
|ResolveNameOwner |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Konfiguration för att lösa namngivnings-URI-ägare. |
|ResolvePartition |sträng, standardvärdet är ”Admin\|\|användare” | Dynamisk|Konfiguration för att lösa systemtjänster. |
|ResolveService |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för matchning av klagomål-baserad tjänst. |
|ResolveSystemService|sträng, standard är L ”Admin\|\|användare”|Dynamisk| Konfiguration för att lösa systemtjänster |
|RollbackApplicationUpgrade |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för återställning av programuppgraderingar. |
|RollbackFabricUpgrade |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för tillbaka löpande klusteruppgradering. |
|ServiceNotifications |sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för händelsebaserad tjänstmeddelanden. |
|SetUpgradeOrchestrationServiceState|sträng, standard är L ”Admin”| Dynamisk|Startar SetUpgradeOrchestrationServiceState på en partition |
|StartApprovedUpgrades |sträng, standard är ”Admin” |Dynamisk| Startar StartApprovedUpgrades på en partition. |
|StartChaos |sträng, standard är ”Admin” |Dynamisk| Startar Chaos - om det inte redan startats. |
|StartClusterConfigurationUpgrade |sträng, standard är ”Admin” |Dynamisk| Startar StartClusterConfigurationUpgrade på en partition. |
|StartInfrastructureTask |sträng, standard är ”Admin” | Dynamisk|Konfiguration för att starta infrastrukturen. |
|StartNodeTransition |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att starta en nodövergång. |
|StartPartitionDataLoss |sträng, standard är ”Admin” |Dynamisk| Startar förlust av data på en partition. |
|StartPartitionQuorumLoss |sträng, standard är ”Admin” |Dynamisk| Startar förlorar kvorum på en partition. |
|StartPartitionRestart |sträng, standard är ”Admin” |Dynamisk| Samtidigt startar du om några eller alla replikerna för en partition. |
|StopChaos |sträng, standard är ”Admin” |Dynamisk| Stoppar Chaos - om den har startats. |
|ToggleVerboseServicePlacementHealthReporting | sträng, standardvärdet är ”Admin\|\|användare” |Dynamisk| Säkerhetskonfiguration för växla utförlig ServicePlacement HealthReporting. |
|UnprovisionApplicationType |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för programmet avetableringen av typen. |
|UnprovisionFabric |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för MSI och/eller kluster Manifest avetableringen av. |
|UnreliableTransportControl |sträng, standard är ”Admin” |Dynamisk| Otillförlitliga Transport för att lägga till och ta bort beteenden. |
|UpdateService |sträng, standard är ”Admin” |Dynamisk|Säkerhetskonfiguration för uppdateringar av tjänsten. |
|UpgradeApplication |sträng, standard är ”Admin” |Dynamisk| Säkerhetskonfiguration för startar eller att avbryta programuppgraderingar. |
|UpgradeComposeDeployment|sträng, standard är L ”Admin”| Dynamisk|Uppgraderar compose-distributionen |
|UpgradeFabric |sträng, standard är ”Admin” |Dynamisk| Konfiguration för att starta klusteruppgradering. |
|Ladda upp |sträng, standard är ”Admin” | Dynamisk|Säkerhetskonfiguration för avbildningen lagra uppladdning klientåtgärden. |

## <a name="securityclientcertificateissuerstores"></a>Säkerhet/ClientCertificateIssuerStores
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standardvärdet är ingen |Dynamisk|X509 utfärdarcertifikatet lagrar för klientcertifikat; Namn = clientIssuerCN; Värde = kommaavgränsad lista över butiker |

## <a name="securityclientx509names"></a>Säkerhet/ClientX509Names
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standardvärdet är ingen|Dynamisk| |

## <a name="securityclustercertificateissuerstores"></a>Säkerhet/ClusterCertificateIssuerStores
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standardvärdet är ingen |Dynamisk|X509 utfärdarcertifikatet lagrar för klustercertifikat; Namn = clusterIssuerCN; Värde = kommaavgränsad lista över butiker |

## <a name="securityclusterx509names"></a>Säkerhet/ClusterX509Names
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standardvärdet är ingen|Dynamisk| |

## <a name="securityservercertificateissuerstores"></a>Säkerhet/ServerCertificateIssuerStores
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, standardvärdet är ingen |Dynamisk|X509 utfärdarcertifikatet lagrar för servercertifikat; Namn = serverIssuerCN; Värde = kommaavgränsad lista över butiker |

## <a name="securityserverx509names"></a>Säkerhet/ServerX509Names
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, standardvärdet är ingen|Dynamisk| |

## <a name="setup"></a>Konfiguration
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ContainerNetworkName|sträng, standard är L ””| Statisk |Nätverksnamnet på ska användas när du konfigurerar en behållare nätverk.|
|ContainerNetworkSetup|bool, standard är FALSKT| Statisk |Om du vill konfigurera en behållare nätverk.|
|FabricDataRoot |Sträng | Inte tillåten |Rotkatalogen för Service Fabric-data. Standard för Azure är d:\svcfab |
|FabricLogRoot |Sträng | Inte tillåten |Rotkatalogen för Service fabric log. Detta är SF loggar och spårningar placering. |
|NodesToBeRemoved|sträng, standardvärdet är ””| Dynamisk |Noder som ska tas bort som en del av konfigurationen uppgraderingen. (Endast för distributioner av fristående)|
|ServiceRunAsAccountName |Sträng | Inte tillåten |Företagsnamnet under som du vill köra fabric värdtjänsten. |
|SkipContainerNetworkResetOnReboot|bool, standard är FALSKT|NotAllowed|Om du vill hoppa över återställning behållare nätverk vid omstart.|
|SkipFirewallConfiguration |Bool, standard är FALSKT | Inte tillåten |Anger om brandväggsinställningar måste anges av systemet eller inte. Detta gäller endast om du använder windows-brandväggen. Om du använder brandväggar från tredje part, måste du öppna portarna för system och program för att använda |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|Leverantörer |sträng, standard är ”DSTS” |Statisk|Kommaavgränsad lista över tokenvalidering leverantörer att aktivera (giltigt providers är: DSTS; AAD). För närvarande endast en enskild provider kan aktiveras när som helst. |

## <a name="traceetw"></a>Spårningen/Etw
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|Nivå |Int, standardvärdet är 4 | Dynamisk |Spårningsnivån som etw kan ha värden 1, 2, 3, 4. Att stödjas du måste hålla spårningsnivån vid 4 |

## <a name="transactionalreplicator"></a>TransactionalReplicator
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Tid i sekunder, är standardvärdet 0.015 | Statisk | Ange tidsintervall i sekunder. Anger hur lång tid som replikatorn väntar efter att ha fått en åtgärd innan du skickar tillbaka en bekräftelse. Andra åtgärder som togs emot under denna tidsperiod kommer att ha sina bekräftelser skickas tillbaka i ett enda meddelande -> minskar nätverkstrafiken men potentiellt minska genomflödet av replikatorn. |
|MaxCopyQueueSize |Uint, standardvärdet är 16384 | Statisk |Detta är maximalt värdet definierar den ursprungliga storleken för kön som innehåller replikeringsåtgärder. Observera att det måste vara delbart med 2. Om åtgärden kommer att begränsas mellan primära och sekundära replikatörer under körning kön växer till den här storleken. |
|MaxPrimaryReplicationQueueMemorySize |Uint, standardvärdet är 0 | Statisk |Det här är det högsta värdet för den primära Replikeringskön i byte. |
|MaxPrimaryReplicationQueueSize |Uint, standardvärdet är 8192 | Statisk |Det här är det maximala antalet åtgärder som kan finnas i den primära Replikeringskön. Observera att det måste vara delbart med 2. |
|MaxReplicationMessageSize |Uint, standardvärdet är 52428800 | Statisk | Maximal meddelandestorlek på replikeringsåtgärder. Standardvärdet är 50MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, standardvärdet är 0 | Statisk |Det här är det högsta värdet för den sekundära Replikeringskön i byte. |
|MaxSecondaryReplicationQueueSize |Uint, standardvärdet är 16384 | Statisk |Det här är det maximala antalet åtgärder som kan finnas i sekundära Replikeringskön. Observera att det måste vara delbart med 2. |
|ReplicatorAddress |sträng, standard är ”localhost:0” | Statisk | Slutpunkt i form av en sträng-'IP:Port' som används av Windows Fabric replikatorn för att upprätta anslutningar med andra repliker för att kunna skicka och ta emot åtgärder. |

## <a name="transport"></a>Transport
| **Parametern** | **Tillåtna värden** |**Uppgradera princip** |**Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, standardvärdet är Common::TimeSpan::FromSeconds(60)|Statisk|Ange tidsintervall i sekunder. Timeout för anslutning inställningen på både inkommande och tar emot sida (inklusive förhandling i skyddat läge) |
|ResolveOption|sträng, standard är L ”okänt”|Statisk|Anger hur FQDN har åtgärdats.  Giltiga värden är ”Ospecificerad/ipv4/ipv6”. |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, standard är SANT |Statisk| Automatisk avsökning och uppgraderingsåtgärden som baseras på en målstatusen fil. |
|MinReplicaSetSize |Int, standardvärdet är 0 |Statisk |MinReplicaSetSize för UpgradeOrchestrationService.
|PlacementConstraints | sträng, standardvärdet är ”” |Statisk| PlacementConstraints för UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Tid i sekunder, är standardvärdet MaxValue |Statisk| Ange tidsintervall i sekunder. QuorumLossWaitDuration för UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Tid i sekunder, standardvärdet är 60 minuter|Statisk| Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för UpgradeOrchestrationService. |
|Tidsgränsen för värdtjänsten-aktivering. Inaktivering och uppgradering. | Tid i sekunder, standardvärdet är 60*24*sju minuter |Statisk| Ange tidsintervall i sekunder. StandByReplicaKeepDuration för UpgradeOrchestrationService. |
|TargetReplicaSetSize |Int, standardvärdet är 0 |Statisk |TargetReplicaSetSize för UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Bool, standard är FALSKT | Statisk|Ställa in att koden uppgradering kräver administratörsgodkännande innan du fortsätter. |

## <a name="upgradeservice"></a>UpgradeService
| **Parametern** | **Tillåtna värden** | **Uppgradera princip** | **Vägledning eller en kort beskrivning** |
| --- | --- | --- | --- |
|BaseUrl | sträng, standardvärdet är ”” |Statisk|BaseUrl för UpgradeService. |
|ClusterId | sträng, standardvärdet är ”” |Statisk|ClusterId för UpgradeService. |
|CoordinatorType | sträng, standard är ”WUTest”|Inte tillåten|CoordinatorType för UpgradeService. |
|MinReplicaSetSize | Int, standardvärdet är 2 |Inte tillåten| MinReplicaSetSize för UpgradeService. |
|OnlyBaseUpgrade | Bool, standard är FALSKT |Dynamisk|OnlyBaseUpgrade för UpgradeService. |
|PlacementConstraints |sträng, standardvärdet är ”” |Inte tillåten|PlacementConstraints för uppgradera tjänsten. |
|TargetReplicaSetSize | Int, standard är 3 |Inte tillåten| TargetReplicaSetSize för UpgradeService. |
|TestCabFolder | sträng, standardvärdet är ”” |Statisk| TestCabFolder för UpgradeService. |
|X509FindType | sträng, standardvärdet är ””|Dynamisk| X509FindType för UpgradeService. |
|X509FindValue | sträng, standardvärdet är ”” |Dynamisk| X509FindValue för UpgradeService. |
|X509SecondaryFindValue | sträng, standardvärdet är ”” |Dynamisk| X509SecondaryFindValue för UpgradeService. |
|X509StoreLocation | sträng, standardvärdet är ”” |Dynamisk| X509StoreLocation för UpgradeService. |
|X509StoreName | sträng, standardvärdet är ”My”|Dynamisk|X509StoreName för UpgradeService. |

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller mer information om hantering av:

[Lägga till, förnyas, ta bort certifikat från din Azure-kluster ](service-fabric-cluster-security-update-certs-azure.md) 

