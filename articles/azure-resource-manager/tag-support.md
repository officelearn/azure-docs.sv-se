---
title: Azure Resource Manager-tagg-stöd för resurser
description: Visar vilka Azure-resurstyper stöder taggar. Innehåller information om alla Azure-tjänster.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 8b7e6d234984e84f5b238d657281dd8b1b9ec423
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056881"
---
# <a name="tag-support-for-azure-resources"></a>Tagg-stöd för Azure-resurser
Den här artikeln beskriver om en resurstyp stöder [taggar](resource-group-using-tags.md). Kolumnen **stöder taggar** anger om resurstypen har en egenskap för taggen. Kolumnen **tagg i rapporten cost** anger om den resurstypen skickar taggen till i rapporten cost.

För att få samma data som en fil med kommaavgränsade värden kan hämta [taggen support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| DomainServices | Ja | Ja |
| DomainServices/oucontainer | Nej | Nej |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nej |  Nej |
| diagnosticSettingsCategories | Nej |  Nej |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| supportProviders | Nej |  Nej |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| aadsupportcases | Nej |  Nej |
| addsservices | Nej |  Nej |
| Agenter | Nej |  Nej |
| anonymousapiusers | Nej |  Nej |
| konfiguration | Nej |  Nej |
| logs | Nej |  Nej |
| rapporter | Nej |  Nej |
| services | Nej |  Nej |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Konfigurationer | Nej |  Nej |
| generateRecommendations | Nej |  Nej |
| Rekommendationer | Nej |  Nej |
| suppressions | Nej |  Nej |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| actionRules | Nej |  Nej |
| aviseringar | Nej |  Nej |
| alertsList | Nej |  Nej |
| alertsSummary | Nej |  Nej |
| alertsSummaryList | Nej |  Nej |
| smartDetectorAlertRules | Nej |  Nej |
| smartDetectorRuntimeEnvironments | Nej |  Nej |
| smartGroups | Nej |  Nej |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Servrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| reportFeedback | Nej |  Nej |
| tjänst | Ja | Ja |
| validateServiceName | Nej |  Nej |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| attestationProviders | Nej |  Nej |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| classicAdministrators | Nej |  Nej |
| denyAssignments | Nej |  Nej |
| elevateAccess | Nej |  Nej |
| Lås | Nej |  Nej |
| behörigheter | Nej |  Nej |
| policyAssignments | Nej |  Nej |
| policyDefinitions | Nej |  Nej |
| policySetDefinitions | Nej |  Nej |
| providerOperations | Nej |  Nej |
| Rolltilldelningar | Nej |  Nej |
| roleDefinitions | Nej |  Nej |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| automationAccounts | Ja | Ja |
| automationAccounts/configurations | Ja | Ja |
| automationAccounts/jobb | Nej |  Nej |
| automationAccounts/runbooks | Ja | Ja |
| automationAccounts/softwareUpdateConfigurations | Nej | Nej |
| automationAccounts/webhooks | Nej |  Nej |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Miljöer | Nej |  Nej |
| miljöer/konton | Nej |  Nej |
| miljöer-konton-namnområden | Nej |  Nej |
| miljöer/konton/namnområden/konfigurationer | Nej |  Nej |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| b2cDirectories | Ja | Nej |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| registreringar | Ja | Ja |
| registreringar/customerSubscriptions | Nej |  Nej |
| registreringar/produkter | Nej |  Nej |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| batchAccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| billingAccounts | Nej |  Nej |
| billingAccounts/billingProfiles | Nej |  Nej |
| billingAccounts/billingProfiles/billingSubscriptions | Nej |  Nej |
| billingAccounts/billingProfiles/fakturor | Nej |  Nej |
| billingAccounts/billingProfiles/fakturor/prisdokument | Nej |  Nej |
| billingAccounts/billingProfiles/operationStatus | Nej |  Nej |
| billingAccounts/billingProfiles/paymentMethods | Nej |  Nej |
| billingAccounts-billingProfiles-principer | Nej |  Nej |
| billingAccounts/billingProfiles/prisdokument | Nej |  Nej |
| billingAccounts-billingProfiles-produkter | Nej |  Nej |
| billingAccounts/billingProfiles/transactions | Nej |  Nej |
| billingAccounts/billingSubscriptions | Nej |  Nej |
| billingAccounts/avdelningar | Nej |  Nej |
| billingAccounts/eligibleOffers | Nej |  Nej |
| billingAccounts/enrollmentAccounts | Nej |  Nej |
| billingAccounts/fakturor | Nej |  Nej |
| billingAccounts/invoiceSections | Nej |  Nej |
| billingAccounts/invoiceSections/billingSubscriptions | Nej |  Nej |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Nej |  Nej |
| billingAccounts/invoiceSections/importRequests | Nej |  Nej |
| billingAccounts/invoiceSections/initiateImportRequest | Nej |  Nej |
| billingAccounts/invoiceSections/initiateTransfer | Nej |  Nej |
| billingAccounts/invoiceSections/operationStatus | Nej |  Nej |
| billingAccounts-invoiceSections-produkter | Nej |  Nej |
| billingAccounts/invoiceSections/disköverföring | Nej |  Nej |
| billingAccounts/produkter | Nej |  Nej |
| billingAccounts/projekt | Nej |  Nej |
| billingAccounts/projekt/billingSubscriptions | Nej |  Nej |
| billingAccounts/projects/importRequests | Nej |  Nej |
| billingAccounts/projekt/initiateImportRequest | Nej |  Nej |
| billingAccounts/projects/operationStatus | Nej |  Nej |
| billingAccounts-projekt-produkter | Nej |  Nej |
| billingAccounts/transaktioner | Nej |  Nej |
| billingPeriods | Nej |  Nej |
| BillingPermissions | Nej |  Nej |
| billingProperty | Nej |  Nej |
| BillingRoleAssignments | Nej |  Nej |
| BillingRoleDefinitions | Nej |  Nej |
| CreateBillingRoleAssignment | Nej |  Nej |
| avdelningar | Nej |  Nej |
| enrollmentAccounts | Nej |  Nej |
| importRequests | Nej |  Nej |
| importRequests/acceptImportRequest | Nej |  Nej |
| importRequests/declineImportRequest | Nej |  Nej |
| Fakturor | Nej |  Nej |
| Överföringar | Nej |  Nej |
| Disköverföringar/acceptTransfer | Nej |  Nej |
| transfers/declineTransfer | Nej |  Nej |
| transfers/operationStatus | Nej |  Nej |
| usagePlans | Nej |  Nej |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| mapApis | Ja | Ja |
| updateCommunicationPreference | Nej |  Nej |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| BizTalk | Ja | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Nej |  Nej |
| blueprintAssignments/assignmentOperations | Nej |  Nej |
| blueprintAssignments/operations | Nej |  Nej |
| skisser | Nej |  Nej |
| blueprints/artifacts | Nej |  Nej |
| blueprints/versions | Nej |  Nej |
| blueprints/versions/artifacts | Nej |  Nej |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| botServices | Ja | Ja |
| botServices/channels | Nej |  Nej |
| botServices/anslutningar | Nej |  Nej |

## <a name="microsoftcache"></a>Microsoft.Cache
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Redis | Ja | Ja |
| RedisConfigDefinition | Nej |  Nej |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| appliedReservations | Nej |  Nej |
| calculatePrice | Nej |  Nej |
| kataloger | Nej |  Nej |
| commercialReservationOrders | Nej |  Nej |
| reservationOrders | Nej |  Nej |
| reservationOrders/calculateRefund | Nej |  Nej |
| reservationOrders/merge | Nej |  Nej |
| reservationOrders/reservations | Nej |  Nej |
| reservationOrders/reservations/revisions | Nej |  Nej |
| reservationOrders/return | Nej |  Nej |
| reservationOrders/split | Nej |  Nej |
| reservationOrders/swap | Nej |  Nej |
| reservationer | Nej |  Nej |
| Resurser | Nej |  Nej |
| validateReservationOrder | Nej |  Nej |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| edgenodes | Nej |  Nej |
| Profiler | Ja | Ja |
| profiler-slutpunkter | Ja | Ja |
| profiler/slutpunkter/customdomains | Nej |  Nej |
| profiler/slutpunkter/ursprung | Nej |  Nej |
| validateProbe | Nej |  Nej |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| certificateOrders | Ja | Ja |
| certificateOrders/certifikat | Nej |  Nej |
| validateCertificateRegistrationInformation | Nej |  Nej |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Funktioner | Nej |  Nej |
| domainNames | Nej |  Nej |
| domainNames/capabilities | Nej |  Nej |
| domainNames/internalLoadBalancers | Nej |  Nej |
| domainNames/serviceCertificates | Nej |  Nej |
| domainNames/platser | Nej |  Nej |
| domainNames-fack-roller | Nej |  Nej |
| moveSubscriptionResources | Nej |  Nej |
| operatingSystemFamilies | Nej |  Nej |
| operatingSystems | Nej |  Nej |
| quotas | Nej |  Nej |
| Resurstyper | Nej |  Nej |
| validateSubscriptionMoveAvailability | Nej |  Nej |
| virtualMachines | Nej |  Nej |
| virtualMachines/diagnosticSettings | Nej |  Nej |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Nej |  Nej |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Funktioner | Nej |  Nej |
| expressRouteCrossConnections | Nej |  Nej |
| expressRouteCrossConnections/peerings | Nej |  Nej |
| gatewaySupportedDevices | Nej |  Nej |
| networkSecurityGroups | Nej |  Nej |
| quotas | Nej |  Nej |
| reservedIps | Nej |  Nej |
| virtualNetworks | Nej |  Nej |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nej |  Nej |
| virtualNetworks/virtualNetworkPeerings | Nej |  Nej |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Funktioner | Nej |  Nej |
| Diskar | Nej |  Nej |
| images | Nej |  Nej |
| osImages | Nej |  Nej |
| osPlatformImages | Nej |  Nej |
| publicImages | Nej |  Nej |
| quotas | Nej |  Nej |
| storageAccounts | Nej |  Nej |
| storageAccounts/services | Nej |  Nej |
| storageAccounts/services/diagnosticSettings | Nej |  Nej |
| storageAccounts/vmImages | Nej |  Nej |
| vmImages | Nej |  Nej |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| RateCard | Nej |  Nej |
| UsageAggregates | Nej |  Nej |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| availabilitySets | Ja | Ja |
| Diskar | Ja | Ja |
| images | Ja | Ja |
| restorePointCollections | Ja | Ja |
| restorePointCollections/restorePoints | Nej |  Nej |
| sharedVMImages | Ja | Ja |
| sharedVMImages/versions | Ja | Ja |
| Ögonblicksbilder | Ja | Ja |
| virtualMachines | Ja | Ja |
| virtualMachines/diagnosticSettings | Nej |  Nej |
| virtualMachines/extensions | Ja | Ja |
| virtualMachineScaleSets | Ja | Ja |
| virtualMachineScaleSets/extensions | Nej |  Nej |
| virtualMachineScaleSets/networkInterfaces | Nej |  Nej |
| virtualMachineScaleSets/publicIPAddresses | Nej |  Nej |
| virtualMachineScaleSets/virtualMachines | Nej |  Nej |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nej |  Nej |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| AggregatedCost | Nej |  Nej |
| Saldon | Nej |  Nej |
| Budgetar | Nej |  Nej |
| Avgifter | Nej |  Nej |
| CostTags | Nej |  Nej |
| Krediter | Nej |  Nej |
| evenemang | Nej |  Nej |
| Prognoser | Nej |  Nej |
| många | Nej |  Nej |
| Marknadsplatser | Nej |  Nej |
| Pricesheets | Nej |  Nej |
| Produkter | Nej |  Nej |
| ReservationDetails | Nej |  Nej |
| ReservationRecommendations | Nej |  Nej |
| ReservationSummaries | Nej |  Nej |
| ReservationTransactions | Nej |  Nej |
| Tags | Nej |  Nej |
| Villkor | Nej |  Nej |
| UsageDetails | Nej |  Nej |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| containerGroups | Ja | Ja |
| serviceAssociationLinks | Nej |  Nej |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| register | Ja | Ja |
| register/versioner | Nej |  Nej |
| registries/builds/cancel | Nej |  Nej |
| registries/builds/getLogLink | Nej |  Nej |
| registries/buildTasks | Ja | Ja |
| register/buildTasks/steg | Nej |  Nej |
| registries/eventGridFilters | Nej |  Nej |
| registries/getBuildSourceUploadUrl | Nej |  Nej |
| registries/GetCredentials | Nej |  Nej |
| registries/importImage | Nej |  Nej |
| registries/queueBuild | Nej |  Nej |
| register/regenerateCredential | Nej |  Nej |
| register/regenerateCredentials | Nej |  Nej |
| register/replikeringar | Ja | Ja |
| register/körningar | Nej |  Nej |
| register/körningar/Avbryt | Nej |  Nej |
| registries/scheduleRun | Nej |  Nej |
| register/uppgifter | Ja | Ja |
| registries/updatePolicies | Nej |  Nej |
| register/webhooks | Ja | Ja |
| register/webhooks/getCallbackConfig | Nej |  Nej |
| register-webhooks-ping | Nej |  Nej |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| containerServices | Ja | Ja |
| managedClusters | Ja | Ja |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| program | Ja | Ja |
| updateCommunicationPreference | Nej |  Nej |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Aviseringar | Nej |  Nej |
| billingAccounts | Nej |  Nej |
| Anslutningar | Ja | Ja |
| avdelningar | Nej |  Nej |
| Dimensioner | Nej |  Nej |
| EnrollmentAccounts | Nej |  Nej |
| Fråga | Nej |  Nej |
| Registrera dig | Nej |  Nej |
| Reportconfigs | Nej |  Nej |
| Rapporter | Nej |  Nej |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| hubbar | Ja | Ja |
| hubs/authorizationPolicies | Nej |  Nej |
| hubs/anslutningar | Nej |  Nej |
| NAV-kopplingar-mappningar | Nej |  Nej |
| hubs/interaktioner | Nej |  Nej |
| hubs/kpi | Nej |  Nej |
| hubs/länkar | Nej |  Nej |
| NAV-profiler | Nej |  Nej |
| hubs/rolltilldelningar | Nej |  Nej |
| hubs/roller | Nej |  Nej |
| hubs/suggestTypeSchema | Nej |  Nej |
| hubbar och vyer | Nej |  Nej |
| hubs/widgetTypes | Nej |  Nej |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Jobb | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Arbetsytor | Ja | Nej |
| workspaces/virtualNetworkPeerings | Nej |  Nej |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| kataloger | Ja | Ja |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| connectionManagers | Ja | Ja |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| dataFactories | Ja | Nej |
| dataFactories/diagnosticSettings | Nej |  Nej |
| dataFactorySchema | Nej |  Nej |
| fabriker | Ja | Nej |
| fabriker/integrationRuntimes | Nej |  Nej |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |
| accounts/dataLakeStoreAccounts | Nej |  Nej |
| accounts/storageAccounts | Nej |  Nej |
| konton/storageAccounts/behållare | Nej |  Nej |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |
| accounts/eventGridFilters | Nej |  Nej |
| konton/firewallRules | Nej |  Nej |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| services | Ja | Ja |
| Services-projekt | Ja | Ja |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Servrar | Ja | Ja |
| servrar/recoverableServers | Nej |  Nej |
| servers/virtualNetworkRules | Nej |  Nej |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Servrar | Ja | Ja |
| servrar/recoverableServers | Nej |  Nej |
| servers/virtualNetworkRules | Nej |  Nej |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Servrar | Ja | Ja |
| servers/advisors | Nej |  Nej |
| servrar/queryTexts | Nej |  Nej |
| servrar/recoverableServers | Nej |  Nej |
| servers/topQueryStatistics | Nej |  Nej |
| servers/virtualNetworkRules | Nej |  Nej |
| servers/waitStatistics | Nej |  Nej |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| IotHubs | Ja | Ja |
| IotHubs/eventGridFilters | Nej |  Nej |
| ProvisioningServices | Ja | Ja |
| Användningar | Nej |  Nej |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Domänkontrollanter | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Labs | Ja | Ja |
| labs/serviceRunners | Ja | Ja |
| labs/virtualMachines | Ja | Ja |
| Scheman | Ja | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Nej |  Nej |
| databaseAccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| domäner | Ja | Ja |
| domains/domainOwnershipIdentifiers | Nej |  Nej |
| generateSsoRequest | Nej |  Nej |
| topLevelDomains | Nej |  Nej |
| validateDomainRegistrationInformation | Nej |  Nej |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| lcsprojects | Nej |  Nej |
| lcsprojects/clouddeployments | Nej |  Nej |
| lcsprojects/anslutningar | Nej |  Nej |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| domäner | Ja | Nej |
| domäner/ämnen | Nej |  Nej |
| eventSubscriptions | Nej |  Nej |
| extensionTopics | Nej |  Nej |
| ämnen | Ja | Nej |
| topicTypes | Nej |  Nej |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Kluster | Ja | Nej |
| namnrymder | Ja | Nej |
| namnområden/authorizationrules | Nej |  Nej |
| namespaces/disasterrecoveryconfigs | Nej |  Nej |
| namnområden/eventhubs | Nej |  Nej |
| namnområden/eventhubs/authorizationrules | Nej |  Nej |
| namnområden/eventhubs/consumergroups | Nej |  Nej |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| funktioner | Nej |  Nej |
| Providers | Nej |  Nej |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| registrera | Nej |  Nej |
| galleryitems | Nej |  Nej |
| generateartifactaccessuri | Nej |  Nej |
| myareas | Nej |  Nej |
| myareas/områden | Nej |  Nej |
| myareas/områden/områden | Nej |  Nej |
| myareas/områden/områden/galleryitems | Nej |  Nej |
| myareas/områden/galleryitems | Nej |  Nej |
| myareas/galleryitems | Nej |  Nej |
| Registrera dig | Nej |  Nej |
| Resurser | Nej |  Nej |
| retrieveresourcesbyid | Nej |  Nej |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Nej |  Nej |
| Programvara | Nej |  Nej |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| hanaInstances | Ja |  Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Kluster | Ja | Ja |
| kluster/program | Nej |  Nej |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Jobb | Ja | Ja |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| labelGroups | Nej |  Nej |
| labelGroups/labels | Nej |  Nej |
| labelGroups/labels/conditions | Nej |  Nej |
| labelGroups/labels/subLabels | Nej |  Nej |
| labelGroups/labels/subLabels/conditions | Nej |  Nej |

## <a name="microsoftinsights"></a>microsoft.insights
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| actiongroups | Ja | Ja |
| activityLogAlerts | Ja | Ja |
| alertrules | Ja | Ja |
| automatedExportSettings | Nej |  Nej |
| autoscalesettings | Ja | Ja |
| Baslinje | Nej |  Nej |
| calculatebaseline | Nej |  Nej |
| Komponenter | Ja | Ja |
| komponenter/händelser | Nej |  Nej |
| components/pricingPlans | Nej |  Nej |
| komponenter/fråga | Nej |  Nej |
| diagnosticSettings | Nej |  Nej |
| diagnosticSettingsCategories | Nej |  Nej |
| eventCategories | Nej |  Nej |
| eventtypes | Nej |  Nej |
| extendedDiagnosticSettings | Nej |  Nej |
| logDefinitions | Nej |  Nej |
| logprofiles | Nej |  Nej |
| logs | Nej |  Nej |
| metricAlerts | Ja | Ja |
| migrateToNewPricingModel | Nej |  Nej |
| myWorkbooks | Nej |  Nej |
| frågor | Nej |  Nej |
| rollbackToLegacyPricingModel | Nej |  Nej |
| scheduledqueryrules | Ja | Ja |
| vmInsightsOnboardingStatuses | Nej |  Nej |
| webbtester | Ja | Ja |
| arbetsböcker | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nej |  Nej |
| diagnosticSettingsCategories | Nej |  Nej |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| IoTApps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| deletedVaults | Nej |  Nej |
| Valv | Ja | Ja |
| valv/accessPolicies | Nej |  Nej |
| Valv/hemligheter | Nej |  Nej |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Kluster | Ja | Ja |
| kluster/databaser | Nej |  Nej |
| kluster/databaser/dataconnections | Nej |  Nej |
| kluster/databaser/eventhubconnections | Nej |  Nej |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| labaccounts | Ja | Ja |
| användare | Nej |  Nej |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| logs | Nej |  Nej |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| integrationAccounts | Ja | Ja |
| Arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| commitmentPlans | Ja | Ja |
| webServices | Ja | Ja |
| Arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |
| konton/arbetsytor | Ja | Ja |
| konton-arbetsytor-projekt | Ja | Ja |
| teamAccounts | Ja | Ja |
| teamAccounts/arbetsytor | Ja | Ja |
| teamAccounts-arbetsytor-projekt | Ja | Ja |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Arbetsytor | Ja | Ja |
| arbetsytor/beräkningar | Nej |  Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Identiteter | Nej |  Nej |
| userAssignedIdentities | Ja | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| getEntities | Nej |  Nej |
| managementGroups | Nej |  Nej |
| Resurser | Nej |  Nej |
| startTenantBackfill | Nej |  Nej |
| tenantBackfillStatus | Nej |  Nej |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |
| accounts/eventGridFilters | Nej |  Nej |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Erbjudanden | Nej |  Nej |
| offerTypes | Nej |  Nej |
| offerTypes/utgivare | Nej |  Nej |
| offerTypes-utgivare-erbjudanden | Nej |  Nej |
| offerTypes/utgivare/erbjudanden/planer | Nej |  Nej |
| offerTypes/utgivare/erbjudanden/planer/avtal | Nej |  Nej |
| offerTypes/utgivare/erbjudanden/planer/konfigurationer | Nej |  Nej |
| offerTypes/publishers/offers/plans/configs/importImage | Nej |  Nej |
| privategalleryitems | Nej |  Nej |
| Produkter | Nej |  Nej |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| classicDevServices | Ja | Ja |
| updateCommunicationPreference | Nej |  Nej |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Avtal | Nej |  Nej |
| offertypes | Nej |  Nej |

## <a name="microsoftmedia"></a>Microsoft.Media
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| mediaservices | Ja | Ja |
| mediaservices/accountFilters | Nej |  Nej |
| mediaservices/tillgångar | Nej |  Nej |
| mediaservices/assets/assetFilters | Nej |  Nej |
| mediaservices/contentKeyPolicies | Nej |  Nej |
| mediaservices/eventGridFilters | Nej |  Nej |
| mediaservices/liveEventOperations | Nej |  Nej |
| mediaservices/liveEvents | Ja | Ja |
| mediaservices/liveEvents/liveOutputs | Nej |  Nej |
| mediaservices/liveOutputOperations | Nej |  Nej |
| mediaservices/streamingEndpointOperations | Nej |  Nej |
| mediaservices/Strömningsslutpunkter | Ja | Ja |
| mediaservices/streamingLocators | Nej |  Nej |
| mediaservices/streamingPolicies | Nej |  Nej |
| mediaservices/omvandlingar | Nej |  Nej |
| mediaservices-transformeringar-jobb | Nej |  Nej |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| projekt | Ja | Ja |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| applicationGateways | Ja | Nej |
| applicationSecurityGroups | Ja | Ja |
| azureFirewallFqdnTags | Nej |  Nej |
| azureFirewalls | Ja | Nej |
| bgpServiceCommunities | Nej |  Nej |
| Anslutningar | Ja | Ja |
| ddosCustomPolicies | Ja | Ja |
| ddosProtectionPlans | Ja | Ja |
| dnsOperationStatuses | Nej |  Nej |
| dnszones | Ja | Ja |
| dnszones/A | Nej |  Nej |
| dnszones/AAAA | Nej |  Nej |
| dnszones/alla | Nej |  Nej |
| dnszones/CAA | Nej |  Nej |
| dnszones/CNAME | Nej |  Nej |
| dnszones/MX | Nej |  Nej |
| dnszones/NS | Nej |  Nej |
| dnszones/PTR | Nej |  Nej |
| dnszones/postuppsättningar | Nej |  Nej |
| dnszones/SOA | Nej |  Nej |
| dnszones/SRV | Nej |  Nej |
| dnszones/TXT | Nej |  Nej |
| expressRouteCircuits | Ja  | Nej |
| expressRouteServiceProviders | Nej |  Nej |
| frontdoors | Ja | Ja |
| frontdoorWebApplicationFirewallPolicies | Ja | Ja |
| getDnsResourceReference | Nej |  Nej |
| interfaceEndpoints | Ja | Ja |
| internalNotify | Nej |  Nej |
| belastningsutjämnare | Ja | Nej |
| localNetworkGateways | Ja | Ja |
| natGateways | Ja | Ja |
| networkIntentPolicies | Ja | Ja |
| networkInterfaces | Ja | Ja |
| networkProfiles | Ja | Ja |
| networkSecurityGroups | Ja | Ja |
| networkWatchers | Ja | Nej |
| networkWatchers/connectionMonitors | Ja | Nej |
| networkWatchers/linser | Ja | Nej |
| networkWatchers/pingMeshes | Ja | Nej |
| privateLinkServices | Ja | Ja |
| publicIPAddresses | Ja | Ja |
| publicIPPrefixes | Ja | Ja |
| routeFilters | Ja | Ja |
| routeTables | Ja | Ja |
| serviceEndpointPolicies | Ja | Ja |
| trafficManagerGeographicHierarchies | Nej |  Nej |
| trafficmanagerprofiles | Ja | Ja |
| trafficmanagerprofiles/termiska kartor | Nej |  Nej |
| virtualHubs | Ja | Ja |
| virtualNetworkGateways | Ja | Nej |
| virtualNetworks | Ja | Ja |
| virtuella nätverk/undernät | Nej |  Nej |
| virtualNetworkTaps | Ja | Ja |
| virtualWans | Ja | Ja |
| vpnGateways | Ja | Nej |
| vpnSites | Ja | Ja |
| webApplicationFirewallPolicies | Ja | Ja |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| namnrymder | Ja | Nej |
| namespaces/notificationHubs | Ja | Nej |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| enheter | Nej |  Nej |
| linkTargets | Nej |  Nej |
| storageInsightConfigs | Nej |  Nej |
| Arbetsytor | Ja | Ja |
| arbetsytor/datakällor | Nej |  Nej |
| arbetsytor/linkedServices | Nej |  Nej |
| arbetsytor/fråga | Nej |  Nej |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| managementassociations | Nej |  Nej |
| managementconfigurations | Ja | Ja |
| lösningar | Ja | Ja |
| Vyer | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| policyEvents | Nej |  Nej |
| policyStates | Nej |  Nej |
| policyTrackedResources | Nej |  Nej |
| reparationer | Nej |  Nej |

## <a name="microsoftportal"></a>Microsoft.Portal
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konsoler | Nej |  Nej |
| Instrumentpaneler | Ja | Ja |
| Användarinställningarna | Nej |  Nej |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Kapaciteter | Ja | Ja |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Nej |  Nej |
| Valv | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| namnrymder | Ja | Ja |
| namnområden/authorizationrules | Nej |  Nej |
| namnområden/hybridconnections | Nej |  Nej |
| namnområden/hybridconnections/authorizationrules | Nej |  Nej |
| namnområden/wcfrelays | Nej |  Nej |
| namnområden/wcfrelays/authorizationrules | Nej |  Nej |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Resurser | Nej |  Nej |
| subscriptionsStatus | Nej |  Nej |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Nej |  Nej |
| childAvailabilityStatuses | Nej |  Nej |
| childResources | Nej |  Nej |
| evenemang | Nej |  Nej |
| impactedResources | Nej |  Nej |
| Meddelanden | Nej |  Nej |

## <a name="microsoftresources"></a>Microsoft.Resources
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Distributioner | Nej |  Nej |
| Distributioner/operations | Nej |  Nej |
| Länkar | Nej |  Nej |
| notifyResourceJobs | Nej |  Nej |
| Providers | Nej |  Nej |
| resourceGroups | Nej |  Nej |
| Resurser | Nej |  Nej |
| Prenumerationer | Nej |  Nej |
| Prenumerationer/providers | Nej |  Nej |
| subscriptions/resourceGroups | Nej |  Nej |
| prenumerationer/resourcegroups/resurser | Nej |  Nej |
| Prenumerationer/resurser | Nej |  Nej |
| prenumerationer/tagnames | Nej |  Nej |
| subscriptions/tagNames/tagValues | Nej |  Nej |
| Klienter | Nej |  Nej |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| program | Ja | Ja |
| saasresources | Nej |  Nej |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| flöden | Ja | Ja |
| förfrågningsåtgärder | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Nej |  Nej |
| searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Nej |  Nej |
| aviseringar | Nej |  Nej |
| allowedConnections | Nej |  Nej |
| installationer | Nej |  Nej |
| applicationWhitelistings | Nej |  Nej |
| AutoProvisioningSettings | Nej |  Nej |
| Efterlevnadskraven | Nej |  Nej |
| dataCollectionAgents | Nej |  Nej |
| discoveredSecuritySolutions | Nej |  Nej |
| externalSecuritySolutions | Nej |  Nej |
| InformationProtectionPolicies | Nej |  Nej |
| jitNetworkAccessPolicies | Nej |  Nej |
| Övervakning | Nej |  Nej |
| övervaka/program mot skadlig kod | Nej |  Nej |
| övervaka/baslinje | Nej |  Nej |
| övervaka/patch | Nej |  Nej |
| Principer | Nej |  Nej |
| prissättningar | Nej |  Nej |
| securityContacts | Nej |  Nej |
| securitySolutions | Nej |  Nej |
| securitySolutionsReferenceData | Nej |  Nej |
| securityStatus | Nej |  Nej |
| securityStatus/slutpunkter | Nej |  Nej |
| securityStatus/undernät | Nej |  Nej |
| securityStatus/virtualMachines | Nej |  Nej |
| securityStatuses | Nej |  Nej |
| securityStatusesSummaries | Nej |  Nej |
| settings | Nej |  Nej |
| uppgifter | Nej |  Nej |
| topologies | Nej |  Nej |
| workspaceSettings | Nej |  Nej |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nej |  Nej |
| diagnosticSettingsCategories | Nej |  Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| namnrymder | Ja | Nej |
| namnområden/authorizationrules | Nej |  Nej |
| namespaces/disasterrecoveryconfigs | Nej |  Nej |
| namespaces/eventgridfilters | Nej |  Nej |
| namnområden/köer | Nej |  Nej |
| namnområden/köer/authorizationrules | Nej |  Nej |
| namnområden/ämnen | Nej |  Nej |
| namnområden/information/authorizationrules | Nej |  Nej |
| namnområden/ämnen/prenumerationer | Nej |  Nej |
| namnområden/ämnen/prenumerationer/regler | Nej |  Nej |
| premiumMessagingRegions | Nej |  Nej |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Kluster | Ja | Ja |
| kluster/program | Nej |  Nej |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| program | Ja | Ja |
| Gateways | Ja | Ja |
| Nätverk | Ja | Ja |
| secrets | Ja | Ja |
| volumes | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| SignalR | Ja | Ja |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Ja | Ja |
| installationer | Ja | Ja |
| applicationDefinitions | Ja | Ja |
| program | Ja | Ja |
| jitRequests | Ja | Ja |

## <a name="microsoftsql"></a>Microsoft.SQL
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| managedInstances | Ja | Ja |
| managedInstances/databaser | Ja (Se kommentaren nedan) | Ja |
| managedInstances/databases/backupShortTermRetentionPolicies | Nej | Nej |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nej | Nej |
| managedInstances/databaser/vulnerabilityAssessments | Nej | Nej |
| managedInstances/databaser/vulnerabilityAssessments/regler/baslinjer | Nej | Nej |
| managedInstances/encryptionProtector | Nej | Nej |
| managedInstances/keys | Nej | Nej |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nej | Nej |
| managedInstances/vulnerabilityAssessments | Nej | Nej |
| Servrar | Ja | Ja |
| servrar/administratörer | Nej |  Nej |
| servers/communicationLinks | Nej |  Nej |
| servrar/databaser | Ja (Se kommentaren nedan) | Ja |
| servers/encryptionProtector | Nej |  Nej |
| servrar/firewallRules | Nej |  Nej |
| servrar/nycklar | Nej |  Nej |
| servrar/restorableDroppedDatabases | Nej |  Nej |
| servrar/serviceobjectives | Nej |  Nej |
| servers/tdeCertificates | Nej |  Nej |

> [!NOTE]
> Huvudservern databasen har inte stöd för taggar, men andra databaser, bland annat Azure SQL Data Warehouse-databaser, stöd för taggar. Azure SQL Data Warehouse-databaser måste vara i aktiva (inte pausad) tillstånd.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Ja | Ja |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nej |  Nej |
| SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| storageAccounts | Ja | Ja |
| storageAccounts/blobServices | Nej |  Nej |
| storageAccounts/fileServices | Nej |  Nej |
| storageAccounts/queueServices | Nej |  Nej |
| storageAccounts/services | Nej |  Nej |
| storageAccounts/tableServices | Nej |  Nej |
| Användningar | Nej |  Nej |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| storageSyncServices | Ja | Ja |
| storageSyncServices/registeredServers | Nej |  Nej |
| storageSyncServices/syncGroups | Nej |  Nej |
| storageSyncServices/syncGroups/cloudEndpoints | Nej |  Nej |
| storageSyncServices/syncGroups/serverEndpoints | Nej |  Nej |
| storageSyncServices/workflows | Nej |  Nej |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| chefer | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| streamingjobs | Ja (Se kommentaren nedan) | Ja |
| streamingjobs/diagnosticSettings | Nej |  Nej |

> [!NOTE]
> Du kan inte lägga till en tagg när streamingjobs körs. Stoppa resurs för att lägga till en tagg.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| CreateSubscription | Nej |  Nej |
| SubscriptionDefinitions | Nej |  Nej |
| SubscriptionOperations | Nej |  Nej |

## <a name="microsoftsupport"></a>microsoft.support
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| supporttickets | Nej |  Nej |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| providerRegistrations | Ja | Ja |
| Resurser | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Miljöer | Ja | Nej |
| miljöer/accessPolicies | Nej |  Nej |
| miljöer/eventsources | Ja | Nej |
| miljöer/referenceDataSets | Ja | Nej |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| konto | Ja | Ja |
| tillägget och Account | Ja | Ja |
| kontot/projektet | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Nej |  Nej |
| apiManagementAccounts/apiAcls | Nej |  Nej |
| apiManagementAccounts/apis | Nej |  Nej |
| apiManagementAccounts/apis/apiAcls | Nej |  Nej |
| apiManagementAccounts/apis/connectionAcls | Nej |  Nej |
| apiManagementAccounts/apis/connections | Nej |  Nej |
| apiManagementAccounts/apis/connections/connectionAcls | Nej |  Nej |
| apiManagementAccounts/apis/localizedDefinitions | Nej |  Nej |
| apiManagementAccounts/connectionAcls | Nej |  Nej |
| apiManagementAccounts/connections | Nej |  Nej |
| billingMeters | Nej |  Nej |
| Certifikat | Ja | Ja |
| connectionGateways | Ja | Ja |
| Anslutningar | Ja | Ja |
| customApis | Ja | Ja |
| deletedSites | Nej |  Nej |
| functions | Nej |  Nej |
| hostingEnvironments | Ja | Ja |
| hostingEnvironments/multiRolePools | Nej |  Nej |
| hostingEnvironments-multiRolePools-instanser | Nej |  Nej |
| hostingEnvironments/workerPools | Nej |  Nej |
| hostingEnvironments-workerPools-instanser | Nej |  Nej |
| publishingUsers | Nej |  Nej |
| Rekommendationer | Nej |  Nej |
| resourceHealthMetadata | Nej |  Nej |
| körningar | Nej |  Nej |
| serverFarms | Ja | Ja |
| serverFarms/workers | Nej |  Nej |
| webbplatser | Ja | Ja |
| sites/domainOwnershipIdentifiers | Nej |  Nej |
| sites/hostNameBindings | Nej |  Nej |
| platser/instanser | Nej |  Nej |
| platser-instanser-tillägg | Nej |  Nej |
| sites/premieraddons | Ja | Ja |
| platser/rekommendationer | Nej |  Nej |
| sites/resourceHealthMetadata | Nej |  Nej |
| platser/platser | Ja | Ja |
| sites/slots/hostNameBindings | Nej |  Nej |
| platser-fack-instanser | Nej |  Nej |
| platser/platser/instanser/tillägg | Nej |  Nej |
| sourceControls | Nej |  Nej |
| Verifiera | Nej |  Nej |
| verifyHostingEnvironmentVnet | Nej |  Nej |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nej |  Nej |
| diagnosticSettingsCategories | Nej |  Nej |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| DeviceServices | Ja | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Resurstyp | Har stöd för taggar | Tagga i rapporten cost |
| ------------- | ----------- | ----------- |
| Komponenter | Nej |  Nej |
| componentsSummary | Nej |  Nej |
| monitorInstances | Nej |  Nej |
| monitorInstancesSummary | Nej |  Nej |
| Övervakare | Nej |  Nej |
| notificationSettings | Nej |  Nej |

## <a name="next-steps"></a>Nästa steg
Om du vill lära dig mer om att lägga till taggar i resurser, se [använda taggar för att organisera Azure-resurser](resource-group-using-tags.md).
