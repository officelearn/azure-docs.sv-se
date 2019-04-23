---
title: Azure Resource Manager-läge för fullständig borttagning efter resurstyp
description: Visar hur resurstyper hantera fullständiga läge borttagning i Azure Resource Manager-mallar.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 02/13/2019
ms.author: tomfitz
ms.openlocfilehash: fded37fee844a01f4d51518f2ca56dcf575704b2
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150157"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Borttagning av Azure-resurser för distributioner av klar
Den här artikeln beskrivs hur resurstyper hanterar borttagning när inte i en mall som har distribuerats i fullständiga läge.

Resurstyperna som markerats med `Yes` tas bort när typen inte är i mallen som distribueras med fullständig läge. 

Resurstyperna som markerats med `No` inte bort automatiskt när inte i mallen, men de är bort om den överordnade resursen tas bort. En fullständig beskrivning av problemet, se [distributionslägen i Azure Resource Manager](deployment-modes.md).

För att få samma data som en fil med kommaavgränsade värden kan hämta [slutföra-läge-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| DomainServices | Ja | 
| DomainServices/oucontainer | Nej | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| diagnosticSettings | Nej | 
| diagnosticSettingsCategories | Nej | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| supportProviders | Nej | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Konfigurationer | Nej | 
| generateRecommendations | Nej | 
| rekommendationer | Nej | 
| suppressions | Nej | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| servrar | Ja | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| reportFeedback | Nej | 
| tjänst | Ja | 
| validateServiceName | Nej | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| attestationProviders | Nej | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| automationAccounts | Ja | 
| automationAccounts/configurations | Ja | 
| automationAccounts/jobb | Nej | 
| automationAccounts/runbooks | Ja | 
| automationAccounts/softwareUpdateConfigurations | Nej | 
| automationAccounts/webhooks | Nej | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Miljöer | Nej | 
| miljöer/konton | Nej | 
| miljöer-konton-namnområden | Nej | 
| miljöer/konton/namnområden/konfigurationer | Nej | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| b2cDirectories | Ja | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| registreringar | Ja | 
| registreringar/customerSubscriptions | Nej | 
| registreringar/produkter | Nej | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| batchAccounts | Ja | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| mapApis | Ja | 
| updateCommunicationPreference | Nej | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| BizTalk | Ja | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| blueprintAssignments | Nej | 
| blueprintAssignments/assignmentOperations | Nej | 
| blueprintAssignments/operations | Nej | 
| skisser | Nej | 
| blueprints/artifacts | Nej | 
| blueprints/versions | Nej | 
| blueprints/versions/artifacts | Nej | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| botServices | Ja | 
| botServices/channels | Nej | 
| botServices/anslutningar | Nej | 

## <a name="microsoftcache"></a>Microsoft.Cache
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Redis | Ja | 
| RedisConfigDefinition | Nej | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| edgenodes | Nej | 
| Profiler | Ja | 
| profiler-slutpunkter | Ja | 
| profiler/slutpunkter/customdomains | Nej | 
| profiler/slutpunkter/ursprung | Nej | 
| validateProbe | Nej | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| certificateOrders | Ja | 
| certificateOrders/certifikat | Nej | 
| validateCertificateRegistrationInformation | Nej | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| classicInfrastructureResources | Nej | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| RateCard | Nej | 
| UsageAggregates | Nej | 

## <a name="microsoftcompute"></a>Microsoft.Compute
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| containerGroups | Ja | 
| serviceAssociationLinks | Nej | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| containerServices | Ja | 
| managedClusters | Ja | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| program | Ja | 
| updateCommunicationPreference | Nej | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Aviseringar | Nej | 
| billingAccounts | Nej | 
| Anslutningar | Ja | 
| Avdelningar | Nej | 
| Dimensioner | Nej | 
| EnrollmentAccounts | Nej | 
| Fråga | Nej | 
| Registrera dig | Nej | 
| Reportconfigs | Nej | 
| Rapporter | Nej | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| jobb | Ja | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| DataBoxEdgeDevices | Ja | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| arbetsytor | Ja | 
| workspaces/virtualNetworkPeerings | Nej | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| kataloger | Ja | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| connectionManagers | Ja | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| dataFactories | Ja | 
| dataFactories/diagnosticSettings | Nej | 
| dataFactorySchema | Nej | 
| fabriker | Ja | 
| fabriker/integrationRuntimes | Nej | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konton | Ja | 
| accounts/dataLakeStoreAccounts | Nej | 
| accounts/storageAccounts | Nej | 
| konton/storageAccounts/behållare | Nej | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konton | Ja | 
| accounts/eventGridFilters | Nej | 
| konton/firewallRules | Nej | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| services | Ja | 
| Services-projekt | Ja | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| servrar | Ja | 
| servrar/recoverableServers | Nej | 
| servers/virtualNetworkRules | Nej | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| servrar | Ja | 
| servrar/recoverableServers | Nej | 
| servers/virtualNetworkRules | Nej | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| servrar | Ja | 
| servers/advisors | Nej | 
| servrar/queryTexts | Nej | 
| servrar/recoverableServers | Nej | 
| servers/topQueryStatistics | Nej | 
| servers/virtualNetworkRules | Nej | 
| servers/waitStatistics | Nej | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| IotHubs | Ja | 
| IotHubs/eventGridFilters | Nej | 
| ProvisioningServices | Ja | 
| Användningar | Nej | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Domänkontrollanter | Ja | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Labs | Ja | 
| labs/serviceRunners | Ja | 
| labs/virtualMachines | Ja | 
| Scheman | Ja | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| databaseAccountNames | Nej | 
| databaseAccounts | Ja | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| domäner | Ja | 
| domains/domainOwnershipIdentifiers | Nej | 
| generateSsoRequest | Nej | 
| topLevelDomains | Nej | 
| validateDomainRegistrationInformation | Nej | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| lcsprojects | Nej | 
| lcsprojects/clouddeployments | Nej | 
| lcsprojects/anslutningar | Nej | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| domäner | Ja | 
| domäner/ämnen | Nej | 
| eventSubscriptions | Nej | 
| extensionTopics | Nej | 
| ämnen | Ja | 
| topicTypes | Nej | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Kluster | Ja | 
| namnrymder | Ja | 
| namnområden/authorizationrules | Nej | 
| namespaces/disasterrecoveryconfigs | Nej | 
| namnområden/eventhubs | Nej | 
| namnområden/eventhubs/authorizationrules | Nej | 
| namnområden/eventhubs/consumergroups | Nej | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| funktioner | Nej | 
| Providers | Nej | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| guestConfigurationAssignments | Nej | 
| Programvara | Nej | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| hanaInstances | Ja | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Kluster | Nej | 
| kluster/program | Nej | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| jobb | Ja | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| labelGroups | Nej | 
| labelGroups/labels | Nej | 
| labelGroups/labels/conditions | Nej | 
| labelGroups/labels/subLabels | Nej | 
| labelGroups/labels/subLabels/conditions | Nej | 

## <a name="microsoftinsights"></a>microsoft.insights
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| diagnosticSettings | Nej | 
| diagnosticSettingsCategories | Nej | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| IoTApps | Ja | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Graph | Ja | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| deletedVaults | Nej | 
| Valv | Ja | 
| valv/accessPolicies | Nej | 
| Valv/hemligheter | Nej | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Kluster | Ja | 
| kluster/databaser | Nej | 
| kluster/databaser/dataconnections | Nej | 
| kluster/databaser/eventhubconnections | Nej | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| labaccounts | Ja | 
| användare | Nej | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| loggar | Nej | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| integrationAccounts | Ja | 
| Arbetsflöden | Ja | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| commitmentPlans | Ja | 
| webServices | Ja | 
| Arbetsytor | Ja | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konton | Ja | 
| konton/arbetsytor | Ja | 
| konton-arbetsytor-projekt | Ja | 
| teamAccounts | Ja | 
| teamAccounts/arbetsytor | Ja | 
| teamAccounts-arbetsytor-projekt | Ja | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| arbetsytor | Ja | 
| arbetsytor/beräkningar | Nej | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Identiteter | Nej | 
| userAssignedIdentities | Ja | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| getEntities | Nej | 
| managementGroups | Nej | 
| resurser | Nej | 
| startTenantBackfill | Nej | 
| tenantBackfillStatus | Nej | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konton | Ja | 
| accounts/eventGridFilters | Nej | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| classicDevServices | Ja | 
| updateCommunicationPreference | Nej | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Avtal | Nej | 
| offertypes | Nej | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| projekt | Ja | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| Resurstyp | Borttagning i complete-läget |
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
| ytterdörrar | Ja | 
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| namnrymder | Ja | 
| namespaces/notificationHubs | Ja | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| enheter | Nej | 
| linkTargets | Nej | 
| storageInsightConfigs | Nej | 
| arbetsytor | Ja | 
| arbetsytor/datakällor | Nej | 
| arbetsytor/linkedServices | Nej | 
| arbetsytor/fråga | Nej | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| managementassociations | Nej | 
| managementconfigurations | Ja | 
| lösningar | Ja | 
| visningar | Ja | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| policyEvents | Nej | 
| policyStates | Nej | 
| policyTrackedResources | Nej | 
| reparationer | Nej | 

## <a name="microsoftportal"></a>Microsoft.Portal
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konsoler | Nej | 
| Instrumentpaneler | Ja | 
| Användarinställningarna | Nej | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| workspaceCollections | Ja | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Kapaciteter | Ja | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konton | Ja | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| backupProtectedItems | Nej | 
| Valv | Ja | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| namnrymder | Ja | 
| namnområden/authorizationrules | Nej | 
| namnområden/hybridconnections | Nej | 
| namnområden/hybridconnections/authorizationrules | Nej | 
| namnområden/wcfrelays | Nej | 
| namnområden/wcfrelays/authorizationrules | Nej | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| resurser | Nej | 
| subscriptionsStatus | Nej | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| availabilityStatuses | Nej | 
| childAvailabilityStatuses | Nej | 
| childResources | Nej | 
| evenemang | Nej | 
| impactedResources | Nej | 
| meddelanden | Nej | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| program | Ja | 
| saasresources | Nej | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| flöden | Ja | 
| förfrågningsåtgärder | Ja | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| resourceHealthMetadata | Nej | 
| searchServices | Ja | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| diagnosticSettings | Nej | 
| diagnosticSettingsCategories | Nej | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Kluster | Ja | 
| kluster/program | Nej | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| program | Ja | 
| Gateways | Ja | 
| Nätverk | Ja | 
| secrets | Ja | 
| volumes | Ja | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| SignalR | Ja | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| applianceDefinitions | Ja | 
| installationer | Ja | 
| applicationDefinitions | Ja | 
| program | Ja | 
| jitRequests | Ja | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Resurstyp | Borttagning i complete-läget |
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| SqlVirtualMachineGroups | Ja | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nej | 
| SqlVirtualMachines | Ja | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| storageAccounts | Ja | 
| storageAccounts/blobServices | Nej | 
| storageAccounts/fileServices | Nej | 
| storageAccounts/queueServices | Nej | 
| storageAccounts/services | Nej | 
| storageAccounts/tableServices | Nej | 
| Användningar | Nej | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| storageSyncServices | Ja | 
| storageSyncServices/registeredServers | Nej | 
| storageSyncServices/syncGroups | Nej | 
| storageSyncServices/syncGroups/cloudEndpoints | Nej | 
| storageSyncServices/syncGroups/serverEndpoints | Nej | 
| storageSyncServices/workflows | Nej | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| chefer | Ja | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| streamingjobs | Ja (Se kommentaren nedan) | 
| streamingjobs/diagnosticSettings | Nej | 

> [!NOTE]
> Du kan inte lägga till en tagg när streamingjobs körs. Stoppa resurs för att lägga till en tagg.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| CreateSubscription | Nej | 
| SubscriptionDefinitions | Nej | 
| SubscriptionOperations | Nej | 

## <a name="microsoftsupport"></a>microsoft.support
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| supporttickets | Nej | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| providerRegistrations | Ja | 
| resurser | Ja | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Miljöer | Ja | 
| miljöer/accessPolicies | Nej | 
| miljöer/eventsources | Ja | 
| miljöer/referenceDataSets | Ja | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| konto | Ja | 
| tillägget och Account | Ja | 
| kontot/projektet | Ja | 

## <a name="microsoftweb"></a>Microsoft.Web
| Resurstyp | Borttagning i complete-läget |
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
| webbplatser | Ja | 
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
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| diagnosticSettings | Nej | 
| diagnosticSettingsCategories | Nej | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| DeviceServices | Ja | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Resurstyp | Borttagning i complete-läget |
| ------------- | ----------- |
| Komponenter | Nej | 
| componentsSummary | Nej | 
| monitorInstances | Nej | 
| monitorInstancesSummary | Nej | 
| Övervakare | Nej | 
| notificationSettings | Nej | 

## <a name="next-steps"></a>Nästa steg
Om du vill lära dig mer om att lägga till taggar i resurser, se [använda taggar för att organisera Azure-resurser](resource-group-using-tags.md).
