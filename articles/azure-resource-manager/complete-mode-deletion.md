---
title: Borttagning av Azure Resource Manager slutfört läge
description: Visar hur resurs typer hanterar fullständig borttagning av läge i Azure Resource Manager mallar.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/27/2019
ms.author: tomfitz
ms.openlocfilehash: 03e91e4be967e822d17144af848f51e73851b1e6
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969188"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Borttagning av Azure-resurser för komplett läges distributioner

I den här artikeln beskrivs hur resurs typer hanterar borttagning när de inte finns i en mall som distribueras i fullständigt läge.

De resurs typer som marker ATS med **Ja** tas bort när typen inte finns i mallen distribuerad med slutfört läge.

Resurs typerna som marker ATS med **Nej** tas inte bort automatiskt när de inte finns i mallen. de tas dock bort om den överordnade resursen tas bort. En fullständig beskrivning av beteendet finns i [Azure Resource Manager distributions lägen](deployment-modes.md).

Hoppa till ett namn område för en resurs leverantör:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. API Management](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. attestering](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Genève](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. batch](#microsoftbatch)
> - [Microsoft. fakturering](#microsoftbilling)
> - [Microsoft. Bingkartssökning](#microsoftbingmaps)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. skiss](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft. Compute](#microsoftcompute)
> - [Microsoft. förbrukning](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. container service](#microsoftcontainerservice)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. data-](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. data migration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. genomik](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. nyckel valv](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrate](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DomainServices | Ja |
> | DomainServices/oucontainer | Nej |
> | DomainServices/ReplicaSets | Ja |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | supportProviders | Nej |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | aadsupportcases | Nej |
> | addsservices | Nej |
> | aktörer | Nej |
> | anonymousapiusers | Nej |
> | konfiguration | Nej |
> | loggar | Nej |
> | Rapporter | Nej |
> | servicehealthmetrics | Nej |
> | services | Nej |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Konfigurationer | Nej |
> | generateRecommendations | Nej |
> | metadata | Nej |
> | Rekommenderade | Nej |
> | utelämningar | Nej |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | actionRules | Ja |
> | aviseringar | Nej |
> | alertsList | Nej |
> | alertsMetaData | Nej |
> | alertsSummary | Nej |
> | alertsSummaryList | Nej |
> | Oss | Nej |
> | smartDetectorAlertRules | Ja |
> | smartDetectorRuntimeEnvironments | Nej |
> | smartGroups | Nej |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | servrar | Ja |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | reportFeedback | Nej |
> | tjänst | Ja |
> | validateServiceName | Nej |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores/eventGridFilters | Nej |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Spring | Ja |

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attestationProviders | Nej |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicAdministrators | Nej |
> | dataAliases | Nej |
> | denyAssignments | Nej |
> | elevateAccess | Nej |
> | findOrphanRoleAssignments | Nej |
> | hålls | Nej |
> | Åtkomst | Nej |
> | policyAssignments | Nej |
> | policyDefinitions | Nej |
> | policySetDefinitions | Nej |
> | providerOperations | Nej |
> | roleAssignments | Nej |
> | roleDefinitions | Nej |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | automationAccounts | Ja |
> | automationAccounts/konfigurationer | Ja |
> | automationAccounts/jobb | Nej |
> | automationAccounts/Runbooks | Ja |
> | automationAccounts/softwareUpdateConfigurations | Nej |
> | automationAccounts/Webhooks | Nej |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores/eventGridFilters | Nej |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genève

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Nej |
> | miljöer/konton | Nej |
> | miljöer/konton/namn områden | Nej |
> | miljöer/konton/namn rymder/konfigurationer | Nej |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | b2cDirectories | Ja |
> | b2ctenants | Nej |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridDataManagers | Ja |
> | postgresInstances | Ja |
> | sqlBigDataClusters | Ja |
> | sqlInstances | Ja |
> | sqlServerRegistrations | Ja |
> | sqlServerRegistrations/sqlServer | Nej |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

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

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | billingAccounts | Nej |
> | billingAccounts/avtal | Nej |
> | billingAccounts/billingPermissions | Nej |
> | billingAccounts/billingProfiles | Nej |
> | billingAccounts/billingProfiles/billingPermissions | Nej |
> | billingAccounts/billingProfiles/billingRoleAssignments | Nej |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Nej |
> | billingAccounts/billingProfiles/billingSubscriptions | Nej |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Nej |
> | billingAccounts/billingProfiles/kunder | Nej |
> | billingAccounts/billingProfiles/fakturor | Nej |
> | billingAccounts/billingProfiles/fakturor/pris dokument | Nej |
> | billingAccounts/billingProfiles/invoiceSections | Nej |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Nej |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Nej |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Nej |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Nej |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Nej |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Nej |
> | billingAccounts/billingProfiles/invoiceSections/Products | Nej |
> | billingAccounts/billingProfiles/invoiceSections/Products/transfer | Nej |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Nej |
> | billingAccounts/billingProfiles/invoiceSections/transaktioner | Nej |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nej |
> | billingAccounts/BillingProfiles/patchOperations | Nej |
> | billingAccounts/billingProfiles/paymentMethods | Nej |
> | billingAccounts/billingProfiles/policys | Nej |
> | billingAccounts/billingProfiles/pris dokument | Nej |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Nej |
> | billingAccounts/billingProfiles/Products | Nej |
> | billingAccounts/billingProfiles/transaktioner | Nej |
> | billingAccounts/billingRoleAssignments | Nej |
> | billingAccounts/billingRoleDefinitions | Nej |
> | billingAccounts/billingSubscriptions | Nej |
> | billingAccounts/createBillingRoleAssignment | Nej |
> | billingAccounts/createInvoiceSectionOperations | Nej |
> | billingAccounts/kunder | Nej |
> | billingAccounts/kunder/billingPermissions | Nej |
> | billingAccounts/kunder/billingSubscriptions | Nej |
> | billingAccounts/kunder/initiateTransfer | Nej |
> | billingAccounts/kunder/principer | Nej |
> | billingAccounts/kunder/produkter | Nej |
> | billingAccounts/kunder/transaktioner | Nej |
> | billingAccounts/kunder/överföringar | Nej |
> | billingAccounts/avdelningar | Nej |
> | billingAccounts/enrollmentAccounts | Nej |
> | billingAccounts/fakturor | Nej |
> | billingAccounts/invoiceSections | Nej |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Nej |
> | billingAccounts/invoiceSections/billingSubscriptions | Nej |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nej |
> | billingAccounts/invoiceSections/höjning | Nej |
> | billingAccounts/invoiceSections/initiateTransfer | Nej |
> | billingAccounts/invoiceSections/patchOperations | Nej |
> | billingAccounts/invoiceSections/productMoveOperations | Nej |
> | billingAccounts/invoiceSections/Products | Nej |
> | billingAccounts/invoiceSections/Products/transfer | Nej |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Nej |
> | billingAccounts/invoiceSections/transaktioner | Nej |
> | billingAccounts/invoiceSections/överföringar | Nej |
> | billingAccounts/lineOfCredit | Nej |
> | billingAccounts/patchOperations | Nej |
> | billingAccounts/paymentMethods | Nej |
> | billingAccounts/produkter | Nej |
> | billingAccounts/transaktioner | Nej |
> | billingPeriods | Nej |
> | billingPermissions | Nej |
> | billingProperty | Nej |
> | billingRoleAssignments | Nej |
> | billingRoleDefinitions | Nej |
> | createBillingRoleAssignment | Nej |
> | enheten | Nej |
> | enrollmentAccounts | Nej |
> | fakturor | Nej |
> | överlåtelse | Nej |
> | överföringar/acceptTransfer | Nej |
> | överföringar/declineTransfer | Nej |
> | överföringar/operationStatus | Nej |
> | överföringar/validateTransfer | Nej |
> | validateAddress | Nej |

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | mapApis | Ja |
> | updateCommunicationPreference | Nej |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blockchainMembers | Ja |
> | cordaMembers | Ja |
> | tittare | Ja |

## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blueprintAssignments | Nej |
> | blueprintAssignments/assignmentOperations | Nej |
> | blueprintAssignments/åtgärder | Nej |
> | modeller | Nej |
> | skisser/artefakter | Nej |
> | skisser/versioner | Nej |
> | skisser/versioner/artefakter | Nej |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | botServices | Ja |
> | botServices/kanaler | Nej |
> | botServices/anslutningar | Nej |
> | Användning | Nej |
> | mallar | Nej |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Redis | Ja |
> | RedisConfigDefinition | Nej |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appliedReservations | Nej |
> | calculateExchange | Nej |
> | calculatePrice | Nej |
> | calculatePurchasePrice | Nej |
> | kataloger | Nej |
> | commercialReservationOrders | Nej |
> | exchange | Nej |
> | placePurchaseOrder | Nej |
> | reservationOrders | Nej |
> | reservationOrders/calculateRefund | Nej |
> | reservationOrders/slå samman | Nej |
> | reservationOrders/reservationer | Nej |
> | reservationOrders/reservationer/revisioner | Nej |
> | reservationOrders/retur | Nej |
> | reservationOrders/Split | Nej |
> | reservationOrders/växling | Nej |
> | reservera | Nej |
> | Resurser | Nej |
> | validateReservationOrder | Nej |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nej |
> | CdnWebApplicationFirewallPolicies | Ja |
> | edgenodes | Nej |
> | filer | Ja |
> | profiler/slut punkter | Ja |
> | profiler/slut punkter/customdomains | Nej |
> | profiler/slut punkter/ursprung | Nej |
> | validateProbe | Nej |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

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
> | Trådlösa | Nej |
> | Domän namn | Ja |
> | Domän namn/funktioner | Nej |
> | Domän namn/internalLoadBalancers | Nej |
> | Domän namn/serviceCertificates | Nej |
> | Domän namn/platser | Nej |
> | Domän namn/platser/roller | Nej |
> | Domän namn/platser/roller/metricDefinitions | Nej |
> | Domän namn/platser/roller/mått | Nej |
> | moveSubscriptionResources | Nej |
> | operatingSystemFamilies | Nej |
> | operatingSystems | Nej |
> | quotas | Nej |
> | resourceTypes | Nej |
> | validateSubscriptionMoveAvailability | Nej |
> | VirtualMachines | Ja |
> | virtualMachines/diagnosticSettings | Nej |
> | virtualMachines/metricDefinitions | Nej |
> | virtualMachines/mått | Nej |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nej |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Trådlösa | Nej |
> | expressRouteCrossConnections | Nej |
> | expressRouteCrossConnections/peering | Nej |
> | gatewaySupportedDevices | Nej |
> | networkSecurityGroups | Ja |
> | quotas | Nej |
> | reservedIps | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nej |
> | virtualNetworks/virtualNetworkPeerings | Nej |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Trådlösa | Nej |
> | disk | Nej |
> | images | Nej |
> | osImages | Nej |
> | osPlatformImages | Nej |
> | publicImages | Nej |
> | quotas | Nej |
> | storageAccounts | Ja |
> | storageAccounts/blobServices | Nej |
> | storageAccounts/fileServices | Nej |
> | storageAccounts/metricDefinitions | Nej |
> | storageAccounts/mått | Nej |
> | storageAccounts/queueServices | Nej |
> | storageAccounts/tjänster | Nej |
> | storageAccounts/tjänster/diagnosticSettings | Nej |
> | storageAccounts/tjänster/metricDefinitions | Nej |
> | storageAccounts/tjänster/mått | Nej |
> | storageAccounts/tableServices | Nej |
> | storageAccounts/vmImages | Nej |
> | vmImages | Nej |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

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
> | diskEncryptionSets | Ja |
> | disk | Ja |
> | gallerier | Ja |
> | gallerier/program | Nej |
> | gallerier/program/versioner | Nej |
> | gallerier/bilder | Nej |
> | gallerier/avbildningar/versioner | Nej |
> | hostGroups | Ja |
> | hostGroups/värdar | Ja |
> | images | Ja |
> | proximityPlacementGroups | Ja |
> | restorePointCollections | Ja |
> | restorePointCollections/restorePoints | Nej |
> | sharedVMExtensions | Ja |
> | sharedVMExtensions/versioner | Nej |
> | sharedVMImages | Ja |
> | sharedVMImages/versioner | Nej |
> | snapshots | Ja |
> | VirtualMachines | Ja |
> | virtualMachines/tillägg | Ja |
> | virtualMachines/metricDefinitions | Nej |
> | virtualMachineScaleSets | Ja |
> | virtualMachineScaleSets/tillägg | Nej |
> | virtualMachineScaleSets/networkInterfaces | Nej |
> | virtualMachineScaleSets/publicIPAddresses | Nej |
> | virtualMachineScaleSets/virtualMachines | Nej |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nej |

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | AggregatedCost | Nej |
> | Saldon | Nej |
> | Budgetar | Nej |
> | Avgifter | Nej |
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
> | Taggar | Nej |
> | Klienter | Nej |
> | Villkor | Nej |
> | UsageDetails | Nej |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerGroups | Ja |
> | serviceAssociationLinks | Nej |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | register | Ja |
> | register/versioner | Nej |
> | register/versioner/Avbryt | Nej |
> | register/build/getLogLink | Nej |
> | register/buildTasks | Ja |
> | register/buildTasks/steg | Nej |
> | register/eventGridFilters | Nej |
> | register/generateCredentials | Nej |
> | register/getBuildSourceUploadUrl | Nej |
> | register/GetCredentials | Nej |
> | register/importImage | Nej |
> | register/queueBuild | Nej |
> | register/regenerateCredential | Nej |
> | register/regenerateCredentials | Nej |
> | register/replikeringar | Ja |
> | register/körningar | Nej |
> | register/körningar/Avbryt | Nej |
> | register/scheduleRun | Nej |
> | register/scopeMaps | Nej |
> | register/uppgifter | Ja |
> | register/token | Nej |
> | register/updatePolicies | Nej |
> | register/Webhooks | Ja |
> | register/Webhooks/getCallbackConfig | Nej |
> | register/Webhooks/ping | Nej |

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerServices | Ja |
> | managedClusters | Ja |
> | openShiftManagedClusters | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aviseringar | Nej |
> | billingAccounts | Nej |
> | Budgetar | Nej |
> | CloudConnectors | Nej |
> | Anslutningsprogram | Ja |
> | enheten | Nej |
> | Dimensioner | Nej |
> | enrollmentAccounts | Nej |
> | Exporter | Nej |
> | ExternalBillingAccounts | Nej |
> | ExternalBillingAccounts/aviseringar | Nej |
> | ExternalBillingAccounts/dimensioner | Nej |
> | ExternalBillingAccounts/prognos | Nej |
> | ExternalBillingAccounts/fråga | Nej |
> | ExternalSubscriptions | Nej |
> | ExternalSubscriptions/aviseringar | Nej |
> | ExternalSubscriptions/dimensioner | Nej |
> | ExternalSubscriptions/prognos | Nej |
> | ExternalSubscriptions/fråga | Nej |
> | Prognostisering | Nej |
> | Söka i data | Nej |
> | Registrera dig | Nej |
> | Reportconfigs | Nej |
> | Rapporter | Nej |
> | Inställningar | Nej |
> | showbackRules | Nej |
> | Vyer | Nej |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Autentiseringsbegäran | Nej |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | typer | Nej |
> | resourceProviders | Ja |

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Utskrifts | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Ja |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbets ytor | Ja |
> | arbets ytor/virtualNetworkPeerings | Nej |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kataloger | Ja |
> | datacatalogs | Ja |
> | datacatalogs/data källor | Nej |
> | datacatalogs/data källor/-genomsökningar | Nej |
> | datacatalogs/data källor/genomsökningar/data uppsättningar | Nej |
> | datacatalogs/data källor/genomsökningar/utlösare | Nej |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataFactories | Ja |
> | dataFactories/diagnosticSettings | Nej |
> | dataFactories/metricDefinitions | Nej |
> | dataFactorySchema | Nej |
> | fabriker | Ja |
> | fabriker/integrationRuntimes | Nej |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/dataLakeStoreAccounts | Nej |
> | konton/storageAccounts | Nej |
> | konton/storageAccounts/behållare | Nej |
> | konton/transferAnalyticsUnits | Nej |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/eventGridFilters | Nej |
> | konton/firewallRules | Nej |

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |
> | tjänster/projekt | Ja |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/resurser | Nej |
> | konton/resurser/data uppsättningar | Nej |
> | konton/resurser/inbjudningar | Nej |
> | konton/resurser/providersharesubscriptions | Nej |
> | konton/resurser/synchronizationSettings | Nej |
> | konton/sharesubscriptions | Nej |
> | konton/sharesubscriptions/consumerSourceDataSets | Nej |
> | konton/sharesubscriptions/datasetmappings | Nej |
> | konton/sharesubscriptions/utlösare | Nej |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | servrar | Ja |
> | servrar/rådgivare | Nej |
> | servrar/privateEndpointConnectionProxies | Nej |
> | servrar/privateEndpointConnections | Nej |
> | servrar/privateLinkResources | Nej |
> | servrar/queryTexts | Nej |
> | servrar/recoverableServers | Nej |
> | servrar/topQueryStatistics | Nej |
> | servrar/virtualNetworkRules | Nej |
> | servrar/waitStatistics | Nej |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | servrar | Ja |
> | servrar/rådgivare | Nej |
> | servrar/privateEndpointConnectionProxies | Nej |
> | servrar/privateEndpointConnections | Nej |
> | servrar/privateLinkResources | Nej |
> | servrar/queryTexts | Nej |
> | servrar/recoverableServers | Nej |
> | servrar/topQueryStatistics | Nej |
> | servrar/virtualNetworkRules | Nej |
> | servrar/waitStatistics | Nej |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | serverGroups | Ja |
> | servrar | Ja |
> | servrar/rådgivare | Nej |
> | servrar/nycklar | Nej |
> | servrar/privateEndpointConnectionProxies | Nej |
> | servrar/privateEndpointConnections | Nej |
> | servrar/privateLinkResources | Nej |
> | servrar/queryTexts | Nej |
> | servrar/recoverableServers | Nej |
> | servrar/topQueryStatistics | Nej |
> | servrar/virtualNetworkRules | Nej |
> | servrar/waitStatistics | Nej |
> | serversv2 | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | artifactSources | Ja |
> | distributioner | Ja |
> | serviceTopologies | Ja |
> | serviceTopologies/tjänster | Ja |
> | serviceTopologies/tjänster/serviceUnits | Ja |
> | steg | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationgroups | Ja |
> | applicationgroups/program | Nej |
> | applicationgroups/Station ära datorer | Nej |
> | applicationgroups/startmenuitems | Nej |
> | hostpools | Ja |
> | hostpools/sessionhosts | Nej |
> | hostpools/sessionhosts/usersessions | Nej |
> | hostpools/usersessions | Nej |
> | arbets ytor | Ja |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ElasticPools | Ja |
> | ElasticPools/IotHubTenants | Ja |
> | IotHubs | Ja |
> | IotHubs/eventGridFilters | Nej |
> | ProvisioningServices | Ja |
> | användningar | Nej |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Pipelines | Ja |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Domänkontrollanter | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labcenters | Ja |
> | Laboration | Ja |
> | labb/miljöer | Ja |
> | labb/serviceRunners | Ja |
> | labb/virtualMachines | Ja |
> | scheman | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | databaseAccountNames | Nej |
> | databaseAccounts | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Ja |
> | domäner/domainOwnershipIdentifiers | Nej |
> | generateSsoRequest | Nej |
> | topLevelDomains | Nej |
> | validateDomainRegistrationInformation | Nej |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | lcsprojects | Nej |
> | lcsprojects/clouddeployments | Nej |
> | lcsprojects/kopplingar | Nej |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Ja |
> | domäner/ämnen | Nej |
> | eventSubscriptions | Nej |
> | extensionTopics | Nej |
> | avsnitt | Ja |
> | topicTypes | Nej |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | Namn områden | Ja |
> | namnrymder/authorizationrules | Nej |
> | namnrymder/disasterrecoveryconfigs | Nej |
> | namnrymder/eventhubs | Nej |
> | namnrymder/eventhubs/authorizationrules | Nej |
> | namnrymder/eventhubs/consumergroups | Nej |
> | namnrymder/networkrulesets | Nej |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | Nej |
> | finansiär | Nej |

## <a name="microsoftgallery"></a>Microsoft. Gallery

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

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autoManagedVmConfigurationProfiles | Ja |
> | configurationProfileAssignments | Nej |
> | guestConfigurationAssignments | Nej |
> | IntelliPoint | Nej |
> | softwareUpdateProfile | Nej |
> | softwareUpdates | Nej |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hanaInstances | Ja |
> | sapMonitors | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedHSMs | Ja |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | kluster/program | Nej |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | faxar | Ja |
> | datorer/tillägg | Ja |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataManagers | Ja |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Delarna | Ja |
> | networkScopes | Ja |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Utskrifts | Ja |

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Nej |
> | diagnosticSettingsCategories | Nej |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appTemplates | Nej |
> | IoTApps | Ja |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Graph | Ja |

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedVaults | Nej |
> | hsmPools | Ja |
> | valv | Ja |
> | valv/accessPolicies | Nej |
> | valv/eventGridFilters | Nej |
> | valv/hemligheter | Nej |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | kluster/attacheddatabaseconfigurations | Nej |
> | kluster/databaser | Nej |
> | kluster/databaser/dataconnections | Nej |
> | kluster/databaser/eventhubconnections | Nej |
> | kluster/sharedidentities | Nej |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labaccounts | Ja |
> | användare | Nej |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hostingEnvironments | Ja |
> | integrationAccounts | Ja |
> | integrationServiceEnvironments | Ja |
> | integrationServiceEnvironments/managedApis | Ja |
> | isolatedEnvironments | Ja |
> | arbetsflöden | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | commitmentPlans | Ja |
> | WebServices | Ja |
> | Arbetsytor | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbets ytor | Ja |
> | arbets ytor/beräkningar | Nej |
> | arbets ytor/eventGridFilters | Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Identiteter | Nej |
> | userAssignedIdentities | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Nej |
> | registrationAssignments | Nej |
> | registrationDefinitions | Nej |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | getEntities | Nej |
> | managementGroups | Nej |
> | Resurser | Nej |
> | startTenantBackfill | Nej |
> | tenantBackfillStatus | Nej |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/eventGridFilters | Nej |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

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
> | Utgivare | Nej |
> | Utgivare/erbjudanden | Nej |
> | Utgivare/erbjudanden/ändringar | Nej |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicDevServices | Ja |
> | updateCommunicationPreference | Nej |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | villkor | Nej |
> | offertypes | Nej |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Media Services | Ja |
> | Media Services/accountFilters | Nej |
> | Media Services/till gångar | Nej |
> | Media Services/assets/assetFilters | Nej |
> | Media Services/contentKeyPolicies | Nej |
> | Media Services/eventGridFilters | Nej |
> | Media Services/liveEventOperations | Nej |
> | Media Services/liveEvents | Ja |
> | Media Services/liveEvents/liveOutputs | Nej |
> | Media Services/liveOutputOperations | Nej |
> | Media Services/mediaGraphs | Nej |
> | Media Services/streamingEndpointOperations | Nej |
> | Media Services/strömnings slut punkter | Ja |
> | Media Services/streamingLocators | Nej |
> | Media Services/streamingPolicies | Nej |
> | Media Services/transformeringar | Nej |
> | Media Services/transformeringar/jobb | Nej |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appClusters | Ja |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | assessmentProjects | Ja |
> | migrateprojects | Ja |
> | samarbetsprojekt | Ja |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ja |
> | objectUnderstandingAccounts | Ja |
> | remoteRenderingAccounts | Ja |
> | spatialAnchorsAccounts | Ja |
> | surfaceReconstructionAccounts | Ja |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | netAppAccounts | Ja |
> | netAppAccounts/backupPolicies | Ja |
> | netAppAccounts/capacityPools | Ja |
> | netAppAccounts/capacityPools/Volumes | Ja |
> | netAppAccounts/capacityPools/Volumes/Backups | Nej |
> | netAppAccounts/capacityPools/Volumes/mountTargets | Ja |
> | netAppAccounts/capacityPools/volym/ögonblicks bilder | Ja |
> | netAppAccounts/valv | Nej |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationGateways | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja |
> | applicationSecurityGroups | Ja |
> | azureFirewallFqdnTags | Nej |
> | azureFirewalls | Ja |
> | bastionHosts | Ja |
> | bgpServiceCommunities | Nej |
> | Anslutning | Ja |
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
> | expressRouteCrossConnections | Ja |
> | expressRouteGateways | Ja |
> | expressRoutePorts | Ja |
> | expressRouteServiceProviders | Nej |
> | firewallPolicies | Ja |
> | frontdoors | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nej |
> | frontdoorWebApplicationFirewallPolicies | Ja |
> | getDnsResourceReference | Nej |
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
> | p2sVpnGateways | Ja |
> | privateDnsOperationStatuses | Nej |
> | privateDnsZones | Ja |
> | privateDnsZones/A | Nej |
> | privateDnsZones/AAAA | Nej |
> | privateDnsZones/alla | Nej |
> | privateDnsZones/CNAME | Nej |
> | privateDnsZones/MX | Nej |
> | privateDnsZones/PTR | Nej |
> | privateDnsZones/SOA | Nej |
> | privateDnsZones/SRV | Nej |
> | privateDnsZones/TXT | Nej |
> | privateDnsZones/virtualNetworkLinks | Ja |
> | privateEndpoints | Ja |
> | privateLinkServices | Ja |
> | publicIPAddresses | Ja |
> | publicIPPrefixes | Ja |
> | routeFilters | Ja |
> | routeTables | Ja |
> | secureGateways | Ja |
> | serviceEndpointPolicies | Ja |
> | trafficManagerGeographicHierarchies | Nej |
> | trafficmanagerprofiles | Ja |
> | trafficmanagerprofiles/termiska kartor | Nej |
> | trafficManagerUserMetricsKeys | Nej |
> | virtualHubs | Ja |
> | virtualNetworkGateways | Ja |
> | virtualNetworks | Ja |
> | virtualNetworkTaps | Ja |
> | virtualWans | Ja |
> | vpnGateways | Ja |
> | vpnSites | Ja |
> | webApplicationFirewallPolicies | Ja |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namn områden | Ja |
> | namnrymder/notificationHubs | Ja |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | osNamespaces | Ja |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | HyperVSites | Ja |
> | ImportSites | Ja |
> | ServerSites | Ja |
> | VMwareSites | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | enheter | Nej |
> | linkTargets | Nej |
> | storageInsightConfigs | Nej |
> | arbets ytor | Ja |
> | arbets ytor/data källor | Nej |
> | arbets ytor/linkedServices | Nej |
> | arbets ytor/fråga | Nej |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managementassociations | Nej |
> | managementconfigurations | Ja |
> | lösningar | Ja |
> | Vyer | Ja |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | legacyPeerings | Nej |
> | peerAsns | Nej |
> | peerings | Ja |
> | peeringServiceProviders | Nej |
> | peeringServices | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | policyEvents | Nej |
> | policyMetadata | Nej |
> | policyStates | Nej |
> | policyTrackedResources | Nej |
> | reparationer | Nej |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konsoler | Nej |
> | Instrument paneler | Ja |
> | userSettings | Nej |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | workspaceCollections | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kapaciteter | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | backupProtectedItems | Nej |
> | valv | Ja |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namn områden | Ja |
> | namnrymder/authorizationrules | Nej |
> | namnrymder/hybridconnections | Nej |
> | namnrymder/hybridconnections/authorizationrules | Nej |
> | namnrymder/wcfrelays | Nej |
> | namnrymder/wcfrelays/authorizationrules | Nej |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Nej |
> | Samlingar | Ja |
> | Samlingar/program | Nej |
> | Samlingar/securityprincipals | Nej |
> | templateImages | Nej |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | frågor | Ja |
> | resourceChangeDetails | Nej |
> | resourceChanges | Nej |
> | Resurser | Nej |
> | resourcesHistory | Nej |
> | subscriptionsStatus | Nej |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilityStatuses | Nej |
> | childAvailabilityStatuses | Nej |
> | childResources | Nej |
> | evenemang | Nej |
> | impactedResources | Nej |
> | metadata | Nej |
> | Ansökningar | Nej |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Distributioner | Nej |
> | distributioner/åtgärder | Nej |
> | deploymentScripts | Ja |
> | deploymentScripts/loggar | Nej |
> | Länkar | Nej |
> | notifyResourceJobs | Nej |
> | finansiär | Nej |
> | resourceGroups | Nej |
> | Resurser | Nej |
> | prenumerationer | Nej |
> | prenumerationer/providers | Nej |
> | prenumerationer/resourceGroups | Nej |
> | prenumerationer/ResourceGroups/resurser | Nej |
> | prenumerationer/resurser | Nej |
> | prenumerationer/tagNames | Nej |
> | prenumerationer/tagNames/tagValues | Nej |
> | Klienter | Nej |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | saasresources | Nej |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | förfrågningsåtgärder | Ja |

## <a name="microsoftsearch"></a>Microsoft. search

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nej |
> | searchServices | Ja |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Nej |
> | advancedThreatProtectionSettings | Nej |
> | aviseringar | Nej |
> | allowedConnections | Nej |
> | applicationWhitelistings | Nej |
> | assessmentMetadata | Nej |
> | utvärderingar | Nej |
> | automatiseringar | Ja |
> | AutoProvisioningSettings | Nej |
> | Godkännanden | Nej |
> | dataCollectionAgents | Nej |
> | deviceSecurityGroups | Nej |
> | discoveredSecuritySolutions | Nej |
> | externalSecuritySolutions | Nej |
> | InformationProtectionPolicies | Nej |
> | iotSecuritySolutions | Ja |
> | iotSecuritySolutions/analyticsModels | Nej |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Nej |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Nej |
> | jitNetworkAccessPolicies | Nej |
> | networkData | Nej |
> | playbookConfigurations | Ja |
> | Rikt | Nej |
> | prissättningar | Nej |
> | regulatoryComplianceStandards | Nej |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Nej |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Nej |
> | securityContacts | Nej |
> | securitySolutions | Nej |
> | securitySolutionsReferenceData | Nej |
> | securityStatuses | Nej |
> | securityStatusesSummaries | Nej |
> | serverVulnerabilityAssessments | Nej |
> | settings | Nej |
> | underbedömningar | Nej |
> | uppgifter | Nej |
> | Topologier | Nej |
> | workspaceSettings | Nej |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Nej |
> | diagnosticSettingsCategories | Nej |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | agg regeringar | Nej |
> | alertRules | Nej |
> | alertRuleTemplates | Nej |
> | dina | Nej |
> | fall | Nej |
> | dataConnectors | Nej |
> | poster | Nej |
> | entityQueries | Nej |
> | officeConsents | Nej |
> | settings | Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namn områden | Ja |
> | namnrymder/authorizationrules | Nej |
> | namnrymder/disasterrecoveryconfigs | Nej |
> | namnrymder/eventgridfilters | Nej |
> | namnrymder/networkrulesets | Nej |
> | namnrymder/köer | Nej |
> | namnrymder/köer/authorizationrules | Nej |
> | namn områden/ämnen | Nej |
> | namnrymder/ämnen/authorizationrules | Nej |
> | namnrymder/ämnen/prenumerationer | Nej |
> | namn områden/ämnen/prenumerationer/regler | Nej |
> | premiumMessagingRegions | Nej |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | kluster | Ja |
> | kluster/program | Nej |
> | containerGroups | Ja |
> | containerGroupSets | Ja |
> | edgeclusters | Ja |
> | edgeclusters/program | Nej |
> | nätet | Ja |
> | secretstores | Ja |
> | secretstores/certifikat | Nej |
> | secretstores/hemligheter | Nej |
> | volumes | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | containerGroups | Ja |
> | gatewayer | Ja |
> | nätet | Ja |
> | hemligheter | Ja |
> | volumes | Ja |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | Nej |
> | providerRegistrations/resourceTypeRegistrations | Nej |
> | distributioner | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SignalR | Ja |
> | SignalR/eventGridFilters | Nej |

## <a name="microsoftsiterecovery"></a>Microsoft. SiteRecovery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SiteRecoveryVault | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridUseBenefits | Nej |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationDefinitions | Ja |
> | program | Ja |
> | jitRequests | Ja |

## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedInstances | Ja |
> | managedInstances/databaser | Ja |
> | managedInstances/databaser/backupShortTermRetentionPolicies | Nej |
> | managedInstances/databaser/scheman/tabeller/kolumner/sensitivityLabels | Nej |
> | managedInstances/databaser/vulnerabilityAssessments | Nej |
> | managedInstances/databaser/vulnerabilityAssessments/regler/bas linjer | Nej |
> | managedInstances/encryptionProtector | Nej |
> | managedInstances/nycklar | Nej |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nej |
> | managedInstances/vulnerabilityAssessments | Nej |
> | servrar | Ja |
> | servrar/administratörer | Nej |
> | servrar/communicationLinks | Nej |
> | servrar/databaser | Ja |
> | servrar/encryptionProtector | Nej |
> | servrar/firewallRules | Nej |
> | servrar/nycklar | Nej |
> | servrar/restorableDroppedDatabases | Nej |
> | servrar/serviceobjectives | Nej |
> | servrar/tdeCertificates | Nej |
> | virtualClusters | Nej |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

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
> | storageAccounts/tjänster/metricDefinitions | Nej |
> | storageAccounts/tableServices | Nej |
> | användningar | Nej |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cacheminnen | Ja |
> | cache-/storageTargets | Nej |
> | usageModels | Nej |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | replicationGroups | Nej |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices/registeredServers | Nej |
> | storageSyncServices/syncGroups | Nej |
> | storageSyncServices/syncGroups/cloudEndpoints | Nej |
> | storageSyncServices/syncGroups/serverEndpoints | Nej |
> | storageSyncServices/arbets flöden | Nej |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices/registeredServers | Nej |
> | storageSyncServices/syncGroups | Nej |
> | storageSyncServices/syncGroups/cloudEndpoints | Nej |
> | storageSyncServices/syncGroups/serverEndpoints | Nej |
> | storageSyncServices/arbets flöden | Nej |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices/registeredServers | Nej |
> | storageSyncServices/syncGroups | Nej |
> | storageSyncServices/syncGroups/cloudEndpoints | Nej |
> | storageSyncServices/syncGroups/serverEndpoints | Nej |
> | storageSyncServices/arbets flöden | Nej |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hantera | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | streamingjobs | Ja |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Avbryt | Nej |
> | CreateSubscription | Nej |
> | Använd | Nej |
> | Ta | Nej |
> | SubscriptionDefinitions | Nej |
> | SubscriptionOperations | Nej |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Ja |
> | miljöer/accessPolicies | Nej |
> | miljöer/eventsources | Ja |
> | miljöer/referenceDataSets | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ja |
> | dedicatedCloudServices | Ja |
> | VirtualMachines | Ja |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | apiManagementAccounts | Nej |
> | apiManagementAccounts/apiAcls | Nej |
> | apiManagementAccounts/API: er | Nej |
> | apiManagementAccounts/API/apiAcls | Nej |
> | apiManagementAccounts/API/connectionAcls | Nej |
> | apiManagementAccounts/API/anslutningar | Nej |
> | apiManagementAccounts/API/Connections/connectionAcls | Nej |
> | apiManagementAccounts/API/localizedDefinitions | Nej |
> | apiManagementAccounts/connectionAcls | Nej |
> | apiManagementAccounts/anslutningar | Nej |
> | billingMeters | Nej |
> | Intyg | Ja |
> | connectionGateways | Ja |
> | Anslutning | Ja |
> | customApis | Ja |
> | deletedSites | Nej |
> | functions | Nej |
> | hostingEnvironments | Ja |
> | hostingEnvironments/multiRolePools | Nej |
> | hostingEnvironments/workerPools | Nej |
> | publishingUsers | Nej |
> | Rekommenderade | Nej |
> | resourceHealthMetadata | Nej |
> | körningar | Nej |
> | Server grupper | Ja |
> | Server grupper/eventGridFilters | Nej |
> | stationer | Ja |
> | platser/konfig  | Nej |
> | platser/eventGridFilters | Nej |
> | platser/hostNameBindings | Nej |
> | platser/networkConfig | Nej |
> | platser/premieraddons | Ja |
> | platser/platser | Ja |
> | platser/platser/eventGridFilters | Nej |
> | platser/platser/hostNameBindings | Nej |
> | platser/platser/networkConfig | Nej |
> | sourceControls | Nej |
> | kontrollerar | Nej |
> | verifyHostingEnvironmentVnet | Nej |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Nej |
> | diagnosticSettingsCategories | Nej |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DeviceServices | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

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
