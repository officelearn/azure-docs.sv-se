---
title: Azure Resource Logs stöds tjänster och scheman
description: Förstå de tjänster och händelseschema som stöds för Azure-resursloggar.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: de102c5dc4104aafc44b87b14aeea0b30cb7c083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248819"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Tjänster, scheman och kategorier för Azure Resource Logs som stöds

> [!NOTE]
> Resursloggar kallades tidigare diagnostikloggar.

[Azure Monitor-resursloggar](../../azure-monitor/platform/platform-logs-overview.md) är loggar som skickas ut av Azure-tjänster som beskriver driften av dessa tjänster eller resurser. Alla resursloggar som är tillgängliga via Azure Monitor delar ett gemensamt schema på den högsta nivån, med flexibilitet för varje tjänst att avge unika egenskaper för sina egna händelser.

En kombination av resurstypen `resourceId` (tillgänglig i `category` egenskapen) och ett unikt identifiering av ett schema. I den här artikeln beskrivs schemat på den högsta nivån för resursloggar och länkar till schemat för varje tjänst.

## <a name="top-level-resource-logs-schema"></a>Schema för resursloggar på högsta nivå

| Namn | Obligatorisk/valfri | Beskrivning |
|---|---|---|
| time | Krävs | Tidsstämpeln (UTC) för händelsen. |
| resourceId | Krävs | Resurs-ID för den resurs som avgav händelsen. För klienttjänster är detta av formuläret /tenants/tenant-id/providers/provider-name. |
| tenantId (hyresgäst) | Krävs för klientloggar | Klient-ID för Active Directory-klienten som den här händelsen är knuten till. Den här egenskapen används endast för loggar på klientnivå, den visas inte i resursnivåloggar. |
| operationName | Krävs | Namnet på den åtgärd som representeras av den här händelsen. Om händelsen representerar en RBAC-åtgärd är detta RBAC-åtgärdsnamnet (t.ex. Microsoft.Storage/storageAccounts/blobServices/blobbar/Read). Modelleras vanligtvis i form av en Resource Manager-åtgärd, även om`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`de inte är faktiska dokumenterade Resource Manager-åtgärder ( ) |
| operationVersion | Valfri | Api-versionen som är associerad med åtgärden, om operationName utfördes med hjälp av ett API (t.ex. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Om det inte finns något API som motsvarar den här åtgärden representerar versionen den versionen av åtgärden om egenskaperna som är associerade med åtgärden ändras i framtiden. |
| category | Krävs | Loggkategorin för händelsen. Kategori är den granularitet där du kan aktivera eller inaktivera loggar på en viss resurs. De egenskaper som visas i egenskapsbloben för en händelse är desamma inom en viss loggkategori och resurstyp. Typiska loggkategorier är "Granska" "Operativ" "Utförande" och "Begäran". |
| resultType | Valfri | Status för händelsen. Typiska värden är Startad, Pågår, Lyckades, Misslyckades, Aktiv och Löst. |
| resultSignature | Valfri | Händelsens understatus. Om den här åtgärden motsvarar ett REST API-anrop är detta HTTP-statuskoden för motsvarande REST-anrop. |
| resultDescription | Valfri | Den statiska textbeskrivningen av denna operation, t.ex. "Hämta lagringsfil." |
| durationMs | Valfri | Varaktigheten av operationen i millisekunder. |
| callerIpAddress | Valfri | Anroparens IP-adress, om åtgärden motsvarar ett API-anrop som skulle komma från en entitet med en allmänt tillgänglig IP-adress. |
| correlationId | Valfri | Ett GUID som används för att gruppera en uppsättning relaterade händelser. Vanligtvis, om två händelser har samma operationName men två olika status (t.ex. "Startade" och "Lyckades" de har samma korrelations-ID. Detta kan också representera andra relationer mellan händelser. |
| identity | Valfri | En JSON-blob som beskriver identiteten på den användare eller det program som utförde åtgärden. Vanligtvis inkluderar detta auktorisering och anspråk / JWT-token från Active Directory. |
| Nivå | Valfri | Allvarlighetsgraden för händelsen. Måste vara en av Informativ, Varning, Fel eller Kritisk. |
| location | Valfri | Regionen för den resurs som avger händelsen, t.ex. "Östra USA" eller "Frankrike South" |
| properties | Valfri | Alla utökade egenskaper som är relaterade till den här specifika kategori av händelser. Alla anpassade/unika egenskaper måste placeras i den här "del B" i schemat. |

## <a name="service-specific-schemas-for-resource-logs"></a>Tjänstspecifika scheman för resursloggar
Schemat för resursdiagnostikloggar varierar beroende på resurs- och loggkategori. I den här listan visas alla tjänster som gör tillgängliga resursloggar och länkar till det tjänst- och kategorispecifika schemat där det finns tillgängligt.

| Tjänst | Schema & dokument |
| --- | --- |
| Azure Active Directory | [Översikt,](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md) [granskningsloggschema](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) och [inloggningsschema](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management resursloggar](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Programgateways |[Logga för programgateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Logganalys för Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Loggning av Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Diagnostikloggar i Azure Database för MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure-databas för PostgreSQL-loggar](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure-datautforskaren | [Azure Data Explorer-loggar](../../data-explorer/using-diagnostic-logs.md) |
| Cognitive Services | [Logga för Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Loggning för Azure-behållarregister](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Azure-loggar för CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB-loggning](../../cosmos-db/logging.md) |
| Data Factory | [Övervaka datafabriker med Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Komma åt loggar för Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Komma åt loggar för Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Händelsehubbar |[Azure Event Hubs loggar](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Schemat är inte tillgängligt. |
| Azure Firewall | Schemat är inte tillgängligt. |
| IoT Hub | [IoT Hub-verksamhet](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Loggning av Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Loggning av Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Lastbalanserare |[Log Analytics för Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Anpassat Logic Apps B2B-spårningsschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Nätverkssäkerhetsgrupper |[Log Analytics för nätverkssäkerhetsgrupper (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS-skydd | [Hantera Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Dedikerad Power BI | [Loggning för Power BI inbäddad i Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Datamodell för Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Aktivera och använda Search Traffic Analytics](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus loggar](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Loggning av Azure SQL-databas](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Jobbloggar](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Loggschema för Traffic Manager](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuella nätverk | Schemat är inte tillgängligt. |
| Virtuella nätverksgatewayer | Schemat är inte tillgängligt. |

## <a name="supported-log-categories-per-resource-type"></a>Loggkategorier som stöds per resurstyp

Vissa kategorier kanske bara stöds för specifika typer av resurser. Det här är en lista över alla som är tillgängliga i någon form.  Kategorier för Microsoft.Sql/servrar/databaser är till exempel inte tillgängliga för alla typer av databaser. Mer information finns i [information om diagnostikloggning av SQL Database](../../sql-database/sql-database-metrics-diag-logging.md). 

|Resurstyp|Kategori|Visningsnamn för kategori|
|---|---|---|
|Microsoft.AAD/domainServices|SystemSäkerhet|SystemSäkerhet|
|Microsoft.AAD/domainServices|AccountManagement|AccountManagement|
|Microsoft.AAD/domainServices|Inloggningsloggoff|Inloggningsloggoff|
|Microsoft.AAD/domainServices|ObjectAccess (objekt)|ObjectAccess (objekt)|
|Microsoft.AAD/domainServices|PolicyChange|PolicyChange|
|Microsoft.AAD/domainServices|PrivilegiumAnvänd|PrivilegiumAnvänd|
|Microsoft.AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft.AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft.AAD/domainServices|KontoLoggon|KontoLoggon|
|microsoft.aadiam/klienter|Signin (på andra)|Signin (på andra)|
|Microsoft.AnalysisServices/servrar|Motor|Motor|
|Microsoft.AnalysisServices/servrar|Tjänst|Tjänst|
|Microsoft.ApiManagement/service|GatewayLogs|Loggar relaterade till ApiManagement Gateway|
|Microsoft.AppPlattform/Fjäder|AnsökanConsole|Programkonsol|
|Microsoft.Automation/automationKonton|Jobbloggar|Jobbloggar|
|Microsoft.Automation/automationKonton|JobStreams (JobbStrömmar)|Jobbströmmar|
|Microsoft.Automation/automationKonton|DscNodeStatus|Status för Dsc-nod|
|Microsoft.Batch/batchKonton|ServiceLogg|Serviceloggar|
|Microsoft.BatchAI/arbetsytor|BaiClusterevent (på andra sätt)|BaiClusterevent (på andra sätt)|
|Microsoft.BatchAI/arbetsytor|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft.BatchAI/arbetsytor|BaiJobEvent (0)|BaiJobEvent (0)|
|Microsoft.Blockchain/blockchainMedlem|BlockchainApplication|Blockchain-program|
|Microsoft.Blockchain/blockchainMedlem|Proxy|Proxy|
|Microsoft.Cdn/profiler/slutpunkter|CoreAnalytics (Kärnanalytika)|Hämtar måtten för slutpunkten, t.ex.|
|Microsoft.ClassicNetwork/networksecuritygroups|Regelflödeshändelse för nätverkssäkerhetsgrupp|Regelflödeshändelse för nätverkssäkerhetsgrupp|
|Microsoft.CognitiveServices/konton|Granska|Granskningsloggar|
|Microsoft.CognitiveServices/konton|RequestResponse|Loggböcker för begäran och svar|
|Microsoft.Container-register/register|ContainerRegistryRepositoryEvents|DatabasEvent loggar (förhandsgranska)|
|Microsoft.Container-register/register|ContainerRegistryLoginEvents|Inloggningshändelser (förhandsgranskning)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Kubernetes API-server|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Kubernetes Controller Manager|
|Microsoft.ContainerService/managedClusters|kube-schemaläggare|Kubernetes Schemaläggare|
|Microsoft.ContainerService/managedClusters|kube-revision|Kubernetes-granskning|
|Microsoft.ContainerService/managedClusters|kluster-automatisk skalning|Kubernetes Kluster Automatisk skalning|
|Microsoft.Databricks/arbetsytor|dbfs (av )|Databricks-filsystem|
|Microsoft.Databricks/arbetsytor|Kluster|Databricks kluster|
|Microsoft.Databricks/arbetsytor|konton|Databricks konton|
|Microsoft.Databricks/arbetsytor|Jobb|Databricks Jobb|
|Microsoft.Databricks/arbetsytor|Anteckningsboken|Databricks-anteckningsbok|
|Microsoft.Databricks/arbetsytor|Ssh|Databricks SSH|
|Microsoft.Databricks/arbetsytor|arbetsyta|Databricks arbetsyta|
|Microsoft.Databricks/arbetsytor|secrets|Databricks Hemligheter|
|Microsoft.Databricks/arbetsytor|sqlPermissions (sqlPermissions)|Databricks SQLPermissions|
|Microsoft.Databricks/arbetsytor|instancePools|Instanspooler|
|Microsoft.DataCatalog/datakategoris|ScanStatusLogevent|ScanStatus|
|Microsoft.DataFactory/fabriker|ActivityRuns|Logg för pipelineaktivitet körs|
|Microsoft.DataFactory/fabriker|PipelineRuns|Logg för pipelinekörningar|
|Microsoft.DataFactory/fabriker|TriggerRuns|Logg för utlösarkörningar|
|Microsoft.DataLakeAnalytics/konton|Granska|Granskningsloggar|
|Microsoft.DataLakeAnalytics/konton|Begäranden|Begär loggar|
|Microsoft.DataLakeStore/konton|Granska|Granskningsloggar|
|Microsoft.DataLakeStore/konton|Begäranden|Begär loggar|
|Microsoft.DataShare/konton|Resurser|Resurser|
|Microsoft.DataShare/konton|Delateckningar|Dela abonnemang|
|Microsoft.DataShare/konton|SentShareSnapshots|Ögonblicksbilder av skickade delningar|
|Microsoft.DataShare/konton|ReceivedShareSnapshots|Mottagna ögonblicksbilder av resurs|
|Microsoft.DBforMySQL/servrar|MySqlSlowLogs|MySQL-serverloggar|
|Microsoft.DBforMySQL/servrar|MySqlAuditLogs|MySQL-granskningsloggar|
|Microsoft.DBforPostgreSQL/servrar|PostgreSQLLogs (på efter)|Serverloggar för PostgreSQL|
|Microsoft.DBforPostgreSQL/servrar|QueryStoreRuntimeStatistics|Statistik över postgreSQL-frågearkiv|
|Microsoft.DBforPostgreSQL/servrar|QueryStoreWaitStatistics|Vänta statistik för efterfrågareklagret efter inlägg|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs (på efter)|Serverloggar för PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|Statistik över postgreSQL-frågearkiv|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Vänta statistik för efterfrågareklagret efter inlägg|
|Microsoft.DesktopVirtualization/workspaces|Checkpoint|Checkpoint|
|Microsoft.DesktopVirtualization/workspaces|Fel|Fel|
|Microsoft.DesktopVirtualization/workspaces|Hantering|Hantering|
|Microsoft.DesktopVirtualization/workspaces|Feed|Feed|
|Microsoft.DesktopVirtualization/applicationGroups|Checkpoint|Checkpoint|
|Microsoft.DesktopVirtualization/applicationGroups|Fel|Fel|
|Microsoft.DesktopVirtualization/applicationGroups|Hantering|Hantering|
|Microsoft.DesktopVirtualization/hostPools|Checkpoint|Checkpoint|
|Microsoft.DesktopVirtualization/hostPools|Fel|Fel|
|Microsoft.DesktopVirtualization/hostPools|Hantering|Hantering|
|Microsoft.DesktopVirtualization/hostPools|Anslutning|Anslutning|
|Microsoft.DesktopVirtualization/hostPools|Värdregistration|Värdregistration|
|Microsoft.Devices/IotHubs|Anslutningar|Anslutningar|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Enhetstelemetri|
|Microsoft.Devices/IotHubs|C2DCommands (C2DCommands)|C2D-kommandon|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Åtgärder för enhetsidentitet|
|Microsoft.Devices/IotHubs|FileUploadOperations|Åtgärder för filuppladdning|
|Microsoft.Devices/IotHubs|Vägar|Vägar|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D Twin Operationer|
|Microsoft.Devices/IotHubs|TwinQueries (TwinQueries)|Dubbla frågor|
|Microsoft.Devices/IotHubs|JobbOperationer|Jobb operationer|
|Microsoft.Devices/IotHubs|DirectMethods (direktmetaller)|Direkta metoder|
|Microsoft.Devices/IotHubs|DistribuerasTracing|Distribuerad spårning (förhandsgranskning)|
|Microsoft.Devices/IotHubs|Konfigurationer|Konfigurationer|
|Microsoft.Devices/IotHubs|DeviceStreams|Enhetsströmmar (förhandsgranskning)|
|Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.|DeviceOperations|Enhetsoperationer|
|Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.|ServiceOperations|Serviceåtgärder|
|Microsoft.DocumentDB/databasKonton|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databasKonton|MongoRequests (|MongoRequests (|
|Microsoft.DocumentDB/databasKonton|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databasKonton|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft.DocumentDB/databasKonton|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft.EnterpriseKnowledgeGraph/tjänster|AuditEvent|AuditEvent-logg|
|Microsoft.EnterpriseKnowledgeGraph/tjänster|DataIssue|DataIssue logg|
|Microsoft.EnterpriseKnowledgeGraph/tjänster|Begäranden|Konfigurationslogg|
|Microsoft.EventHub/namnområden|Arkivbild|Arkiv loggar|
|Microsoft.EventHub/namnområden|OperationalLogs|Operativa loggar|
|Microsoft.EventHub/namnområden|Automatisk skalningsloggar|Loggar för automatisk skalning|
|Microsoft.EventHub/namnområden|KafkaCoordinatorLogs|Kafka samordnare loggar|
|Microsoft.EventHub/namnområden|KafkaUserErrorLogs|Kafka användarfel loggar|
|Microsoft.EventHub/namnområden|EventHubVNetConnectionEvent|Anslutningsloggar för VNet/IP-filtrering|
|Microsoft.EventHub/namnområden|CustomerManagedKeyUserLogs|Kundhanterade nyckelloggar|
|Microsoft.HealthcareApis/tjänster|AuditLogs|Granskningsloggar|
|Microsoft.Insights/AutoscaleSettings|Autoskalavärderingar|Utvärderingar för automatisk skalning|
|Microsoft.Insights/AutoscaleSettings|Automatisk skalningsåtgärd|Åtgärder för automatisk skalning|
|Microsoft.IoTSpaces/Diagram|Spårning|Spårning|
|Microsoft.IoTSpaces/Diagram|Operativa|Operativa|
|Microsoft.IoTSpaces/Diagram|Granska|Granska|
|Microsoft.IoTSpaces/Diagram|UserDefinedFunction AnvändareDefinedFunction|UserDefinedFunction AnvändareDefinedFunction|
|Microsoft.IoTSpaces/Diagram|Ingress|Ingress|
|Microsoft.IoTSpaces/Diagram|Utgående|Utgående|
|Microsoft.KeyVault/valv|AuditEvent|Granskningsloggar|
|Microsoft.Kusto/Kluster|Efterföljande Ingestion|Lyckade ingest-operationer|
|Microsoft.Kusto/Kluster|Misslyckade skrivningar|Misslyckade intagsåtgärder|
|Microsoft.Logic/arbetsflöden|Workflowruntime|Diagnostikhändelser för arbetsflödeskörning|
|Microsoft.Logic/integrationKonton|IntegrationAccountTrackingEvents|Spåra händelser för integrationskonto|
|Microsoft.MachineLearningServices/arbetsytor|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/arbetsytor|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/arbetsytor|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft.Media/mediaservices|KeyDeliveryRequests|Viktiga leveransförfrågningar|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Händelse i nätverkssäkerhetsgruppen|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Regelräknare för nätverkssäkerhetsgrupp|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Regelflödeshändelse för nätverkssäkerhetsgrupp|
|Microsoft.Network/publicIPAdresser|DDoSProtectionAnmälningar|DDoS-skyddsmeddelanden|
|Microsoft.Network/publicIPAdresser|DDoSMitigationFlowLogs|Flödesloggar för DDoS-begränsningsbeslut|
|Microsoft.Network/publicIPAdresser|DDoSMitigationRapporter|Rapporter om DDoS-mildrande åtgärder|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM-skyddsvarningar|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Åtkomstlogg för programgateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Prestandalogg för programgateway|
|Microsoft.Network/applicationGateways|AnsökanGatewayFirewallLog|Brandväggslogg för programgateway|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Programregel för Azure-brandvägg|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Regel för Azure-brandväggens nätverk|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostikloggar för gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Tunnel diagnostiska loggar|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnostikloggar för rutt|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE diagnostiska loggar|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S-diagnostikloggar|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Händelse av hälsoresultat för Traffic Manager-avsökning|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Peering rutttabellloggar|
|Microsoft.Network/vpnGateways|GatewayDiagnosticLog|Diagnostikloggar för gateway|
|Microsoft.Network/vpnGateways|TunnelDiagnosticLog|Tunnel diagnostiska loggar|
|Microsoft.Network/vpnGateways|RouteDiagnosticLog|Diagnostikloggar för rutt|
|Microsoft.Network/vpnGateways|IKEDiagnosticLog|IKE diagnostiska loggar|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Åtkomstlogg för frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Brandväggslogg för Frontdoor-webbprogram|
|Microsoft.Network/p2sVpnGateways|GatewayDiagnosticLog|Diagnostikloggar för gateway|
|Microsoft.Network/p2sVpnGateways|IKEDiagnosticLog|IKE diagnostiska loggar|
|Microsoft.Network/p2sVpnGateways|P2SDiagnosticLog|P2S-diagnostikloggar|
|Microsoft.Network/bastionHosts|BastionAuditLogs|Loggar för granskning av bastioner|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Varningshändelser för belastningsutjämnare|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Hälsostatus för belastningsutjämningsavsökning|
|Microsoft.PowerBIDedicated/capacity|Motor|Motor|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Rapporteringsdata för Azure-säkerhetskopiering|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Grundläggande Azure-säkerhetskopieringsdata|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Jobbdata för Addon Azure-säkerhetskopiering|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Addon Azure-aviseringsdata för säkerhetskopiering|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicy|Addon Azure-principdata för säkerhetskopiering|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|Addon Azure-lagringsdata för säkerhetskopiering|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupSkyddadeInstance|Addon Azure-data för skyddad instans för säkerhetskopiering|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery Jobb|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery-händelser|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Replikerade objekt för azure-återställning av webbplatsen|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Statistik över azure-återställningsreplikering|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Återställningspunkter för Azure Site-återställning|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Uppladdningshastighet för Azure Site Recovery Replication-data|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery Protected Disk Data Churn|
|Microsoft.Search/searchServices|OperationLogs|Åtgärdsloggar|
|Microsoft.ServiceBus/namnområden|OperationalLogs|Operativa loggar|
|Microsoft.Sql/servrar/databaser|SQLInsights|SQL-insikter|
|Microsoft.Sql/servrar/databaser|Automatiskinställning|Automatisk inställning|
|Microsoft.Sql/servrar/databaser|QueryStoreRuntimeStatistics|Frågebutikskörningsstatistik|
|Microsoft.Sql/servrar/databaser|QueryStoreWaitStatistics|Vänta statistik för frågearkiv|
|Microsoft.Sql/servrar/databaser|Fel|Fel|
|Microsoft.Sql/servrar/databaser|DatabasWaitStatistics|Statistik över väntetid för databas|
|Microsoft.Sql/servrar/databaser|Timeouter|Timeouter|
|Microsoft.Sql/servrar/databaser|Block|Block|
|Microsoft.Sql/servrar/databaser|Dödlägen|Dödlägen|
|Microsoft.Sql/servrar/databaser|Granska|Granskningsloggar|
|Microsoft.Sql/servrar/databaser|SQLSecurityAuditEvents|Sql Security Audit Händelse|
|Microsoft.Sql/servrar/databaser|DmsArbetare|Dms Arbetstagare|
|Microsoft.Sql/servrar/databaser|ExecRequests|Exec-begäranden|
|Microsoft.Sql/servrar/databaser|RequestSteps|Steg för begäran|
|Microsoft.Sql/servrar/databaser|SqlRequests (SqlRequests)|Sql-begäranden|
|Microsoft.Sql/servrar/databaser|Väntar|Väntar|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Statistik över resursanvändning|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Sql Security Audit Händelse|
|Microsoft.Sql/managedInstances/databaser|SQLInsights|SQL-insikter|
|Microsoft.Sql/managedInstances/databaser|QueryStoreRuntimeStatistics|Frågebutikskörningsstatistik|
|Microsoft.Sql/managedInstances/databaser|QueryStoreWaitStatistics|Vänta statistik för frågearkiv|
|Microsoft.Sql/managedInstances/databaser|Fel|Fel|
|Microsoft.Storage/storageAccounts/tableServices|LagringLäs|LagringLäs|
|Microsoft.Storage/storageAccounts/tableServices|LagringSkriva|LagringSkriva|
|Microsoft.Storage/storageAccounts/tableServices|LagringTa bort|LagringTa bort|
|Microsoft.Storage/storageAccounts/blobServices|LagringLäs|LagringLäs|
|Microsoft.Storage/storageAccounts/blobServices|LagringSkriva|LagringSkriva|
|Microsoft.Storage/storageAccounts/blobServices|LagringTa bort|LagringTa bort|
|Microsoft.Storage/storageAccounts/fileServices|LagringLäs|LagringLäs|
|Microsoft.Storage/storageAccounts/fileServices|LagringSkriva|LagringSkriva|
|Microsoft.Storage/storageAccounts/fileServices|LagringTa bort|LagringTa bort|
|Microsoft.Storage/storageAccounts/queueServices|LagringLäs|LagringLäs|
|Microsoft.Storage/storageAccounts/queueServices|LagringSkriva|LagringSkriva|
|Microsoft.Storage/storageAccounts/queueServices|LagringTa bort|LagringTa bort|
|Microsoft.StreamAnalytics/streamingjobs|Körnings-|Körnings-|
|Microsoft.StreamAnalytics/streamingjobs|Redigering|Redigering|
|microsoft.web/hostingenvironments|AppServiceMiljöPlattformar|Plattformsloggar för App Service-miljö|
|microsoft.web/webbplatser|FunctionAppLogs|Funktionsprogramloggar|
|microsoft.web/webbplatser|AppServiceHTTPLogs|HTTP-loggar|
|microsoft.web/webbplatser|AppServiceConsoleLogs|App Service Console loggar|
|microsoft.web/webbplatser|AppServiceAppLogs|Programloggar för apptjänst|
|microsoft.web/webbplatser|AppServiceFileAuditLogs|Granskningsloggar för innehållsinnehåll|
|microsoft.web/webbplatser|AppServiceAuditLogs|Komma åt granskningsloggar|
|microsoft.web/sites/slots|FunctionAppLogs|Funktionsprogramloggar|
|microsoft.web/sites/slots|AppServiceHTTPLogs|HTTP-loggar|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Konsolloggar|
|microsoft.web/sites/slots|AppServiceAppLogs|Programloggar|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Granskningsloggar för innehållsinnehåll|
|microsoft.web/sites/slots|AppServiceAuditLogs|Komma åt granskningsloggar|

## <a name="next-steps"></a>Efterföljande moment

* [Läs mer om resursloggar](../../azure-monitor/platform/platform-logs-overview.md)
* [Strömma resursresursloggar till **eventhubbar**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Ändra diagnostikinställningar för resursloggen med hjälp av AZURE Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analysera loggar från Azure-lagring med Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
