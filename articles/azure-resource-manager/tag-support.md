---
title: Azure Resource Manager-tagg-stöd för resurser
description: Visar vilka Azure-resurstyper stöder taggar. Innehåller information om alla Azure-tjänster.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: 99bac69d096095f660e2624194b72e1390c6fc22
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275572"
---
# <a name="tag-support-for-azure-resources"></a>Tagg-stöd för Azure-resurser
Den här artikeln beskriver om en resurstyp stöder [taggning](resource-group-using-tags.md).

## <a name="aad-domain-services"></a>AAD Domain Services
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| domäner | Nej | 

## <a name="ad-hybrid-health-service"></a>Hybrid AD-tjänsten för hälsotillstånd
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| services | Nej | 
| addsservices | Nej | 
| konfiguration | Nej | 
| agenter | Nej | 
| aadsupportcases | Nej | 
| Rapporter | Nej | 
| servicehealthmetrics | Nej | 
| loggar | Nej | 
| anonymousapiusers | Nej | 

## <a name="analysis-services"></a>Analysis Services
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| servrar | Ja | 

## <a name="api-hubs"></a>API Hubs
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| apiManagementAccounts | Nej | 
| apiManagementAccounts/connectionProviders | Nej | 
| apiManagementAccounts/anslutningar | Nej | 
| apiManagementAccounts/connectionAcls | Nej | 
| apiManagementAccounts/connectionProviderAcls | Nej | 
| apiManagementAccounts/API: er | Nej | 

## <a name="api-management"></a>API Management
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| tjänst | Ja | 

## <a name="automation"></a>Automation
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| AutomationAccounts | Ja | 
| automationAccounts/runbooks | Ja | 
| automationAccounts/konfigurationer | Ja | 
| automationAccounts/webhooks | Nej | 
| automationAccounts/softwareUpdateConfigurations | Nej | 
| automationAccounts/jobb | Nej | 

## <a name="batch"></a>Batch
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| batchAccounts | Ja | 

## <a name="batch-ai"></a>Batch AI
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Kluster | Ja | 
| jobb | Ja | 
| fileservers | Ja | 
| arbetsytor | Ja | 
| arbetsytor/kluster | Nej | 
| arbetsytor/fileservers | Nej | 
| arbetsytor/experiment | Nej | 
| arbetsytor-experiment-jobb | Nej | 

## <a name="bing-maps"></a>Bing-kartor
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| mapApis | Ja | 

## <a name="biztalk-services"></a>Biztalk Services
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| BizTalk | Ja | 

## <a name="cache"></a>Cache
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Redis | Ja | 

## <a name="cdn"></a>CDN
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Profiler | Ja | 
| profiler-slutpunkter | Ja | 
| profiler/slutpunkter/ursprung | Nej | 
| profiler/slutpunkter/customdomains | Nej | 
| validateProbe | Nej | 
| edgenodes | Nej | 

## <a name="classic-compute"></a>Klassisk Compute
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| domainNames | Nej | 
| domainNames/platser | Nej | 
| domainNames-fack-roller | Nej | 
| virtuella datorer | Nej | 
| virtuella datorer/diagnosticSettings | Nej | 
| virtuella datorer/metricDefinitions | Nej | 
| virtuella datorer/mått | Nej | 

## <a name="classic-infrastructure-migrate"></a>Migrera klassiska infrastruktur
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| classicInfrastructureResources | Nej | 

## <a name="classic-network"></a>Klassiskt nätverk
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| virtuella nätverk | Nej | 
| virtuella nätverk/virtualNetworkPeerings | Nej | 
| virtuella nätverk/remoteVirtualNetworkPeeringProxies | Nej | 

## <a name="classic-storage"></a>Klassisk lagring
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| storageAccounts/tjänster | Nej | 
| storageAccounts/tjänster/diagnosticSettings | Nej | 

## <a name="compute"></a>Compute
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| availabilitySets | Ja | 
| virtuella datorer | Ja | 
| virtuella datorer /-tillägg | Ja | 
| virtualMachineScaleSets | Ja | 
| virtualMachineScaleSets /-tillägg | Nej | 
| virtualMachineScaleSets/virtuella datorer | Nej | 
| virtualMachineScaleSets/networkInterfaces | Nej | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nej | 
| virtualMachineScaleSets/publicIPAddresses | Nej | 
| restorePointCollections | Ja | 
| restorePointCollections/restorePoints | Nej | 
| virtuella datorer/diagnosticSettings | Nej | 
| virtuella datorer/metricDefinitions | Nej | 
| sharedVMImages | Ja | 
| sharedVMImages/versioner | Ja | 
| Diskar | Ja | 
| ögonblicksbilder | Ja | 
| images | Ja | 

## <a name="container"></a>Container
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| containerGroups | Ja | 

## <a name="container-instance"></a>Containerinstans
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| containerGroups | Ja | 
| serviceAssociationLinks | Nej | 

## <a name="container-service"></a>Container Service
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| containerServices | Ja | 

## <a name="cortana-analytics"></a>Cortana Analytics
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 

## <a name="cosmos-db"></a>Cosmos DB
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| databaseAccounts | Ja | 
| databaseAccountNames | Nej | 

## <a name="cost-management"></a>Cost Management
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Anslutningar | Ja | 

## <a name="data-box-edge"></a>Data Box Edge
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| DataBoxEdgeDevices | Ja | 
| DataBoxEdgeDevices/checkNameAvailability | Nej | 

## <a name="data-catalog"></a>Data Catalog
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| kataloger | Ja | 

## <a name="data-connect"></a>Ansluta data
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| connectionManagers | Ja | 

## <a name="data-factory"></a>Data Factory
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| dataFactories | Ja | 
| fabriker | Ja | 
| fabriker/integrationRuntimes | Nej | 
| dataFactories/diagnosticSettings | Nej | 
| dataFactories/metricDefinitions | Nej | 
| dataFactorySchema | Nej | 

## <a name="devices"></a>Enheter
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| IotHubs | Ja | 
| IotHubs/eventGridFilters | Nej | 
| ProvisioningServices | Ja | 

## <a name="devspaces"></a>Devspaces
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Domänkontrollanter | Ja | 

## <a name="devtest-lab"></a>Devtest Lab
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Labs | Ja | 
| Scheman | Ja | 
| Labs/virtuella datorer | Ja | 
| Labs/serviceRunners | Ja | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| lcsprojects | Nej | 
| lcsprojects/anslutningar | Nej | 
| lcsprojects/clouddeployments | Nej | 

## <a name="event-grid"></a>Event Grid
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| eventSubscriptions | Nej | 
| ämnen | Ja | 
| domäner | Ja | 
| domäner/ämnen | Nej | 
| topicTypes | Nej | 
| extensionTopics | Nej | 

## <a name="event-hub"></a>Händelsehubb
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Namnområden | Ja | 
| Kluster | Ja | 

## <a name="hana-on-azure"></a>Hana på Azure
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| hanaInstances | Ja | 

## <a name="hdinsight"></a>HDInsight
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Kluster | Ja | 
| kluster/program | Nej | 

## <a name="import-export"></a>Import/Export
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| jobb | Ja | 

## <a name="insights"></a>Insikter
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Komponenter | Ja | 
| komponenter/fråga | Nej | 
| komponenter/mått | Nej | 
| komponenter/händelser | Nej | 
| webbtester | Ja | 
| frågor | Nej | 
| scheduledqueryrules | Ja | 
| komponenter/pricingPlans | Nej | 
| migrateToNewPricingModel | Nej | 
| rollbackToLegacyPricingModel | Nej | 
| automatedExportSettings | Nej | 
| arbetsböcker | Ja | 
| myWorkbooks | Nej | 
| loggar | Nej | 

## <a name="key-vault"></a>Key Vault
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Valv | Ja | 
| Valv/hemligheter | Nej | 
| valv/accessPolicies | Nej | 
| deletedVaults | Nej | 

## <a name="log-analytics"></a>Log Analytics
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| loggar | Nej | 

## <a name="logic"></a>Logik
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Arbetsflöden | Ja | 
| integrationAccounts | Ja | 

## <a name="machine-learning-services"></a>Machine Learning Services
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| arbetsytor | Ja | 
| arbetsytor/beräkningar | Nej | 

## <a name="managed-identity"></a>Hanterad identitet
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Identiteter | Nej | 
| userAssignedIdentities | Ja | 

## <a name="mariadb"></a>MariaDB
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| servrar | Ja | 
| servrar/recoverableServers | Nej | 
| servrar/virtualNetworkRules | Nej | 

## <a name="marketplace-apps"></a>Marketplace-appar
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| classicDevServices | Ja | 

## <a name="marketplace-ordering"></a>Marketplace ordning
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Avtal | Nej | 
| offertypes | Nej | 

## <a name="media"></a>Media
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| mediaservices | Ja | 
| mediaservices/tillgångar | Nej | 
| mediaservices/contentKeyPolicies | Nej | 
| mediaservices/streamingLocators | Nej | 
| mediaservices/streamingPolicies | Nej | 
| mediaservices/eventGridFilters | Nej | 
| mediaservices/omvandlingar | Nej | 
| mediaservices-transformeringar-jobb | Nej | 
| mediaservices/Strömningsslutpunkter | Ja | 
| mediaservices/liveEvents | Ja | 
| mediaservices/liveEvents/liveOutputs | Nej | 
| mediaservices/streamingEndpointOperations | Nej | 
| mediaservices/liveEventOperations | Nej | 
| mediaservices/liveOutputOperations | Nej | 
| mediaservices/tillgångar/assetFilters | Nej | 
| mediaservices/accountFilters | Nej | 

## <a name="mysql"></a>MySQL
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| servrar | Ja | 
| servrar/recoverableServers | Nej | 
| servrar/virtualNetworkRules | Nej | 

## <a name="network"></a>Nätverk
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| virtuella nätverk | Ja | 
| publicIPAddresses | Ja | 
| networkInterfaces | Ja | 
| interfaceEndpoints | Ja | 
| belastningsutjämnare | Ja | 
| networkSecurityGroups | Ja | 
| applicationSecurityGroups | Ja | 
| serviceEndpointPolicies | Ja | 
| networkIntentPolicies | Ja | 
| routeTables | Ja | 
| publicIPPrefixes | Ja | 
| networkWatchers | Ja | 
| networkWatchers/connectionMonitors | Ja | 
| networkWatchers/linser | Ja | 
| networkWatchers/pingMeshes | Ja | 
| virtualNetworkGateways | Ja | 
| localNetworkGateways | Ja | 
| anslutningar | Ja | 
| applicationGateways | Ja | 
| expressRouteCircuits | Ja | 
| routeFilters | Ja | 
| virtualWans | Ja | 
| vpnSites | Ja | 
| virtualHubs | Ja | 
| vpnGateways | Ja | 
| azureFirewalls | Ja | 
| virtualNetworkTaps | Ja | 
| privateLinkServices | Ja | 
| ddosProtectionPlans | Ja | 
| networkProfiles | Ja | 
| frontdoors | Ja | 
| frontdoorWebApplicationFirewallPolicies | Ja | 
| webApplicationFirewallPolicies | Ja | 

## <a name="notification-hubs"></a>Notification Hubs
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Namnområden | Ja | 
| namnområden/notificationHubs | Ja | 

## <a name="portal"></a>Portalen
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Instrumentpaneler | Ja | 

## <a name="portal-sdk"></a>Portalen SDK
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| rootResources | Ja | 

## <a name="postgresql"></a>PostgreSQL
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| servrar | Ja | 
| servrar/recoverableServers | Nej | 
| servrar/virtualNetworkRules | Nej | 
| servrar/topQueryStatistics | Nej | 
| servrar/queryTexts | Nej | 
| servrar/waitStatistics | Nej | 
| servrar/rådgivare | Nej | 

## <a name="power-bi"></a>Power BI
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| workspaceCollections | Ja | 

## <a name="recovery-services"></a>Recovery Services
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Valv | Ja | 
| backupProtectedItems | Nej | 

## <a name="relay"></a>Relä
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Namnområden | Ja | 

## <a name="resources"></a>Resurser
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Resursgrupper | Nej | 
| prenumerationerna/resursgrupperna | Nej | 

## <a name="scheduler"></a>Scheduler
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| förfrågningsåtgärder | Ja | 
| flöden | Ja | 

## <a name="search"></a>Search
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| searchServices | Ja | 
| resourceHealthMetadata | Nej | 

## <a name="security"></a>Säkerhet
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| dataCollectionAgents | Nej | 

## <a name="service-bus"></a>Service Bus
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Namnområden | Ja | 
| namnområden/eventgridfilters | Nej | 

## <a name="service-fabric"></a>Service Fabric
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Kluster | Ja | 
| kluster/program | Nej | 

## <a name="service-fabric-mesh"></a>Service Fabric Mesh
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| program | Ja | 
| Nätverk | Ja | 
| volumes | Ja | 

## <a name="signalr-service"></a>SignalR Service
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| SignalR | Ja | 

## <a name="site-recovery"></a>Site Recovery
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| SiteRecoveryVault | Ja | 

## <a name="solutions"></a>Lösningar
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| program | Ja | 
| applicationDefinitions | Ja | 
| jitRequests | Ja | 

## <a name="sql-virtual-machine"></a>SQL-dator
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| DWVM | Ja | 

## <a name="storage"></a>Storage
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| storageAccounts | Ja | 
| storageAccounts/blobServices | Nej | 
| storageAccounts/tableServices | Nej | 
| storageAccounts/queueServices | Nej | 
| storageAccounts/fileServices | Nej | 
| storageAccounts/tjänster | Nej | 
| storageAccounts/tjänster/metricDefinitions | Nej | 

## <a name="storage-sync"></a>Synkronisering av lagring
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| storageSyncServices | Ja | 
| storageSyncServices/syncGroups | Nej | 
| storageSyncServices/syncGroups/cloudEndpoints | Nej | 
| storageSyncServices/syncGroups/serverEndpoints | Nej | 
| storageSyncServices/registeredServers | Nej | 
| storageSyncServices/arbetsflöden | Nej | 

## <a name="storsimple"></a>Storsimple
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| chefer | Ja | 

## <a name="stream-analytics"></a>Stream Analytics
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| streamingjobs | Ja | 
| streamingjobs/diagnosticSettings | Nej | 
| streamingjobs/metricDefinitions | Nej | 

## <a name="subscription"></a>Prenumeration
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| SubscriptionDefinitions | Nej | 
| SubscriptionOperations | Nej | 

## <a name="support"></a>Support
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| supporttickets | Nej | 

## <a name="visual-studio"></a>Visual Studio
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konto | Ja | 
| kontot/projektet | Ja | 
| tillägget och Account | Ja | 
| konto | Ja | 
| kontot/projektet | Ja | 
| tillägget och Account | Ja | 

## <a name="web"></a>Webb
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| platser/instanser | Nej | 
| platser-fack-instanser | Nej | 
| platser-instanser-tillägg | Nej | 
| platser/platser/instanser/tillägg | Nej | 
| publishingUsers | Nej | 
| Verifiera | Nej | 
| sourceControls | Nej | 
| platser/hostNameBindings | Nej | 
| platser/domainOwnershipIdentifiers | Nej | 
| hostNameBindings-platser/platser | Nej | 
| certifikat | Ja | 
| Servergrupper | Ja | 
| Servergrupper/arbetare | Nej | 
| Platser | Ja | 
| platser/platser | Ja | 
| platser/mått | Nej | 
| platser-fack-mått | Nej | 
| platser/premieraddons | Ja | 
| hostingEnvironments | Ja | 
| hostingEnvironments/multiRolePools | Nej | 
| hostingEnvironments/workerPools | Nej | 
| hostingEnvironments/mått | Nej | 
| functions | Nej | 
| deletedSites | Nej | 
| apiManagementAccounts | Nej | 
| apiManagementAccounts/anslutningar | Nej | 
| apiManagementAccounts/connectionAcls | Nej | 
| apiManagementAccounts/API: er/anslutningar/connectionAcls | Nej | 
| apiManagementAccounts/API: er/connectionAcls | Nej | 
| apiManagementAccounts/apiAcls | Nej | 
| apiManagementAccounts/API: er/apiAcls | Nej | 
| apiManagementAccounts/API: er | Nej | 
| apiManagementAccounts/API: er/localizedDefinitions | Nej | 
| apiManagementAccounts-API: er-anslutningar | Nej | 
| anslutningar | Ja | 
| customApis | Ja | 
| connectionGateways | Ja | 
| billingMeters | Nej | 
| verifyHostingEnvironmentVnet | Nej | 

## <a name="xrm"></a>XRM
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| organisationer | Nej | 

## <a name="next-steps"></a>Nästa steg
Om du vill lära dig mer om att lägga till taggar i resurser, se [använda taggar för att organisera Azure-resurser](resource-group-using-tags.md).
