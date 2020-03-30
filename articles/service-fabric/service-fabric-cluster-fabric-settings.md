---
title: Ändra klusterinställningar för Azure Service Fabric
description: I den här artikeln beskrivs infrastrukturinställningarna och de principer för uppgradering av infrastruktur som du kan anpassa.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: a4e64a4db70d419a3ef6441545d53abd298c85bb
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346798"
---
# <a name="customize-service-fabric-cluster-settings"></a>Anpassa Service Fabric-klusterinställningar
I den här artikeln beskrivs de olika inställningarna för infrastruktur för ditt Service Fabric-kluster som du kan anpassa. För kluster som finns i Azure kan du anpassa inställningarna via [Azure-portalen](https://portal.azure.com) eller med hjälp av en Azure Resource Manager-mall. Mer information finns i [Uppgradera konfigurationen av ett Azure-kluster](service-fabric-cluster-config-upgrade-azure.md). För fristående kluster kan du anpassa inställningarna genom att uppdatera *filen ClusterConfig.json* och utföra en konfigurationsuppgradering i klustret. Mer information finns i [Uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md).

Det finns tre olika uppgraderingsprinciper:

- **Dynamisk** – Ändringar i en dynamisk konfiguration orsakar inga processomstarter av tjänstinfrastrukturprocesser eller tjänstvärdprocesser. 
- **Statisk** – Ändringar i en statisk konfiguration gör att noden Service Fabric startas om för att ändringen ska förbrukas. Tjänster på noderna startas om.
- **Inte Tillåten** – Dessa inställningar kan inte ändras. För att ändra dessa inställningar måste klustret förstöras och ett nytt kluster skapas. 

Följande är en lista över inställningar för Fabric som du kan anpassa, ordnade efter avsnitt.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/http

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|sträng är standardvärdet "Ingen"|Statisk| Detta validerar inte servercertifikatet. efterträda begäran. Se config ServiceCertificateThumbprints för den kommaavgränsade listan över tumavtryck för fjärrfiss som den omvända proxyn kan lita på. Se config ServiceCommonNameAndIssuer för ämnesnamnet och utfärdartumavtrycket för fjärrcertifikat som den omvända proxyn kan lita på. Mer information finns i [Omvänd proxy säker anslutning](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize (BodyChunkSize) |Uint, standard är 16384 |Dynamisk| Ger storleken på för biten i byte som används för att läsa kroppen. |
|CrlCheckingFlag|uint, standard är 0x40000000 |Dynamisk| Flaggor för validering av program-/tjänstcertifikatkedjek. t.ex. CRL kontroll 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x400000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Inställning till 0 inaktiverar CRL kontroll Fullständig lista över värden som stöds dokumenteras av dwFlags av CertGetCertificateChain:https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|StandardhttpRequestTimeout |Tid på några sekunder. standard är 120 |Dynamisk|Ange tidsintervall i sekunder.  Ger standardtidsgränsen för begäran för de http-begäranden som bearbetas i http-appgatewayen. |
|FramåtClientCertificate|bool, standard är FALSKT|Dynamisk|När den är inställd på false begär inte omvänd proxy för klientcertifikatet. När den är inställd på true begär omvänd proxy för klientcertifikatet under SSL-handskakningen och vidarebefordrar den base64-kodade PEM-formatsträngen till tjänsten i ett huvud med namnet X-Client-Certificate.Tjänsten kan misslyckas med begäran med lämplig statuskod efter att ha inspekterat certifikatdata. Om detta är sant och klienten inte visar ett certifikat, kommer omvänd proxy att vidarebefordra ett tomt huvud och låta tjänsten hantera ärendet. Omvänd proxy fungerar som ett genomskinligt lager. Mer information finns i [Konfigurera autentisering av klientcertifikat](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |sträng är standardvärdet "Ingen" |Statisk| Anger vilken typ av säkerhetsautentiseringsuppgifter som ska användas vid slutpunkten för http-appgateway Giltiga värden är Ingen/X509. |
|GatewayX509CertificateFindType |sträng, är standard "FindByThumbprint" |Dynamisk| Anger hur du söker efter certifikat i det arkiv som anges av gatewayX509CertificateStoreName Stöds värde: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | sträng, är standard "" |Dynamisk| Sökfiltervärde som används för att hitta http-appgatewaycertifikatet. Det här certifikatet är konfigurerat på https-slutpunkten och kan även användas för att verifiera appens identitet om tjänsterna behöver. FindValue är uppslagna först; och om detta inte existerar; FindValueSecondary är uppslagna. |
|GatewayX509CertificateFindValueSecondary GatewayX509CertificateFindValueSecondary GatewayX509CertificateFindValueSecondary GatewayX | sträng, är standard "" |Dynamisk|Sökfiltervärde som används för att hitta http-appgatewaycertifikatet. Det här certifikatet är konfigurerat på https-slutpunkten och kan även användas för att verifiera appens identitet om tjänsterna behöver. FindValue är uppslagna först; och om detta inte existerar; FindValueSecondary är uppslagna.|
|GatewayX509CertificateStoreName |sträng är standard "Min" |Dynamisk| Namn på X.509-certifikatarkiv som innehåller certifikat för http-appgateway. |
|HttpRequestConnectTimeout|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(5)|Dynamisk|Ange tidsintervall i sekunder.  Ger tidsgränsen för anslutning för de http-begäranden som skickas från http-appgatewayen.  |
|IgnoreraCrlOfflineError|bool, standard är SANT|Dynamisk|Om du vill ignorera CRL-offlinefel för verifiering av program-/tjänstcertifikat. |
|IsEnabled |Bool, standard är falskt |Statisk| Aktiverar/inaktiverar HttpApplicationGateway. HttpApplicationGateway är inaktiverat som standard och den här konfigurationen måste ställas in för att aktivera den. |
|Antalparalleloperationer | Uint, standard är 5000 |Statisk|Antal läsningar som ska publiceras i http-serverkön. Detta styr antalet samtidiga begäranden som kan uppfyllas av HttpGateway. |
|Ta bortServiceResponseHeaders|sträng är standardvärdet "Datum; Server"|Statisk|Semi colon/ kommaavgränsad lista över svarshuvuden som ska tas bort från servicesvaret. innan du vidarebefordrar den till kunden. Om detta är inställt på tom sträng; alla rubriker som returneras av tjänsten som de är. Dvs skriv inte över datum och server |
|ResolveServiceBackoffInterval |Tid i sekunder, standard är 5 |Dynamisk|Ange tidsintervall i sekunder.  Ger standardavlägstintervallet innan du försöker utföra en misslyckad lösningsåtgärd. |
|SecureOnlyMode|bool, standard är FALSKT|Dynamisk| SecureOnlyMode: true: Reverse Proxy vidarebefordras endast till tjänster som publicerar säkra slutpunkter. false: Reverse Proxy kan vidarebefordra begäranden till säkra/osäkra slutpunkter. Mer information finns i [Omvänd markeringslogik för proxyslutpunkt](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|sträng, är standard ""|Dynamisk|Den kommaavgränsade listan med tumavtryck för fjärrcertifikat som den omvända proxyn kan lita på. Mer information finns i [Omvänd proxy säker anslutning](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/http/serviceCommonNameAndIssuer

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|X509NameMap, standard är Ingen|Dynamisk| Ämnesnamn och emittent tumavtryck för fjärrcertifikat som den omvända proxyn kan lita på. Mer information finns i [Omvänd proxy säker anslutning](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int är standard 0|Statisk|MinReplicaSetSize för BackupRestoreService |
|Placeringsspärrar|sträng, är standard ""|Statisk|  Tjänsten PlacementConstraints for BackupRestore |
|SecretEncryptionCertThumbprint|sträng, är standard ""|Dynamisk|Tumavtryck för det hemliga krypterings-X509-certifikatet |
|SecretEncryptionCertX509StoreName|sträng är standard "Min"|   Dynamisk|    Detta anger certifikatet som ska användas för kryptering och dekryptering av creds Name of X.509-certifikatarkiv som används för att kryptera dekrypteringsarkivets autentiseringsuppgifter som används av tjänsten Återställning av säkerhetskopiering |
|TargetReplicaSetSize|int är standard 0|Statisk| TargetReplicaSetSize för BackupRestoreService |

## <a name="clustermanager"></a>Klustermanager

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Bool, standard är falskt |Dynamisk|Om anpassade uppgraderingssorteringsprinciper är tillåtna eller inte. Detta används för att utföra 2-fas uppgradering aktivera den här funktionen. Service Fabric 6.5 lägger till stöd för att ange sorteringsprincip för uppgraderingsdomäner under kluster- eller programuppgraderingar. Principer som stöds är numeriska, lexikografiska, bakåtnumeriska och bakåtkolicografiska. Standard är Numeriskt. För att kunna använda den här funktionen måste klustermanifestinställningen ClusterManager/ AllowCustomUpgradeSortPolicies ställas in på True som ett andra konfigurationsuppgraderingssteg efter att SF 6.5-koden har slutfört uppgraderingen. Det är viktigt att detta görs på två faser, annars kan koduppgraderingen bli förvirrad om uppgraderingsordern under den första uppgraderingen.|
|EnableDefaultServicesUpgrade | Bool, standard är falskt |Dynamisk|Aktivera uppgradering av standardtjänster under programuppgradering. Standardtjänstbeskrivningar skrivs över efter uppgraderingen. |
|FabricUpgradeHealthCheckInterval |Tid i sekunder, standard är 60 |Dynamisk|Frekvensen av hälsostatuskontroll under en övervakad Fabric-uppgradering |
|FabricUpgradeStatusPollInterval |Tid i sekunder, standard är 60 |Dynamisk|Frekvensen för avsökning för uppgraderingsstatus för fabric. Det här värdet bestämmer uppdateringshastigheten för alla GetFabricUpgradeProgress-anrop |
|ImageBuilderTimeoutBuffer |Tid i sekunder, standard är 3 |Dynamisk|Ange tidsintervall i sekunder. Den tid som ska tillåta Image Builder specifika timeout-fel för att återgå till klienten. Om bufferten är för liten. sedan klienten timeout innan servern och får en allmän timeout fel. |
|InfrastrukturTaskHealthCheckRetryTimeout | Tid i sekunder, standard är 60 |Dynamisk|Ange tidsintervall i sekunder. Hur lång tid det tar att försöka igen misslyckades hälsokontroller när en infrastrukturaktivitet efterbehandlings. Observera en godkänd hälsokontroll kommer att återställa denna timer. |
|InfrastrukturTaskHealthCheckStableDuration | Tid i sekunder, standard är 0|Dynamisk| Ange tidsintervall i sekunder. Den tid som ska observera på varandra följande skickade hälsokontroller innan efterbearbetningen av en infrastrukturaktivitet avslutas. Observera en misslyckad hälsokontroll kommer att återställa denna timer. |
|InfrastrukturTaskHealthCheckWaitDuration |Tid i sekunder, standard är 0|Dynamisk| Ange tidsintervall i sekunder. Hur lång tid det ska ta innan hälsokontroller påbörjas efter bearbetningen av en infrastrukturaktivitet. |
|InfrastrukturTaskProcessingInterval | Tid i sekunder, standard är 10 |Dynamisk|Ange tidsintervall i sekunder. Bearbetningsintervallet som används av tillståndsdatorn för bearbetning av infrastrukturaktivitet. |
|MaxKommunikationTimeout |Tid i sekunder, standard är 600 |Dynamisk|Ange tidsintervall i sekunder. Den maximala timeouten för intern kommunikation mellan ClusterManager och andra systemtjänster (dvs. Namngivningstjänst; Redundanschef och etc.). Den här timeouten ska vara mindre än den globala MaxOperationTimeout (eftersom det kan finnas flera meddelanden mellan systemkomponenter för varje klientåtgärd). |
|MaxDataMigrationTimeout |Tid i sekunder, standard är 600 |Dynamisk|Ange tidsintervall i sekunder. Den maximala timeouten för datamigreringsåterställningsoperationer efter att en Fabric-uppgradering har ägt rum. |
|MaxOperationRetryDelay |Tid i sekunder, standard är 5|Dynamisk| Ange tidsintervall i sekunder. Den maximala fördröjningen för interna återförsök när fel påträffas. |
|MaxOperationTimeout |Tid i sekunder, standard är MaxValue |Dynamisk| Ange tidsintervall i sekunder. Den maximala globala timeouten för internt bearbetningsåtgärder på ClusterManager. |
|MaxTimeoutRetryBuffer | Tid i sekunder, standard är 600 |Dynamisk|Ange tidsintervall i sekunder. Den maximala tidsgränsen för åtgärden när den internt `<Original Time out> + <MaxTimeoutRetryBuffer>`försöker igen på grund av timeout är . Ytterligare timeout läggs till i steg om MinOperationTimeout. |
|MinOperationTimeout | Tid i sekunder, standard är 60 |Dynamisk|Ange tidsintervall i sekunder. Den minsta globala timeouten för internt bearbetningsåtgärder på ClusterManager. |
|MinReplicaSetSize |Int, standard är 3 |Inte tillåtet|MinReplicaSetSize för ClusterManager. |
|Placeringsspärrar | sträng, är standard "" |Inte tillåtet|Placeringsspärrarna för ClusterManager. |
|KvorumLossWaitDuration |Tid i sekunder, standard är MaxValue |Inte tillåtet| Ange tidsintervall i sekunder. KvorumförlustenVarvar för ClusterManager. |
|ReplicaRestartWaitDuration |Tid i sekunder, standard är (60,0 \* 30)|Inte tillåtet|Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Tid i sekunder, standard är 1200 |Dynamisk| Ange tidsintervall i sekunder. Om ReplicaSetCheckTimeout är inställt på det maximala värdet för DWORD; då är det åsidosätts med värdet av denna config för rollback. Värdet som används för roll-forward åsidosätts aldrig. |
|SkipRollbackUpdateDefaultService | Bool, standard är falskt |Dynamisk|CM hoppar över att återställa uppdaterade standardtjänster under återställningen av programuppgradering. |
|StandByReplicaKeepDuration | Tid i sekunder, standard är (3600,0 \* 2)|Inte tillåtet|Ange tidsintervall i sekunder. StandByReplicaKeepDuration för ClusterManager. |
|TargetReplicaSetSize |Int, standard är 7 |Inte tillåtet|TargetReplicaSetSize för ClusterManager. |
|UpgradeHealthCheckInterval |Tid i sekunder, standard är 60 |Dynamisk|Frekvensen av hälsostatuskontroller under en övervakad programuppgraderingar |
|UppgraderaStatusPollInterval |Tid i sekunder, standard är 60 |Dynamisk|Frekvensen för avsökning för programuppgraderingsstatus. Det här värdet bestämmer uppdateringshastigheten för alla GetApplicationUpgradeProgress-anrop |
|KomplettClientRequest | Bool, standard är falskt |Dynamisk| Fullständig klientbegäran när den accepteras av CM. |

## <a name="common"></a>Common

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Tid i sekunder, standard är 1 |Dynamisk|Ange tidsintervall i sekunder. Intervall för prestandaövervakning. Om du ställer in 0 eller negativt värde inaktiveras övervakningen. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmenteringEmptyNodeDistributionPolicy
| **Parametern** | **Tillåtna värden** |**Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|KeyIntegerValueMap, standard är Ingen|Dynamisk|Anger den principdefragmentation som följer när noder töms. För ett givet mått 0 anger att SF ska försöka defragmentera noder jämnt över UDs och FDs; 1 anger endast att noderna måste defragmenteras |

## <a name="defragmentationmetrics"></a>DefragmenteringMetri
| **Parametern** | **Tillåtna värden** |**Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|KeyBoolValueMap, standard är Ingen|Dynamisk|Bestämmer den uppsättning mått som ska användas för defragmentering och inte för belastningsutjämning. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmenteringMetricsPercentOrNumberOfEmptyNodesTriggeringTreshold
| **Parametern** | **Tillåtna värden** |**Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|KeyDoubleValueMap, standard är Ingen|Dynamisk|Bestämmer antalet lediga noder som behövs för att betrakta kluster defragmenteras genom att ange antingen procent i intervallet [0,0 - 1,0] eller antal tomma noder som antal >= 1,0 |

## <a name="diagnostics"></a>Diagnostik

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, standard är sant | Dynamisk | Uteslut HTTP-begäranden som inte påverkar klustrets tillstånd från granskning. För närvarande; Endast begäranden av typen GET är undantagna. men detta kan komma att ändras. |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, standard är sant | Dynamisk |Om personifiering krävs eller inte vid åtkomst till diagnostikarkiv för programmets räkning. |
|AppEtwTraceDeletionInDays |Int, standard är 3 | Dynamisk |Antal dagar efter vilka vi tar bort gamla ETL-filer som innehåller program ETW-spår. |
|ApplicationLogsFormatVersion |Int, standard är 0 | Dynamisk |Version för programloggar format. Värden som stöds är 0 och 1. Version 1 innehåller fler fält från ETW-händelseposten än version 0. |
|GranskninghttpRequests |Bool, standard är falskt | Dynamisk | Aktivera eller inaktivera HTTP-granskning. Syftet med granskningen är att se de aktiviteter som har utförts mot klustret. inklusive vem som initierade begäran. Observera att detta är ett bästa försök att logga; och spårförlust kan förekomma. HTTP-begäranden med "Användarautentisering" registreras inte. |
|Fångahttptlemetry|Bool, standard är sant | Dynamisk | Aktivera eller inaktivera HTTP-telemetri. Syftet med telemetri är att Service Fabric ska kunna samla in telemetridata för att planera framtida arbete och identifiera problemområden. Telemetri registrerar inga personuppgifter eller begärandeorganet. Telemetri fångar in alla HTTP-begäranden om inte annat konfigureras. |
|ClusterId (kluster) |String | Dynamisk |Klustrets unika id. Detta genereras när klustret skapas. |
|ConsumerInstances (ConsumerInstances) |String | Dynamisk |Listan över DCA-konsumentinstanser. |
|DiskFullSafetySpaceInMB |Int, standard är 1024 | Dynamisk |Återstående diskutrymme i MB för att skydda mot användning av DCA. |
|AktiveraCircularTraceSession |Bool, standard är falskt | Statisk |Flaggan anger om cirkulära spårningssessioner ska användas. |
|AktiveraPlattformEventsFileSink |Bool, standard är falskt | Statisk |Aktivera/inaktivera plattformshändelser som skrivs till disk |
|EnableTelemetry |Bool, standard är sant | Dynamisk |Detta kommer att aktivera eller inaktivera telemetri. |
|FelEn är intehttptlemetry | Bool, standard är falskt | Dynamisk | Om HTTP-telemetriinsamling är aktiverad. endast misslyckade begäranden. Detta för att minska antalet händelser som genereras för telemetri. |
|HttpTelemetryCapturePercentage | int är standard 50 | Dynamisk | Om HTTP-telemetriinsamling är aktiverad. endast en slumpmässig procentandel av begäranden. Detta för att minska antalet händelser som genereras för telemetri. |
|MaxDiskQuotaInMB |Int, standard är 65536 | Dynamisk |Diskkvot i MB för Windows Fabric-loggfiler. |
|ProducentIntances |String | Dynamisk |Listan över dca-producentinstanser. |

## <a name="dnsservice"></a>DnsService (1)
| **Parametern** | **Tillåtna värden** |**Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AktiveraPartitionedQuery|bool, standard är FALSKT|Statisk|Flaggan för att aktivera stöd för DNS-frågor för partitionerade tjänster. Funktionen är inaktiverad som standard. Mer information finns i [Service Fabric DNS Service.](service-fabric-dnsservice.md)|
|InstansCount|int är standardvärdet -1|Statisk|Standardvärdet är -1 vilket innebär att DnsService körs på varje nod. OneBox behöver detta vara inställt på 1 eftersom DnsService använder välkänd port 53, så det kan inte ha flera instanser på samma dator.|
|IsEnabled|bool, standard är FALSKT|Statisk|Aktiverar/inaktiverar DnsService. DnsService är inaktiverat som standard och den här konfigurationen måste ställas in för att aktivera den. |
|PartitionPrefix|sträng är standard "--"|Statisk|Styr partitionsprefixsträngens värde i DNS-frågor för partitionerade tjänster. Värdet: <ul><li>Bör vara RFC-kompatibel eftersom det kommer att vara en del av en DNS-fråga.</li><li>Bör inte innehålla en punkt, '.', som punkt stör DNS suffix beteende.</li><li>Bör inte vara längre än 5 tecken.</li><li>Det går inte att vara en tom sträng.</li><li>Om partitionsprefix-inställningen åsidosätts måste PartitionSuffix åsidosättas och vice versa.</li></ul>Mer information finns i [Service Fabric DNS Service.](service-fabric-dnsservice.md).|
|PartitionSuffix|sträng, är standard ""|Statisk|Styr strängvärdet för partitionssuffix i DNS-frågor för partitionerade tjänster. Värdet: <ul><li>Bör vara RFC-kompatibel eftersom det kommer att vara en del av en DNS-fråga.</li><li>Bör inte innehålla en punkt, '.', som punkt stör DNS suffix beteende.</li><li>Bör inte vara längre än 5 tecken.</li><li>Om partitionsprefix-inställningen åsidosätts måste PartitionSuffix åsidosättas och vice versa.</li></ul>Mer information finns i [Service Fabric DNS Service.](service-fabric-dnsservice.md). |

## <a name="eventstoreservice"></a>EventStoreService

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int är standard 0|Statisk|Tjänsten MinReplicaSetSize för EventStore |
|Placeringsspärrar|sträng, är standard ""|Statisk|  Tjänsten PlacementConstraints för EventStore |
|TargetReplicaSetSize|int är standard 0|Statisk| Tjänsten TargetReplicaSetSize för EventStore |

## <a name="fabricclient"></a>TygClient

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Tid för anslutninginitialiseringTimeout |Tid i sekunder, standard är 2 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för anslutning för varje gång klienten försöker öppna en anslutning till gatewayen.|
|HälsooperationTimeout |Tid i sekunder, standard är 120 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för ett rapportmeddelande som skickas till Hälsohanteraren. |
|HälsaRapporteraRetrySendInterval |Tid i sekunder, standard är 30, är minst 1 |Dynamisk|Ange tidsintervall i sekunder. Det intervall med vilket rapportkomponenten skickar in ackumulerade hälsorapporter till Health Manager. |
|HälsaRapporterSendInterval |Tid i sekunder, standard är 30 |Dynamisk|Ange tidsintervall i sekunder. Det intervall med vilket rapportkomponenten skickar ackumulerade hälsorapporter till Hälsohanteraren. |
|KeepAliveIntervalInSeconds |Int, standard är 20 |Statisk|Det intervall med vilket FabricClient-transporten skickar keep-alive-meddelanden till gatewayen. För 0; keepAlive är inaktiverat. Måste vara ett positivt värde. |
|MaxFileSenderTrådar |Uint, standard är 10 |Statisk|Det maximala antalet filer som överförs parallellt. |
|NodeAdresser |sträng, är standard "" |Statisk|En samling adresser (anslutningssträngar) på olika noder som kan användas för att kommunicera med namngivningstjänsten. Inledningsvis ansluter klienten välja en av adresserna slumpmässigt. Om mer än en anslutningssträng levereras och en anslutning misslyckas på grund av ett kommunikations- eller timeout-fel. klienten växlar för att använda nästa adress sekventiellt. Mer information om återförsök semantik finns i avsnittet Namntjänstadress igen för mer information om semantik. |
|PartitionLocationCacheLimit |Int, standard är 100000 |Statisk|Antal partitioner som cachelagrats för tjänstupplösning (inställt på 0 för ingen gräns). |
|Försök igen |Tid i sekunder, standard är 3 |Dynamisk|Ange tidsintervall i sekunder. Back-off-intervallet innan du försöker utföra åtgärden igen. |
|ServiceChangePollInterval |Tid i sekunder, standard är 120 |Dynamisk|Ange tidsintervall i sekunder. Intervallet mellan efterföljande avsökningar för tjänständringar från klienten till gatewayen för registrerade meddelanden om tjänständringsmeddelanden motringningar. |

## <a name="fabrichost"></a>FabricHost (tyg)

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AktiveringMaxFailureCount |Int, standard är 10 |Dynamisk|Detta är det maximala antalet för vilket system som kommer att försöka misslyckades aktivering innan du ger upp. |
|AktiveringMaxRetryInterval |Tid i sekunder, standard är 300 |Dynamisk|Ange tidsintervall i sekunder. Maximalt intervall för återförsök för aktivering. Vid varje kontinuerligt fel beräknas återförsöksintervallet som Min( ActivationMaxRetryInterval; Antal kontinuerliga fel * AktiveringRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tid i sekunder, standard är 5 |Dynamisk|Ange tidsintervall i sekunder. Backoff-intervall vid varje aktiveringsfel;Vid varje kontinuerligt aktiveringsfel kommer systemet att försöka igen aktiveringen i upp till MaxActivationFailureCount. Återförsöksintervallet vid varje försök är en produkt av kontinuerligt aktiveringsfel och aktiveringsavbackeintervallet. |
|AktiveraRestartManagement |Bool, standard är falskt |Dynamisk|Detta för att aktivera omstart av servern. |
|AktiveraServiceFabricAutomaticUpdates |Bool, standard är falskt |Dynamisk|Detta för att aktivera automatisk uppdatering av fabric via Windows Update. |
|AktiveraServiceFabricBaseUpgrade |Bool, standard är falskt |Dynamisk|Detta för att aktivera basuppdatering för servern. |
|FailureReportingExpeditedReportingIntervalEnabled | Bool, standard är sant | Statisk | Möjliggör snabbare uppladdningshastigheter i DCA när FabricHost är i felrapporteringsläge. |
|FailureReportingTimeout | TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(60) | Statisk |Ange tidsintervall i sekunder. Timeout för DCA felrapportering i fallet FabricHost stöter på ett tidigt startfel. | 
|RunDCAOnStartupFailure | Bool, standard är sant | Statisk |Avgör om dca ska startas för att ladda upp loggar när de ställs inför startproblem i FabricHost. | 
|StartTimeout |Tid i sekunder, standard är 300 |Dynamisk|Ange tidsintervall i sekunder. Time out för fabricactivationmanager start. |
|StopTimeout |Tid i sekunder, standard är 300 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för värdtjänstaktivering. avaktivering och uppgradering. |

## <a name="fabricnode"></a>FabricNod

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |sträng, är standard "FindByThumbprint" |Dynamisk|Anger hur du söker efter certifikat i det arkiv som anges av värdet För ClientAuthX509StoreName Supported: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindVärde |sträng, är standard "" | Dynamisk|Sökfiltervärde som används för att hitta certifikat för standardadministratörsrollen FabricClient. |
|ClientAuthX509FindValueSecondary |sträng, är standard "" |Dynamisk|Sökfiltervärde som används för att hitta certifikat för standardadministratörsrollen FabricClient. |
|ClientAuthX509StoreName |sträng är standard "Min" |Dynamisk|Namnet på X.509-certifikatarkivet som innehåller certifikat för standardadministratörsrollen FabricClient. |
|ClusterX509FindType |sträng, är standard "FindByThumbprint" |Dynamisk|Anger hur du söker efter klustercertifikat i det arkiv som anges av värden som stöds av ClusterX509StoreName: "FindByThumbprint"; "FindBySubjectName" med "FindBySubjectName"; när det finns flera matchningar; den med längst utgångsdatum används. |
|KlusterX509Findvärde |sträng, är standard "" |Dynamisk|Sökfiltervärde som används för att hitta klustercertifikat. |
|ClusterX509FindValueSecondary |sträng, är standard "" |Dynamisk|Sökfiltervärde som används för att hitta klustercertifikat. |
|ClusterX509StoreName |sträng är standard "Min" |Dynamisk|Namnet på X.509-certifikatarkivet som innehåller klustercertifikat för att skydda kommunikationen inom kluster. |
|EndApplicationPortRange |Int, standard är 0 |Statisk|(ingen inklusive) av programportar som hanteras av värd undersystemet. Krävs om EndpointFilteringEnabled är sant i Hosting. |
|ServerAuthX509FindType |sträng, är standard "FindByThumbprint" |Dynamisk|Anger hur du söker efter servercertifikat i det arkiv som anges av värdet För ServerAuthX509StoreName Stöds: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509Findvärde |sträng, är standard "" |Dynamisk|Sökfiltervärde som används för att hitta servercertifikat. |
|ServerAuthX509FindValueSecondary |sträng, är standard "" |Dynamisk|Sökfiltervärde som används för att hitta servercertifikat. |
|ServerAuthX509StoreName |sträng är standard "Min" |Dynamisk|Namn på X.509-certifikatarkiv som innehåller servercertifikat för förrättstjänst. |
|StartApplicationPortRange |Int, standard är 0 |Statisk|Start av programportarna som hanteras av värd undersystemet. Krävs om EndpointFilteringEnabled är sant i Hosting. |
|StateTraceInterval |Tid i sekunder, standard är 300 |Statisk|Ange tidsintervall i sekunder. Intervallet för spårning av nodstatus på varje nod och uppnoder på FM/FMM. |
|AnvändareRoleClientX509FindType |sträng, är standard "FindByThumbprint" |Dynamisk|Anger hur du söker efter certifikat i det arkiv som anges av värdet UserRoleClientX509StoreName Supported: FindByThumbprint; FindBySubjectName. |
|AnvändareRoleClientX509FindVärde |sträng, är standard "" |Dynamisk|Sökfiltervärde som används för att hitta certifikat för standardanvändarrollen FabricClient. |
|AnvändareRoleClientX509FindValueSecondary |sträng, är standard "" |Dynamisk|Sökfiltervärde som används för att hitta certifikat för standardanvändarrollen FabricClient. |
|AnvändareRoleClientX509StoreName |sträng är standard "Min" |Dynamisk|Namnet på X.509-certifikatarkivet som innehåller certifikat för standardrollen FabricClient. |

## <a name="failovermanager"></a>RedundansManager

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, standard är FALSKT |Dynamisk|Flagga för att ange om det är tillåtet att ta bort nodtillstånd för en frönod |
|BuildReplicaTimeLimit|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(3600)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att bygga en tillståndskänslig replik. varefter en varningshälsorapport kommer att inledas |
|ClusterPauseThreshold|int är standard 1|Dynamisk|Om antalet noder i systemet går under det här värdet sedan placering; Belastningsutjämning. och redundans stoppas. |
|SkapaInanceTimeLimit|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(300)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att skapa en tillståndslös instans. varefter en varningshälsorapport kommer att inledas |
|Förväntadklusterstorlek|int är standard 1|Dynamisk|När klustret startas startas. FM kommer att vänta på att många noder ska rapportera sig själva innan de börjar placera andra tjänster. inklusive systemtjänster som namngivning. Om du ökar det här värdet ökar tiden det tar för ett kluster att starta. men förhindrar de tidiga noderna från att bli överbelastade och även ytterligare drag som kommer att behövas när fler noder kommer online. Det här värdet bör i allmänhet anges till en liten del av den ursprungliga klusterstorleken. |
|ExpectedNodeDeactivationDuration|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(60.0 \* 30)|Dynamisk|Ange tidsintervall i sekunder. Detta är den förväntade varaktigheten för en nod att slutföra inaktivering i. |
|FörväntadNodeFabricUppgraderingUnderrering|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(60.0 \* 30)|Dynamisk|Ange tidsintervall i sekunder. Det här är den förväntade varaktigheten för en nod som ska uppgraderas under Uppgraderingen av Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(60.0 \* 30)|Dynamisk|Ange tidsintervall i sekunder. Detta är den förväntade varaktigheten för alla repliker som ska uppgraderas på en nod under programuppgradering. |
|IsSingletonReplicaMoveTillåtunderdingsgrad|bool, standard är SANT|Dynamisk|Om den är inställd på true; repliker med en målreplikuppsättningsstorlek på 1 tillåts att flytta under uppgraderingen. |
|MinReplicaSetSize|int är standard 3|Inte tillåtet|Detta är den minsta replikuppsättningsstorleken för FM. Om antalet aktiva FM-repliker sjunker under det här värdet. FM kommer att avvisa ändringar i klustret tills åtminstone det minsta antalet repliker återställs |
|Placeringsspärrar|sträng, är standard ""|Inte tillåtet|Eventuella placeringsbegränsningar för redundanshanterarens repliker |
|PlaceringTimeLimit|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(600)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att nå antal målrepliker. varefter en varningshälsorapport kommer att inledas |
|KvorumLossWaitDuration |Tid i sekunder, standard är MaxValue |Dynamisk|Ange tidsintervall i sekunder. Detta är den max varaktighet för vilken vi tillåter en partition att vara i ett tillstånd av kvorum förlust. Om partitionen fortfarande är i kvorumförlust efter denna varaktighet; partitionen återställs från kvorumförlust genom att betrakta nedrepliker som förlorade. Observera att detta potentiellt kan medföra dataförlust. |
|Konfigurera omTimeLimit|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(300)|Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för omkonfigurering. varefter en varningshälsorapport kommer att inledas |
|ReplicaRestartWaitDuration|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(60.0 \* 30)|Inte tillåtet|Ange tidsintervall i sekunder. Det här är ReplicaRestartWaitDuration för FMService |
| SeedNodeQuorumAdditionalBufferNodes | int är standard 0 | Dynamisk | Buffert av frönoder som behövs för att vara upp (tillsammans med kvorum av frönoder) FM bör tillåta att maximalt (totalNumSeedNodes - (seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) frönoder att gå ner. |
|StandByReplicaKeepDuration|Tidsintervall, standard är Vanligt::TimeSpan::FromSeconds(3600.0 \* 24 \* 7)|Inte tillåtet|Ange tidsintervall i sekunder. Detta är StandByReplicaKeepDuration för FMService |
|TargetReplicaSetSize|int, standard är 7|Inte tillåtet|Detta är målantalet FM-repliker som Windows Fabric kommer att underhålla. Ett högre antal resulterar i högre tillförlitlighet fm-data; med en liten prestanda kompromiss. |
|AnvändareMaxStandByReplicaCount |Int, standard är 1 |Dynamisk|Standard max antal StandBy-repliker som systemet behåller för användartjänster. |
|AnvändareReplicaRestartWaitDuration |Tid i sekunder, standard är 60,0 \* 30 |Dynamisk|Ange tidsintervall i sekunder. När en beständig replik går ner; Windows Fabric väntar på den här varaktigheten för repliken att komma tillbaka upp innan du skapar nya ersättningsrepliker (vilket skulle kräva en kopia av tillståndet). |
|AnvändareStandByReplicaKeepDuration |Tid i sekunder, standard är 3600,0 \* 24 \* 7 |Dynamisk|Ange tidsintervall i sekunder. När en beständig replik kommer tillbaka från ett ned-tillstånd. den kan redan ha ersatts. Den här timern avgör hur länge FM-infrastrukturen behåller reservrepliken innan den tas bort. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Slutförd ÅtgärdHållvarvarunderenSeconds | Int, standard är 604800 |Statisk| Det är ungefär hur länge du ska hålla åtgärder som är i ett terminaltillstånd. Detta beror också på StoredActionCleanupIntervalInSeconds; eftersom arbetet med rensning endast utförs på det intervallet. 604800 är 7 dagar. |
|DataLossCheckPollIntervalInSeconds DataLossCheckPollIntervalInSeconds DataLossCheckPollIntervalInSeconds DataLos|int är standard 5|Statisk|Det här är tiden mellan de kontroller som systemet utför i väntan på att dataförlust ska inträffa. Antalet gånger dataförlustnumret kommer att kontrolleras per intern iteration är DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds DataLossCheckWaitDurationInSeconds DataLossCheckWaitDurationInSeconds DataLos|int, standard är 25|Statisk|Den totala tiden; på några sekunder. att systemet väntar på att dataförlust ska ske. Detta används internt när Api:et StartPartitionDataLossAsync() anropas. |
|MinReplicaSetSize |Int, standard är 0 |Statisk|MinReplicaSetSize för FaultAnalysisService. |
|Placeringsspärrar | sträng, är standard ""|Statisk| Placeringsspärrarna för FaultAnalysisService. |
|KvorumLossWaitDuration | Tid i sekunder, standard är MaxValue |Statisk|Ange tidsintervall i sekunder. Kvorumlosswaitdurationen för faultanalysisService. |
|ReplicaDropWaitDurationInSeconds|int, standard är 600|Statisk|Den här parametern används när api:et för dataförlust anropas. Den styr hur länge systemet väntar på att en replik ska släppas när repliken har tagits bort internt. |
|ReplicaRestartWaitDuration |Tid i sekunder, standard är 60 minuter|Statisk|Ange tidsintervall i sekunder. The ReplicaRestartWaitDuration for FaultAnalysisService. |
|StandByReplicaKeepDuration| Tid i sekunder, standard är (60*24*7) minuter |Statisk|Ange tidsintervall i sekunder. StandByReplicaKeepDuration för FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, standard är 3600 |Statisk|Det är så ofta butiken städas upp. Endast åtgärder i ett terminaltillstånd. och som slutförts minst CompletedActionKeepDurationInSeconds sedan kommer att tas bort. |
|StoredChaosEventCleanupIntervalInSeconds StoredChaosEventCleanupIntervalInSeconds StoredChaosEventCleanupIntervalInSeconds StoredCha | Int, standard är 3600 |Statisk|Det är så ofta butiken kommer att granskas för sanering; om antalet händelser är mer än 30000. rensningen kommer att sparka in. |
|TargetReplicaSetSize |Int, standard är 0 |Statisk|NOT_PLATFORM_UNIX_START TargetReplicaSetSize för FaultAnalysisService. |

## <a name="federation"></a>Federation

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|LeaseDuration |Tid i sekunder, standard är 30 |Dynamisk|Varaktighet som ett hyresavtal varar mellan en nod och dess grannar. |
|LeaseDurationAcrossFaultDomain |Tid i sekunder, standard är 30 |Dynamisk|Varaktighet som ett lån varar mellan en nod och dess grannar över feldomäner. |

## <a name="filestoreservice"></a>FileStoreService

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Bool, standard är SANT|Dynamisk|Config för att avgöra om fillagringstjänsten accepterar segmentbaserad filöverföring eller inte under kopieringsprogrampaketet. |
|AnonymousAccessEnabled | Bool, standard är sant |Statisk|Aktivera/inaktivera anonym åtkomst till FileStoreService-resurserna. |
|CommonName1Ntlmx509Kommernamn|sträng, är standard ""|Statisk| Det gemensamma namnet på X509-certifikatet som används för att generera HMAC på CommonName1NtlmPasswordSecret när NTLM-autentisering används |
|CommonName1Ntlmx509StoreLocation|sträng, är standard "LocalMachine"|Statisk|Lagringsplatsen för X509-certifikatet som används för att generera HMAC på CommonName1NtlmPasswordSecret när NTLM-autentisering används |
|CommonName1Ntlmx509StoreName|sträng, är standard "MY"| Statisk|Butiksnamnet på X509-certifikatet som används för att generera HMAC på CommonName1NtlmPasswordSecret när NTLM-autentisering används |
|CommonName2Ntlmx509Kommernamn|sträng, är standard ""|Statisk|Det gemensamma namnet på X509-certifikatet som används för att generera HMAC på CommonName2NtlmPasswordSecret när NTLM-autentisering används |
|CommonName2Ntlmx509HandelPlats|sträng, är standard "LocalMachine"| Statisk|Lagringsplatsen för X509-certifikatet som används för att generera HMAC på CommonName2NtlmPasswordSecret när NTLM-autentisering används |
|CommonName2Ntlmx509StoreName|sträng, är standard "MY"|Statisk| Butiksnamnet på X509-certifikatet som används för att generera HMAC på CommonName2NtlmPasswordSecret när NTLM-autentisering används |
|CommonNameNtlmPasswordSecret|SecureString, standard är Vanligt::SecureString("")| Statisk|Lösenordshemligheten som används som seed för att generera samma lösenord när NTLM-autentisering används |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, standard är Vanligt::TimeSpan::FromMinutes(5)|Dynamisk|Ange tidsintervall i sekunder. Tidsintervallet mellan kontroll av diskutrymme för rapportering av hälsohändelse när disken är nära på utrymme. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, standard är Vanligt::TimeSpan::FrånMinuter(15)|Dynamisk|Ange tidsintervall i sekunder. Tidsintervallet mellan kontroll av diskutrymme för rapportering av hälsohändelse när det finns tillräckligt med utrymme på disken. |
|AktiveraImageStoreHealthReporting |bool, standard är SANT |Statisk|Config för att avgöra om fillagringstjänsten ska rapportera dess hälsa. |
|FreeDiskSpaceNotificationSizeInKB|int64, standard är\*25 1024 |Dynamisk|Storleken på ledigt diskutrymme under vilket hälsovarning kan uppstå. Minimivärdet för den här config och FreeDiskSpaceNotificationThresholdPercentage config används för att bestämma sändning av hälsovarningen. |
|FreeDiskSpaceNotificationThresholdPercentage|dubbel, standard är 0,02 |Dynamisk|Den procentandel ledigt diskutrymme under vilket hälsovarning kan uppstå. Minimivärdet för den här config och FreeDiskSpaceNotificationInMB config används för att bestämma sändning av hälsovarning. |
|Generera V1CommonNameAccount| bool, standard är SANT|Statisk|Anger om ett konto med användarnamn V1-genereringsalgoritm ska genereras. Från och med Service Fabric version 6.1; ett konto med v2 generation skapas alltid. V1-kontot är nödvändigt för uppgraderingar från/till versioner som inte stöder V2-generationen (före 6.1).|
|MaxCopyOperationDtrådar | Uint, standard är 0 |Dynamisk| Det maximala antalet parallella filer som sekundärt kan kopiera från primär. '0' == antal kärnor. |
|MaxFileOperationTrådar | Uint, standard är 100 |Statisk| Det maximala antalet parallella trådar som tillåts utföra FileOperations (Copy/Move) i den primära. '0' == antal kärnor. |
|MaxRequestProcessingTrådar | Uint, standard är 200 |Statisk|Det maximala antalet parallella trådar som tillåts bearbeta begäranden i den primära. '0' == antal kärnor. |
|MaxSecondaryFileCopyFailureTrereshold | Uint, standard är 25|Dynamisk|Det maximala antalet filkopieringsförsök på den sekundära innan de ger upp. |
|MaxStoreOperations | Uint, standard är 4096 |Statisk|Det maximala antalet parallelllagertransaktionsåtgärder som tillåts på primär. '0' == antal kärnor. |
|NamingOperationTimeout |Tid i sekunder, standard är 60 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att utföra namngivningsåtgärden. |
|PrimärRedovisningNTLMPasswordSecret | SecureString, standard är tom |Statisk| Lösenordshemligheten som används som seed för att generera samma lösenord när NTLM-autentisering används. |
|PrimaryAccountNTLMX509StoreLocation | sträng, är standard "LocalMachine"|Statisk| Lagringsplatsen för X509-certifikatet som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|PrimaryAccountNTLMX509StoreName | sträng, är standard "MY"|Statisk| Butiksnamnet på X509-certifikatet som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|PrimaryAccountNTLMX509Dumbavtryck | sträng, är standard ""|Statisk|Tumavtrycket för X509-certifikatet som används för att generera HMAC på PrimaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|Primärredovisningstyp | sträng, är standard "" |Statisk|Huvudmannens primära accountType till ACL som FileStoreService-aktier. |
|PrimaryAccountUserName | sträng, är standard "" |Statisk|Huvudmannens primära konto Användarnamn till ACL som FileStoreService delar. |
|PrimärKontoAnvändarpassord | SecureString, standard är tom |Statisk|Huvudnumret för huvudmannen till ACL som FileStoreService delar. |
|QueryOperationTimeout | Tid i sekunder, standard är 60 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen för att utföra frågeåtgärden. |
|SecondaryAccountNTLMPasswordSecret | SecureString, standard är tom |Statisk| Lösenordshemligheten som används som seed för att generera samma lösenord när NTLM-autentisering används. |
|SecondaryAccountNTLMX509StoreLocation | sträng, är standard "LocalMachine" |Statisk|Lagringsplatsen för X509-certifikatet som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|SecondaryAccountNTLMX509StoreName | sträng, är standard "MY" |Statisk|Butiksnamnet på X509-certifikatet som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|SecondaryAccountNTLMX509Dumbavtryck | sträng, är standard ""| Statisk|Tumavtrycket för X509-certifikatet som används för att generera HMAC på SecondaryAccountNTLMPasswordSecret när NTLM-autentisering används. |
|SecondaryAccountType | sträng, är standard ""|Statisk| Den sekundära AccountType av huvudmannen till ACL FileStoreService-resurserna. |
|SecondaryAccountUserName | sträng, är standard ""| Statisk|Det sekundära kontot Användarnamn för huvudmannen till ACL som FileStoreService delar. |
|SekundärtkontoAnvändarpassord | SecureString, standard är tom |Statisk|Huvudnamnet för huvudmannen till ACL som FileStoreService delar. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, standard är 500|Dynamisk|Fördröjningen för filkopieringsförsök (i millisekunder).|
|AnvändChunkContentInTransportMessage|bool, standard är SANT|Dynamisk|Flaggan för att använda den nya versionen av uppladdningsprotokollet som introducerades i v6.4. Den här protokollversionen använder serviceinfrastrukturtransport för att ladda upp filer till bildarkivet vilket ger bättre prestanda än SMB-protokoll som används i tidigare versioner. |

## <a name="healthmanager"></a>HälsaManager

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AktiveraApplicationTypeHealthEvaluation |Bool, standard är falskt |Statisk|Utvärderingsprincipen för klusterhälsa: aktivera hälsoutvärdering per programtyp. |
|MaxSuggestedNumberOfEntityHealthReports|Int, standard är 100 |Dynamisk|Det maximala antalet hälsorapporter som en entitet kan ha innan du tar upp frågor om watchdog hälsa rapportering logik. Varje hälsoenhet är tänkt att ha ett relativt litet antal hälsorapporter. Om rapportantalet går över detta nummer. det kan finnas problem med vakthundens genomförande. En entitet med för många rapporter flaggas via en hälsorapport för varning när entiteten utvärderas. |

## <a name="healthmanagerclusterhealthpolicy"></a>HälsaManager/ClusterHealthPolicy

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ÖvervägWarningAsError |Bool, standard är falskt |Statisk|Utvärderingsprincipen för klusterhälsa: varningar behandlas som fel. |
|MaxPercentUnhealthyApplications | Int, standard är 0 |Statisk|Klusterhälsoutvärderingsprincip: maximal procentandel felaktiga program som tillåts för att klustret ska vara felfritt. |
|MaxPercentUnhealthyNodes | Int, standard är 0 |Statisk|Klusterhälsoutvärderingsprincip: maximal procentandel felaktiga noder som tillåts för att klustret ska vara felfritt. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HälsaManager/ClusterUpgradeHealthPolicy

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int är standard 10|Statisk|Hälsoutvärderingsprincipen för klusteruppgradering: maximal procent av felen för delta som tillåts för klustret att vara felfritt |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, standard är 15|Statisk|Hälsoutvärderingsprincipen för klusteruppgradering: maximal procentandel av delta av felaktiga noder i en uppgraderingsdomän som tillåts för att klustret ska vara felfritt |

## <a name="hosting"></a>Värd

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AktiveringMaxFailureCount |Heltal, standard är 10 |Dynamisk|Antal gånger som systemet misslyckades med aktiveringen innan de gav upp |
|AktiveringMaxRetryInterval |Tid i sekunder, standard är 300 |Dynamisk|Vid varje kontinuerligt aktiveringsfel försöker systemet aktiveringen i upp till ActivationMaxFailureCount. ActivationMaxRetryInterval anger Väntetidsintervall före återförsök efter varje aktiveringsfel |
|ActivationRetryBackoffInterval |Tid i sekunder är standard 5 |Dynamisk|Backoff-intervall vid varje aktiveringsfel. Vid varje kontinuerligt aktiveringsfel försöker systemet aktiveringen i upp till MaxActivationFailureCount. Återförsöksintervallet vid varje försök är en produkt av kontinuerligt aktiveringsfel och aktiveringsavbackeintervallet. |
|AktiveringTimeout| TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(180)|Dynamisk| Ange tidsintervall i sekunder. Tidsgränsen för programaktivering. avaktivering och uppgradering. |
|ApplicationHostCloseTimeout| TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(120)|Dynamisk| Ange tidsintervall i sekunder. När Fabric exit detekteras i en självaktiverad processer; FabricRuntime stänger alla repliker i användarens värdprocess (applicationhost). Det här är tidsgränsen för stängningsåtgärden. |
| CnsNetworkPluginCnmUrlPort | wstring, standard är L"48080" | Statisk | Url-port för Azure cnm api |
| CnsNetworkPluginCnsUrlPort | wstring, standard är L"10090" | Statisk | Url-port för Azure cns |
|ContainerServiceArguments|sträng är standard "-H localhost:2375 -H npipe://"|Statisk|Service Fabric (SF) hanterar docker daemon (utom på Windows klientmaskiner som Win10). Med den här konfigurationen kan användaren ange anpassade argument som ska skickas till docker-demon när den startas. När anpassade argument anges skickar Service Fabric inte något annat argument till Docker-motorn förutom argumentet "--pidfile". Därför bör användarna inte ange argumentet "--pidfile" som en del av sina kundargument. Dessutom bör de anpassade argumenten se till att docker-demonen lyssnar på standardnamnsröret i Windows (eller Unix-domänuttaget på Linux) för att Service Fabric ska kunna kommunicera med den.|
|ContainerServiceLogFileMaxSizeInKb|int, standard är 32768|Statisk|Maximal filstorlek för loggfil som genereras av dockerbehållare.  Endast Windows.|
|ContainerImageDownloadTimeout|int, antal sekunder, standard är 1200 (20 minuter)|Dynamisk|Antal sekunder före hämtning av bild time out.|
|ContainerImagesToSkip|sträng, bildnamn avgränsade med lodrät linjetecken, är standard ""|Statisk|Namn på en eller flera behållaravbildningar som inte bör tas bort.  Används med parametern PruneContainerImages.|
|ContainerServiceLogFileNamePrefix|sträng, är standard "sfcontainerlogs"|Statisk|Filnamnsprefix för loggfiler som genereras av dockerbehållare.  Endast Windows.|
|ContainerServiceLogFileRetentionCount|int är standard 10|Statisk|Antal loggfiler som genereras av dockerbehållare innan loggfiler skrivs över.  Endast Windows.|
|SkapaFabricRuntimeTimeout|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(120)|Dynamisk| Ange tidsintervall i sekunder. Timeout-värdet för sync FabricCreateRuntime-anropet |
|DefaultContainerRepositoryAccountName|sträng, är standard ""|Statisk|Standardautentiseringsuppgifter som används i stället för autentiseringsuppgifter som anges i ApplicationManifest.xml |
|DefaultContainerRepositoryPassword|sträng, är standard ""|Statisk|Standardautentiseringsuppgifter för lösenord som används i stället för autentiseringsuppgifter som anges i ApplicationManifest.xml|
|DefaultContainerRepositoryPasswordType|sträng, är standard ""|Statisk|När inte tom sträng, kan värdet vara "Krypterad" eller "SecretsStoreRef".|
|DefaultDnsSearchSuffixEmpty|bool, standard är FALSKT|Statisk|Som standard läggs tjänstnamnet till i SF DNS-namnet för behållartjänster. Den här funktionen stoppar det här beteendet så att ingenting läggs till i SF DNS-namnet som standard i lösningsvägen.|
|DeploymentMaxFailureCount|int är standard 20| Dynamisk|Programdistributionen görs på nytt för DeploymentMaxFailureCount gånger innan du misslyckas distributionen av programmet på noden.| 
|DeploymentMaxRetryInterval| TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(3600)|Dynamisk| Ange tidsintervall i sekunder. Maximalt återförsöksintervall för distributionen. Vid varje kontinuerligt fel beräknas återförsöksintervallet som Min( DeploymentMaxRetryInterval; Antal kontinuerliga fel * DistributionRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(10)|Dynamisk|Ange tidsintervall i sekunder. Back-off-intervall för distributionsfel. Vid varje kontinuerligt distributionsfel försöker systemet igen distributionen för upp till MaxDeploymentFailureCount. Återförsöksintervallet är en produkt av kontinuerlig distributionsfel och distributionsbackoff-intervallet. |
|Inaktiveracontainers|bool, standard är FALSKT|Statisk|Config för att inaktivera behållare - används i stället för DisableContainerServiceStartOnContainerActivatorOpen som är föråldrad config |
|InaktiveraDockerRequestRetry|bool, standard är FALSKT |Dynamisk| Som standard kommunicerar SF med DD (docker dameon) med en timeout på DockerRequestTimeout för varje http-begäran som skickas till den. Om DD inte svarar inom denna tidsperiod; SF skickar begäran igen om åtgärden på den högsta nivån fortfarande har återstående tid.  Med hyperv behållare; DD tar ibland mycket mer tid att ta upp behållaren eller inaktivera den. I sådana fall DD begäran time out från SF perspektiv och SF försöker operationen. Ibland verkar detta tillför mer press på DD. Denna config gör det möjligt att inaktivera detta nytt försök och vänta på DD att svara. |
|DnsServerListTwoIps | Bool, standard är FALSKT | Statisk | Den här flaggan lägger till den lokala DNS-servern två gånger för att lindra återkommande problem. |
| DoNotInjectLocalDnsServer | bool, standard är FALSKT | Statisk | Förhindrar körningen att injicera den lokala IP-servern som DNS-server för behållare. |
|AktiveraActivateNoWindow| bool, standard är FALSKT|Dynamisk| Den aktiverade processen skapas i bakgrunden utan någon konsol. |
|AktiveraContainerServiceDebugMode|bool, standard är SANT|Statisk|Aktivera/inaktivera loggning för dockerbehållare.  Endast Windows.|
|AktiveraDockerHealthCheckIntegration|bool, standard är SANT|Statisk|Möjliggör integrering av hälsokontrollhändelser för dockor med hälsorapporten Service Fabric system |
|AktiveraProcessDebugging|bool, standard är FALSKT|Dynamisk| Aktiverar startprogramvärdar under felsökning |
|SlutpunktProviderEnabled| bool, standard är FALSKT|Statisk| Aktiverar hantering av slutpunktsresurser efter infrastruktur. Kräver specifikation av start- och slutapplikationsportområdet i FabricNode. |
|FabricContainerAppsEnabled| bool, standard är FALSKT|Statisk| |
|FirewallPolicyEnabled|bool, standard är FALSKT|Statisk| Aktiverar öppna brandväggsportar för slutpunktsresurser med explicita portar som anges i ServiceManifest |
|GetCodePackageActivationContextTimeout|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(120)|Dynamisk|Ange tidsintervall i sekunder. Timeout-värdet för CodePackageActivationContext-anrop. Detta gäller inte för ad hoc-tjänster. |
|GovernOnlyMainMemoryForProcesses|bool, standard är FALSKT|Statisk|Standardbeteende för resursstyrning är att sätta gräns som anges i MemoryInMB på mängden totalt minne (RAM + swap) som processen använder. Om gränsen överskrids. processen kommer att få OutOfMemory undantag. Om den här parametern är inställd på true; begränsas endast på mängden RAM-minne som en process ska använda. Om denna gräns överskrids; och om denna inställning är sann; då OS kommer att byta huvudminnet till disk. |
|IPProviderEnabled|bool, standard är FALSKT|Statisk|Aktiverar hantering av IP-adresser. |
|IsDefaultContainerRepositoryPasswordEnkrypterad|bool, standard är FALSKT|Statisk|Om DefaultContainerRepositoryPassword är krypterat eller inte.|
|LinuxExternalExecutablePath|sträng är standardvärdet "/usr/bin/" |Statisk|Den primära katalogen med externa körbara kommandon på noden.|
|NTLMAuthenticationEnabled|bool, standard är FALSKT|Statisk| Aktiverar stöd för att använda NTLM med hjälp av kodpaket som körs som andra användare så att processerna mellan datorer kan kommunicera säkert. |
|NTLMAuthenticationPasswordSecret|SecureString, standard är Vanligt::SecureString("")|Statisk|Är en krypterad har som används för att generera lösenordet för NTLM-användare. Måste ställas in om NTLMAuthenticationEnabled är sant. Validerad av distributionsören. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, standard är Vanligt::TimeSpan::FromMinutes(3)|Dynamisk|Ange tidsintervall i sekunder. Miljöspecifika inställningar Det periodiska intervall med vilket Hosting söker efter nya certifikat som ska användas för FileStoreService NTLM-konfiguration. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, standard är vanligt::TimeSpan::FromMinutes(4)|Dynamisk| Ange tidsintervall i sekunder. Tidsgränsen för att konfigurera NTLM-användare med vanliga namn. NTLM-användarna behövs för FileStoreService-resurser. |
|PruneContainerImages|bool, standard är FALSKT|Dynamisk| Ta bort oanvända programbehållarens bilder från noder. När en ApplicationType avregistreras från Service Fabric-klustret tas behållaravbildningar som användes av det här programmet bort på noder där de hämtades av Service Fabric. Beskärningen körs varje timme, så det kan ta upp till en timme (plus tid att beskära bilden) för bilder som ska tas bort från klustret.<br>Service Fabric kommer aldrig att ladda ner eller ta bort bilder som inte är relaterade till ett program.  Orelaterade bilder som har hämtats manuellt eller på annat sätt måste tas bort uttryckligen.<br>Bilder som inte ska tas bort kan anges i parametern ContainerImagesToSkip.| 
|RegistreraCodePackageHostTimeout|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(120)|Dynamisk| Ange tidsintervall i sekunder. Timeout-värdet för Sync-anropet FabricRegisterCodeCodePackageHost. Detta gäller endast för programvärdar för flera kodpaket som FWP |
|RequestTimeout|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(30)|Dynamisk| Ange tidsintervall i sekunder. Detta representerar tidsgränsen för kommunikation mellan användarens programvärd och Fabric-process för olika värdrelaterade operationer, till exempel fabriksregistrering. registrering av körning. |
|RunAsPolicyEnabled| bool, standard är FALSKT|Statisk| Aktiverar köra kodpaket som en annan lokal användare än den användare under vilken infrastrukturprocess körs. För att aktivera denna princip måste Fabric köras som SYSTEM eller som användare som har SeAssignPrimaryTokenPrivilege. |
|Tid för servicefactoryregistrationTimeout| TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(120)|Dynamisk|Ange tidsintervall i sekunder. Timeout-värdet för synkroniseringsregistret(tillståndslös/tillståndskänslig)ServiceFactory-anropet |
|ServiceTypeDisableFailureThreshold |Heltal, standard är 1 |Dynamisk|Detta är tröskelvärdet för antalet misslyckade försök varefter FailoverManager (FM) meddelas om att inaktivera tjänsttypen på den noden och prova en annan nod för placering. |
|ServiceTypeDisableGraceInterval|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(30)|Dynamisk|Ange tidsintervall i sekunder. Tidsintervall efter vilket tjänsttypen kan inaktiveras |
|ServiceTypeRegistrationTimeout |Tid i sekunder, standard är 300 |Dynamisk|Maximal tid för ServiceType att registreras med tyg |
|AnvändaContainerServiceArguments|bool, standard är SANT|Statisk|Den här konfigurationen talar om för värd att hoppa över passningsargument (som anges i config ContainerServiceArguments) till dockerdemon.|

## <a name="httpgateway"></a>Mer från HttpGateway

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, standard är 50 |Statisk| Antal läsningar som ska publiceras i http-serverkön. Detta styr antalet samtidiga begäranden som kan uppfyllas av HttpGateway. |
|HttpGatewayHealthReportSendInterval |Tid i sekunder, standard är 30 |Statisk|Ange tidsintervall i sekunder. Det intervall med vilket Http Gateway skickar ackumulerade hälsorapporter till hälsohanteraren. |
|HttpStrictTransportSecurityHeader|sträng,standard är ""|Dynamisk| Ange värdet för http-strikt transportsäkerhet som ska inkluderas i varje svar som skickas av HttpGateway. När den är inställd på tom sträng; Det här huvudet inkluderas inte i gateway-svaret.|
|IsEnabled|Bool, standard är falskt |Statisk| Aktiverar/inaktiverar HttpGateway. HttpGateway är inaktiverat som standard. |
|MaxEntityBodySize |Uint, standard är 4194304 |Dynamisk|Ger den maximala storleken på kroppen som kan förväntas från en http-begäran. Standardvärdet är 4 MB. Httpgateway kommer att misslyckas en begäran om den har en mängd storlek > det här värdet. Minsta lässegmentstorlek är 4096 byte. Så detta måste vara >= 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Enabled |Bool, standard är falskt |Statisk|Flaggan Aktiverad för ImageStoreService. Standard: falskt |
|MinReplicaSetSize | Int, standard är 3 |Statisk|MinReplicaSetSize för ImageStoreService. |
|Placeringsspärrar | sträng, är standard "" |Statisk| Placeringsspärrarna för ImageStoreService. |
|KvorumLossWaitDuration | Tid i sekunder, standard är MaxValue |Statisk| Ange tidsintervall i sekunder. Kvorumlosswaitdurationen för ImageStoreService. |
|ReplicaRestartWaitDuration | Tid i sekunder, standard är 60,0 \* 30 |Statisk|Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för ImageStoreService. |
|StandByReplicaKeepDuration | Tid i sekunder, standard är 3600,0 \* 2 |Statisk| Ange tidsintervall i sekunder. StandByReplicaKeepDuration för ImageStoreService. |
|TargetReplicaSetSize | Int, standard är 7 |Statisk|TargetReplicaSetSize för ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger (ktlLogger)

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AutomatiskMinnekonfigurering |Int, standard är 1 |Dynamisk|Flagga som anger om minnesinställningarna ska konfigureras automatiskt och dynamiskt. Om noll används minneskonfigurationsinställningarna direkt och ändras inte baserat på systemförhållanden. Om en sedan minnesinställningarna konfigureras automatiskt och kan ändras baserat på systemförhållanden. |
|MaximumDestagingWriteOutstandingInKB | Int, standard är 0 |Dynamisk|Antalet KB för att tillåta den delade loggen att avancera före den dedikerade loggen. Använd 0 för att ange ingen gräns.
|SharedLogId |sträng, är standard "" |Statisk|Unikt guid för delad loggbehållare. Använd "" om du använder standardsökvägen under infrastrukturdatarot. |
|SharedLogPath |sträng, är standard "" |Statisk|Sökväg och filnamn till plats för att placera delad loggbehållare. Använd "" för att använda standardsökvägen under infrastrukturdatarot. |
|SharedLogSizeInMB |Int, standard är 8192 |Statisk|Antalet MB som ska allokeras i den delade loggbehållaren. |
|SharedLogThrottleLimitInPercentUsed|int är standard 0 | Statisk | Procentandelen användning av den delade loggen som kommer att inducera begränsning. Värdet ska vara mellan 0 och 100. Värdet 0 innebär att standardprocentvärdet ska användas. Värdet 100 innebär ingen begränsning alls. Ett värde mellan 1 och 99 anger procentandelen logganvändning över vilken begränsning ska ske. till exempel om den delade loggen är 10GB och värdet är 90 kommer begränsning att ske när 9GB används. Användning av standardvärdet rekommenderas.|
|SkrivBufferMemoryPoolMaximumInKB | Int, standard är 0 |Dynamisk|Antalet KB som gör att skrivbuffertminnespoolen kan växa fram till. Använd 0 för att ange ingen gräns. |
|SkrivBufferMemoryPoolMinimumInKB |Int, standard är 8388608 |Dynamisk|Antalet KB som ursprungligen ska allokeras för skrivbuffertminnespoolen. Använd 0 för att ange ingen gräns Standard bör vara förenligt med SharedLogSizeInMB nedan. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|IsEnabled|bool, standard är FALSKT|Statisk|Flagga som styr närvaro och status för tjänsten Hanterad identitetstoken i klustret;detta är en förutsättning för att använda funktionen hanterad identitet för Service Fabric-program.|

## <a name="management"></a>Hantering

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan, standard är Vanligt::TimeSpan::FromMinutes(5)|Dynamisk|Ange tidsintervall i sekunder. Rensningsintervallet för tillåten för avregistrerande programtyp under automatisk rensning av programtyp.|
|AzureStorageMaxAnslutningar | Int, standard är 5000 |Dynamisk|Det maximala antalet samtidiga anslutningar till azure storage. |
|AzureStorageMaxWorkerTrådar | Int, standard är 25 |Dynamisk|Det maximala antalet arbetstrådar parallellt. |
|AzureStorageOperationTimeout | Tid i sekunder, standard är 6000 |Dynamisk|Ange tidsintervall i sekunder. Time out för xstore operation för att slutföra. |
|CleanupApplicationPackageOnProvisionSuccess|bool, standard är FALSKT |Dynamisk|Aktiverar eller inaktiverar det automatiska rensningspaketet för program vid lyckad etablering. |
|CleanupUnusedApplicationTypes|Bool, standard är FALSKT |Dynamisk|Den här konfigurationen om den är aktiverad gör det möjligt att automatiskt avregistrera oanvända programtypsversioner som hoppar över de tre senaste oanvända versionerna, vilket trimmar diskutrymmet som upptas av bildarkivet. Den automatiska rensningen utlöses i slutet av lyckad bestämmelse för den specifika apptypen och körs även regelbundet en gång om dagen för alla programtyper. Antalet oanvända versioner som ska hoppa över kan konfigureras med parametern "MaxUnusedAppTypeVersionsToKeep". |
|InaktiveraChecksumValidation | Bool, standard är falskt |Statisk| Med den här konfigurationen kan vi aktivera eller inaktivera kontrollsummaverifiering under programetablering. |
|InaktiveraServerSideCopy | Bool, standard är falskt |Statisk|Den här konfigurationen aktiverar eller inaktiverar kopia på serversidan av programpaketet på ImageStore under programetablering. |
|ImageCachingEnabled | Bool, standard är sant |Statisk|Denna konfiguration gör det möjligt för oss att aktivera eller inaktivera cachelagring. |
|ImageStoreConnectionString |SecureString (SecureString) |Statisk|Anslutningssträng till Root för ImageStore. |
|ImageStoreMinimumTransferBPS | Int, standard är 1024 |Dynamisk|Den lägsta överföringshastigheten mellan klustret och ImageStore. Det här värdet används för att bestämma timeouten när du öppnar den externa ImageStore. Ändra det här värdet endast om svarstiden mellan klustret och ImageStore är hög för att ge mer tid för klustret att hämta från den externa ImageStore. |
|MaxUnusedAppTypeVersionsToKeep | Int, standard är 3 |Dynamisk|Den här konfigurationen definierar antalet oanvända programtypsversioner som ska hoppas över för rensning. Den här parametern är endast tillämplig om parametern CleanupUnusedApplicationTypes är aktiverad. |


## <a name="metricactivitythresholds"></a>MetricActivitySvarshåll
| **Parametern** | **Tillåtna värden** |**Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|KeyIntegerValueMap, standard är Ingen|Dynamisk|Bestämmer uppsättningen MetricActivityThresholds för måtten i klustret. Balansering fungerar om maxNodeLoad är större än MetricActivityThresholds. För defragmått definierar den mängden belastning som är lika med eller under vilken Service Fabric kommer att betrakta noden som är tom |

## <a name="metricbalancingthresholds"></a>MetricBalancingDetrholds
| **Parametern** | **Tillåtna värden** |**Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|KeyDoubleValueMap, standard är Ingen|Dynamisk|Bestämmer uppsättningen MetricBalancingThresholds för måtten i klustret. Balansering fungerar om maxNodeLoad/minNodeLoad är större än MetricBalancingThresholds. Defragmentering fungerar om maxNodeLoad/minNodeLoad i minst en FD eller UD är mindre än MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parametern** | **Tillåtna värden** |**Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|KeyDoubleValueMap, standard är Ingen|Dynamisk|Bestämmer den del av belastningen som fastnar med repliken när den byts Det tar värde mellan 0 (belastningen fastnar inte med replik) och 1 (belastningsstickor med replik - standard) |

## <a name="namingservice"></a>NamingService

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, standard är 0 |Statisk|Det maximala antalet poster som underhålls i LRU-tjänstbeskrivningscachen vid namngivningsgatewayen (inställt på 0 för ingen gräns). |
|MaxClientAnslutningar |Int, standard är 1000 |Dynamisk|Det högsta tillåtna antalet klientanslutningar per gateway. |
|MaxFileOperationTimeout |Tid i sekunder, standard är 30 |Dynamisk|Ange tidsintervall i sekunder. Den maximala tidsgränsen för fillagringstjänståtgärden. Begäranden som anger en större timeout avvisas. |
|MaxIndexedEmptyPartitioner |Int, standard är 1000 |Dynamisk|Det maximala antalet tomma partitioner som kommer att finnas kvar i meddelandecachen för synkronisering av återanslutande klienter. Alla tomma partitioner ovanför detta nummer tas bort från indexet i stigande uppslagsversionsordning. Återanslut klienter kan fortfarande synkronisera och ta emot missade tomma partitionsuppdateringar. men synkroniseringsprotokollet blir dyrare. |
|MaxMessageSize |Int, standard är\*4 1024\*1024 |Statisk|Den maximala meddelandestorleken för klientnodkommunikation när du använder namngivning. DOS attack lindring; standardvärdet är 4 MB. |
|MaxNamingServiceHealthReports | Int, standard är 10 |Dynamisk|Det maximala antalet långsamma åtgärder som namngivning av butikstjänstrapporter är felfritt samtidigt. Om 0; alla långsamma operationer skickas. |
|MaxOperationTimeout |Tid i sekunder, standard är 600 |Dynamisk|Ange tidsintervall i sekunder. Den maximala tidsgränsen tillåts för klientåtgärder. Begäranden som anger en större timeout avvisas. |
|MaxOutstandingNotificationsPerClient |Int, standard är 1000 |Dynamisk|Det maximala antalet utestående meddelanden innan en klientregistrering stängs med tvång av gatewayen. |
|MinReplicaSetSize | Int, standard är 3 |Inte tillåtet| Det minsta antalet naming service-repliker som krävs för att skriva in för att slutföra en uppdatering. Om det finns färre repliker än den här aktiva i systemet nekar tillförlitlighetssystemet uppdateringar till Namntjänstarkivet tills repliker återställs. Det här värdet får aldrig vara mer än TargetReplicaSetSize. |
|PartitionCount |Int, standard är 3 |Inte tillåtet|Antalet partitioner i namntjänstarkivet som ska skapas. Varje partition äger en enskild partitionsnyckel som motsvarar dess index. så partitionsnycklar [0; PartitionCount] finns. Om du ökar antalet namingtjänstpartitioner ökar skalan som namngivningstjänsten kan utföra genom att minska den genomsnittliga mängden data som lagras av en säkerhetskopieringsreplikuppsättning. till en kostnad av ökad användning av resurser (eftersom PartitionCount*ReplicaSetSize-tjänstrepliker måste underhållas).|
|Placeringsspärrar | sträng, är standard "" |Inte tillåtet| Placeringsvillkor för namngivningstjänsten. |
|KvorumLossWaitDuration | Tid i sekunder, standard är MaxValue |Inte tillåtet| Ange tidsintervall i sekunder. När en namngivningstjänst hamnar i kvorumförlust; den här timern startar. När det löper ut fm kommer att betrakta ner repliker som förlorade; och försöka återvinna kvorum. Inte för att detta kan leda till dataförlust. |
|RepairInterval | Tid i sekunder, standard är 5 |Statisk| Ange tidsintervall i sekunder. Intervall där namninkonsekvensreparationen mellan myndighetens ägare och namnägare startar. |
|ReplicaRestartWaitDuration | Tid i sekunder, standard är (60,0 * 30)|Inte tillåtet| Ange tidsintervall i sekunder. När en namntjänstreplik går ner. den här timern startar. När det löper ut fm kommer att börja ersätta repliker som är nere (det ännu inte anser dem förlorade). |
|ServiceDescriptionCacheLimit | Int, standard är 0 |Statisk| Det maximala antalet poster som underhålls i LRU-tjänstbeskrivningscachen på namngivningslagringstjänsten (inställt på 0 för ingen gräns). |
|ServiceAnmälingTimeout |Tid i sekunder, standard är 30 |Dynamisk|Ange tidsintervall i sekunder. Tidsgränsen som används när servicemeddelanden levereras till klienten. |
|StandByReplicaKeepDuration | Tid i sekunder, standard är 3600,0 * 2 |Inte tillåtet| Ange tidsintervall i sekunder. När en namingtjänstreplik kommer tillbaka från ett nedåtläge. den kan redan ha ersatts. Den här timern avgör hur länge FM-infrastrukturen behåller reservrepliken innan den tas bort. |
|TargetReplicaSetSize |Int, standard är 7 |Inte tillåtet|Antalet replikuppsättningar för varje partition i namngivningstjänstarkivet. Om du ökar antalet replikuppsättningar ökar tillförlitlighetsnivån för informationen i Namngivningstjänstarkivet. minska ändringen att informationen kommer att gå förlorad till följd av nodfel. till en kostnad av ökad belastning på Windows Fabric och hur lång tid det tar att utföra uppdateringar av namngivningsdata.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parametern** | **Tillåtna värden** |**Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|KeyDoubleValueMap, standard är Ingen|Dynamisk|Nodkapacitetsprocent per måttnamn. används som buffert för att hålla en ledig plats på en nod för redundansfallet. |

## <a name="nodecapacities"></a>NodeCapacities NodeCapacities NodeCapacities Node

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup) |NodeCapacityCollectionMap |Statisk|En samling nodkapaciteter för olika mått. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup) |NodeFaultDomainIdCollection |Statisk|Beskriver de feldomäner som en nod tillhör. Feldomänen definieras via en URI som beskriver platsen för noden i datacentret.  Feldomän-URI:er är av formatet fd:/fd/ följt av ett URI-sökvägssegment.|
|UppgraderaDomänenId |sträng, är standard "" |Statisk|Beskriver den uppgraderingsdomän som en nod tillhör. |

## <a name="nodeproperties"></a>NodeProperties

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup) |NodePropertyCollectionMap |Statisk|En samling strängnyckel-värde-par för nodegenskaper. |

## <a name="paas"></a>Paas

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ClusterId (kluster) |sträng, är standard "" |Inte tillåtet|X509 certifikatarkiv som används av tyg för konfigurationsskydd. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Räknare |String | Dynamisk |Kommaavgränsad lista över prestandaräknare att samla in. |
|IsEnabled |Bool, standard är sant | Dynamisk |Flagga anger om prestandaräknarsamling på lokal nod är aktiverad. |
|MaxCounterBinaryFileSizeInMB |Int, standard är 1 | Dynamisk |Maximal storlek (i MB) för varje binär fil för prestandaräknare. |
|NewCounterBinaryFileCreationInMinutes |Int, standard är 10 | Dynamisk |Maximalt intervall (i sekunder) varefter en ny binär fil för prestandaräknare skapas. |
|SamplingIntervalInSeconds |Int, standard är 60 | Dynamisk |Samplingsintervall för prestandaräknare som samlas in. |

## <a name="placementandloadbalancing"></a>PlaceringOchloadBalancing

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, standard är 0 | Dynamisk|Bestämmer prioriteten för tillhörighetsbegränsningen: 0: Hård; 1: Mjuk; negativ: Ignorera. |
|ApplicationCapacityConstraintPriority | Int, standard är 0 | Dynamisk|Bestämmer prioriteten för kapacitetsbegränsning: 0: Hård; 1: Mjuk; negativ: Ignorera. |
|AutoDetectAvailableResources|bool, standard är SANT|Statisk|Denna config kommer att utlösa automatisk identifiering av tillgängliga resurser på nod (CPU och minne) När denna config är inställd på sant - vi kommer att läsa verklig kapacitet och korrigera dem om användaren anges dålig nod kapacitet eller inte definiera dem alls Om denna config är inställd på false - vi kommer att spåra en varning om att användaren anges dålig nod kapacitet; men vi kommer inte att rätta till dem; vilket innebär att användaren vill ha den kapacitet som anges som > än noden verkligen har eller om kapaciteten är odefinierad; det kommer att ta obegränsad kapacitet |
|BalancingDelayAfterNewNode | Tid i sekunder, standard är 120 |Dynamisk|Ange tidsintervall i sekunder. Börja inte balansera aktiviteter inom den här perioden efter att du har lagt till en ny nod. |
|BalancingDelayAfterNodeDown | Tid i sekunder, standard är 120 |Dynamisk|Ange tidsintervall i sekunder. Börja inte balansera aktiviteter inom den här perioden efter en nod ned-händelse. |
|KapacitetConstraintPriority | Int, standard är 0 | Dynamisk|Bestämmer prioriteten för kapacitetsbegränsning: 0: Hård; 1: Mjuk; negativ: Ignorera. |
|På varandra följandeDroppedMovementsHealthReportLimit | Int, standard är 20 | Dynamisk|Definierar antalet på varandra följande gånger som ResourceBalancer-utfärdade rörelser tas bort innan diagnostik utförs och hälsovarningar avges. Negativt: Inga varningar som avges under detta tillstånd. |
|ConstraintFixPartialDelayAfterNewNode | Tid i sekunder, standard är 120 |Dynamisk| Ange tidsintervall i sekunder. DDo fix inte FaultDomain och UpgradeDomain constraint violations within this period after adding a new node. |
|ConstraintFixPartialDelayAfterNodeDown | Tid i sekunder, standard är 120 |Dynamisk| Ange tidsintervall i sekunder. Åtgärda inte felfel i felbegränsningen FaultDomain och UpgradeDomain inom den här perioden efter en nod nedåthändelse. |
|BegränsningViolationHealthReportLimit | Int, standard är 50 |Dynamisk| Definierar antalet gånger begränsning som bryter mot repliken måste permanent avfixeras innan diagnostik utförs och hälsorapporter avges. |
|DetailedConstraintViolationHealthReportLimit | Int, standard är 200 |Dynamisk| Definierar antalet gånger begränsning som bryter mot repliken måste permanent avfixeras innan diagnostik utförs och detaljerade hälsorapporter avges. |
|DetaljeradDiagnostikInfoListLimit | Int, standard är 15 |Dynamisk| Definierar antalet diagnostiska poster (med detaljerad information) per begränsning som ska inkluderas före trunkering i Diagnostik.|
|DetaljeradNodeListLimit | Int, standard är 15 |Dynamisk| Definierar antalet noder per begränsning som ska inkluderas före trunkering i rapporterna Ej placerad replik. |
|DetaljeradpartitionListLimit | Int, standard är 15 |Dynamisk| Definierar antalet partitioner per diagnostikpost för en begränsning som ska inkluderas före trunkering i Diagnostik. |
|DetaljeradVerboseHealthReportLimit | Int, standard är 200 | Dynamisk|Definierar hur många gånger en oplacerad replik måste vara permanent oplacerad innan detaljerade hälsorapporter skickas ut. |
|Framtvinga TjänsterMetricCapacities|bool, standard är FALSKT | Statisk |Möjliggör skydd av infrastrukturtjänster. Alla användartjänster är under ett jobbobjekt/cgroup och är begränsade till angiven mängd resurser. Detta måste vara statiskt (kräver omstart av FabricHost) som skapande / borttagning av användarjobb objekt och ange gränser som görs under öppna Fabric Host. |
|FaultDomainConstraintPriority | Int, standard är 0 |Dynamisk| Bestämmer prioriteten för feldomänbegränsning: 0: Hård; 1: Mjuk; negativ: Ignorera. |
|GlobalMovementThrottleCountingInterval | Tid i sekunder, standard är 600 |Statisk| Ange tidsintervall i sekunder. Ange längden på det tidigare intervallet som du vill spåra per domänreplikförflyttningar (används tillsammans med GlobalMovementThrottleThreshold). Kan ställas in på 0 för att ignorera global begränsning helt och hållet. |
|GlobalMovementThrottleThreshold | Uint, standard är 1000 |Dynamisk| Maximalt antal tillåtna rörelser i balanseringsfasen i det tidigare intervallet som anges av GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdFör balans | Uint, standard är 0 | Dynamisk|Maximalt antal tillåtna rörelser i balanseringsfasen i det senaste intervallet som anges av GlobalMovementThrottleCountingInterval. 0 anger ingen gräns. |
|GlobalMovementThrottleThresholdForPlacement | Uint, standard är 0 |Dynamisk| Maximalt antal tillåtna rörelser i placeringsfasen i det senaste intervallet som anges av GlobalMovementThrottleCountingInterval.0 anger ingen gräns.|
|GlobalMovementThrottleThresholdPercentage|dubbel är standard 0|Dynamisk|Maximalt antal totala förflyttningar som tillåts i balanserings- och placeringsfaser (uttryckt i procent av det totala antalet repliker i klustret) under det senaste intervallet som anges av GlobalMovementThrottleCountingInterval. 0 anger ingen gräns. Om både detta och GlobalMovementThrottleThreshold anges; då mer konservativ gräns används.|
|GlobalMovementThrottleThresholdPercentageForBalancing|dubbel är standard 0|Dynamisk|Maximalt antal tillåtna rörelser i balanseringsfasen (uttryckt i procent av det totala antalet repliker i PLB) under det tidigare intervallet som anges av GlobalMovementThrottleCountingInterval. 0 anger ingen gräns. Om både detta och GlobalMovementThrottleThresholdForBalancing anges; då mer konservativ gräns används.|
|InBuildThrottlingAssociatedMetric | sträng, är standard "" |Statisk| Det associerade måttnamnet för den här begränsningen. |
|InBuildThrottlingEnabled | Bool, standard är falskt |Dynamisk| Bestäm om begränsningen i build är aktiverad. |
|InBuildThrottlingGlobalMaxValue | Int, standard är 0 |Dynamisk|Det maximala antalet in-build-repliker som tillåts globalt. |
|Avbrottsbalansering FörAllFailoverUnitUpdates | Bool, standard är falskt | Dynamisk|Avgör om någon typ av redundansenhetsuppdatering ska avbryta snabb eller långsam balanseringskörning. Med angiven "falsk" balanseringskörning avbryts om FailoverUnit: skapas/tas bort. har saknade repliker; ändrad primär replikplats eller ändrat antal repliker. Balanseringskörningen avbryts INTE i andra fall - om FailoverUnit: har extra repliker; ändrat en replikflagga. endast partitionsversionen eller något annat ärende. |
|MinConstraintCheckInterval | Tid i sekunder, standard är 1 |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta tid som måste gå före två på varandra följande constraint checkrundor. |
|MinLoadBalancingInterval | Tid i sekunder, standard är 5 |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta tid som måste gå före två på varandra följande balanseringsrundor. |
|MinPlacementInterval | Tid i sekunder, standard är 1 |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta tid som måste gå före två på varandra följande placeringsrundor. |
|FlyttaExistingReplicaForPlacement | Bool, standard är sant |Dynamisk|Ange vilken som avgör om befintlig replik ska flyttas under placeringen. |
|MovementPerPartitionThrottleCountingInterval | Tid i sekunder, standard är 600 |Statisk| Ange tidsintervall i sekunder. Ange längden på det tidigare intervallet för vilket replikrörelser för varje partition (används tillsammans med MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, standard är 50 |Dynamisk| Ingen balanseringsrelaterad rörelse kommer att ske för en partition om antalet balanseringsrelaterade rörelser för repliker av den partitionen har nått eller överskridit MovementPerFailoverUnitThrottleThreshold under det tidigare intervallet som indikeras av MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, standard är falskt |Dynamisk| Inställning som avgör om överordnade repliker kan flyttas för att åtgärda tillhörighetsbegränsningar.|
|Delvis Placerade Tjänster | Bool, standard är sant |Dynamisk| Avgör om alla tjänstrepliker i klustret kommer att placeras "allt eller inget" med tanke på begränsade lämpliga noder för dem.|
|PlaceChildUtanFörälder | Bool, standard är sant | Dynamisk|Inställning som avgör om underordnad servicereplik kan placeras om ingen överordnad replik är uppe. |
|PlaceringConstraintPriority | Int, standard är 0 | Dynamisk|Bestämmer prioriteten för placeringsbegränsning: 0: Hård; 1: Mjuk; negativ: Ignorera. |
|PlaceringConstraintValidationCacheSize | Int, standard är 10000 |Dynamisk| Begränsar storleken på tabellen som används för snabb validering och cachelagring av placeringsbegränsningsuttryck. |
|PlaceringSökTimeout | Tid i sekunder, standard är 0,5 |Dynamisk| Ange tidsintervall i sekunder. När du placerar tjänster; på sin höjd så här länge innan du returnerar ett resultat. |
|PLBRefreshGap | Tid i sekunder, standard är 1 |Dynamisk| Ange tidsintervall i sekunder. Definierar den minsta tid som måste gå innan PLB uppdaterar tillståndet igen. |
|PreferredLocationConstraintPriority | Int, standard är 2| Dynamisk|Bestämmer prioriteten för önskat platsvillkor: 0: Hård; 1: Mjuk; 2: Optimering; negativt: Ignorera |
|PreferredPrimaryDomainsConstraintPriority| Int, standard är 1 | Dynamisk| Bestämmer prioriteten för önskad primär domänbegränsning: 0: Hård; 1: Mjuk; negativt: Ignorera |
|FöredrarUppgraderadeUD:er|bool, standard är FALSKT|Dynamisk|Aktiverar och inaktiverar logik som föredrar att flytta till redan uppgraderade UDs. Från och med SF 7.0 ändras standardvärdet för den här parametern från SANT till FALSKT.|
|PreventTransientOvercommit | Bool, standard är falskt | Dynamisk|Bestämmer om PLB omedelbart räknar med resurser som frigörs av de initierade rörelserna. Som standard; PLB kan initiera flytta ut och flytta in på samma nod som kan skapa övergående överåtagande. Om du ställer in den här parametern på true förhindras att dessa typer av överåtaganden och defrag för defrag på begäran (aka placementWithMove) inaktiveras. |
|ScaleoutCountConstraintPriority | Int, standard är 0 |Dynamisk| Bestämmer prioriteten för skalningsantalsbegränsningen: 0: Hård; 1: Mjuk; negativ: Ignorera. |
|DelklusserbartEnabled|Bool, standard är FALSKT | Dynamisk |Bekräfta delkluseringen vid beräkning av standardavvikelse för balansering |
|DelkluseraReportingPolicy| Int, standard är 1 |Dynamisk|Definierar hur och om de delklussande hälsorapporterna skickas: 0: Rapportera inte; 1: Varning; 2: OK |
|SwapPrimaryThrottlingAssocieradeMetric | sträng, är standard ""|Statisk| Det associerade måttnamnet för den här begränsningen. |
|SwapPrimaryThrottlingEnabled | Bool, standard är falskt|Dynamisk| Bestäm om begränsningen för växlingsprimär begränsning är aktiverad. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, standard är 0 |Dynamisk| Det maximala antalet swap-primära repliker som tillåts globalt. |
|TraceCRMReasons |Bool, standard är sant |Dynamisk|Anger om orsaker till CRM-utfärdade meddelanden ska spåras till kanalen för drifthändelser. |
|UppgraderaDomainConstraintPriority | Int, standard är 1| Dynamisk|Bestämmer prioriteten för uppgraderingsdomänbegränsningen: 0: Hård; 1: Mjuk; negativ: Ignorera. |
|AnvändaMoveCostReports | Bool, standard är falskt | Dynamisk|Instruerar LB att ignorera kostnadselementet i poängfunktionen. potentiellt stort antal drag för bättre balanserad placering. |
|AnvändSeparateSecondaryLoad | Bool, standard är sant | Dynamisk|Inställning som avgör om du använder olika sekundära belastningar. |
|AnvändSeparateSecondaryMoveCost|Bool, standard är FALSKT | Dynamisk|Inställning som avgör om PLB ska använda olika flyttkostnader för sekundär på varje nod Om UseSeparateSecondaryMoveCost är inaktiverat: - Rapporterad flyttkostnad för sekundär på en nod resulterar i översmärtningsflyttningskostnad för varje sekundär (på alla andra noder) Om UseSeparateSecondaryMoveCost är aktiverat: - Rapporterad flyttkostnad för sekundär på en nod börjar endast gälla på den sekundära (ingen effekt på sekundärfiler på andra noder) - Om replikkrasch inträffar - skapas ny replik med standardflyttkostnaden som anges på tjänsten nivå - Om PLB flyttar befintlig replika - flytta kostnaden går med det |
|ValidatePlacementConstraint | Bool, standard är sant |Dynamisk| Anger om placementConstraint-uttrycket för en tjänst valideras när tjänstens ServiceDescription uppdateras. |
|ValideraPrimaryPlacementConstraintOnPromote| Bool, standard är SANT |Dynamisk|Anger om placementConstraint-uttrycket för en tjänst utvärderas för primär invald vid redundans. |
|VerboseHealthReportLimit | Int, standard är 20 | Dynamisk|Definierar hur många gånger en replik måste gå oplacerad innan en hälsovarning rapporteras för den (om detaljerad hälsorapportering är aktiverad). |
|NodeLoadsOperationalTracingEnabled | Bool, standard är sant |Dynamisk|Config som aktiverar nodinläsning driftstrukturspårning i händelsearkivet. |
|NodeLoadsOperationalTracingInterval | TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(20) | Dynamisk|Ange tidsintervall i sekunder. Intervallet som noden ska spåras läses in i händelsearkivet för varje tjänstdomän. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AnsökanUpgradeMaxReplicaCloseDuration | Tid i sekunder, standard är 900 |Dynamisk|Ange tidsintervall i sekunder. Den varaktighet som systemet väntar på innan du avslutar tjänstvärdar som har repliker som har fastnat i nära under programuppgradering.|
|FabricUpgradeMaxReplicaCloseDuration | Tid i sekunder, standard är 900 |Dynamisk| Ange tidsintervall i sekunder. Den varaktighet som systemet väntar på innan du avslutar tjänstvärdar som har repliker som har fastnat i nära under infrastrukturuppgradering. |
|GraciösReplicaShutdownMaxDuration|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(120)|Dynamisk|Ange tidsintervall i sekunder. Den varaktighet som systemet väntar på innan du avslutar tjänstvärdar som har repliker som har fastnat i nära. Om det här värdet är inställt på 0 instrueras inte repliker att stängas.|
|NodeDeactivationMaxReplicaCloseDuration | Tid i sekunder, standard är 900 |Dynamisk|Ange tidsintervall i sekunder. Den varaktighet som systemet väntar på innan du avslutar tjänstvärdar som har repliker som har fastnat i nära under nodavaktivering. |
|PeriodicApiSlowTraceInterval | Tid i sekunder, standard är 5 minuter |Dynamisk| Ange tidsintervall i sekunder. PeriodicApiSlowTraceInterval definierar intervallet över vilket långsamma API-anrop kommer att spåras av API-övervakaren. |
|ReplicaChangeRoleFailureRestartTrerhold|int är standard 10|Dynamisk| Heltal. Ange antalet API-fel under den primära befordran efter vilken automatisk begränsningsåtgärd (omstart av repliker) ska tillämpas. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, standard är 2147483647|Dynamisk| Heltal. Ange antalet API-fel under primär kampanj efter vilken varningshälsorapporten ska höjas.|
|ServiceApiHealthDuration | Tid i sekunder, standard är 30 minuter |Dynamisk| Ange tidsintervall i sekunder. ServiceApiHealthDuration definierar hur länge väntar vi på att ett tjänst-API ska köras innan vi rapporterar att det är felfritt. |
|ServiceReconfigurationApiHealthDuration | Tid i sekunder, standard är 30 |Dynamisk| Ange tidsintervall i sekunder. ServiceReconfigurationApiHealthDuration definierar hur länge väntar vi på att ett tjänst-API ska köras innan vi rapporterar felfritt. Detta gäller API-anrop som påverkar tillgängligheten.|

## <a name="replication"></a>Replikering
| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, standard är Vanligt::TimeSpan::FromMilliseconds(15)|Statisk|Ange tidsintervall i sekunder. Bestämmer hur lång tid replikören väntar efter att ha fått en åtgärd innan en bekräftelse skickas tillbaka. Andra åtgärder som tas emot under den här tidsperioden kommer att få sina bekräftelser skickade tillbaka i ett enda meddelande-> vilket minskar nätverkstrafiken men eventuellt minskar dataflödet för replikatorn.|
|MaxCopyQueueSize|uint, standard är 1024|Statisk|Det här är det maximala värdet som definierar den ursprungliga storleken för kön som underhåller replikeringsåtgärder. Observera att det måste vara en effekt på 2. Om kön växer till den här storleksåtgärden under körning begränsas mellan de primära och sekundära replikatorerna.|
|MaxPrimaryReplicationQueueMemorySize|uint, standard är 0|Statisk|Detta är det maximala värdet för den primära replikeringskön i byte.|
|MaxPrimaryReplicationQueueSize|uint, standard är 1024|Statisk|Det här är det maximala antalet åtgärder som kan finnas i den primära replikeringskön. Observera att det måste vara en effekt på 2.|
|MaxReplicationMessageSize|uint, standard är 52428800|Statisk|Maximal meddelandestorlek för replikeringsåtgärder. Standard är 50MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, standard är 0|Statisk|Detta är det maximala värdet för den sekundära replikeringskön i byte.|
|MaxSecondaryReplicationQueueSize|uint, standard är 2048|Statisk|Det här är det maximala antalet åtgärder som kan finnas i den sekundära replikeringskön. Observera att det måste vara en effekt på 2.|
|KöHälsaÖvervakarInval|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(30)|Statisk|Ange tidsintervall i sekunder. Det här värdet bestämmer den tidsperiod som används av Replikatorn för att övervaka alla varnings-/felhälsohändelser i replikeringsåtgärdskön. Värdet "0" inaktiverar hälsoövervakning |
|KöHälsaVarningsväsningArbetssättPercent|uint, standard är 80|Statisk|Det här värdet bestämmer replikeringsköanvändningen(i procent) varefter vi rapporterar varning om hög köanvändning. Vi gör det efter ett respitintervall för QueueHealthMonitoringInterval. Om köanvändningen sjunker under den här procentsatsen i respitintervallet|
|ReplicatorLäggare|sträng, är standard "localhost:0"|Statisk|Slutpunkten i form av en sträng -'IP:Port' som används av Windows Fabric Replicator för att upprätta anslutningar med andra repliker för att skicka/ta emot åtgärder.|
|ReplicatorListenAdress|sträng, är standard "localhost:0"|Statisk|Slutpunkten i form av en sträng -'IP:Port' som används av Windows Fabric Replicator för att ta emot åtgärder från andra repliker.|
|ReplicatorPublishLäggadress|sträng, är standard "localhost:0"|Statisk|Slutpunkten i form av en sträng -'IP:Port' som används av Windows Fabric Replicator för att skicka åtgärder till andra repliker.|
|ÅterförsökInterval|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(5)|Statisk|Ange tidsintervall i sekunder. När en åtgärd går förlorad eller avvisas avgör timern hur ofta replikatorn ska försöka skicka åtgärden igen.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|IsEnabled|bool, standard är FALSKT |Statisk|Kontrollerar om tjänsten är aktiverad i klustret eller inte. |

## <a name="runas"></a>Runas

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, är standard "" |Dynamisk|Anger kontonamnet RunAs. Detta behövs bara för kontotypen "DomainUser" eller "ManagedServiceAccount". Giltiga värden är "domän\användare" eller "user@domain". |
|RunAsAccountType|sträng, är standard "" |Dynamisk|Anger kontotypen RunAs. Detta behövs för alla RunAs-avsnitt Giltiga värden är "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword (På)|sträng, är standard "" |Dynamisk|Anger lösenordet för RunAs-kontot. Detta behövs bara för kontotypen "DomainUser". |

## <a name="runas_dca"></a>RunAs_DCA

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, är standard "" |Dynamisk|Anger kontonamnet RunAs. Detta behövs bara för kontotypen "DomainUser" eller "ManagedServiceAccount". Giltiga värden är "domän\användare" eller "user@domain". |
|RunAsAccountType|sträng, är standard "" |Dynamisk|Anger kontotypen RunAs. Detta behövs för alla RunAs-avsnitt Giltiga värden är "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword (På)|sträng, är standard "" |Dynamisk|Anger lösenordet för RunAs-kontot. Detta behövs bara för kontotypen "DomainUser". |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, är standard "" |Dynamisk|Anger kontonamnet RunAs. Detta behövs bara för kontotypen "DomainUser" eller "ManagedServiceAccount". Giltiga värden är "domän\användare" eller "user@domain". |
|RunAsAccountType|sträng, är standard "" |Dynamisk|Anger kontotypen RunAs. Detta behövs för alla RunAs-avsnitt Giltiga värden är "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword (På)|sträng, är standard "" |Dynamisk|Anger lösenordet för RunAs-kontot. Detta behövs bara för kontotypen "DomainUser". |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|RunAsAccountName |sträng, är standard "" |Dynamisk|Anger kontonamnet RunAs. Detta behövs bara för kontotypen "DomainUser" eller "ManagedServiceAccount". Giltiga värden är "domän\användare" eller "user@domain". |
|RunAsAccountType|sträng, är standard "" |Dynamisk|Anger kontotypen RunAs. Detta behövs för alla RunAs-avsnitt Giltiga värden är "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword (På)|sträng, är standard "" |Dynamisk|Anger lösenordet för RunAs-kontot. Detta behövs bara för kontotypen "DomainUser". |

## <a name="security"></a>Säkerhet
| **Parametern** | **Tillåtna värden** |**Uppgraderingsprincip**| **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|sträng, är standard ""|Statisk|AAD Cert Endpoint Format, standard Azure Commercial, angiven för icke-standardmiljö som Azure Government "https:\//login.microsoftonline.us/{0}/federationmetadata/2007-06/federationmetadata.xml" |
|AADClientApplication|sträng, är standard ""|Statisk|Namn eller ID för native klientprogram som representerar Fabric-klienter |
|AADClusterApplication|sträng, är standard ""|Statisk|Namn eller ID för webb-API-program som representerar klustret |
|AADLoginEndpoint|sträng, är standard ""|Statisk|AAD Login Endpoint, standard Azure Commercial, angiven för icke-standardmiljö som Azure Government "https:\//login.microsoftonline.us" |
|AADTenantId|sträng, är standard ""|Statisk|Klient-ID (GUID) |
|AccepteraExpireradPindClusterCertificate|bool, standard är FALSKT|Dynamisk|Flagga som anger om du vill acceptera utgångna klustercertifikat som deklarerats med tumavtryck gäller endast klustercertifikat. för att hålla klustret vid liv. |
|AdminClientCertThumbprints|sträng, är standard ""|Dynamisk|Tumavtryck för certifikat som används av klienter i administratörsrollen. Det är en kommaavgränsad namnlista. |
|AADTokenEndpointFormat|sträng, är standard ""|Statisk|AAD Token Endpoint, standard Azure Commercial, angiven för icke-standardmiljö som Azure Government "https:\//login.microsoftonline.us/{0}" |
|AdminClientClaims|sträng, är standard ""|Dynamisk|Alla möjliga anspråk som förväntas från administratörsklienter. samma format som ClientClaims; den här listan läggs internt till i ClientClaims; så inget behov av att också lägga till samma poster till ClientClaims. |
|AdminClientId-entiteter|sträng, är standard ""|Dynamisk|Windows-identiteter för fabric-klienter i administratörsrollen. används för att auktorisera privilegierade tygoperationer. Det är en kommaavgränsad lista. varje post är ett domännamn eller gruppnamn. För enkelhetens skull; Kontot som kör fabric.exe tilldelas automatiskt administratörsrollen. så är gruppen ServiceFabricAdministratorer. |
|AppRunAsAccountGroupX509Mapp|sträng är standard /home/sfuser/sfusercerts |Statisk|Mapp där AppRunAsAccountGroup X509-certifikat och privata nycklar finns |
|CertifikatExpirySafetyMargin|TimeSpan, standard är vanligt::TimeSpan::FromMinutes(43200)|Statisk|Ange tidsintervall i sekunder. Säkerhetsmarginal för certifikatets utgång. certifikatets hälsorapportstatus ändras från OK till Varning när förfallodatumet är närmare än så. Standard är 30 dagar. |
|CertifikatHealthReportingInterval|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(3600 * 8)|Statisk|Ange tidsintervall i sekunder. Ange intervall för hälsorapportering för certifikat. standard till 8 timmar; inställningen till 0 inaktiverar redovisning av certifikathälsarapportering |
|ClientCertThumbprints|sträng, är standard ""|Dynamisk|Tumavtryck av certifikat som används av klienter för att prata med klustret. kluster använder den här auktorisera inkommande anslutningen. Det är en kommaavgränsad namnlista. |
|ClientClaimAuthEnabled|bool, standard är FALSKT|Statisk|Anger om anspråksbaserad autentisering är aktiverad på klienter. ställa in detta sant implicit ställer ClientRoleEnabled. |
|ClientClaims (KlientClaims)|sträng, är standard ""|Dynamisk|Alla möjliga anspråk som förväntas från klienter för anslutning till gateway. Detta är en "ELLER" lista: ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry ... varje Skadeenspråk är en "OCH"-lista: ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|ClientId-enheter|sträng, är standard ""|Dynamisk|Windows-identiteter för FabricClient; namngaten använder detta för att auktorisera inkommande anslutningar. Det är en kommaavgränsad lista. varje post är ett domännamn eller gruppnamn. För enkelhetens skull; kontot som kör fabric.exe tillåts automatiskt. så är grupp ServiceFabricAllowedUsers och ServiceFabricAdministratorer. |
|ClientRoleEnabled|bool, standard är FALSKT|Statisk|Anger om klientrollen är aktiverad. när den är inställd på true; klienter tilldelas roller baserat på deras identiteter. För V2; om du aktiverar detta innebär att klienten inte i AdminClientCommonNames/AdminClientId-entiteter bara kan utföra skrivskyddade åtgärder. |
|ClusterCertThumbprints|sträng, är standard ""|Dynamisk|Tumavtryck av certifikat som tillåts ansluta till klustret. en kommaavgränsad namnlista. |
|ClusterCredentialType|sträng är standardvärdet "Ingen"|Inte tillåtet|Anger vilken typ av säkerhetsautentiseringsuppgifter som ska användas för att skydda klustret. Giltiga värden är "None/X509/Windows" |
|ClusterId-entiteter|sträng, är standard ""|Dynamisk|Windows-identiteter för klusternoder. används för klustermedlemskapsauktorisering. Det är en kommaavgränsad lista. varje post är ett domännamn eller gruppnamn |
|ClusterSpn|sträng, är standard ""|Inte tillåtet|Tjänstens huvudnamn för klustret. när tyget körs som en enda domänanvändare (gMSA/domänanvändarkonto). Det är SPN för leasing lyssnare och lyssnare i fabric.exe: federation lyssnare; interna replikeringslyssnare. servavlyssnare och namnge gateway listener. Detta bör lämnas tomt när tyget körs som maskinkonton; i vilket fall ansluta sida beräkning lyssnare SPN från lyssnaren transportadress. |
|CrlCheckingFlag|uint, standard är 0x40000000|Dynamisk|Standardflagga för validering av certifikatkedja. kan åsidosättas med komponentspecifik flagg. t.ex. Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x400000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x800000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Inställning till 0 inaktiverar CRL-kontroll Fullständig lista över värden som stöds dokumenteras av dwFlags av CertGetificateTor:https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, standard är Vanligt::TimeSpan::FrånMinuter(15)|Dynamisk|Ange tidsintervall i sekunder. Hur länge CRL-kontroll är inaktiverad för ett visst certifikat efter att offlinefel har uppgetts. om CRL-offlinefel kan ignoreras. |
|CrlOfflineHealthReportTtl|TimeSpan, standard är Vanligt::TimeSpan::FromMinutes(1440)|Dynamisk|Ange tidsintervall i sekunder. |
|InaktiveraFirewallRuleForDomainProfile| bool, standard är SANT |Statisk| Anger om brandväggsregeln inte ska aktiveras för domänprofilen |
|InaktiveraFirewallRuleForPrivateProfile| bool, standard är SANT |Statisk| Anger om brandväggsregeln inte ska aktiveras för privat profil | 
|InaktiveraFirewallRuleForPublicProfile| bool, standard är SANT | Statisk|Anger om brandväggsregeln inte ska aktiveras för offentlig profil |
| EnforceLinuxMinTlsVersion | bool, standard är FALSKT | Statisk | Om den är inställd på true; Endast TLS version 1.2+ stöds.  Om falskt; stöd för tidigare TLS-versioner. Gäller endast Linux |
| FramtvingaprevalidationPåsäkerhetsändringar | bool, standard är FALSKT| Dynamisk | Flagga som styr beteendet för klusteruppgradering när du upptäcker ändringar av dess säkerhetsinställningar. Om värdet är "true" försöker klusteruppgraderingen se till att minst ett av certifikaten som matchar någon av presentationsreglerna kan passera en motsvarande verifieringsregel. Förhandsvalningen körs innan de nya inställningarna tillämpas på en nod, men körs bara på noden som är värd för den primära repliken för Klusterhanteraren-tjänsten när uppgraderingen initieras. Standardvärdet är för närvarande inställt på "false". från och med version 7.1 ställs inställningen in på "true" för nya Azure Service Fabric-kluster.|
|FabricHostSpn (tyg)| sträng, är standard "" |Statisk| Tjänstens huvudnamn FabricHost; när tyg körs som en enda domänanvändare (gMSA/domänanvändarkonto) och FabricHost körs under datorkonto. Det är SPN för IPC lyssnare för FabricHost; som som standard ska lämnas tom eftersom FabricHost körs under maskinkonto |
|IgnoreraCrlOfflineError|bool, standard är FALSKT|Dynamisk|Om du vill ignorera CRL-offlinefel när serversidan verifierar inkommande klientcertifikat |
|IgnoreraSvrCrlOfflineError|bool, standard är SANT|Dynamisk|Om du vill ignorera crl-offlinefel när klientsidan verifierar inkommande servercertifikat. standard till sant. Attacker med återkallade servercertifikat kräver komprometterande DNS. svårare än med återkallade klientcertifikat. |
|ServerAuthCredentialType|sträng är standardvärdet "Ingen"|Statisk|Anger vilken typ av säkerhetsautentiseringsuppgifter som ska användas för att skydda kommunikationen mellan FabricClient och klustret. Giltiga värden är "None/X509/Windows" |
|ServerCertThumbprints|sträng, är standard ""|Dynamisk|Tumavtryck för servercertifikat som används av klustret för att prata med klienter. klienter använder detta för att autentisera klustret. Det är en kommaavgränsad namnlista. |
|InställningarX509StoreName| sträng, är standard "MY"| Dynamisk|X509-certifikatarkiv som används av tyg för konfigurationsskydd |
|AnvändaClusterCertForIpcServerTlsSecurity|bool, standard är FALSKT|Statisk|Om klustercertifikat ska användas för att skydda TLS-transportenhet för IPC Server |
|X509Mapp|sträng är standardvärdet /var/lib/waagent|Statisk|Mapp där X509-certifikat och privata nycklar finns |
|TLS1_2_CipherList| sträng| Statisk|Om den är inställd på en sträng som inte är tom. åsidosätter listan över chiffer som stöds för TLS1.2 och lägre. Se dokumentationen "openssl-chiffer" för att hämta listan över chiffer som stöds och listformatet Exempel på stark chifferlista för TLS1.2: "ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384: ECDHE-RSA-AES-128-GCM-SHA256:ECDHE-ECDSA-AES256-CBC-SHA384:ECDHE-ECDSA-AES128-CBC-SHA256:ECDHE-RSA-AES256-CBC-SHA384:ECDHE-RSA-AES128-CBC-SHA256" Gäller endast Linux. |

## <a name="securityadminclientx509names"></a>Säkerhet/AdminClientX509Namn

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|X509NameMap, standard är Ingen|Dynamisk|Detta är en lista över "Namn" och "Värde" par. Varje "Namn" är av ämnesnamn eller DnsName för X509-certifikat som auktoriserats för administratörsklientåtgärder. För ett givet "Namn" är "Värde" en komma separat lista över certifikattumavtryck för emittenten, om inte tom, måste den direkta utfärdaren av administratörsklientcertifikat finnas i listan. |

## <a name="securityclientaccess"></a>Säkerhet/klientåtkomst

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AktiveraNod |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för aktivering av en nod. |
|AvbrytTestCommand |sträng, är standard "Admin" |Dynamisk| Ställer in en specifik TestCommand - om det är under flygning. |
|CodePackageControl |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för omstart av kodpaket. |
|SkapaApplication |sträng, är standard "Admin" | Dynamisk|Säkerhetskonfiguration för att skapa program. |
|Skapakomeldeployment|sträng, är standard "Admin"| Dynamisk|Skapar en sammansättningsdistribution som beskrivs av skrivfiler |
|SkapaGatewayResource|sträng, är standard "Admin"| Dynamisk|Skapa en gatewayresurs |
|Skapa namn |sträng, är standard "Admin" |Dynamisk|Säkerhetskonfiguration för att skapa URI.Security configuration for Naming URI creation. |
|Skapa Nätverk|sträng, är standard "Admin" |Dynamisk|Skapar ett behållarnätverk |
|CreateService |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att skapa tjänsten. |
|SkapaServiceFromTemplate |sträng, är standard "Admin" |Dynamisk|Säkerhetskonfiguration för att skapa tjänster från mallen. |
|Skapavolym|sträng, är standard "Admin"|Dynamisk|Skapar en volym |
|Inaktiveranod |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att inaktivera en nod. |
|InaktiveranObeniderBatch |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att inaktivera flera noder. |
|Ta bort |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfigurationer för klientborttagning av avbildningsklient. |
|DeleteApplication |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för borttagning av program. |
|Ta bortutvecklad delläggning|sträng, är standard "Admin"| Dynamisk|Tar bort sammansättningsdistributionen |
|Ta bortGatewayResource|sträng, är standard "Admin"| Dynamisk|Tar bort en gatewayresurs |
|DeleteName (borttagningsnamn) |sträng, är standard "Admin" |Dynamisk|Säkerhetskonfiguration för namngivning av URI-borttagning. |
|DeleteNetwork|sträng, är standard "Admin" |Dynamisk|Tar bort ett behållarnätverk |
|DeleteService |sträng, är standard "Admin" |Dynamisk|Säkerhetskonfiguration för borttagning av tjänsten. |
|Ta bortvolym|sträng, är standard "Admin"|Dynamisk|Tar bort en volym.| 
|Räkna upp Egenskaper |sträng, är standard\|\|"Admin User" | Dynamisk|Säkerhetskonfiguration för namngivningseskapsuppräkning. |
|Räkna uppUbnamn |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för namngivning av URI-uppräkning. |
|FileContent |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för klientfilöverföring för avbildningsarkiv (extern till kluster). |
|Filladdad |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för hämtning av klientfilar för avbildningsfiler (extern till kluster). |
|AvslutaInfrastrukturTask |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att slutföra infrastrukturuppgifter. |
|GetChaosReport | sträng, är standard\|\|"Admin User" |Dynamisk| Hämtar status för Chaos inom ett visst tidsintervall. |
|GetClusterConfiguration | sträng, är standard\|\|"Admin User" | Dynamisk|Inducerar GetClusterConfiguration på en partition. |
|GetClusterConfigurationUpgradeStatus | sträng, är standard\|\|"Admin User" |Dynamisk| Inducerar GetClusterConfigurationUpgradeStatus på en partition. |
|GetFabricUpgradeStatus |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för avsökning av klusteruppgraderingsstatus. |
|GetFolderSize |sträng, är standard "Admin" |Dynamisk|Säkerhetskonfiguration för FileStoreServices mappstorlek |
|HämtaNodeDeactivationStatus |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för kontroll av avaktiveringsstatus. |
|GetNodeTransitionProgress | sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för att få framsteg på ett nodövergångskommando. |
|GetPartitionDataLossProgress | sträng, är standard\|\|"Admin User" | Dynamisk|Hämtar förloppet för ett anropa api-anrop för dataförlust. |
|GetPartitionQuorumLossProgress | sträng, är standard\|\|"Admin User" |Dynamisk| Hämtar förloppet för ett anropande kvorumförlust api-anrop. |
|GetPartitionRestartProgress | sträng, är standard\|\|"Admin User" |Dynamisk| Hämtar förloppet för ett api-anrop för omstartspartitioner. |
|GetSecrets|sträng, är standard "Admin"|Dynamisk|Hämta hemliga värden |
|GetServiceDescription |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för tjänstmeddelanden med lång omröstning och lästjänstbeskrivningar. |
|FåStagingLocation |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för hämtning av avbildningslager av klienten. |
|GetStoreLocation |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för klientlagringsplatshämtning för avbildningsarklag. |
|GetUpgradeOrchestrationServiceState|sträng, är standard "Admin"| Dynamisk|Inducerar GetUpgradeOrchestrationServiceState på en partition |
|FåUppgradesPendingApproval |sträng, är standard "Admin" |Dynamisk| Inducerar GetUpgradesPendingApproval på en partition. |
|GetUpgradeStatus |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för uppgraderingsstatus för avsökningsprogram. |
|Internlista |sträng, är standard "Admin" | Dynamisk|Säkerhetskonfiguration för klientfilslisteåtgärd för avbildningsarkiv (intern). |
|InvokeContainerApi|sträng,standard är "Admin"|Dynamisk|Anropa behållar-API |
|InvokeInfrastructureCommand |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för kommandon för hantering av infrastrukturuppgiftsfunktioner. |
|ÅberopaInfrastrukturQuery |sträng, är standard\|\|"Admin User" | Dynamisk|Säkerhetskonfiguration för att fråga infrastrukturuppgifter. |
|Visa lista |sträng, är standard\|\|"Admin User" | Dynamisk|Säkerhetskonfiguration för klientfilslisteåtgärd för avbildningsarkiv. |
|FlyttaNästaFabricUpgradeDomain |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att återuppta klusteruppgraderingar med en explicit uppgraderingsdomän. |
|MoveNextUpgradeDomain |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att återuppta programuppgraderingar med en explicit uppgraderingsdomän. |
|MoveReplicaControl |sträng, är standard "Admin" | Dynamisk|Flytta repliken. |
|NamnExister |sträng, är standard\|\|"Admin User" | Dynamisk|Säkerhetskonfiguration för namngivning av URI-förekomstkontroller. |
|NodeControl |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för start; stoppa; och starta om noder. |
|NodeStateReflyttad |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för rapportnodtillstånd har tagits bort. |
|Pinga |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för klientping. |
|FördeployPackageToNode |sträng, är standard "Admin" |Dynamisk| Api för fördistribution. |
|PrefixResolveService |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för klagomålsbaserad tjänstprefixmatchning. |
|EgenskapReadBatch |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för namngivningsegenskapsläsningsåtgärder. |
|FastighetsförmedlingSbatch |sträng, är standard "Admin" |Dynamisk|Säkerhetskonfigurationer för namngivningsegenskapsskrivningsåtgärder. |
|ProvisionApplicationType |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för etablering av programtyp. |
|ProvisionFabric |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för MSI- och/eller klustermanifestetablering. |
|Söka i data |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för frågor. |
|RecoverPartition |sträng, är standard "Admin" | Dynamisk|Säkerhetskonfiguration för återställning av en partition. |
|RecoverPartitions |sträng, är standard "Admin" | Dynamisk|Säkerhetskonfiguration för återställning av partitioner. |
|RecoverServicePartitions |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för återställning av tjänstpartitioner. |
|RecoverSystemPartitioner |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för återställning av systemtjänstpartitioner. |
|Ta bortNodeDeactivations |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för återställning av inaktivering på flera noder. |
|RapportFabricUpgradeHealth |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att återuppta klusteruppgraderingar med den aktuella uppgraderingen. |
|RapportFault |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för rapportering av fel. |
|RapportHälsa |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för rapportering av hälsotillstånd. |
|RapportUpgradeHealth |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att återuppta programuppgraderingar med den aktuella uppgraderingen. |
|Återställpartitionload |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för återställningsbelastning för en redundansunit. |
|ResolveNameOwner |sträng, är standard\|\|"Admin User" | Dynamisk|Säkerhetskonfiguration för att lösa namngivning av URI-ägare. |
|ResolvePartition (Löspartition) |sträng, är standard\|\|"Admin User" | Dynamisk|Säkerhetskonfiguration för att lösa systemtjänster. |
|ResolveService |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för klagomålsbaserad tjänstlösning. |
|ResolveSystemService|sträng, är standard\|\|"Admin User"|Dynamisk| Säkerhetskonfiguration för att lösa systemtjänster |
|RollbackApplicationUpgrade |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för uppgradering av programmet för att återställa programmet. |
|RollbackFabricUpgrade |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att återställa klusteruppgraderingar. |
|ServiceAnmälningar |sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för händelsebaserade tjänstmeddelanden. |
|SetUpgradeOrchestrationServiceState|sträng, är standard "Admin"| Dynamisk|Inducerar SetUpgradeOrchestrationServiceState på en partition |
|StartApprovedUpgrades StartApprovedUpgrades StartApprovedUpgrades StartAp |sträng, är standard "Admin" |Dynamisk| Inducerar StartApprovedUpgrades på en partition. |
|StartChaos (startkaos) |sträng, är standard "Admin" |Dynamisk| Startar Chaos - om det inte redan har startats. |
|StartClusterKonfigureringUppgrade |sträng, är standard "Admin" |Dynamisk| Inducerar StartClusterConfigurationUpgrade på en partition. |
|StartInfrastrukturTask |sträng, är standard "Admin" | Dynamisk|Säkerhetskonfiguration för start av infrastrukturuppgifter. |
|StartNodeTransition |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att starta en nodövergång. |
|StartPartitionDataLoss |sträng, är standard "Admin" |Dynamisk| Inducerar dataförlust på en partition. |
|StartPartitionQuorumLoss |sträng, är standard "Admin" |Dynamisk| Inducerar kvorumförlust på en partition. |
|StartPartitionRestart |sträng, är standard "Admin" |Dynamisk| Startar samtidigt om vissa eller alla repliker av en partition. |
|StopChaos (stoppKaos) |sträng, är standard "Admin" |Dynamisk| Stoppar Kaos - om det har startats. |
|VäxlaVerboseServicePlacementHealthReporting | sträng, är standard\|\|"Admin User" |Dynamisk| Säkerhetskonfiguration för Växla utförlig serviceplacement hälsorapportering. |
|Ej etableringApplicationType |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för programtyp avetablering. |
|OetableraFabric |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för AVetablering av MSI och/eller klustermanifest. |
|OpålitligTransportControl |sträng, är standard "Admin" |Dynamisk| Opålitlig transport för att lägga till och ta bort beteenden. |
|UpdateService |sträng, är standard "Admin" |Dynamisk|Säkerhetskonfiguration för tjänstuppdateringar. |
|UppgraderingTilläms |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för att starta eller avbryta programuppgraderingar. |
|UppgraderingSdeprodera|sträng, är standard "Admin"| Dynamisk|Uppgraderar sammansättningsdistributionen |
|UppgraderaFabric |sträng, är standard "Admin" |Dynamisk| Säkerhetskonfiguration för start av klusteruppgraderingar. |
|Ladda upp |sträng, är standard "Admin" | Dynamisk|Säkerhetskonfiguration för klientöverföring av avbildningslager. |

## <a name="securityclientcertificateissuerstores"></a>Säkerhet/ClientCertificateIssuerStores

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|IssuerStoreKeyValueMap, standard är Ingen |Dynamisk|X509 utfärdarcertifikatarkiv för klientcertifikat. Namn = clientIssuerCN; Värde = kommaavgränsad lista över butiker |

## <a name="securityclientx509names"></a>Säkerhet/ClientX509Namn

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|X509NameMap, standard är Ingen|Dynamisk|Detta är en lista över "Namn" och "Värde" par. Varje "Namn" är av ämnesnamn eller DnsName för X509-certifikat som auktoriserats för klientåtgärder. För ett givet "Namn" är "Värde" en komma separat lista över certifikattumavtryck för emittenten, om inte tom, måste den direkta utfärdaren av klientcertifikat finnas i listan.|

## <a name="securityclustercertificateissuerstores"></a>Säkerhet/ClusterCertificateIssuerStores

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|IssuerStoreKeyValueMap, standard är Ingen |Dynamisk|X509 utfärdarcertifikatarkiv för klustercertifikat. Namn = clusterIssuerCN; Värde = kommaavgränsad lista över butiker |

## <a name="securityclusterx509names"></a>Säkerhet/ClusterX509Namn

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|X509NameMap, standard är Ingen|Dynamisk|Detta är en lista över "Namn" och "Värde" par. Varje "Namn" är av ämnesansent namn eller DnsName för X509-certifikat som auktoriserats för klusteråtgärder. För ett givet "Namn", "Värde" är en komma separat lista över certifikattumavtryck för emittenten, om inte tom, måste den direkta utfärdaren av klustercertifikat finnas i listan.|

## <a name="securityservercertificateissuerstores"></a>Säkerhet/ServerCertificateIssuerStores

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|IssuerStoreKeyValueMap, standard är Ingen |Dynamisk|X509 utfärdarcertifikatarkiv för servercertifikat. Namn = serverIssuerCN; Värde = kommaavgränsad lista över butiker |

## <a name="securityserverx509names"></a>Säkerhet/ServerX509Namn

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)|X509NameMap, standard är Ingen|Dynamisk|Detta är en lista över "Namn" och "Värde" par. Varje "Namn" är av ämnesnamn eller DnsName för X509-certifikat som auktoriserats för serveråtgärder. För ett givet "Namn" är "Värde" en komma separat lista över certifikattumavtryck för emittenten, om inte tom, måste den direkta utfärdaren av servercertifikat finnas i listan.|

## <a name="setup"></a>Installation

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|ContainerNetworkName|sträng, är standard ""| Statisk |Det nätverksnamn som ska användas när du konfigurerar ett behållarnätverk.|
|ContainerNetworkSetup|bool, standard är FALSKT (Linux) och standard är SANT (Windows)| Statisk |Om ett behållarnätverk ska konfigureras.|
|FabricDataRoot |String | Inte tillåtet |Datarotkatalog för tjänstinfrastruktur. Standard för Azure är d:\svcfab |
|FabricLogRoot (TygLoggRoot) |String | Inte tillåtet |Rotkatalog för tjänstvävnadslogg. Det är här SF loggar och spår placeras. |
|NodernaTa bort|sträng, är standard ""| Dynamisk |Noderna som ska tas bort som en del av konfigurationsuppgraderingen. (Endast för fristående distributioner)|
|ServiceRunAsAccountName |String | Inte tillåtet |Kontonamnet som programvärdtjänsten ska köras under. |
|SkipContainerNetworkResetOnReboot|bool, standard är FALSKT|InteTillåta|Om du vill hoppa över återställning av behållarnätverk vid omstart.|
|SkipFirewallKonfigurering |Bool, standard är falskt | Inte tillåtet |Anger om brandväggsinställningarna behöver ställas in av systemet eller inte. Detta gäller endast om du använder Windows-brandväggen. Om du använder brandväggar från tredje part måste du öppna portarna för att systemet och programmen ska kunna använda |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Leverantörer |sträng är standard "DSTS" |Statisk|Kommaavgränsad lista över tokenvalideringsleverantörer som ska aktiveras (giltiga providers är: DSTS; AAD). För närvarande kan endast en enda provider aktiveras när som helst. |

## <a name="traceetw"></a>Spåra/Etw

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Nivå |Int, standard är 4 | Dynamisk |Spårningsmängdsnivå kan ta värden 1, 2, 3, 4. För att få stöd måste du hålla spårningsnivån på 4 |

## <a name="transactionalreplicator"></a>Transaktionellreplikator

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Tid i sekunder, standard är 0,015 | Statisk | Ange tidsintervall i sekunder. Bestämmer hur lång tid replikören väntar efter att ha fått en åtgärd innan en bekräftelse skickas tillbaka. Andra åtgärder som tas emot under den här tidsperioden kommer att få sina bekräftelser skickade tillbaka i ett enda meddelande-> vilket minskar nätverkstrafiken men eventuellt minskar dataflödet för replikatorn. |
|MaxCopyQueueSize |Uint, standard är 16384 | Statisk |Det här är det maximala värdet som definierar den ursprungliga storleken för kön som underhåller replikeringsåtgärder. Observera att det måste vara en effekt på 2. Om kön växer till den här storleksåtgärden under körning begränsas mellan de primära och sekundära replikatorerna. |
|MaxPrimaryReplicationQueueMemorySize |Uint, standard är 0 | Statisk |Detta är det maximala värdet för den primära replikeringskön i byte. |
|MaxPrimaryReplicationQueueSize |Uint, standard är 8192 | Statisk |Det här är det maximala antalet åtgärder som kan finnas i den primära replikeringskön. Observera att det måste vara en effekt på 2. |
|MaxReplicationMessageSize |Uint, standard är 52428800 | Statisk | Maximal meddelandestorlek för replikeringsåtgärder. Standard är 50MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, standard är 0 | Statisk |Detta är det maximala värdet för den sekundära replikeringskön i byte. |
|MaxSecondaryReplicationQueueSize |Uint, standard är 16384 | Statisk |Det här är det maximala antalet åtgärder som kan finnas i den sekundära replikeringskön. Observera att det måste vara en effekt på 2. |
|ReplicatorLäggare |sträng, är standard "localhost:0" | Statisk | Slutpunkten i form av en sträng -'IP:Port' som används av Windows Fabric Replicator för att upprätta anslutningar med andra repliker för att skicka/ta emot åtgärder. |

## <a name="transport"></a>Transport
| **Parametern** | **Tillåtna värden** |**Uppgraderingsprincip** |**Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|AnslutningÖppnaTimeout|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(60)|Statisk|Ange tidsintervall i sekunder. Time out för anslutningsinställningar på både inkommande och accepterande sida (inklusive säkerhetsförhandling i säkert läge) |
|FrameHeaderErrorCheckaEnbard|bool, standard är SANT|Statisk|Standardinställning för felkontroll på ramhuvudet i icke-säkert läge. komponentinställningen åsidosätter detta. |
|MessageErrorCheckingEnabled|bool, standard är FALSKT|Statisk|Standardinställning för felkontroll på meddelandehuvud och brödtext i icke-säkert läge. komponentinställningen åsidosätter detta. |
|ResolveOption (LösOption)|sträng är standardvärdet "ospecificerat"|Statisk|Bestämmer hur FQDN ska matchas.  Giltiga värden är "unspecified/ipv4/ipv6". |
|SkickaTimeout|TimeSpan, standard är Vanligt::TimeSpan::FromSeconds(300)|Dynamisk|Ange tidsintervall i sekunder. Skicka timeout för att identifiera fast anslutning. TCP-felrapporter är inte tillförlitliga i vissa miljöer. Detta kan behöva justeras enligt tillgänglig nätverksbandbredd och\*storlek på utgående\/\*data (MaxMessageSize SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UppgraderingOrchestrationService

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|Automatisk uppgradEringsbar | Bool, standard är sant |Statisk| Automatisk avsöknings- och uppgraderingsåtgärd baserat på en måltillståndsfil. |
|Automatisk uppgraderingInstallEraEnabled|Bool, standard är FALSKT|Statisk|Automatisk avsökning, etablering och installation av koduppgraderingsåtgärd baserat på en måltillståndsfil.|
|GoalStateExpirationReminderInDays|int är standard 30|Statisk|Anger antalet återstående dagar efter vilken påminnelse om måltillstånd ska visas.|
|MinReplicaSetSize |Int, standard är 0 |Statisk |MinReplicaSetSize för UpgradeOrchestrationService.|
|Placeringsspärrar | sträng, är standard "" |Statisk| Placeringsspärrarna för UpgradeOrchestrationService. |
|KvorumLossWaitDuration | Tid i sekunder, standard är MaxValue |Statisk| Ange tidsintervall i sekunder. Kvorumlosswaitdurationen för UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Tid i sekunder, standard är 60 minuter|Statisk| Ange tidsintervall i sekunder. ReplicaRestartWaitDuration för UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Tid i sekunder, standard är 60*24*7 minuter |Statisk| Ange tidsintervall i sekunder. StandByReplicaKeepDuration för UpgradeOrchestrationService. |
|TargetReplicaSetSize |Int, standard är 0 |Statisk |TargetReplicaSetSize för UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Bool, standard är falskt | Statisk|Om du vill göra koduppgradering krävs administratörsgodkännande innan du fortsätter. |

## <a name="upgradeservice"></a>UpgradeService

| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|BaseUrl (avbasurl) | sträng, är standard "" |Statisk|BaseUrl för UpgradeService. |
|ClusterId (kluster) | sträng, är standard "" |Statisk|ClusterId för UpgradeService. |
|CoordinatorType | sträng, är standard "WUTest"|Inte tillåtet|CoordinatorType för UpgradeService. |
|MinReplicaSetSize | Int, standard är 2 |Inte tillåtet| MinReplicaSetSize för UpgradeService. |
|OnlyBaseUpgrade | Bool, standard är falskt |Dynamisk|OnlyBaseUpgrade för UpgradeService. |
|Placeringsspärrar |sträng, är standard "" |Inte tillåtet|Tjänsten PlacementConstraints for Upgrade. |
|PollIntervalInSeconds PollIntervalInSeconds PollIntervalInSeconds PollInter|Tidsintervall är standard vanligt::TimeSpan::FromSeconds(60) |Dynamisk|Ange tidsintervall i sekunder. Intervallet mellan UpgradeService-avsökning för ARM-hanteringsåtgärder. |
|TargetReplicaSetSize | Int, standard är 3 |Inte tillåtet| TargetReplicaSetSize för UpgradeService. |
|TestCabFolder | sträng, är standard "" |Statisk| TestCabFolder för UpgradeService. |
|X509FindType | sträng, är standard ""|Dynamisk| X509FindType för UpgradeService. |
|X509Findvärde | sträng, är standard "" |Dynamisk| X509FindValue för UpgradeService. |
|X509SecondaryFindValue | sträng, är standard "" |Dynamisk| X509SecondaryFindValue för UpgradeService. |
|X509StorePlats | sträng, är standard "" |Dynamisk| X509StoreLocation för UpgradeService. |
|X509StoreName | sträng är standard "Min"|Dynamisk|X509StoreName för UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities AnvändareServiceMetricCapacities
| **Parametern** | **Tillåtna värden** | **Uppgraderingsprincip** | **Vägledning eller kort beskrivning** |
| --- | --- | --- | --- |
|EgenskapGroup (EgenskapGroup)| UserServiceMetricCapacitiesMap, standard är Ingen | Statisk | En samling resursstyrningsgränser för användartjänster måste vara statisk eftersom det påverkar logiken för automatisk detection |

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Uppgradera konfigurationen av ett Azure-kluster](service-fabric-cluster-config-upgrade-azure.md) och [uppgradera konfigurationen av ett fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md).
