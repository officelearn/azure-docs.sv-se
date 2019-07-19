---
title: Azure Resource Manager slutfört läge borttagning efter resurs typ
description: Visar hur resurs typer hanterar fullständig borttagning av läge i Azure Resource Manager mallar.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 5ac442c0ae1e397fd1e8b58fdbcf61eb8712046c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302847"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Borttagning av Azure-resurser för komplett läges distributioner
I den här artikeln beskrivs hur resurs typer hanterar borttagning när de inte finns i en mall som distribueras i fullständigt läge.

Resurs typerna som marker ATS `Yes` med tas bort när typen inte finns i mallen distribuerad med slutfört läge. 

Resurs typerna som marker ATS `No` med tas inte bort automatiskt när de inte finns i mallen, men de tas bort om den överordnade resursen tas bort. En fullständig beskrivning av beteendet finns i [Azure Resource Manager distributions lägen](deployment-modes.md).


## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DomainServices | Ja | 
> | DomainServices/oucontainer | Nej | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Nej | 
> | diagnosticSettingsCategories | Nej | 

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | supportProviders | Nej | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | aadsupportcases | Nej | 
> | addsservices | Nej | 
> | Aktörer | Nej | 
> | anonymousapiusers | Nej | 
> | konfiguration | Nej | 
> | logs | Nej | 
> | rapporter | Nej | 
> | services | Nej | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konfigurationer | Nej | 
> | generateRecommendations | Nej | 
> | Rekommenderade | Nej | 
> | utelämningar | Nej | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | actionRules | Nej | 
> | aviseringar | Nej | 
> | alertsList | Nej | 
> | alertsSummary | Nej | 
> | alertsSummaryList | Nej | 
> | smartDetectorAlertRules | Nej | 
> | smartDetectorRuntimeEnvironments | Nej | 
> | smartGroups | Nej | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Brygghuvudservrar | Ja | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | reportFeedback | Nej | 
> | tjänst | Ja | 
> | validateServiceName | Nej | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attestationProviders | Nej | 

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicAdministrators | Nej | 
> | denyAssignments | Nej | 
> | elevateAccess | Nej | 
> | hålls | Nej | 
> | behörigheter | Nej | 
> | policyAssignments | Nej | 
> | policyDefinitions | Nej | 
> | policySetDefinitions | Nej | 
> | providerOperations | Nej | 
> | roleAssignments | Nej | 
> | roleDefinitions | Nej | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | AutomationAccounts | Ja | 
> | automationAccounts/konfigurationer | Ja | 
> | automationAccounts/jobb | Nej | 
> | automationAccounts/Runbooks | Ja | 
> | automationAccounts/softwareUpdateConfigurations | Nej | 
> | automationAccounts/webhooks | Nej | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Nej | 
> | miljöer/konton | Nej | 
> | miljöer/konton/namn områden | Nej | 
> | miljöer/konton/namn rymder/konfigurationer | Nej | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | b2cDirectories | Ja | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | registreringar | Ja | 
> | registreringar/customerSubscriptions | Nej | 
> | registreringar/produkter | Nej | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | batchAccounts | Ja | 

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | billingAccounts | Nej | 
> | billingAccounts/billingProfiles | Nej | 
> | billingAccounts/billingProfiles/billingSubscriptions | Nej | 
> | billingAccounts/billingProfiles/fakturor | Nej | 
> | billingAccounts/billingProfiles/fakturor/pris dokument | Nej | 
> | billingAccounts/billingProfiles/operationStatus | Nej | 
> | billingAccounts/billingProfiles/paymentMethods | Nej | 
> | billingAccounts/billingProfiles/policys | Nej | 
> | billingAccounts/billingProfiles/pris dokument | Nej | 
> | billingAccounts/billingProfiles/Products | Nej | 
> | billingAccounts/billingProfiles/transaktioner | Nej | 
> | billingAccounts/billingSubscriptions | Nej | 
> | billingAccounts/avdelningar | Nej | 
> | billingAccounts/eligibleOffers | Nej | 
> | billingAccounts/enrollmentAccounts | Nej | 
> | billingAccounts/fakturor | Nej | 
> | billingAccounts/invoiceSections | Nej | 
> | billingAccounts/invoiceSections/billingSubscriptions | Nej | 
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nej | 
> | billingAccounts/invoiceSections/importRequests | Nej | 
> | billingAccounts/invoiceSections/initiateImportRequest | Nej | 
> | billingAccounts/invoiceSections/initiateTransfer | Nej | 
> | billingAccounts/invoiceSections/operationStatus | Nej | 
> | billingAccounts/invoiceSections/Products | Nej | 
> | billingAccounts/invoiceSections/överföringar | Nej | 
> | billingAccounts/produkter | Nej | 
> | billingAccounts/projekt | Nej | 
> | billingAccounts/projekt/billingSubscriptions | Nej | 
> | billingAccounts/projekt/importRequests | Nej | 
> | billingAccounts/projekt/initiateImportRequest | Nej | 
> | billingAccounts/projekt/operationStatus | Nej | 
> | billingAccounts/projekt/produkter | Nej | 
> | billingAccounts/transaktioner | Nej | 
> | billingPeriods | Nej | 
> | BillingPermissions | Nej | 
> | billingProperty | Nej | 
> | BillingRoleAssignments | Nej | 
> | BillingRoleDefinitions | Nej | 
> | CreateBillingRoleAssignment | Nej | 
> | enheten | Nej | 
> | enrollmentAccounts | Nej | 
> | importRequests | Nej | 
> | importRequests/acceptImportRequest | Nej | 
> | importRequests/declineImportRequest | Nej | 
> | fakturor | Nej | 
> | överlåtelse | Nej | 
> | överföringar/acceptTransfer | Nej | 
> | överföringar/declineTransfer | Nej | 
> | överföringar/operationStatus | Nej | 
> | usagePlans | Nej | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | mapApis | Ja | 
> | updateCommunicationPreference | Nej | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | BizTalk | Ja | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blueprintAssignments | Nej | 
> | blueprintAssignments/assignmentOperations | Nej | 
> | blueprintAssignments/åtgärder | Nej | 
> | modeller | Nej | 
> | skisser/artefakter | Nej | 
> | skisser/versioner | Nej | 
> | blueprints/versions/artifacts | Nej | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | botServices | Ja | 
> | botServices/channels | Nej | 
> | botServices/anslutningar | Nej | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Redis | Ja | 
> | RedisConfigDefinition | Nej | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appliedReservations | Nej | 
> | calculatePrice | Nej | 
> | kataloger | Nej | 
> | commercialReservationOrders | Nej | 
> | reservationOrders | Nej | 
> | reservationOrders/calculateRefund | Nej | 
> | reservationOrders/slå samman | Nej | 
> | reservationOrders/reservationer | Nej | 
> | reservationOrders/reservationer/revisioner | Nej | 
> | reservationOrders/retur | Nej | 
> | reservationOrders/split | Nej | 
> | reservationOrders/växling | Nej | 
> | reservera | Nej | 
> | Resurser | Nej | 
> | validateReservationOrder | Nej | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | edgenodes | Nej | 
> | profiles | Ja | 
> | profiler/slut punkter | Ja | 
> | profiler/slut punkter/customdomains | Nej | 
> | profiler/slut punkter/ursprung | Nej | 
> | validateProbe | Nej | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certificateOrders | Ja | 
> | certificateOrders/certifikat | Nej | 
> | validateCertificateRegistrationInformation | Nej | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | trådlösa | Nej | 
> | Domän namn | Nej | 
> | Domän namn/funktioner | Nej | 
> | Domän namn/internalLoadBalancers | Nej | 
> | Domän namn/serviceCertificates | Nej | 
> | Domän namn/platser | Nej | 
> | Domän namn/platser/roller | Nej | 
> | moveSubscriptionResources | Nej | 
> | operatingSystemFamilies | Nej | 
> | operatingSystems | Nej | 
> | quotas | Nej | 
> | resourceTypes | Nej | 
> | validateSubscriptionMoveAvailability | Nej | 
> | virtualMachines | Nej | 
> | virtualMachines/diagnosticSettings | Nej | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nej | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | trådlösa | Nej | 
> | expressRouteCrossConnections | Nej | 
> | expressRouteCrossConnections/peering | Nej | 
> | gatewaySupportedDevices | Nej | 
> | networkSecurityGroups | Nej | 
> | quotas | Nej | 
> | reservedIps | Nej | 
> | virtualNetworks | Nej | 
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nej | 
> | virtualNetworks/virtualNetworkPeerings | Nej | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | trådlösa | Nej | 
> | Disk | Nej | 
> | images | Nej | 
> | osImages | Nej | 
> | osPlatformImages | Nej | 
> | publicImages | Nej | 
> | quotas | Nej | 
> | storageAccounts | Nej | 
> | storageAccounts/tjänster | Nej | 
> | storageAccounts/services/diagnosticSettings | Nej | 
> | storageAccounts/vmImages | Nej | 
> | vmImages | Nej | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | RateCard | Nej | 
> | UsageAggregates | Nej | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilitySets | Ja | 
> | Disk | Ja | 
> | images | Ja | 
> | restorePointCollections | Ja | 
> | restorePointCollections/restorePoints | Nej | 
> | sharedVMImages | Ja | 
> | sharedVMImages/versioner | Ja | 
> | Ögonblicks bilder | Ja | 
> | virtualMachines | Ja | 
> | virtualMachines/diagnosticSettings | Nej | 
> | virtualMachines/tillägg | Ja | 
> | virtualMachineScaleSets | Ja | 
> | virtualMachineScaleSets/extensions | Nej | 
> | virtualMachineScaleSets/networkInterfaces | Nej | 
> | virtualMachineScaleSets/publicIPAddresses | Nej | 
> | virtualMachineScaleSets/virtualMachines | Nej | 
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nej | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | AggregatedCost | Nej | 
> | Saldon | Nej | 
> | Budgetar | Nej | 
> | Utgifts | Nej | 
> | CostTags | Nej | 
> | krediter | Nej | 
> | evenemang | Nej | 
> | Prognoser | Nej | 
> | samtliga | Nej | 
> | Marknads platser | Nej | 
> | Pricesheets | Nej | 
> | läkemedle | Nej | 
> | ReservationDetails | Nej | 
> | ReservationRecommendations | Nej | 
> | ReservationSummaries | Nej | 
> | ReservationTransactions | Nej | 
> | Tags | Nej | 
> | Villkor | Nej | 
> | UsageDetails | Nej | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerGroups | Ja | 
> | serviceAssociationLinks | Nej | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | register | Ja | 
> | register/versioner | Nej | 
> | register/versioner/Avbryt | Nej | 
> | register/build/getLogLink | Nej | 
> | register/buildTasks | Ja | 
> | register/buildTasks/steg | Nej | 
> | registries/eventGridFilters | Nej | 
> | registries/getBuildSourceUploadUrl | Nej | 
> | register/GetCredentials | Nej | 
> | register/importImage | Nej | 
> | register/queueBuild | Nej | 
> | register/regenerateCredential | Nej | 
> | register/regenerateCredentials | Nej | 
> | register/replikeringar | Ja | 
> | register/körningar | Nej | 
> | register/körningar/Avbryt | Nej | 
> | register/scheduleRun | Nej | 
> | register/uppgifter | Ja | 
> | registries/updatePolicies | Nej | 
> | register/Webhooks | Ja | 
> | register/Webhooks/getCallbackConfig | Nej | 
> | register/Webhooks/ping | Nej | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerServices | Ja | 
> | managedClusters | Ja | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja | 
> | updateCommunicationPreference | Nej | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aviseringar | Nej | 
> | billingAccounts | Nej | 
> | Anslutningar | Ja | 
> | enheten | Nej | 
> | Dimensioner | Nej | 
> | enrollmentAccounts | Nej | 
> | Söka i data | Nej | 
> | Registrera dig | Nej | 
> | Reportconfigs | Nej | 
> | Rapporter | Nej | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | NAV | Ja | 
> | hubbar/authorizationPolicies | Nej | 
> | hubbar/anslutningar | Nej | 
> | hubbar/anslutningar/mappningar | Nej | 
> | hubbar/interaktioner | Nej | 
> | hubbar/KPI | Nej | 
> | hubbar/länkar | Nej | 
> | hubbar/profiler | Nej | 
> | hubbar/roleAssignments | Nej | 
> | hubbar/roller | Nej | 
> | hubbar/suggestTypeSchema | Nej | 
> | hubbar/vyer | Nej | 
> | hubbar/widgetTypes | Nej | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utskrifts | Ja | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Ja | 

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Arbets ytor | Ja | 
> | workspaces/virtualNetworkPeerings | Nej | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kataloger | Ja | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | connectionManagers | Ja | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataFactories | Ja | 
> | dataFactories/diagnosticSettings | Nej | 
> | dataFactorySchema | Nej | 
> | fabriker | Ja | 
> | fabriker/integrationRuntimes | Nej | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja | 
> | accounts/dataLakeStoreAccounts | Nej | 
> | konton/storageAccounts | Nej | 
> | konton/storageAccounts/behållare | Nej | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja | 
> | accounts/eventGridFilters | Nej | 
> | konton/firewallRules | Nej | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja | 
> | tjänster/projekt | Ja | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Brygghuvudservrar | Ja | 
> | servrar/recoverableServers | Nej | 
> | servrar/virtualNetworkRules | Nej | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Brygghuvudservrar | Ja | 
> | servrar/recoverableServers | Nej | 
> | servrar/virtualNetworkRules | Nej | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Brygghuvudservrar | Ja | 
> | servrar/rådgivare | Nej | 
> | servrar/queryTexts | Nej | 
> | servrar/recoverableServers | Nej | 
> | servers/topQueryStatistics | Nej | 
> | servrar/virtualNetworkRules | Nej | 
> | servrar/waitStatistics | Nej | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | IotHubs | Ja | 
> | IotHubs/eventGridFilters | Nej | 
> | ProvisioningServices | Ja | 
> | användningar | Nej | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Domänkontrollanter | Ja | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Laboration | Ja | 
> | labb/serviceRunners | Ja | 
> | labb/virtualMachines | Ja | 
> | scheman | Ja | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | databaseAccountNames | Nej | 
> | databaseAccounts | Ja | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Ja | 
> | domäner/domainOwnershipIdentifiers | Nej | 
> | generateSsoRequest | Nej | 
> | topLevelDomains | Nej | 
> | validateDomainRegistrationInformation | Nej | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | lcsprojects | Nej | 
> | lcsprojects/clouddeployments | Nej | 
> | lcsprojects/kopplingar | Nej | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Ja | 
> | domäner/ämnen | Nej | 
> | eventSubscriptions | Nej | 
> | extensionTopics | Nej | 
> | Avsnitt | Ja | 
> | topicTypes | Nej | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja | 
> | namn områden | Ja | 
> | namnrymder/authorizationrules | Nej | 
> | namnrymder/disasterrecoveryconfigs | Nej | 
> | namnrymder/eventhubs | Nej | 
> | namnrymder/eventhubs/authorizationrules | Nej | 
> | namnrymder/eventhubs/consumergroups | Nej | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | Nej | 
> | Providers | Nej | 

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certifiering | Nej | 
> | galleryitems | Nej | 
> | generateartifactaccessuri | Nej | 
> | områden | Nej | 
> | område/områden | Nej | 
> | område/områden/områden | Nej | 
> | område/områden/områden/galleryitems | Nej | 
> | områdets/områden/galleryitems | Nej | 
> | områden/galleryitems | Nej | 
> | Registrera dig | Nej | 
> | Resurser | Nej | 
> | retrieveresourcesbyid | Nej | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | guestConfigurationAssignments | Nej | 
> | IntelliPoint | Nej | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hanaInstances | Ja | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja | 
> | kluster/program | Nej | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utskrifts | Ja | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labelGroups | Nej | 
> | labelGroups/etiketter | Nej | 
> | labelGroups/etiketter/villkor | Nej | 
> | labelGroups/labels/subLabels | Nej | 
> | labelGroups/labels/subLabels/conditions | Nej | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | actiongroups | Ja | 
> | activityLogAlerts | Ja | 
> | alertrules | Ja | 
> | automatedExportSettings | Nej | 
> | autoscalesettings | Ja | 
> | planen | Nej | 
> | calculatebaseline | Nej | 
> | Delarna | Ja | 
> | komponenter/händelser | Nej | 
> | komponenter/pricingPlans | Nej | 
> | komponenter/fråga | Nej | 
> | diagnosticSettings | Nej | 
> | diagnosticSettingsCategories | Nej | 
> | eventCategories | Nej | 
> | eventtypes | Nej | 
> | extendedDiagnosticSettings | Nej | 
> | logDefinitions | Nej | 
> | logprofiles | Nej | 
> | logs | Nej | 
> | metricAlerts | Ja |
> | migrateToNewPricingModel | Nej | 
> | Mina arbets böcker | Nej | 
> | frågor | Nej | 
> | rollbackToLegacyPricingModel | Nej | 
> | scheduledqueryrules | Ja | 
> | vmInsightsOnboardingStatuses | Nej | 
> | webbtester | Ja | 
> | Arbets böcker | Ja | 

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Nej | 
> | diagnosticSettingsCategories | Nej | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | IoTApps | Ja | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Graph | Ja | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedVaults | Nej | 
> | valv | Ja | 
> | valv/accessPolicies | Nej | 
> | valv/hemligheter | Nej | 

## <a name="microsoftkusto"></a>Microsoft. Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja | 
> | kluster/databaser | Nej | 
> | kluster/databaser/dataconnections | Nej | 
> | kluster/databaser/eventhubconnections | Nej | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labaccounts | Ja | 
> | användare | Nej | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | logs | Nej | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | integrationAccounts | Ja | 
> | arbetsflöden | Ja | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | commitmentPlans | Ja | 
> | WebServices | Ja | 
> | Arbetsytor | Ja | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja | 
> | konton/arbets ytor | Ja | 
> | konton/arbets ytor/projekt | Ja | 
> | teamAccounts | Ja | 
> | teamAccounts/arbets ytor | Ja | 
> | teamAccounts/arbets ytor/projekt | Ja | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Arbets ytor | Ja | 
> | arbets ytor/beräkningar | Nej | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Identiteter | Nej | 
> | userAssignedIdentities | Ja | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | getEntities | Nej | 
> | managementGroups | Nej | 
> | Resurser | Nej | 
> | startTenantBackfill | Nej | 
> | tenantBackfillStatus | Nej | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja | 
> | accounts/eventGridFilters | Nej | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | budgivning | Nej | 
> | offerTypes | Nej | 
> | offerTypes/utgivare | Nej | 
> | offerTypes/utgivare/erbjudanden | Nej | 
> | offerTypes/utgivare/erbjudanden/planer | Nej | 
> | offerTypes/utgivare/erbjudanden/planer/avtal | Nej | 
> | offerTypes/utgivare/erbjudanden/planer/konfigurationer | Nej | 
> | offerTypes/utgivare/erbjudanden/planer/configs/importImage | Nej | 
> | privategalleryitems | Nej | 
> | läkemedle | Nej | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicDevServices | Ja | 
> | updateCommunicationPreference | Nej | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | villkor | Nej | 
> | offertypes | Nej | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Media Services | Ja | 
> | Media Services/accountFilters | Nej | 
> | Media Services/till gångar | Nej | 
> | mediaservices/assets/assetFilters | Nej | 
> | mediaservices/contentKeyPolicies | Nej | 
> | mediaservices/eventGridFilters | Nej | 
> | Media Services/liveEventOperations | Nej | 
> | Media Services/liveEvents | Ja | 
> | Media Services/liveEvents/liveOutputs | Nej | 
> | mediaservices/liveOutputOperations | Nej | 
> | Media Services/streamingEndpointOperations | Nej | 
> | Media Services/strömnings slut punkter | Ja | 
> | Media Services/streamingLocators | Nej | 
> | Media Services/streamingPolicies | Nej | 
> | Media Services/transformeringar | Nej | 
> | Media Services/transformeringar/jobb | Nej | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | samarbetsprojekt | Ja | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationGateways | Ja | 
> | applicationSecurityGroups | Ja | 
> | azureFirewallFqdnTags | Nej | 
> | azureFirewalls | Ja | 
> | bgpServiceCommunities | Nej | 
> | anslutning | Ja | 
> | ddosCustomPolicies | Ja | 
> | ddosProtectionPlans | Ja | 
> | dnsOperationStatuses | Nej | 
> | dnszones | Ja | 
> | dnszones/A | Nej | 
> | dnszones/AAAA | Nej | 
> | dnszones/alla | Nej | 
> | dnszones/CAA | Nej | 
> | dnszones/CNAME | Nej | 
> | dnszones/MX | Nej | 
> | dnszones/NS | Nej | 
> | dnszones/PTR | Nej | 
> | dnszones/Recordset | Nej | 
> | dnszones/SOA | Nej | 
> | dnszones/SRV | Nej | 
> | dnszones/TXT | Nej | 
> | expressRouteCircuits | Ja | 
> | expressRouteServiceProviders | Nej | 
> | frontdoors | Ja | 
> | frontdoorWebApplicationFirewallPolicies | Ja | 
> | getDnsResourceReference | Nej | 
> | interfaceEndpoints | Ja | 
> | internalNotify | Nej | 
> | Belastningsutjämnare | Ja | 
> | localNetworkGateways | Ja | 
> | natGateways | Ja | 
> | networkIntentPolicies | Ja | 
> | networkInterfaces | Ja | 
> | networkProfiles | Ja | 
> | networkSecurityGroups | Ja | 
> | networkWatchers | Ja | 
> | networkWatchers/connectionMonitors | Ja | 
> | networkWatchers/linser | Ja | 
> | networkWatchers/pingMeshes | Ja | 
> | privateLinkServices | Ja | 
> | publicIPAddresses | Ja | 
> | publicIPPrefixes | Ja | 
> | routeFilters | Ja | 
> | routeTables | Ja | 
> | serviceEndpointPolicies | Ja | 
> | trafficManagerGeographicHierarchies | Nej | 
> | trafficmanagerprofiles | Ja | 
> | trafficmanagerprofiles/termiska kartor | Nej | 
> | virtualHubs | Ja | 
> | virtualNetworkGateways | Ja | 
> | virtualNetworks | Ja | 
> | virtualNetworkTaps | Ja | 
> | virtualWans | Ja | 
> | vpnGateways | Ja | 
> | vpnSites | Ja | 
> | webApplicationFirewallPolicies | Ja | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja | 
> | namnrymder/notificationHubs | Ja | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | enheter | Nej | 
> | linkTargets | Nej | 
> | storageInsightConfigs | Nej | 
> | Arbets ytor | Ja | 
> | arbets ytor/data källor | Nej | 
> | arbets ytor/linkedServices | Nej | 
> | arbets ytor/fråga | Nej | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managementassociations | Nej | 
> | managementconfigurations | Ja | 
> | lösningar | Ja | 
> | Vyer | Ja | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | policyEvents | Nej | 
> | policyStates | Nej | 
> | policyTrackedResources | Nej | 
> | reparationer | Nej | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konsoler | Nej | 
> | Instrument paneler | Ja | 
> | userSettings | Nej | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | workspaceCollections | Ja | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kapaciteter | Ja | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | backupProtectedItems | Nej | 
> | valv | Ja | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja | 
> | namnrymder/authorizationrules | Nej | 
> | namnrymder/hybridconnections | Nej | 
> | namnrymder/hybridconnections/authorizationrules | Nej | 
> | namnrymder/wcfrelays | Nej | 
> | namnrymder/wcfrelays/authorizationrules | Nej | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Resurser | Nej | 
> | subscriptionsStatus | Nej | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilityStatuses | Nej | 
> | childAvailabilityStatuses | Nej | 
> | childResources | Nej | 
> | evenemang | Nej | 
> | impactedResources | Nej | 
> | ansökningar | Nej | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | distributioner | Nej | 
> | distributioner/åtgärder | Nej | 
> | Länkar | Nej | 
> | notifyResourceJobs | Nej | 
> | Providers | Nej | 
> | resourceGroups | Nej | 
> | Resurser | Nej | 
> | Prenumerationer | Nej | 
> | prenumerationer/providers | Nej | 
> | prenumerationer/resourceGroups | Nej | 
> | prenumerationer/ResourceGroups/resurser | Nej | 
> | prenumerationer/resurser | Nej | 
> | prenumerationer/tagNames | Nej | 
> | prenumerationer/tagNames/tagValues | Nej | 
> | klienter | Nej | 

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja | 
> | saasresources | Nej | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | planta | Ja | 
> | förfrågningsåtgärder | Ja | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nej | 
> | searchServices | Ja | 

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | advancedThreatProtectionSettings | Nej | 
> | aviseringar | Nej | 
> | allowedConnections | Nej | 
> | redskap | Nej | 
> | applicationWhitelistings | Nej | 
> | AutoProvisioningSettings | Nej | 
> | Godkännanden | Nej | 
> | dataCollectionAgents | Nej | 
> | discoveredSecuritySolutions | Nej | 
> | externalSecuritySolutions | Nej | 
> | InformationProtectionPolicies | Nej | 
> | jitNetworkAccessPolicies | Nej | 
> | Granska | Nej | 
> | övervakning/program mot skadlig kod | Nej | 
> | övervakning/bas linje | Nej | 
> | övervakning/korrigering | Nej | 
> | rikt | Nej | 
> | prissättningar | Nej | 
> | securityContacts | Nej | 
> | securitySolutions | Nej | 
> | securitySolutionsReferenceData | Nej | 
> | securityStatus | Nej | 
> | securityStatus/slut punkter | Nej | 
> | securityStatus/undernät | Nej | 
> | securityStatus/virtualMachines | Nej | 
> | securityStatuses | Nej | 
> | securityStatusesSummaries | Nej | 
> | settings | Nej | 
> | uppgifter | Nej | 
> | topologier | Nej | 
> | workspaceSettings | Nej | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Nej | 
> | diagnosticSettingsCategories | Nej | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja | 
> | namnrymder/authorizationrules | Nej | 
> | namnrymder/disasterrecoveryconfigs | Nej | 
> | namnrymder/eventgridfilters | Nej | 
> | namnrymder/köer | Nej | 
> | namnrymder/köer/authorizationrules | Nej | 
> | namn områden/ämnen | Nej | 
> | namnrymder/ämnen/authorizationrules | Nej | 
> | namnrymder/ämnen/prenumerationer | Nej | 
> | namn områden/ämnen/prenumerationer/regler | Nej | 
> | premiumMessagingRegions | Nej | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja | 
> | kluster/program | Nej | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja | 
> | gatewayer | Ja | 
> | nätet | Ja | 
> | secrets | Ja | 
> | volumes | Ja | 

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SignalR | Ja | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applianceDefinitions | Ja | 
> | redskap | Ja | 
> | applicationDefinitions | Ja | 
> | program | Ja | 
> | jitRequests | Ja | 

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedInstances | Ja |
> | managedInstances/databaser | Ja |
> | managedInstances/databases/backupShortTermRetentionPolicies | Nej |
> | managedInstances/databaser/scheman/tabeller/kolumner/sensitivityLabels | Nej |
> | managedInstances/databaser/vulnerabilityAssessments | Nej |
> | managedInstances/databaser/vulnerabilityAssessments/regler/bas linjer | Nej |
> | managedInstances/encryptionProtector | Nej |
> | managedInstances/nycklar | Nej |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nej |
> | managedInstances/vulnerabilityAssessments | Nej |
> | Brygghuvudservrar | Ja | 
> | servrar/administratörer | Nej | 
> | servrar/communicationLinks | Nej | 
> | servrar/databaser | Ja | 
> | servers/encryptionProtector | Nej | 
> | servrar/firewallRules | Nej | 
> | servrar/nycklar | Nej | 
> | servrar/restorableDroppedDatabases | Nej | 
> | servrar/serviceobjectives | Nej | 
> | servrar/tdeCertificates | Nej | 


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ja | 
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Nej | 
> | SqlVirtualMachines | Ja | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageAccounts | Ja | 
> | storageAccounts/blobServices | Nej | 
> | storageAccounts/fileServices | Nej | 
> | storageAccounts/queueServices | Nej | 
> | storageAccounts/tjänster | Nej | 
> | storageAccounts/tableServices | Nej | 
> | användningar | Nej | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja | 
> | storageSyncServices/registeredServers | Nej | 
> | storageSyncServices/syncGroups | Nej | 
> | storageSyncServices/syncGroups/cloudEndpoints | Nej | 
> | storageSyncServices/syncGroups/serverEndpoints | Nej | 
> | storageSyncServices/arbets flöden | Nej | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hantera | Ja | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | streamingjobs | Ja | 
> | streamingjobs/diagnosticSettings | Nej | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CreateSubscription | Nej | 
> | SubscriptionDefinitions | Nej | 
> | SubscriptionOperations | Nej | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | supporttickets | Nej | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | Ja | 
> | Resurser | Ja | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Ja | 
> | miljöer/accessPolicies | Nej | 
> | miljöer/eventsources | Ja | 
> | miljöer/referenceDataSets | Ja | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konto | Ja | 
> | konto/tillägg | Ja | 
> | konto/projekt | Ja | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | apiManagementAccounts | Nej | 
> | apiManagementAccounts/apiAcls | Nej | 
> | apiManagementAccounts/apis | Nej | 
> | apiManagementAccounts/apis/apiAcls | Nej | 
> | apiManagementAccounts/apis/connectionAcls | Nej | 
> | apiManagementAccounts/API/anslutningar | Nej | 
> | apiManagementAccounts/apis/connections/connectionAcls | Nej | 
> | apiManagementAccounts/apis/localizedDefinitions | Nej | 
> | apiManagementAccounts/connectionAcls | Nej | 
> | apiManagementAccounts/anslutningar | Nej | 
> | billingMeters | Nej | 
> | Intyg | Ja | 
> | connectionGateways | Ja | 
> | anslutning | Ja | 
> | customApis | Ja | 
> | deletedSites | Nej | 
> | functions | Nej | 
> | hostingEnvironments | Ja | 
> | hostingEnvironments/multiRolePools | Nej | 
> | hostingEnvironments/multiRolePools/instances | Nej | 
> | hostingEnvironments/workerPools | Nej | 
> | hostingEnvironments/workerPools/instances | Nej | 
> | publishingUsers | Nej | 
> | Rekommenderade | Nej | 
> | resourceHealthMetadata | Nej | 
> | körningar | Nej | 
> | serverFarms | Ja | 
> | Server grupper/arbetare | Nej | 
> | stationer | Ja | 
> | sites/domainOwnershipIdentifiers | Nej | 
> | platser/hostNameBindings | Nej | 
> | platser/instanser | Nej | 
> | platser/instanser/tillägg | Nej | 
> | platser/premieraddons | Ja | 
> | platser/rekommendationer | Nej | 
> | platser/resourceHealthMetadata | Nej | 
> | platser/platser | Ja | 
> | sites/slots/hostNameBindings | Nej | 
> | platser/platser/instanser | Nej | 
> | platser/platser/instanser/tillägg | Nej | 
> | sourceControls | Nej | 
> | kontrollerar | Nej | 
> | verifyHostingEnvironmentVnet | Nej | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Nej | 
> | diagnosticSettingsCategories | Nej | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DeviceServices | Ja | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Delarna | Nej | 
> | componentsSummary | Nej | 
> | monitorInstances | Nej | 
> | monitorInstancesSummary | Nej | 
> | Övervakare | Nej | 
> | notificationSettings | Nej | 

## <a name="next-steps"></a>Nästa steg

Hämta samma data som en fil med kommaavgränsade värden genom att ladda ned [Complete-mode-deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).