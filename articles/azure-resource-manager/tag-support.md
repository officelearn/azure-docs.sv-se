---
title: Stöd för Azure Resource Manager-tagg för resurser
description: Visar vilka typer av Azure-resurs typer som stöder taggar. Innehåller information om alla Azure-tjänster.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 72bb11cd064c90c2bbe1e9e6452dcbf07fe37817
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304875"
---
# <a name="tag-support-for-azure-resources"></a>Tagga stöd för Azure-resurser
I den här artikeln beskrivs om en resurs typ stöder [taggar](resource-group-using-tags.md). Den kolumn som har etiketten **stöder Taggar** anger om resurs typen har en egenskap för taggen. Kolumnen med etiketten **tagg i Cost** visar om den resurs typen skickar taggen till kostnads rapporten.

Hämta samma data som en fil med kommaavgränsade värden genom att ladda ned [tag-support. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| DomainServices | Ja | Ja |
| DomainServices/oucontainer | Nej | Nej |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nej |  Nej |
| diagnosticSettingsCategories | Nej |  Nej |

## <a name="microsoftaddons"></a>Microsoft. addons
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| supportProviders | Nej |  Nej |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| aadsupportcases | Nej |  Nej |
| addsservices | Nej |  Nej |
| Aktörer | Nej |  Nej |
| anonymousapiusers | Nej |  Nej |
| konfiguration | Nej |  Nej |
| logs | Nej |  Nej |
| rapporter | Nej |  Nej |
| services | Nej |  Nej |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konfigurationer | Nej |  Nej |
| generateRecommendations | Nej |  Nej |
| Rekommenderade | Nej |  Nej |
| utelämningar | Nej |  Nej |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
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
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Brygghuvudservrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| reportFeedback | Nej |  Nej |
| tjänst | Ja | Ja |
| validateServiceName | Nej |  Nej |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| attestationProviders | Nej |  Nej |

## <a name="microsoftauthorization"></a>Microsoft. Authorization
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| classicAdministrators | Nej |  Nej |
| denyAssignments | Nej |  Nej |
| elevateAccess | Nej |  Nej |
| hålls | Nej |  Nej |
| behörigheter | Nej |  Nej |
| policyAssignments | Nej |  Nej |
| policyDefinitions | Nej |  Nej |
| policySetDefinitions | Nej |  Nej |
| providerOperations | Nej |  Nej |
| roleAssignments | Nej |  Nej |
| roleDefinitions | Nej |  Nej |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| AutomationAccounts | Ja | Ja |
| automationAccounts/konfigurationer | Ja | Ja |
| automationAccounts/jobb | Nej |  Nej |
| automationAccounts/Runbooks | Ja | Ja |
| automationAccounts/softwareUpdateConfigurations | Nej | Nej |
| automationAccounts/webhooks | Nej |  Nej |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| utrymmen | Nej |  Nej |
| miljöer/konton | Nej |  Nej |
| miljöer/konton/namn områden | Nej |  Nej |
| miljöer/konton/namn rymder/konfigurationer | Nej |  Nej |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| b2cDirectories | Ja | Nej |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| registreringar | Ja | Ja |
| registreringar/customerSubscriptions | Nej |  Nej |
| registreringar/produkter | Nej |  Nej |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| batchAccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| billingAccounts | Nej |  Nej |
| billingAccounts/billingProfiles | Nej |  Nej |
| billingAccounts/billingProfiles/billingSubscriptions | Nej |  Nej |
| billingAccounts/billingProfiles/fakturor | Nej |  Nej |
| billingAccounts/billingProfiles/fakturor/pris dokument | Nej |  Nej |
| billingAccounts/billingProfiles/operationStatus | Nej |  Nej |
| billingAccounts/billingProfiles/paymentMethods | Nej |  Nej |
| billingAccounts/billingProfiles/policys | Nej |  Nej |
| billingAccounts/billingProfiles/pris dokument | Nej |  Nej |
| billingAccounts/billingProfiles/Products | Nej |  Nej |
| billingAccounts/billingProfiles/transaktioner | Nej |  Nej |
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
| billingAccounts/invoiceSections/Products | Nej |  Nej |
| billingAccounts/invoiceSections/överföringar | Nej |  Nej |
| billingAccounts/produkter | Nej |  Nej |
| billingAccounts/projekt | Nej |  Nej |
| billingAccounts/projekt/billingSubscriptions | Nej |  Nej |
| billingAccounts/projekt/importRequests | Nej |  Nej |
| billingAccounts/projekt/initiateImportRequest | Nej |  Nej |
| billingAccounts/projekt/operationStatus | Nej |  Nej |
| billingAccounts/projekt/produkter | Nej |  Nej |
| billingAccounts/transaktioner | Nej |  Nej |
| billingPeriods | Nej |  Nej |
| BillingPermissions | Nej |  Nej |
| billingProperty | Nej |  Nej |
| BillingRoleAssignments | Nej |  Nej |
| BillingRoleDefinitions | Nej |  Nej |
| CreateBillingRoleAssignment | Nej |  Nej |
| enheten | Nej |  Nej |
| enrollmentAccounts | Nej |  Nej |
| importRequests | Nej |  Nej |
| importRequests/acceptImportRequest | Nej |  Nej |
| importRequests/declineImportRequest | Nej |  Nej |
| fakturor | Nej |  Nej |
| överlåtelse | Nej |  Nej |
| överföringar/acceptTransfer | Nej |  Nej |
| överföringar/declineTransfer | Nej |  Nej |
| överföringar/operationStatus | Nej |  Nej |
| usagePlans | Nej |  Nej |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| mapApis | Ja | Ja |
| updateCommunicationPreference | Nej |  Nej |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| BizTalk | Ja | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Nej |  Nej |
| blueprintAssignments/assignmentOperations | Nej |  Nej |
| blueprintAssignments/åtgärder | Nej |  Nej |
| modeller | Nej |  Nej |
| skisser/artefakter | Nej |  Nej |
| skisser/versioner | Nej |  Nej |
| blueprints/versions/artifacts | Nej |  Nej |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| botServices | Ja | Ja |
| botServices/channels | Nej |  Nej |
| botServices/anslutningar | Nej |  Nej |

## <a name="microsoftcache"></a>Microsoft.Cache
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Redis | Ja | Ja |
| RedisConfigDefinition | Nej |  Nej |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| appliedReservations | Nej |  Nej |
| calculatePrice | Nej |  Nej |
| kataloger | Nej |  Nej |
| commercialReservationOrders | Nej |  Nej |
| reservationOrders | Nej |  Nej |
| reservationOrders/calculateRefund | Nej |  Nej |
| reservationOrders/slå samman | Nej |  Nej |
| reservationOrders/reservationer | Nej |  Nej |
| reservationOrders/reservationer/revisioner | Nej |  Nej |
| reservationOrders/retur | Nej |  Nej |
| reservationOrders/split | Nej |  Nej |
| reservationOrders/växling | Nej |  Nej |
| reservera | Nej |  Nej |
| Resurser | Nej |  Nej |
| validateReservationOrder | Nej |  Nej |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| edgenodes | Nej |  Nej |
| profiles | Ja | Ja |
| profiler/slut punkter | Ja | Ja |
| profiler/slut punkter/customdomains | Nej |  Nej |
| profiler/slut punkter/ursprung | Nej |  Nej |
| validateProbe | Nej |  Nej |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| certificateOrders | Ja | Ja |
| certificateOrders/certifikat | Nej |  Nej |
| validateCertificateRegistrationInformation | Nej |  Nej |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| trådlösa | Nej |  Nej |
| Domän namn | Nej |  Nej |
| Domän namn/funktioner | Nej |  Nej |
| Domän namn/internalLoadBalancers | Nej |  Nej |
| Domän namn/serviceCertificates | Nej |  Nej |
| Domän namn/platser | Nej |  Nej |
| Domän namn/platser/roller | Nej |  Nej |
| moveSubscriptionResources | Nej |  Nej |
| operatingSystemFamilies | Nej |  Nej |
| operatingSystems | Nej |  Nej |
| quotas | Nej |  Nej |
| resourceTypes | Nej |  Nej |
| validateSubscriptionMoveAvailability | Nej |  Nej |
| virtualMachines | Nej |  Nej |
| virtualMachines/diagnosticSettings | Nej |  Nej |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Nej |  Nej |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| trådlösa | Nej |  Nej |
| expressRouteCrossConnections | Nej |  Nej |
| expressRouteCrossConnections/peering | Nej |  Nej |
| gatewaySupportedDevices | Nej |  Nej |
| networkSecurityGroups | Nej |  Nej |
| quotas | Nej |  Nej |
| reservedIps | Nej |  Nej |
| virtualNetworks | Nej |  Nej |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nej |  Nej |
| virtualNetworks/virtualNetworkPeerings | Nej |  Nej |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| trådlösa | Nej |  Nej |
| Disk | Nej |  Nej |
| images | Nej |  Nej |
| osImages | Nej |  Nej |
| osPlatformImages | Nej |  Nej |
| publicImages | Nej |  Nej |
| quotas | Nej |  Nej |
| storageAccounts | Nej |  Nej |
| storageAccounts/tjänster | Nej |  Nej |
| storageAccounts/services/diagnosticSettings | Nej |  Nej |
| storageAccounts/vmImages | Nej |  Nej |
| vmImages | Nej |  Nej |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| RateCard | Nej |  Nej |
| UsageAggregates | Nej |  Nej |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| availabilitySets | Ja | Ja |
| Disk | Ja | Ja |
| images | Ja | Ja |
| restorePointCollections | Ja | Ja |
| restorePointCollections/restorePoints | Nej |  Nej |
| sharedVMImages | Ja | Ja |
| sharedVMImages/versioner | Ja | Ja |
| Ögonblicks bilder | Ja | Ja |
| virtualMachines | Ja | Ja |
| virtualMachines/diagnosticSettings | Nej |  Nej |
| virtualMachines/tillägg | Ja | Ja |
| virtualMachineScaleSets | Ja | Ja |
| virtualMachineScaleSets/extensions | Nej |  Nej |
| virtualMachineScaleSets/networkInterfaces | Nej |  Nej |
| virtualMachineScaleSets/publicIPAddresses | Nej |  Nej |
| virtualMachineScaleSets/virtualMachines | Nej |  Nej |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nej |  Nej |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| AggregatedCost | Nej |  Nej |
| Saldon | Nej |  Nej |
| Budgetar | Nej |  Nej |
| Utgifts | Nej |  Nej |
| CostTags | Nej |  Nej |
| krediter | Nej |  Nej |
| evenemang | Nej |  Nej |
| Prognoser | Nej |  Nej |
| samtliga | Nej |  Nej |
| Marknads platser | Nej |  Nej |
| Pricesheets | Nej |  Nej |
| läkemedle | Nej |  Nej |
| ReservationDetails | Nej |  Nej |
| ReservationRecommendations | Nej |  Nej |
| ReservationSummaries | Nej |  Nej |
| ReservationTransactions | Nej |  Nej |
| Tags | Nej |  Nej |
| Villkor | Nej |  Nej |
| UsageDetails | Nej |  Nej |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| containerGroups | Ja | Ja |
| serviceAssociationLinks | Nej |  Nej |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| register | Ja | Ja |
| register/versioner | Nej |  Nej |
| register/versioner/Avbryt | Nej |  Nej |
| register/build/getLogLink | Nej |  Nej |
| register/buildTasks | Ja | Ja |
| register/buildTasks/steg | Nej |  Nej |
| registries/eventGridFilters | Nej |  Nej |
| registries/getBuildSourceUploadUrl | Nej |  Nej |
| register/GetCredentials | Nej |  Nej |
| register/importImage | Nej |  Nej |
| register/queueBuild | Nej |  Nej |
| register/regenerateCredential | Nej |  Nej |
| register/regenerateCredentials | Nej |  Nej |
| register/replikeringar | Ja | Ja |
| register/körningar | Nej |  Nej |
| register/körningar/Avbryt | Nej |  Nej |
| register/scheduleRun | Nej |  Nej |
| register/uppgifter | Ja | Ja |
| registries/updatePolicies | Nej |  Nej |
| register/Webhooks | Ja | Ja |
| register/Webhooks/getCallbackConfig | Nej |  Nej |
| register/Webhooks/ping | Nej |  Nej |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| containerServices | Ja | Ja |
| managedClusters | Ja | Ja |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| program | Ja | Ja |
| updateCommunicationPreference | Nej |  Nej |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Aviseringar | Nej |  Nej |
| billingAccounts | Nej |  Nej |
| Anslutningar | Ja | Ja |
| enheten | Nej |  Nej |
| Dimensioner | Nej |  Nej |
| enrollmentAccounts | Nej |  Nej |
| Söka i data | Nej |  Nej |
| Registrera dig | Nej |  Nej |
| Reportconfigs | Nej |  Nej |
| Rapporter | Nej |  Nej |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| NAV | Ja | Ja |
| hubbar/authorizationPolicies | Nej |  Nej |
| hubbar/anslutningar | Nej |  Nej |
| hubbar/anslutningar/mappningar | Nej |  Nej |
| hubbar/interaktioner | Nej |  Nej |
| hubbar/KPI | Nej |  Nej |
| hubbar/länkar | Nej |  Nej |
| hubbar/profiler | Nej |  Nej |
| hubbar/roleAssignments | Nej |  Nej |
| hubbar/roller | Nej |  Nej |
| hubbar/suggestTypeSchema | Nej |  Nej |
| hubbar/vyer | Nej |  Nej |
| hubbar/widgetTypes | Nej |  Nej |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| utskrifts | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Arbets ytor | Ja | Nej |
| workspaces/virtualNetworkPeerings | Nej |  Nej |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| kataloger | Ja | Ja |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| connectionManagers | Ja | Ja |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| dataFactories | Ja | Nej |
| dataFactories/diagnosticSettings | Nej |  Nej |
| dataFactorySchema | Nej |  Nej |
| fabriker | Ja | Nej |
| fabriker/integrationRuntimes | Nej |  Nej |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |
| accounts/dataLakeStoreAccounts | Nej |  Nej |
| konton/storageAccounts | Nej |  Nej |
| konton/storageAccounts/behållare | Nej |  Nej |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |
| accounts/eventGridFilters | Nej |  Nej |
| konton/firewallRules | Nej |  Nej |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| services | Ja | Ja |
| tjänster/projekt | Ja | Ja |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Brygghuvudservrar | Ja | Ja |
| servrar/recoverableServers | Nej |  Nej |
| servrar/virtualNetworkRules | Nej |  Nej |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Brygghuvudservrar | Ja | Ja |
| servrar/recoverableServers | Nej |  Nej |
| servrar/virtualNetworkRules | Nej |  Nej |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Brygghuvudservrar | Ja | Ja |
| servrar/rådgivare | Nej |  Nej |
| servrar/queryTexts | Nej |  Nej |
| servrar/recoverableServers | Nej |  Nej |
| servers/topQueryStatistics | Nej |  Nej |
| servrar/virtualNetworkRules | Nej |  Nej |
| servrar/waitStatistics | Nej |  Nej |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| IotHubs | Ja | Ja |
| IotHubs/eventGridFilters | Nej |  Nej |
| ProvisioningServices | Ja | Ja |
| användningar | Nej |  Nej |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Domänkontrollanter | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Laboration | Ja | Ja |
| labb/serviceRunners | Ja | Ja |
| labb/virtualMachines | Ja | Ja |
| scheman | Ja | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Nej |  Nej |
| databaseAccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| domäner | Ja | Ja |
| domäner/domainOwnershipIdentifiers | Nej |  Nej |
| generateSsoRequest | Nej |  Nej |
| topLevelDomains | Nej |  Nej |
| validateDomainRegistrationInformation | Nej |  Nej |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| lcsprojects | Nej |  Nej |
| lcsprojects/clouddeployments | Nej |  Nej |
| lcsprojects/kopplingar | Nej |  Nej |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| domäner | Ja | Nej |
| domäner/ämnen | Nej |  Nej |
| eventSubscriptions | Nej |  Nej |
| extensionTopics | Nej |  Nej |
| Avsnitt | Ja | Nej |
| topicTypes | Nej |  Nej |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| kluster | Ja | Nej |
| namn områden | Ja | Nej |
| namnrymder/authorizationrules | Nej |  Nej |
| namnrymder/disasterrecoveryconfigs | Nej |  Nej |
| namnrymder/eventhubs | Nej |  Nej |
| namnrymder/eventhubs/authorizationrules | Nej |  Nej |
| namnrymder/eventhubs/consumergroups | Nej |  Nej |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| funktioner | Nej |  Nej |
| Providers | Nej |  Nej |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| certifiering | Nej |  Nej |
| galleryitems | Nej |  Nej |
| generateartifactaccessuri | Nej |  Nej |
| områden | Nej |  Nej |
| område/områden | Nej |  Nej |
| område/områden/områden | Nej |  Nej |
| område/områden/områden/galleryitems | Nej |  Nej |
| områdets/områden/galleryitems | Nej |  Nej |
| områden/galleryitems | Nej |  Nej |
| Registrera dig | Nej |  Nej |
| Resurser | Nej |  Nej |
| retrieveresourcesbyid | Nej |  Nej |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Nej |  Nej |
| IntelliPoint | Nej |  Nej |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| hanaInstances | Ja |  Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| kluster | Ja | Ja |
| kluster/program | Nej |  Nej |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| utskrifts | Ja | Ja |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| labelGroups | Nej |  Nej |
| labelGroups/etiketter | Nej |  Nej |
| labelGroups/etiketter/villkor | Nej |  Nej |
| labelGroups/labels/subLabels | Nej |  Nej |
| labelGroups/labels/subLabels/conditions | Nej |  Nej |

## <a name="microsoftinsights"></a>microsoft.insights
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| actiongroups | Ja | Ja |
| activityLogAlerts | Ja | Ja |
| alertrules | Ja | Ja |
| automatedExportSettings | Nej |  Nej |
| autoscalesettings | Ja | Ja |
| planen | Nej |  Nej |
| calculatebaseline | Nej |  Nej |
| Delarna | Ja | Ja |
| komponenter/händelser | Nej |  Nej |
| komponenter/pricingPlans | Nej |  Nej |
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
| Mina arbets böcker | Nej |  Nej |
| frågor | Nej |  Nej |
| rollbackToLegacyPricingModel | Nej |  Nej |
| scheduledqueryrules | Ja | Ja |
| vmInsightsOnboardingStatuses | Nej |  Nej |
| webbtester | Ja | Ja |
| Arbets böcker | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nej |  Nej |
| diagnosticSettingsCategories | Nej |  Nej |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| IoTApps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| deletedVaults | Nej |  Nej |
| valv | Ja | Ja |
| valv/accessPolicies | Nej |  Nej |
| valv/hemligheter | Nej |  Nej |

## <a name="microsoftkusto"></a>Microsoft. Kusto
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| kluster | Ja | Ja |
| kluster/databaser | Nej |  Nej |
| kluster/databaser/dataconnections | Nej |  Nej |
| kluster/databaser/eventhubconnections | Nej |  Nej |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| labaccounts | Ja | Ja |
| användare | Nej |  Nej |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| logs | Nej |  Nej |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| integrationAccounts | Ja | Ja |
| arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| commitmentPlans | Ja | Ja |
| WebServices | Ja | Ja |
| Arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |
| konton/arbets ytor | Ja | Ja |
| konton/arbets ytor/projekt | Ja | Ja |
| teamAccounts | Ja | Ja |
| teamAccounts/arbets ytor | Ja | Ja |
| teamAccounts/arbets ytor/projekt | Ja | Ja |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Arbets ytor | Ja | Ja |
| arbets ytor/beräkningar | Nej |  Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Identiteter | Nej |  Nej |
| userAssignedIdentities | Ja | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| getEntities | Nej |  Nej |
| managementGroups | Nej |  Nej |
| Resurser | Nej |  Nej |
| startTenantBackfill | Nej |  Nej |
| tenantBackfillStatus | Nej |  Nej |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |
| accounts/eventGridFilters | Nej |  Nej |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| budgivning | Nej |  Nej |
| offerTypes | Nej |  Nej |
| offerTypes/utgivare | Nej |  Nej |
| offerTypes/utgivare/erbjudanden | Nej |  Nej |
| offerTypes/utgivare/erbjudanden/planer | Nej |  Nej |
| offerTypes/utgivare/erbjudanden/planer/avtal | Nej |  Nej |
| offerTypes/utgivare/erbjudanden/planer/konfigurationer | Nej |  Nej |
| offerTypes/utgivare/erbjudanden/planer/configs/importImage | Nej |  Nej |
| privategalleryitems | Nej |  Nej |
| läkemedle | Nej |  Nej |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| classicDevServices | Ja | Ja |
| updateCommunicationPreference | Nej |  Nej |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| villkor | Nej |  Nej |
| offertypes | Nej |  Nej |

## <a name="microsoftmedia"></a>Microsoft.Media
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Media Services | Ja | Ja |
| Media Services/accountFilters | Nej |  Nej |
| Media Services/till gångar | Nej |  Nej |
| mediaservices/assets/assetFilters | Nej |  Nej |
| mediaservices/contentKeyPolicies | Nej |  Nej |
| mediaservices/eventGridFilters | Nej |  Nej |
| Media Services/liveEventOperations | Nej |  Nej |
| Media Services/liveEvents | Ja | Ja |
| Media Services/liveEvents/liveOutputs | Nej |  Nej |
| mediaservices/liveOutputOperations | Nej |  Nej |
| Media Services/streamingEndpointOperations | Nej |  Nej |
| Media Services/strömnings slut punkter | Ja | Ja |
| Media Services/streamingLocators | Nej |  Nej |
| Media Services/streamingPolicies | Nej |  Nej |
| Media Services/transformeringar | Nej |  Nej |
| Media Services/transformeringar/jobb | Nej |  Nej |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| samarbetsprojekt | Ja | Ja |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| applicationGateways | Ja | Nej |
| applicationSecurityGroups | Ja | Ja |
| azureFirewallFqdnTags | Nej |  Nej |
| azureFirewalls | Ja | Nej |
| bgpServiceCommunities | Nej |  Nej |
| anslutning | Ja | Ja |
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
| dnszones/Recordset | Nej |  Nej |
| dnszones/SOA | Nej |  Nej |
| dnszones/SRV | Nej |  Nej |
| dnszones/TXT | Nej |  Nej |
| expressRouteCircuits | Ja  | Nej |
| expressRouteServiceProviders | Nej |  Nej |
| frontdoors | Ja, men begränsat (se [Obs! nedan](#frontdoor)) | Ja |
| frontdoorWebApplicationFirewallPolicies | Ja, men begränsat (se [Obs! nedan](#frontdoor)) | Ja |
| getDnsResourceReference | Nej |  Nej |
| interfaceEndpoints | Ja | Ja |
| internalNotify | Nej |  Nej |
| Belastningsutjämnare | Ja | Nej |
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
| virtualNetworkGateways | Ja | Ja |
| virtualNetworks | Ja | Ja |
| virtualNetworks/undernät | Nej |  Nej |
| virtualNetworkTaps | Ja | Ja |
| virtualWans | Ja | Ja |
| vpnGateways | Ja | Nej |
| vpnSites | Ja | Ja |
| webApplicationFirewallPolicies | Ja | Ja |

<a id="frontdoor" />

För Azures frontend-tjänst kan du använda taggar när du skapar resursen, men att uppdatera eller lägga till taggar stöds inte för närvarande.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| namn områden | Ja | Nej |
| namnrymder/notificationHubs | Ja | Nej |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| enheter | Nej |  Nej |
| linkTargets | Nej |  Nej |
| storageInsightConfigs | Nej |  Nej |
| Arbets ytor | Ja | Ja |
| arbets ytor/data källor | Nej |  Nej |
| arbets ytor/linkedServices | Nej |  Nej |
| arbets ytor/fråga | Nej |  Nej |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| managementassociations | Nej |  Nej |
| managementconfigurations | Ja | Ja |
| lösningar | Ja | Ja |
| Vyer | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| policyEvents | Nej |  Nej |
| policyStates | Nej |  Nej |
| policyTrackedResources | Nej |  Nej |
| reparationer | Nej |  Nej |

## <a name="microsoftportal"></a>Microsoft.Portal
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konsoler | Nej |  Nej |
| Instrument paneler | Ja | Ja |
| userSettings | Nej |  Nej |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| kapaciteter | Ja | Ja |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konton | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Nej |  Nej |
| valv | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| namn områden | Ja | Ja |
| namnrymder/authorizationrules | Nej |  Nej |
| namnrymder/hybridconnections | Nej |  Nej |
| namnrymder/hybridconnections/authorizationrules | Nej |  Nej |
| namnrymder/wcfrelays | Nej |  Nej |
| namnrymder/wcfrelays/authorizationrules | Nej |  Nej |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Resurser | Nej |  Nej |
| subscriptionsStatus | Nej |  Nej |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Nej |  Nej |
| childAvailabilityStatuses | Nej |  Nej |
| childResources | Nej |  Nej |
| evenemang | Nej |  Nej |
| impactedResources | Nej |  Nej |
| ansökningar | Nej |  Nej |

## <a name="microsoftresources"></a>Microsoft.Resources
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| distributioner | Nej |  Nej |
| distributioner/åtgärder | Nej |  Nej |
| Länkar | Nej |  Nej |
| notifyResourceJobs | Nej |  Nej |
| Providers | Nej |  Nej |
| resourceGroups | Nej |  Nej |
| Resurser | Nej |  Nej |
| Prenumerationer | Nej |  Nej |
| prenumerationer/providers | Nej |  Nej |
| prenumerationer/resourceGroups | Nej |  Nej |
| prenumerationer/ResourceGroups/resurser | Nej |  Nej |
| prenumerationer/resurser | Nej |  Nej |
| prenumerationer/tagNames | Nej |  Nej |
| prenumerationer/tagNames/tagValues | Nej |  Nej |
| klienter | Nej |  Nej |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| program | Ja | Ja |
| saasresources | Nej |  Nej |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| planta | Ja | Ja |
| förfrågningsåtgärder | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Nej |  Nej |
| searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Nej |  Nej |
| aviseringar | Nej |  Nej |
| allowedConnections | Nej |  Nej |
| redskap | Nej |  Nej |
| applicationWhitelistings | Nej |  Nej |
| AutoProvisioningSettings | Nej |  Nej |
| Godkännanden | Nej |  Nej |
| dataCollectionAgents | Nej |  Nej |
| discoveredSecuritySolutions | Nej |  Nej |
| externalSecuritySolutions | Nej |  Nej |
| InformationProtectionPolicies | Nej |  Nej |
| jitNetworkAccessPolicies | Nej |  Nej |
| Granska | Nej |  Nej |
| övervakning/program mot skadlig kod | Nej |  Nej |
| övervakning/bas linje | Nej |  Nej |
| övervakning/korrigering | Nej |  Nej |
| rikt | Nej |  Nej |
| prissättningar | Nej |  Nej |
| securityContacts | Nej |  Nej |
| securitySolutions | Nej |  Nej |
| securitySolutionsReferenceData | Nej |  Nej |
| securityStatus | Nej |  Nej |
| securityStatus/slut punkter | Nej |  Nej |
| securityStatus/undernät | Nej |  Nej |
| securityStatus/virtualMachines | Nej |  Nej |
| securityStatuses | Nej |  Nej |
| securityStatusesSummaries | Nej |  Nej |
| settings | Nej |  Nej |
| uppgifter | Nej |  Nej |
| topologier | Nej |  Nej |
| workspaceSettings | Nej |  Nej |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nej |  Nej |
| diagnosticSettingsCategories | Nej |  Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| namn områden | Ja | Nej |
| namnrymder/authorizationrules | Nej |  Nej |
| namnrymder/disasterrecoveryconfigs | Nej |  Nej |
| namnrymder/eventgridfilters | Nej |  Nej |
| namnrymder/köer | Nej |  Nej |
| namnrymder/köer/authorizationrules | Nej |  Nej |
| namn områden/ämnen | Nej |  Nej |
| namnrymder/ämnen/authorizationrules | Nej |  Nej |
| namnrymder/ämnen/prenumerationer | Nej |  Nej |
| namn områden/ämnen/prenumerationer/regler | Nej |  Nej |
| premiumMessagingRegions | Nej |  Nej |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| kluster | Ja | Ja |
| kluster/program | Nej |  Nej |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| program | Ja | Ja |
| gatewayer | Ja | Ja |
| nätet | Ja | Ja |
| secrets | Ja | Ja |
| volumes | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| SignalR | Ja | Ja |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Ja | Ja |
| redskap | Ja | Ja |
| applicationDefinitions | Ja | Ja |
| program | Ja | Ja |
| jitRequests | Ja | Ja |

## <a name="microsoftsql"></a>Microsoft.SQL
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| managedInstances | Ja | Ja |
| managedInstances/databaser | Ja (se anmärkning nedan) | Ja |
| managedInstances/databases/backupShortTermRetentionPolicies | Nej | Nej |
| managedInstances/databaser/scheman/tabeller/kolumner/sensitivityLabels | Nej | Nej |
| managedInstances/databaser/vulnerabilityAssessments | Nej | Nej |
| managedInstances/databaser/vulnerabilityAssessments/regler/bas linjer | Nej | Nej |
| managedInstances/encryptionProtector | Nej | Nej |
| managedInstances/nycklar | Nej | Nej |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nej | Nej |
| managedInstances/vulnerabilityAssessments | Nej | Nej |
| Brygghuvudservrar | Ja | Ja |
| servrar/administratörer | Nej |  Nej |
| servrar/communicationLinks | Nej |  Nej |
| servrar/databaser | Ja (se anmärkning nedan) | Ja |
| servers/encryptionProtector | Nej |  Nej |
| servrar/firewallRules | Nej |  Nej |
| servrar/nycklar | Nej |  Nej |
| servrar/restorableDroppedDatabases | Nej |  Nej |
| servrar/serviceobjectives | Nej |  Nej |
| servrar/tdeCertificates | Nej |  Nej |

> [!NOTE]
> Huvud databasen stöder inte taggar, men andra databaser, inklusive Azure SQL Data Warehouse-databaser, stöder taggar. Azure SQL Data Warehouse-databaser måste vara i aktiv (inte pausad) status.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Ja | Ja |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nej |  Nej |
| SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| storageAccounts | Ja | Ja |
| storageAccounts/blobServices | Nej |  Nej |
| storageAccounts/fileServices | Nej |  Nej |
| storageAccounts/queueServices | Nej |  Nej |
| storageAccounts/tjänster | Nej |  Nej |
| storageAccounts/tableServices | Nej |  Nej |
| användningar | Nej |  Nej |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| storageSyncServices | Ja | Ja |
| storageSyncServices/registeredServers | Nej |  Nej |
| storageSyncServices/syncGroups | Nej |  Nej |
| storageSyncServices/syncGroups/cloudEndpoints | Nej |  Nej |
| storageSyncServices/syncGroups/serverEndpoints | Nej |  Nej |
| storageSyncServices/arbets flöden | Nej |  Nej |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| hantera | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| streamingjobs | Ja (se anmärkning nedan) | Ja |
| streamingjobs/diagnosticSettings | Nej |  Nej |

> [!NOTE]
> Du kan inte lägga till en tagg när streamingjobs körs. Stoppa resursen för att lägga till en tagg.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| CreateSubscription | Nej |  Nej |
| SubscriptionDefinitions | Nej |  Nej |
| SubscriptionOperations | Nej |  Nej |

## <a name="microsoftsupport"></a>microsoft.support
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| supporttickets | Nej |  Nej |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| providerRegistrations | Ja | Ja |
| Resurser | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| utrymmen | Ja | Nej |
| miljöer/accessPolicies | Nej |  Nej |
| miljöer/eventsources | Ja | Nej |
| miljöer/referenceDataSets | Ja | Nej |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| konto | Ja | Ja |
| konto/tillägg | Ja | Ja |
| konto/projekt | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Nej |  Nej |
| apiManagementAccounts/apiAcls | Nej |  Nej |
| apiManagementAccounts/apis | Nej |  Nej |
| apiManagementAccounts/apis/apiAcls | Nej |  Nej |
| apiManagementAccounts/apis/connectionAcls | Nej |  Nej |
| apiManagementAccounts/API/anslutningar | Nej |  Nej |
| apiManagementAccounts/apis/connections/connectionAcls | Nej |  Nej |
| apiManagementAccounts/apis/localizedDefinitions | Nej |  Nej |
| apiManagementAccounts/connectionAcls | Nej |  Nej |
| apiManagementAccounts/anslutningar | Nej |  Nej |
| billingMeters | Nej |  Nej |
| Intyg | Ja | Ja |
| connectionGateways | Ja | Ja |
| anslutning | Ja | Ja |
| customApis | Ja | Ja |
| deletedSites | Nej |  Nej |
| functions | Nej |  Nej |
| hostingEnvironments | Ja | Ja |
| hostingEnvironments/multiRolePools | Nej |  Nej |
| hostingEnvironments/multiRolePools/instances | Nej |  Nej |
| hostingEnvironments/workerPools | Nej |  Nej |
| hostingEnvironments/workerPools/instances | Nej |  Nej |
| publishingUsers | Nej |  Nej |
| Rekommenderade | Nej |  Nej |
| resourceHealthMetadata | Nej |  Nej |
| körningar | Nej |  Nej |
| serverFarms | Ja | Ja |
| Server grupper/arbetare | Nej |  Nej |
| stationer | Ja | Ja |
| sites/domainOwnershipIdentifiers | Nej |  Nej |
| platser/hostNameBindings | Nej |  Nej |
| platser/instanser | Nej |  Nej |
| platser/instanser/tillägg | Nej |  Nej |
| platser/premieraddons | Ja | Ja |
| platser/rekommendationer | Nej |  Nej |
| platser/resourceHealthMetadata | Nej |  Nej |
| platser/platser | Ja | Ja |
| sites/slots/hostNameBindings | Nej |  Nej |
| platser/platser/instanser | Nej |  Nej |
| platser/platser/instanser/tillägg | Nej |  Nej |
| sourceControls | Nej |  Nej |
| kontrollerar | Nej |  Nej |
| verifyHostingEnvironmentVnet | Nej |  Nej |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nej |  Nej |
| diagnosticSettingsCategories | Nej |  Nej |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| DeviceServices | Ja | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Resurstyp | Stöder Taggar | Tagga i kostnads rapport |
| ------------- | ----------- | ----------- |
| Delarna | Nej |  Nej |
| componentsSummary | Nej |  Nej |
| monitorInstances | Nej |  Nej |
| monitorInstancesSummary | Nej |  Nej |
| Övervakare | Nej |  Nej |
| notificationSettings | Nej |  Nej |

## <a name="next-steps"></a>Nästa steg
Information om hur du använder taggar för resurser finns i [använda taggar för att ordna dina Azure-resurser](resource-group-using-tags.md).
