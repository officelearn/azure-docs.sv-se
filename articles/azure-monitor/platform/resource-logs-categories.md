---
title: Azure Monitor resurs loggar som stöds tjänster och kategorier
description: Referens för Azure Monitor förstå tjänster och händelse scheman som stöds för Azures resurs loggar.
ms.subservice: logs
ms.topic: reference
ms.date: 06/03/2020
ms.openlocfilehash: 1fd6f07151c93b64c150f01e5c0b5c7f4cffed85
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593016"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Kategorier som stöds för Azure-resurs loggar

> [!NOTE]
> Resurs loggar kallades tidigare för diagnostikloggar. Namnet ändrades i oktober 2019 när de typer av loggar som samlats in av Azure Monitor flyttas för att inkludera mer än bara Azure-resursen.

[Azure Monitor resurs loggar](./platform-logs-overview.md) genereras av Azure-tjänster som beskriver driften av dessa tjänster eller resurser. Alla resurs loggar som är tillgängliga via Azure Monitor dela ett gemensamt schema på högsta nivå, med flexibilitet för varje tjänst för att generera unika egenskaper för sina egna händelser.

En kombination av resurs typen (tillgänglig i `resourceId` egenskapen) och `category` unikt identifiera ett schema. Det finns ett gemensamt schema för alla resurs loggar med tjänstspecifika fält och läggs till för olika logg kategorier. Mer information finns i [common and Service Specific schema för Azures resurs loggar]()

## <a name="supported-log-categories-per-resource-type"></a>Logg kategorier som stöds per resurs typ

Följande är en lista över de typer av loggar som är tillgängliga för varje resurs typ. 

Vissa kategorier kan bara användas för vissa typer av resurser. Se den Resource-/regionsspecifika dokumentationen om du anser att du saknar en resurs. Till exempel är inte kategorierna Microsoft. SQL/Server/Databass tillgängliga för alla typer av databaser. Mer information finns i [information om SQL Database diagnostisk loggning](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Om du fortfarande saknar något kan du öppna en GitHub-kommentar längst ned i den här artikeln.

## <a name="microsoftaadiamtenants"></a>Microsoft. aadiam/klient organisationer

|Kategori|Kategori visnings namn|
|---|---|
|Inloggning|Inloggning|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servers

|Kategori|Kategori visnings namn|
|---|---|
|Motor|Motor|
|Tjänst|Tjänst|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Kategori|Kategori visnings namn|
|---|---|
|GatewayLogs|Loggar som rör API Management-Gateway|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/våren

|Kategori|Kategori visnings namn|
|---|---|
|ApplicationConsole|Program konsol|
|SystemLogs|System loggar|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Kategori|Kategori visnings namn|
|---|---|
|JobLogs|Jobb loggar|
|JobStreams|Jobb strömmar|
|DscNodeStatus|DSC-nods status|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Kategori|Kategori visnings namn|
|---|---|
|ServiceLog|Tjänst loggar|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/arbets ytor

|Kategori|Kategori visnings namn|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Kategori|Kategori visnings namn|
|---|---|
|BlockchainApplication|Blockchain-program|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. blockchain/cordaMembers

|Kategori|Kategori visnings namn|
|---|---|
|BlockchainApplication|Blockchain-program|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Kategori|Kategori visnings namn|
|---|---|
|WebApplicationFirewallLogs|Brand Väggs loggar för webb program|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profiler

|Kategori|Kategori visnings namn|
|---|---|
|AzureCdnAccessLog|Azure CDN Access-logg|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profiler/slut punkter

|Kategori|Kategori visnings namn|
|---|---|
|CoreAnalytics|Hämtar måtten för slut punkten, till exempel bandbredd, utgående data osv.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

|Kategori|Kategori visnings namn|
|---|---|
|Regel flödes händelse för nätverks säkerhets grupp|Regel flödes händelse för nätverks säkerhets grupp|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konton

|Kategori|Kategori visnings namn|
|---|---|
|Granska|Granskningsloggar|
|RequestResponse|Förfrågningar och svars loggar|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Kategori|Kategori visnings namn|
|---|---|
|ContainerRegistryLoginEvents|Inloggnings händelser|
|ContainerRegistryRepositoryEvents|RepositoryEvent-loggar|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Kategori|Kategori visnings namn|
|---|---|
|kluster – autoskalning|Kubernetes-kluster autoskalning|
|kube-apiserver|Kubernetes-API-Server|
|Kube – granskning|Kubernetes granskning|
|Kube-Controller-Manager|Kubernetes Controller Manager|
|Kube – Scheduler|Kubernetes Scheduler|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Kategori|Kategori visnings namn|
|---|---|
|AuditLogs|Gransknings loggar för MiniRP-anrop|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. Databricks/arbets ytor

|Kategori|Kategori visnings namn|
|---|---|
|konton|Databricks-konton|
|kluster|Databricks-kluster|
|dBFS|Databricks-filsystem|
|instancePools|Instans-pooler|
|utskrifts|Databricks-jobb|
|notebook|Databricks-anteckningsbok|
|secrets|Databricks hemligheter|
|sqlPermissions|Databricks SQLPermissions|
|SSH|Databricks SSH|
|arbetsyta|Databricks-arbetsyta|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft. DataCatalog/datacatalogs

|Kategori|Kategori visnings namn|
|---|---|
|ScanStatusLogEvent|ScanStatus|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Kategori|Kategori visnings namn|
|---|---|
|ActivityRuns|Pipeline-aktivitet kör logg|
|PipelineRuns|Pipeline kör logg|
|TriggerRuns|Utlös körnings logg|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/konton

|Kategori|Kategori visnings namn|
|---|---|
|Granska|Granskningsloggar|
|Begäranden|Begär ande loggar|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers

|Kategori|Kategori visnings namn|
|---|---|
|MySqlAuditLogs|Gransknings loggar för MariaDB|
|MySqlSlowLogs|MariaDB Server-loggar|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Kategori|Kategori visnings namn|
|---|---|
|MySqlAuditLogs|MySQL gransknings loggar|
|MySqlSlowLogs|MySQL server-loggar|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers

|Kategori|Kategori visnings namn|
|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|
|QueryStoreRuntimeStatistics|PostgreSQL för fråge lagrings körning|
|QueryStoreWaitStatistics|Väntande statistik för PostgreSQL Query Store|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Kategori|Kategori visnings namn|
|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Kategori|Kategori visnings namn|
|---|---|
|PostgreSQLLogs|PostgreSQL Server-loggar|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Kategori|Kategori visnings namn|
|---|---|
|Checkpoint|Checkpoint|
|Fel|Fel|
|Hantering|Hantering|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Kategori|Kategori visnings namn|
|---|---|
|Checkpoint|Checkpoint|
|Anslutning|Anslutning|
|Fel|Fel|
|HostRegistration|HostRegistration|
|Hantering|Hantering|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/arbets ytor

|Kategori|Kategori visnings namn|
|---|---|
|Checkpoint|Checkpoint|
|Fel|Fel|
|Feed|Feed|
|Hantering|Hantering|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Kategori|Kategori visnings namn|
|---|---|
|C2DCommands|C2D-kommandon|
|C2DTwinOperations|C2D dubbla åtgärder|
|Konfigurationer|Konfigurationer|
|Anslutningar|Anslutningar|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|Enhets identitets åtgärder|
|DeviceStreams|Enhets strömmar (förhands granskning)|
|DeviceTelemetry|Enhetstelemetri|
|DirectMethods|Direkta metoder|
|DistributedTracing|Distribuerad spårning (för hands version)|
|FileUploadOperations|Fil överförings åtgärder|
|JobsOperations|Jobb åtgärder|
|Vägar|Vägar|
|TwinQueries|Dubbla frågor|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Kategori|Kategori visnings namn|
|---|---|
|DeviceOperations|Enhets åtgärder|
|ServiceOperations|Tjänst åtgärder|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Kategori|Kategori visnings namn|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/Services

|Kategori|Kategori visnings namn|
|---|---|
|AuditEvent|AuditEvent-logg|
|DataIssue|DataIssue-logg|
|Begäranden|Konfigurations logg|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Kategori|Kategori visnings namn|
|---|---|
|DeliveryFailures|Loggar för leverans problem|
|PublishFailures|Publicera Felaktiga loggar|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Kategori|Kategori visnings namn|
|---|---|
|DeliveryFailures|Loggar för leverans problem|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Kategori|Kategori visnings namn|
|---|---|
|DeliveryFailures|Loggar för leverans problem|
|PublishFailures|Publicera Felaktiga loggar|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/Namespaces

|Kategori|Kategori visnings namn|
|---|---|
|ArchiveLogs|Arkiv loggar|
|AutoScaleLogs|Automatisk skalnings loggar|
|CustomerManagedKeyUserLogs|Customer-Managed nyckel loggar|
|EventHubVNetConnectionEvent|Anslutnings loggar för VNet/IP-filtrering|
|KafkaCoordinatorLogs|Kafka koordinator loggar|
|KafkaUserErrorLogs|Kafka användar fel loggar|
|OperationalLogs|Drift loggar|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/Services

|Kategori|Kategori visnings namn|
|---|---|
|AuditLogs|Granskningsloggar|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Kategori|Kategori visnings namn|
|---|---|
|AutoscaleEvaluations|Autoskala-utvärderingar|
|AutoscaleScaleActions|Åtgärder för autoskalning av skala|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

|Kategori|Kategori visnings namn|
|---|---|
|AppAvailabilityResults|Tillgänglighets resultat|
|AppBrowserTimings|Tids inställningar för webbläsare|
|AppDependencies|Beroenden|
|AppEvents|Händelser|
|AppExceptions|Undantag|
|AppMetrics|Mått|
|AppPageViews|Sid visningar|
|AppPerformanceCounters|Prestandaräknare|
|AppRequests|Begäranden|
|AppSystemEvents|System händelser|
|AppTraces|Spårningar|


## <a name="microsoftiotspacesgraph"></a>Microsoft. IoTSpaces/Graph

|Kategori|Kategori visnings namn|
|---|---|
|Granska|Granska|
|Utgående|Utgående|
|Ingress|Ingress|
|Operativ|Operativ|
|Spårning|Spårning|
|UserDefinedFunction|UserDefinedFunction|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv

|Kategori|Kategori visnings namn|
|---|---|
|AuditEvent|Granskningsloggar|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Kategori|Kategori visnings namn|
|---|---|
|FailedIngestion|Misslyckade inmatnings åtgärder|
|SucceededIngestion|Framgångs åtgärder|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

|Kategori|Kategori visnings namn|
|---|---|
|IntegrationAccountTrackingEvents|Spårnings händelser för integrations konto|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/arbets flöden

|Kategori|Kategori visnings namn|
|---|---|
|WorkflowRuntime|Diagnostiska händelser för arbets flödes körning|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/arbets ytor

|Kategori|Kategori visnings namn|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services

|Kategori|Kategori visnings namn|
|---|---|
|KeyDeliveryRequests|Begär Anden om nyckel leverans|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Kategori|Kategori visnings namn|
|---|---|
|ApplicationGatewayAccessLog|Application Gateway åtkomst logg|
|ApplicationGatewayFirewallLog|Application Gateway brand Väggs logg|
|ApplicationGatewayPerformanceLog|Application Gateway prestanda logg|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Kategori|Kategori visnings namn|
|---|---|
|AzureFirewallApplicationRule|Regel för Azure brand Väggs program|
|AzureFirewallNetworkRule|Nätverks regel för Azure Firewall|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Kategori|Kategori visnings namn|
|---|---|
|BastionAuditLogs|Gransknings loggar för skydds|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Kategori|Kategori visnings namn|
|---|---|
|PeeringRouteLog|Tabell loggar för peering-routning|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Kategori|Kategori visnings namn|
|---|---|
|FrontdoorAccessLog|Ytterdörr åtkomst logg|
|FrontdoorWebApplicationFirewallLog|Ytterdörr webb program brand Väggs logg|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/belastningsutjämnare

|Kategori|Kategori visnings namn|
|---|---|
|LoadBalancerAlertEvent|Load Balancer aviserings händelser|
|LoadBalancerProbeHealthStatus|Load Balancer avsökningens hälso status|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

|Kategori|Kategori visnings namn|
|---|---|
|NetworkSecurityGroupEvent|Händelse för nätverks säkerhets grupp|
|NetworkSecurityGroupRuleCounter|Regel räknare för nätverks säkerhets grupp|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Kategori|Kategori visnings namn|
|---|---|
|DDoSMitigationFlowLogs|Flödes loggar för DDoSa beslut om minskning|
|DDoSMitigationReports|Rapporter för DDoS-åtgärder|
|DDoSProtectionNotifications|DDoS skydds meddelanden|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Kategori|Kategori visnings namn|
|---|---|
|ProbeHealthStatusEvents|Traffic Manager händelse av hälso resultat för avsökning|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Kategori|Kategori visnings namn|
|---|---|
|GatewayDiagnosticLog|Gateway-diagnostikloggar|
|IKEDiagnosticLog|IKE-diagnostikloggar|
|P2SDiagnosticLog|P2S diagnostikloggar|
|RouteDiagnosticLog|Vidarebefordra diagnostikloggar|
|TunnelDiagnosticLog|Tunnel diagnostikloggar|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Kategori|Kategori visnings namn|
|---|---|
|VMProtectionAlerts|Aviseringar för VM-skydd|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

|Kategori|Kategori visnings namn|
|---|---|
|Motor|Motor|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. RecoveryServices/valv

|Kategori|Kategori visnings namn|
|---|---|
|AddonAzureBackupAlerts|Aviserings data för addon Azure Backup|
|AddonAzureBackupJobs|Jobb data för addon Azure Backup|
|AddonAzureBackupPolicy|Princip data för addon Azure Backup|
|AddonAzureBackupProtectedInstance|Addon Azure Backup skyddade instans data|
|AddonAzureBackupStorage|Tillägg för Azure Backup lagrings data|
|AzureBackupReport|Azure Backup rapporterings data|
|AzureSiteRecoveryEvents|Azure Site Recovery händelser|
|AzureSiteRecoveryJobs|Azure Site Recovery jobb|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery skyddad disk data omsättning|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery återställnings punkter|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikerade objekt|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery överförings hastighet för replikeringsdata|
|AzureSiteRecoveryReplicationStats|Statistik för Azure Site Recovery-replikering|
|CoreAzureBackup|Kärn Azure Backup data|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namnrymder

|Kategori|Kategori visnings namn|
|---|---|
|HybridConnectionsEvent|HybridConnections-händelser|


## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices

|Kategori|Kategori visnings namn|
|---|---|
|OperationLogs|Åtgärds loggar|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder

|Kategori|Kategori visnings namn|
|---|---|
|OperationalLogs|Drift loggar|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Kategori|Kategori visnings namn|
|---|---|
|AllLogs|Azure SignalR service-loggar.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Kategori|Kategori visnings namn|
|---|---|
|DevOpsOperationsAudit|Gransknings loggar för DevOps-åtgärder|
|ResourceUsageStats|Statistik för resursanvändning|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/-databaser

|Kategori|Kategori visnings namn|
|---|---|
|Fel|Fel|
|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|
|QueryStoreWaitStatistics|Väntande statistik för Query Store|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser

|Kategori|Kategori visnings namn|
|---|---|
|AutomaticTuning|Automatisk inställning|
|Delar|Delar|
|DatabaseWaitStatistics|Väntande statistik över databasen|
|Dödlägen|Dödlägen|
|DevOpsOperationsAudit|Gransknings loggar för DevOps-åtgärder|
|DmsWorkers|DMS-arbetare|
|Fel|Fel|
|ExecRequests|Exec-begäranden|
|QueryStoreRuntimeStatistics|Körnings statistik för Query Store|
|QueryStoreWaitStatistics|Väntande statistik för Query Store|
|RequestSteps|Förfrågnings steg|
|SQLInsights|SQL Insights|
|SqlRequests|SQL-begäranden|
|SQLSecurityAuditEvents|Säkerhets gransknings händelse i SQL|
|Timeouter|Timeouter|
|Väntar|Väntar|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Kategori|Kategori visnings namn|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Kategori|Kategori visnings namn|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Kategori|Kategori visnings namn|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Kategori|Kategori visnings namn|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Kategori|Kategori visnings namn|
|---|---|
|Redigering|Redigering|
|Körnings-|Körnings-|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

|Kategori|Kategori visnings namn|
|---|---|
|AppServiceEnvironmentPlatformLogs|App Service-miljön plattforms loggar|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

|Kategori|Kategori visnings namn|
|---|---|
|AppServiceAppLogs|App Service program loggar|
|AppServiceAuditLogs|Åtkomst gransknings loggar|
|AppServiceConsoleLogs|App Service konsol loggar|
|AppServiceFileAuditLogs|Gransknings loggar för ändring av webbplats innehåll|
|AppServiceHTTPLogs|HTTP-loggar|
|FunctionAppLogs|Funktions program loggar|
|ScanLogs|Loggar för virus sökning|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/lotss


|Kategori|Kategori visnings namn|
|---|---|
|AppServiceAppLogs|App Service program loggar|
|AppServiceAuditLogs|Åtkomst gransknings loggar|
|AppServiceConsoleLogs|App Service konsol loggar|
|AppServiceFileAuditLogs|Gransknings loggar för ändring av webbplats innehåll|
|AppServiceHTTPLogs|HTTP-loggar|
|FunctionAppLogs|Funktions program loggar|
|ScanLogs|Loggar för virus sökning|


## <a name="next-steps"></a>Nästa steg

* [Läs mer om resurs loggar](./platform-logs-overview.md)
* [Strömma resurs resurs loggar till **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Ändra diagnostikinställningar för resurs loggen med hjälp av Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Analysera loggar från Azure Storage med Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

