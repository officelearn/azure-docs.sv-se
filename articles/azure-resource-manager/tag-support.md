---
title: Azure Resource Manager-tagg-stöd för resurser
description: Visar vilka Azure-resurstyper stöder taggar. Innehåller information om alla Azure-tjänster.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 02/13/2019
ms.author: tomfitz
ms.openlocfilehash: add222c024b5adde1ce81d6ed7f725d0a691c232
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246059"
---
# <a name="tag-support-for-azure-resources"></a>Tagg-stöd för Azure-resurser
Den här artikeln beskriver om en resurstyp stöder [taggar](resource-group-using-tags.md).

För att få samma data som en fil med kommaavgränsade värden kan hämta [taggen support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| DomainServices | Ja | 
| DomainServices/oucontainer | Nej | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| diagnosticSettings | Nej | 
| diagnosticSettingsCategories | Nej | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| supportProviders | Nej | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| aadsupportcases | Nej | 
| addsservices | Nej | 
| agenter | Nej | 
| anonymousapiusers | Nej | 
| konfiguration | Nej | 
| loggar | Nej | 
| rapporter | Nej | 
| services | Nej | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Konfigurationer | Nej | 
| generateRecommendations | Nej | 
| rekommendationer | Nej | 
| suppressions | Nej | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| actionRules | Nej | 
| aviseringar | Nej | 
| alertsList | Nej | 
| alertsSummary | Nej | 
| alertsSummaryList | Nej | 
| smartDetectorAlertRules | Nej | 
| smartDetectorRuntimeEnvironments | Nej | 
| smartGroups | Nej | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| servrar | Ja | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| reportFeedback | Nej | 
| tjänst | Ja | 
| validateServiceName | Nej | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| attestationProviders | Nej | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| classicAdministrators | Nej | 
| denyAssignments | Nej | 
| elevateAccess | Nej | 
| Lås | Nej | 
| behörigheter | Nej | 
| policyAssignments | Nej | 
| policyDefinitions | Nej | 
| policySetDefinitions | Nej | 
| providerOperations | Nej | 
| Rolltilldelningar | Nej | 
| roleDefinitions | Nej | 

## <a name="microsoftautomation"></a>Microsoft.Automation
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| automationAccounts | Ja | 
| automationAccounts/configurations | Ja | 
| automationAccounts/jobb | Nej | 
| automationAccounts/runbooks | Ja | 
| automationAccounts/softwareUpdateConfigurations | Nej | 
| automationAccounts/webhooks | Nej | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Miljöer | Nej | 
| miljöer/konton | Nej | 
| miljöer-konton-namnområden | Nej | 
| miljöer/konton/namnområden/konfigurationer | Nej | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| b2cDirectories | Ja | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| registreringar | Ja | 
| registreringar/customerSubscriptions | Nej | 
| registreringar/produkter | Nej | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| batchAccounts | Ja | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| billingAccounts | Nej | 
| billingAccounts/billingProfiles | Nej | 
| billingAccounts/billingProfiles/billingSubscriptions | Nej | 
| billingAccounts/billingProfiles/fakturor | Nej | 
| billingAccounts/billingProfiles/fakturor/prisdokument | Nej | 
| billingAccounts/billingProfiles/operationStatus | Nej | 
| billingAccounts/billingProfiles/paymentMethods | Nej | 
| billingAccounts-billingProfiles-principer | Nej | 
| billingAccounts/billingProfiles/prisdokument | Nej | 
| billingAccounts-billingProfiles-produkter | Nej | 
| billingAccounts/billingProfiles/transactions | Nej | 
| billingAccounts/billingSubscriptions | Nej | 
| billingAccounts/avdelningar | Nej | 
| billingAccounts/eligibleOffers | Nej | 
| billingAccounts/enrollmentAccounts | Nej | 
| billingAccounts/fakturor | Nej | 
| billingAccounts/invoiceSections | Nej | 
| billingAccounts/invoiceSections/billingSubscriptions | Nej | 
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Nej | 
| billingAccounts/invoiceSections/importRequests | Nej | 
| billingAccounts/invoiceSections/initiateImportRequest | Nej | 
| billingAccounts/invoiceSections/initiateTransfer | Nej | 
| billingAccounts/invoiceSections/operationStatus | Nej | 
| billingAccounts-invoiceSections-produkter | Nej | 
| billingAccounts/invoiceSections/disköverföring | Nej | 
| billingAccounts/produkter | Nej | 
| billingAccounts/projekt | Nej | 
| billingAccounts/projekt/billingSubscriptions | Nej | 
| billingAccounts/projects/importRequests | Nej | 
| billingAccounts/projekt/initiateImportRequest | Nej | 
| billingAccounts/projects/operationStatus | Nej | 
| billingAccounts-projekt-produkter | Nej | 
| billingAccounts/transaktioner | Nej | 
| billingPeriods | Nej | 
| BillingPermissions | Nej | 
| billingProperty | Nej | 
| BillingRoleAssignments | Nej | 
| BillingRoleDefinitions | Nej | 
| CreateBillingRoleAssignment | Nej | 
| avdelningar | Nej | 
| enrollmentAccounts | Nej | 
| importRequests | Nej | 
| importRequests/acceptImportRequest | Nej | 
| importRequests/declineImportRequest | Nej | 
| Fakturor | Nej | 
| Överföringar | Nej | 
| Disköverföringar/acceptTransfer | Nej | 
| transfers/declineTransfer | Nej | 
| transfers/operationStatus | Nej | 
| usagePlans | Nej | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| mapApis | Ja | 
| updateCommunicationPreference | Nej | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| BizTalk | Ja | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| blueprintAssignments | Nej | 
| blueprintAssignments/assignmentOperations | Nej | 
| blueprintAssignments/operations | Nej | 
| skisser | Nej | 
| blueprints/artifacts | Nej | 
| blueprints/versions | Nej | 
| blueprints/versions/artifacts | Nej | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| botServices | Ja | 
| botServices/channels | Nej | 
| botServices/anslutningar | Nej | 

## <a name="microsoftcache"></a>Microsoft.Cache
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Redis | Ja | 
| RedisConfigDefinition | Nej | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| appliedReservations | Nej | 
| calculatePrice | Nej | 
| kataloger | Nej | 
| commercialReservationOrders | Nej | 
| reservationOrders | Nej | 
| reservationOrders/calculateRefund | Nej | 
| reservationOrders/merge | Nej | 
| reservationOrders/reservations | Nej | 
| reservationOrders/reservations/revisions | Nej | 
| reservationOrders/return | Nej | 
| reservationOrders/split | Nej | 
| reservationOrders/swap | Nej | 
| reservationer | Nej | 
| resurser | Nej | 
| validateReservationOrder | Nej | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| edgenodes | Nej | 
| Profiler | Ja | 
| profiler-slutpunkter | Ja | 
| profiler/slutpunkter/customdomains | Nej | 
| profiler/slutpunkter/ursprung | Nej | 
| validateProbe | Nej | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| certificateOrders | Ja | 
| certificateOrders/certifikat | Nej | 
| validateCertificateRegistrationInformation | Nej | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Funktioner | Nej | 
| domainNames | Nej | 
| domainNames/capabilities | Nej | 
| domainNames/internalLoadBalancers | Nej | 
| domainNames/serviceCertificates | Nej | 
| domainNames/platser | Nej | 
| domainNames-fack-roller | Nej | 
| moveSubscriptionResources | Nej | 
| operatingSystemFamilies | Nej | 
| operatingSystems | Nej | 
| quotas | Nej | 
| Resurstyper | Nej | 
| validateSubscriptionMoveAvailability | Nej | 
| virtualMachines | Nej | 
| virtualMachines/diagnosticSettings | Nej | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| classicInfrastructureResources | Nej | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Funktioner | Nej | 
| expressRouteCrossConnections | Nej | 
| expressRouteCrossConnections/peerings | Nej | 
| gatewaySupportedDevices | Nej | 
| networkSecurityGroups | Nej | 
| quotas | Nej | 
| reservedIps | Nej | 
| virtualNetworks | Nej | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nej | 
| virtualNetworks/virtualNetworkPeerings | Nej | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Funktioner | Nej | 
| Diskar | Nej | 
| images | Nej | 
| osImages | Nej | 
| osPlatformImages | Nej | 
| publicImages | Nej | 
| quotas | Nej | 
| storageAccounts | Nej | 
| storageAccounts/services | Nej | 
| storageAccounts/services/diagnosticSettings | Nej | 
| storageAccounts/vmImages | Nej | 
| vmImages | Nej | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| RateCard | Nej | 
| UsageAggregates | Nej | 

## <a name="microsoftcompute"></a>Microsoft.Compute
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| availabilitySets | Ja | 
| Diskar | Ja | 
| images | Ja | 
| restorePointCollections | Ja | 
| restorePointCollections/restorePoints | Nej | 
| sharedVMImages | Ja | 
| sharedVMImages/versions | Ja | 
| ögonblicksbilder | Ja | 
| virtualMachines | Ja | 
| virtualMachines/diagnosticSettings | Nej | 
| virtualMachines/extensions | Ja | 
| virtualMachineScaleSets | Ja | 
| virtualMachineScaleSets/extensions | Nej | 
| virtualMachineScaleSets/networkInterfaces | Nej | 
| virtualMachineScaleSets/publicIPAddresses | Nej | 
| virtualMachineScaleSets/virtualMachines | Nej | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nej | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| AggregatedCost | Nej | 
| Saldon | Nej | 
| Budgetar | Nej | 
| Avgifter | Nej | 
| CostTags | Nej | 
| Krediter | Nej | 
| evenemang | Nej | 
| Prognoser | Nej | 
| många | Nej | 
| Marknadsplatser | Nej | 
| Pricesheets | Nej | 
| produkter | Nej | 
| ReservationDetails | Nej | 
| ReservationRecommendations | Nej | 
| ReservationSummaries | Nej | 
| ReservationTransactions | Nej | 
| Taggar | Nej | 
| Villkor | Nej | 
| UsageDetails | Nej | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| containerGroups | Ja | 
| serviceAssociationLinks | Nej | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| register | Ja | 
| register/versioner | Nej | 
| registries/builds/cancel | Nej | 
| registries/builds/getLogLink | Nej | 
| registries/buildTasks | Ja | 
| register/buildTasks/steg | Nej | 
| registries/eventGridFilters | Nej | 
| registries/getBuildSourceUploadUrl | Nej | 
| registries/GetCredentials | Nej | 
| registries/importImage | Nej | 
| registries/queueBuild | Nej | 
| register/regenerateCredential | Nej | 
| register/regenerateCredentials | Nej | 
| register/replikeringar | Ja | 
| register/körningar | Nej | 
| register/körningar/Avbryt | Nej | 
| registries/scheduleRun | Nej | 
| register/uppgifter | Ja | 
| registries/updatePolicies | Nej | 
| register/webhooks | Ja | 
| register/webhooks/getCallbackConfig | Nej | 
| register-webhooks-ping | Nej | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| containerServices | Ja | 
| managedClusters | Ja | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| program | Ja | 
| updateCommunicationPreference | Nej | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Aviseringar | Nej | 
| billingAccounts | Nej | 
| Anslutningar | Ja | 
| Avdelningar | Nej | 
| Dimensioner | Nej | 
| EnrollmentAccounts | Nej | 
| Söka i data | Nej | 
| Registrera dig | Nej | 
| Reportconfigs | Nej | 
| Rapporter | Nej | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| hubbar | Ja | 
| hubs/authorizationPolicies | Nej | 
| hubs/anslutningar | Nej | 
| NAV-kopplingar-mappningar | Nej | 
| hubs/interaktioner | Nej | 
| hubs/kpi | Nej | 
| hubs/länkar | Nej | 
| NAV-profiler | Nej | 
| hubs/rolltilldelningar | Nej | 
| hubs/roller | Nej | 
| hubs/suggestTypeSchema | Nej | 
| hubbar och vyer | Nej | 
| hubs/widgetTypes | Nej | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| jobb | Ja | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| DataBoxEdgeDevices | Ja | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| arbetsytor | Ja | 
| workspaces/virtualNetworkPeerings | Nej | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| kataloger | Ja | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| connectionManagers | Ja | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| dataFactories | Ja | 
| dataFactories/diagnosticSettings | Nej | 
| dataFactorySchema | Nej | 
| fabriker | Ja | 
| fabriker/integrationRuntimes | Nej | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 
| accounts/dataLakeStoreAccounts | Nej | 
| accounts/storageAccounts | Nej | 
| konton/storageAccounts/behållare | Nej | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 
| accounts/eventGridFilters | Nej | 
| konton/firewallRules | Nej | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| services | Ja | 
| Services-projekt | Ja | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| servrar | Ja | 
| servrar/recoverableServers | Nej | 
| servers/virtualNetworkRules | Nej | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| servrar | Ja | 
| servrar/recoverableServers | Nej | 
| servers/virtualNetworkRules | Nej | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| servrar | Ja | 
| servers/advisors | Nej | 
| servrar/queryTexts | Nej | 
| servrar/recoverableServers | Nej | 
| servers/topQueryStatistics | Nej | 
| servers/virtualNetworkRules | Nej | 
| servers/waitStatistics | Nej | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| IotHubs | Ja | 
| IotHubs/eventGridFilters | Nej | 
| ProvisioningServices | Ja | 
| Användningar | Nej | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Domänkontrollanter | Ja | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Labs | Ja | 
| labs/serviceRunners | Ja | 
| labs/virtualMachines | Ja | 
| Scheman | Ja | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| databaseAccountNames | Nej | 
| databaseAccounts | Ja | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| domäner | Ja | 
| domains/domainOwnershipIdentifiers | Nej | 
| generateSsoRequest | Nej | 
| topLevelDomains | Nej | 
| validateDomainRegistrationInformation | Nej | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| lcsprojects | Nej | 
| lcsprojects/clouddeployments | Nej | 
| lcsprojects/anslutningar | Nej | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| domäner | Ja | 
| domäner/ämnen | Nej | 
| eventSubscriptions | Nej | 
| extensionTopics | Nej | 
| ämnen | Ja | 
| topicTypes | Nej | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Kluster | Ja | 
| namnrymder | Ja | 
| namnområden/authorizationrules | Nej | 
| namespaces/disasterrecoveryconfigs | Nej | 
| namnområden/eventhubs | Nej | 
| namnområden/eventhubs/authorizationrules | Nej | 
| namnområden/eventhubs/consumergroups | Nej | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| funktioner | Nej | 
| Providers | Nej | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| registrera | Nej | 
| galleryitems | Nej | 
| generateartifactaccessuri | Nej | 
| myareas | Nej | 
| myareas/områden | Nej | 
| myareas/områden/områden | Nej | 
| myareas/områden/områden/galleryitems | Nej | 
| myareas/områden/galleryitems | Nej | 
| myareas/galleryitems | Nej | 
| Registrera dig | Nej | 
| resurser | Nej | 
| retrieveresourcesbyid | Nej | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| guestConfigurationAssignments | Nej | 
| Programvara | Nej | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| hanaInstances | Ja | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Kluster | Ja | 
| kluster/program | Nej | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| jobb | Ja | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| labelGroups | Nej | 
| labelGroups/labels | Nej | 
| labelGroups/labels/conditions | Nej | 
| labelGroups/labels/subLabels | Nej | 
| labelGroups/labels/subLabels/conditions | Nej | 

## <a name="microsoftinsights"></a>microsoft.insights
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| actiongroups | Ja | 
| activityLogAlerts | Ja | 
| alertrules | Ja | 
| automatedExportSettings | Nej | 
| autoscalesettings | Ja | 
| baslinje | Nej | 
| calculatebaseline | Nej | 
| Komponenter | Ja | 
| komponenter/händelser | Nej | 
| components/pricingPlans | Nej | 
| komponenter/fråga | Nej | 
| diagnosticSettings | Nej | 
| diagnosticSettingsCategories | Nej | 
| eventCategories | Nej | 
| eventtypes | Nej | 
| extendedDiagnosticSettings | Nej | 
| logDefinitions | Nej | 
| logprofiles | Nej | 
| loggar | Nej | 
| metricAlerts | Ja |
| migrateToNewPricingModel | Nej | 
| myWorkbooks | Nej | 
| frågor | Nej | 
| rollbackToLegacyPricingModel | Nej | 
| scheduledqueryrules | Ja | 
| vmInsightsOnboardingStatuses | Nej | 
| webbtester | Ja | 
| arbetsböcker | Ja | 

## <a name="microsoftintune"></a>Microsoft.Intune
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| diagnosticSettings | Nej | 
| diagnosticSettingsCategories | Nej | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| IoTApps | Ja | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Graph | Ja | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| deletedVaults | Nej | 
| Valv | Ja | 
| valv/accessPolicies | Nej | 
| Valv/hemligheter | Nej | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Kluster | Ja | 
| kluster/databaser | Nej | 
| kluster/databaser/dataconnections | Nej | 
| kluster/databaser/eventhubconnections | Nej | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| labaccounts | Ja | 
| användare | Nej | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| loggar | Nej | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| integrationAccounts | Ja | 
| Arbetsflöden | Ja | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| commitmentPlans | Ja | 
| webServices | Ja | 
| Arbetsytor | Ja | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 
| konton/arbetsytor | Ja | 
| konton-arbetsytor-projekt | Ja | 
| teamAccounts | Ja | 
| teamAccounts/arbetsytor | Ja | 
| teamAccounts-arbetsytor-projekt | Ja | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| arbetsytor | Ja | 
| arbetsytor/beräkningar | Nej | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Identiteter | Nej | 
| userAssignedIdentities | Ja | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| getEntities | Nej | 
| managementGroups | Nej | 
| resurser | Nej | 
| startTenantBackfill | Nej | 
| tenantBackfillStatus | Nej | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 
| accounts/eventGridFilters | Nej | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Erbjudanden | Nej | 
| offerTypes | Nej | 
| offerTypes/utgivare | Nej | 
| offerTypes-utgivare-erbjudanden | Nej | 
| offerTypes/utgivare/erbjudanden/planer | Nej | 
| offerTypes/utgivare/erbjudanden/planer/avtal | Nej | 
| offerTypes/utgivare/erbjudanden/planer/konfigurationer | Nej | 
| offerTypes/publishers/offers/plans/configs/importImage | Nej | 
| privategalleryitems | Nej | 
| produkter | Nej | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| classicDevServices | Ja | 
| updateCommunicationPreference | Nej | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Avtal | Nej | 
| offertypes | Nej | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| mediaservices | Ja | 
| mediaservices/accountFilters | Nej | 
| mediaservices/tillgångar | Nej | 
| mediaservices/assets/assetFilters | Nej | 
| mediaservices/contentKeyPolicies | Nej | 
| mediaservices/eventGridFilters | Nej | 
| mediaservices/liveEventOperations | Nej | 
| mediaservices/liveEvents | Ja | 
| mediaservices/liveEvents/liveOutputs | Nej | 
| mediaservices/liveOutputOperations | Nej | 
| mediaservices/streamingEndpointOperations | Nej | 
| mediaservices/Strömningsslutpunkter | Ja | 
| mediaservices/streamingLocators | Nej | 
| mediaservices/streamingPolicies | Nej | 
| mediaservices/omvandlingar | Nej | 
| mediaservices-transformeringar-jobb | Nej | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| projekt | Ja | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| applicationGateways | Ja | 
| applicationSecurityGroups | Ja | 
| azureFirewallFqdnTags | Nej | 
| azureFirewalls | Ja | 
| bgpServiceCommunities | Nej | 
| anslutningar | Ja | 
| ddosCustomPolicies | Ja | 
| ddosProtectionPlans | Ja | 
| dnsOperationStatuses | Nej | 
| dnszones | Ja | 
| dnszones/A | Nej | 
| dnszones/AAAA | Nej | 
| dnszones/alla | Nej | 
| dnszones/CAA | Nej | 
| dnszones/CNAME | Nej | 
| dnszones/MX | Nej | 
| dnszones/NS | Nej | 
| dnszones/PTR | Nej | 
| dnszones/postuppsättningar | Nej | 
| dnszones/SOA | Nej | 
| dnszones/SRV | Nej | 
| dnszones/TXT | Nej | 
| expressRouteCircuits | Ja | 
| expressRouteServiceProviders | Nej | 
| frontdoors | Ja | 
| frontdoorWebApplicationFirewallPolicies | Ja | 
| getDnsResourceReference | Nej | 
| interfaceEndpoints | Ja | 
| internalNotify | Nej | 
| belastningsutjämnare | Ja | 
| localNetworkGateways | Ja | 
| natGateways | Ja | 
| networkIntentPolicies | Ja | 
| networkInterfaces | Ja | 
| networkProfiles | Ja | 
| networkSecurityGroups | Ja | 
| networkWatchers | Ja | 
| networkWatchers/connectionMonitors | Ja | 
| networkWatchers/linser | Ja | 
| networkWatchers/pingMeshes | Ja | 
| privateLinkServices | Ja | 
| publicIPAddresses | Ja | 
| publicIPPrefixes | Ja | 
| routeFilters | Ja | 
| routeTables | Ja | 
| serviceEndpointPolicies | Ja | 
| trafficManagerGeographicHierarchies | Nej | 
| trafficmanagerprofiles | Ja | 
| trafficmanagerprofiles/termiska kartor | Nej | 
| virtualHubs | Ja | 
| virtualNetworkGateways | Ja | 
| virtualNetworks | Ja | 
| virtualNetworkTaps | Ja | 
| virtualWans | Ja | 
| vpnGateways | Ja | 
| vpnSites | Ja | 
| webApplicationFirewallPolicies | Ja | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| namnrymder | Ja | 
| namespaces/notificationHubs | Ja | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| enheter | Nej | 
| linkTargets | Nej | 
| storageInsightConfigs | Nej | 
| arbetsytor | Ja | 
| arbetsytor/datakällor | Nej | 
| arbetsytor/linkedServices | Nej | 
| arbetsytor/fråga | Nej | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| managementassociations | Nej | 
| managementconfigurations | Ja | 
| lösningar | Ja | 
| visningar | Ja | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| policyEvents | Nej | 
| policyStates | Nej | 
| policyTrackedResources | Nej | 
| reparationer | Nej | 

## <a name="microsoftportal"></a>Microsoft.Portal
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konsoler | Nej | 
| Instrumentpaneler | Ja | 
| Användarinställningarna | Nej | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| workspaceCollections | Ja | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Kapaciteter | Ja | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| backupProtectedItems | Nej | 
| Valv | Ja | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| namnrymder | Ja | 
| namnområden/authorizationrules | Nej | 
| namnområden/hybridconnections | Nej | 
| namnområden/hybridconnections/authorizationrules | Nej | 
| namnområden/wcfrelays | Nej | 
| namnområden/wcfrelays/authorizationrules | Nej | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| resurser | Nej | 
| subscriptionsStatus | Nej | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| availabilityStatuses | Nej | 
| childAvailabilityStatuses | Nej | 
| childResources | Nej | 
| evenemang | Nej | 
| impactedResources | Nej | 
| meddelanden | Nej | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| distributioner | Nej | 
| Distributioner/operations | Nej | 
| Länkar | Nej | 
| notifyResourceJobs | Nej | 
| Providers | Nej | 
| resourceGroups | Nej | 
| resurser | Nej | 
| prenumerationer | Nej | 
| Prenumerationer/providers | Nej | 
| subscriptions/resourceGroups | Nej | 
| prenumerationer/resourcegroups/resurser | Nej | 
| Prenumerationer/resurser | Nej | 
| prenumerationer/tagnames | Nej | 
| subscriptions/tagNames/tagValues | Nej | 
| Klienter | Nej | 

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| program | Ja | 
| saasresources | Nej | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| flöden | Ja | 
| förfrågningsåtgärder | Ja | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| resourceHealthMetadata | Nej | 
| searchServices | Ja | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| advancedThreatProtectionSettings | Nej | 
| aviseringar | Nej | 
| allowedConnections | Nej | 
| installationer | Nej | 
| applicationWhitelistings | Nej | 
| AutoProvisioningSettings | Nej | 
| Efterlevnadskraven | Nej | 
| dataCollectionAgents | Nej | 
| discoveredSecuritySolutions | Nej | 
| externalSecuritySolutions | Nej | 
| InformationProtectionPolicies | Nej | 
| jitNetworkAccessPolicies | Nej | 
| övervakning | Nej | 
| övervaka/program mot skadlig kod | Nej | 
| övervaka/baslinje | Nej | 
| övervaka/patch | Nej | 
| principer | Nej | 
| prissättningar | Nej | 
| securityContacts | Nej | 
| securitySolutions | Nej | 
| securitySolutionsReferenceData | Nej | 
| securityStatus | Nej | 
| securityStatus/slutpunkter | Nej | 
| securityStatus/undernät | Nej | 
| securityStatus/virtualMachines | Nej | 
| securityStatuses | Nej | 
| securityStatusesSummaries | Nej | 
| settings | Nej | 
| uppgifter | Nej | 
| topologies | Nej | 
| workspaceSettings | Nej | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| diagnosticSettings | Nej | 
| diagnosticSettingsCategories | Nej | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| namnrymder | Ja | 
| namnområden/authorizationrules | Nej | 
| namespaces/disasterrecoveryconfigs | Nej | 
| namespaces/eventgridfilters | Nej | 
| namnområden/köer | Nej | 
| namnområden/köer/authorizationrules | Nej | 
| namnområden/ämnen | Nej | 
| namnområden/information/authorizationrules | Nej | 
| namnområden/ämnen/prenumerationer | Nej | 
| namnområden/ämnen/prenumerationer/regler | Nej | 
| premiumMessagingRegions | Nej | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Kluster | Ja | 
| kluster/program | Nej | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| program | Ja | 
| Gateways | Ja | 
| Nätverk | Ja | 
| secrets | Ja | 
| volumes | Ja | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| SignalR | Ja | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| applianceDefinitions | Ja | 
| installationer | Ja | 
| applicationDefinitions | Ja | 
| program | Ja | 
| jitRequests | Ja | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| managedInstances | Ja |
| managedInstances/databaser | Ja (Se kommentaren nedan) |
| managedInstances/databases/backupShortTermRetentionPolicies | Nej |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nej |
| managedInstances/databaser/vulnerabilityAssessments | Nej |
| managedInstances/databaser/vulnerabilityAssessments/regler/baslinjer | Nej |
| managedInstances/encryptionProtector | Nej |
| managedInstances/keys | Nej |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nej |
| managedInstances/vulnerabilityAssessments | Nej |
| servrar | Ja | 
| servrar/administratörer | Nej | 
| servers/communicationLinks | Nej | 
| servrar/databaser | Ja (Se kommentaren nedan) | 
| servers/encryptionProtector | Nej | 
| servrar/firewallRules | Nej | 
| servrar/nycklar | Nej | 
| servrar/restorableDroppedDatabases | Nej | 
| servrar/serviceobjectives | Nej | 
| servers/tdeCertificates | Nej | 

> [!NOTE]
> Huvudservern databasen har inte stöd för taggar, men andra databaser, inklusive Data Warehouse-databaser har stöd för taggar.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| SqlVirtualMachineGroups | Ja | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nej | 
| SqlVirtualMachines | Ja | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| storageAccounts | Ja | 
| storageAccounts/blobServices | Nej | 
| storageAccounts/fileServices | Nej | 
| storageAccounts/queueServices | Nej | 
| storageAccounts/services | Nej | 
| storageAccounts/tableServices | Nej | 
| Användningar | Nej | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| storageSyncServices | Ja | 
| storageSyncServices/registeredServers | Nej | 
| storageSyncServices/syncGroups | Nej | 
| storageSyncServices/syncGroups/cloudEndpoints | Nej | 
| storageSyncServices/syncGroups/serverEndpoints | Nej | 
| storageSyncServices/workflows | Nej | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| chefer | Ja | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| streamingjobs | Ja (Se kommentaren nedan) | 
| streamingjobs/diagnosticSettings | Nej | 

> [!NOTE]
> Du kan inte lägga till en tagg när streamingjobs körs. Stoppa resurs för att lägga till en tagg.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| CreateSubscription | Nej | 
| SubscriptionDefinitions | Nej | 
| SubscriptionOperations | Nej | 

## <a name="microsoftsupport"></a>microsoft.support
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| supporttickets | Nej | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| providerRegistrations | Ja | 
| resurser | Ja | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Miljöer | Ja | 
| miljöer/accessPolicies | Nej | 
| miljöer/eventsources | Ja | 
| miljöer/referenceDataSets | Ja | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| konto | Ja | 
| tillägget och Account | Ja | 
| kontot/projektet | Ja | 

## <a name="microsoftweb"></a>Microsoft.Web
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| apiManagementAccounts | Nej | 
| apiManagementAccounts/apiAcls | Nej | 
| apiManagementAccounts/apis | Nej | 
| apiManagementAccounts/apis/apiAcls | Nej | 
| apiManagementAccounts/apis/connectionAcls | Nej | 
| apiManagementAccounts/apis/connections | Nej | 
| apiManagementAccounts/apis/connections/connectionAcls | Nej | 
| apiManagementAccounts/apis/localizedDefinitions | Nej | 
| apiManagementAccounts/connectionAcls | Nej | 
| apiManagementAccounts/connections | Nej | 
| billingMeters | Nej | 
| certifikat | Ja | 
| connectionGateways | Ja | 
| anslutningar | Ja | 
| customApis | Ja | 
| deletedSites | Nej | 
| functions | Nej | 
| hostingEnvironments | Ja | 
| hostingEnvironments/multiRolePools | Nej | 
| hostingEnvironments-multiRolePools-instanser | Nej | 
| hostingEnvironments/workerPools | Nej | 
| hostingEnvironments-workerPools-instanser | Nej | 
| publishingUsers | Nej | 
| rekommendationer | Nej | 
| resourceHealthMetadata | Nej | 
| körningar | Nej | 
| serverFarms | Ja | 
| serverFarms/workers | Nej | 
| Platser | Ja | 
| sites/domainOwnershipIdentifiers | Nej | 
| sites/hostNameBindings | Nej | 
| platser/instanser | Nej | 
| platser-instanser-tillägg | Nej | 
| sites/premieraddons | Ja | 
| platser/rekommendationer | Nej | 
| sites/resourceHealthMetadata | Nej | 
| platser/platser | Ja | 
| sites/slots/hostNameBindings | Nej | 
| platser-fack-instanser | Nej | 
| platser/platser/instanser/tillägg | Nej | 
| sourceControls | Nej | 
| Verifiera | Nej | 
| verifyHostingEnvironmentVnet | Nej | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| diagnosticSettings | Nej | 
| diagnosticSettingsCategories | Nej | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| DeviceServices | Ja | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Resurstyp | Har stöd för taggar |
| ------------- | ----------- |
| Komponenter | Nej | 
| componentsSummary | Nej | 
| monitorInstances | Nej | 
| monitorInstancesSummary | Nej | 
| Övervakare | Nej | 
| notificationSettings | Nej | 

## <a name="next-steps"></a>Nästa steg
Om du vill lära dig mer om att lägga till taggar i resurser, se [använda taggar för att organisera Azure-resurser](resource-group-using-tags.md).
