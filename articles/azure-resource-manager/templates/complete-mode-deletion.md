---
title: Borttagning i complete-läget
description: Visar hur resurs typer hanterar fullständig borttagning av läge i Azure Resource Manager mallar.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b24bf2d8076dbaa1975f5444d597647156a31f4f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715590"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Borttagning av Azure-resurser för komplett läges distributioner

I den här artikeln beskrivs hur resurs typer hanterar borttagning när de inte finns i en mall som distribueras i fullständigt läge.

De resurs typer som marker ATS med **Ja** tas bort när typen inte finns i mallen distribuerad med slutfört läge.

Resurs typerna som marker ATS med **Nej** tas inte bort automatiskt när de inte finns i mallen. de tas dock bort om den överordnade resursen tas bort. En fullständig beskrivning av beteendet finns i [Azure Resource Manager distributions lägen](deployment-modes.md).

Om du distribuerar till [fler än en resurs grupp i en mall](cross-resource-group-deployment.md)är resurserna i resurs gruppen som anges i distributions åtgärden tillgängliga för borttagning. Resurser i de sekundära resurs grupperna tas inte bort.

Hoppa till ett namn område för en resurs leverantör:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. attestering](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Genève](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. fakturering](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. förbrukning](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DomainServices | Ja |
> | DomainServices / oucontainer | Inga |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | supportProviders | Inga |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | aadsupportcases | Inga |
> | addsservices | Inga |
> | aktörer | Inga |
> | anonymousapiusers | Inga |
> | konfiguration | Inga |
> | loggar | Inga |
> | rapporter | Inga |
> | servicehealthmetrics | Inga |
> | services | Inga |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konfigurationer | Inga |
> | generateRecommendations | Inga |
> | metadata | Inga |
> | rekommenderade | Inga |
> | utelämningar | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | actionRules | Ja |
> | aviseringar | Inga |
> | alertsList | Inga |
> | alertsMetaData | Inga |
> | alertsSummary | Inga |
> | alertsSummaryList | Inga |
> | oss | Inga |
> | smartDetectorAlertRules | Ja |
> | smartDetectorRuntimeEnvironments | Inga |
> | smartGroups | Inga |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | servrar | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | reportFeedback | Inga |
> | tjänst | Ja |
> | validateServiceName | Inga |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Inga |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Spring | Ja |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attestationProviders | Inga |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicAdministrators | Inga |
> | dataAliases | Inga |
> | denyAssignments | Inga |
> | elevateAccess | Inga |
> | findOrphanRoleAssignments | Inga |
> | hålls | Inga |
> | behörigheter | Inga |
> | policyAssignments | Inga |
> | policyDefinitions | Inga |
> | policySetDefinitions | Inga |
> | providerOperations | Inga |
> | roleAssignments | Inga |
> | roleAssignmentsUsageMetrics | Inga |
> | roleDefinitions | Inga |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | automationAccounts | Ja |
> | automationAccounts/konfigurationer | Ja |
> | automationAccounts/jobb | Inga |
> | automationAccounts/Runbooks | Ja |
> | automationAccounts / softwareUpdateConfigurations | Inga |
> | automationAccounts/Webhooks | Inga |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Inga |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Inga |
> | miljöer/konton | Inga |
> | miljöer/konton/namn områden | Inga |
> | miljöer/konton/namn rymder/konfigurationer | Inga |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | b2cDirectories | Ja |
> | b2ctenants | Inga |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridDataManagers | Ja |
> | postgresInstances | Ja |
> | sqlBigDataClusters | Ja |
> | sqlInstances | Ja |
> | sqlServerRegistrations | Ja |
> | sqlServerRegistrations/sqlServer | Inga |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | registreringar | Ja |
> | registreringar/customerSubscriptions | Inga |
> | registreringar/produkter | Inga |
> | verificationKeys | Inga |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | batchAccounts | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | billingAccounts | Inga |
> | billingAccounts/avtal | Inga |
> | billingAccounts / billingPermissions | Inga |
> | billingAccounts / billingProfiles | Inga |
> | billingAccounts / billingProfiles / billingPermissions | Inga |
> | billingAccounts / billingProfiles / billingRoleAssignments | Inga |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Inga |
> | billingAccounts / billingProfiles / billingSubscriptions | Inga |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Inga |
> | billingAccounts/billingProfiles/kunder | Inga |
> | billingAccounts/billingProfiles/instruktioner | Inga |
> | billingAccounts/billingProfiles/fakturor | Inga |
> | billingAccounts/billingProfiles/fakturor/pris dokument | Inga |
> | billingAccounts / billingProfiles / invoiceSections | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Inga |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Inga |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Inga |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Inga |
> | billingAccounts/billingProfiles/invoiceSections/Products | Inga |
> | billingAccounts/billingProfiles/invoiceSections/Products/transfer | Inga |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Inga |
> | billingAccounts/billingProfiles/invoiceSections/transaktioner | Inga |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Inga |
> | billingAccounts / BillingProfiles / patchOperations | Inga |
> | billingAccounts / billingProfiles / paymentMethods | Inga |
> | billingAccounts/billingProfiles/policys | Inga |
> | billingAccounts/billingProfiles/pris dokument | Inga |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Inga |
> | billingAccounts/billingProfiles/Products | Inga |
> | billingAccounts/billingProfiles/transaktioner | Inga |
> | billingAccounts / billingRoleAssignments | Inga |
> | billingAccounts / billingRoleDefinitions | Inga |
> | billingAccounts / billingSubscriptions | Inga |
> | billingAccounts/billingSubscriptions/fakturor | Inga |
> | billingAccounts / createBillingRoleAssignment | Inga |
> | billingAccounts / createInvoiceSectionOperations | Inga |
> | billingAccounts/kunder | Inga |
> | billingAccounts/kunder/billingPermissions | Inga |
> | billingAccounts/kunder/billingSubscriptions | Inga |
> | billingAccounts/kunder/initiateTransfer | Inga |
> | billingAccounts/kunder/principer | Inga |
> | billingAccounts/kunder/produkter | Inga |
> | billingAccounts/kunder/transaktioner | Inga |
> | billingAccounts/kunder/överföringar | Inga |
> | billingAccounts/avdelningar | Inga |
> | billingAccounts / enrollmentAccounts | Inga |
> | billingAccounts/fakturor | Inga |
> | billingAccounts / invoiceSections | Inga |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Inga |
> | billingAccounts / invoiceSections / billingSubscriptions | Inga |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Inga |
> | billingAccounts/invoiceSections/höjning | Inga |
> | billingAccounts / invoiceSections / initiateTransfer | Inga |
> | billingAccounts / invoiceSections / patchOperations | Inga |
> | billingAccounts / invoiceSections / productMoveOperations | Inga |
> | billingAccounts/invoiceSections/Products | Inga |
> | billingAccounts/invoiceSections/Products/transfer | Inga |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Inga |
> | billingAccounts/invoiceSections/transaktioner | Inga |
> | billingAccounts/invoiceSections/överföringar | Inga |
> | billingAccounts / lineOfCredit | Inga |
> | billingAccounts / patchOperations | Inga |
> | billingAccounts / paymentMethods | Inga |
> | billingAccounts/produkter | Inga |
> | billingAccounts/transaktioner | Inga |
> | billingPeriods | Inga |
> | billingPermissions | Inga |
> | billingProperty | Inga |
> | billingRoleAssignments | Inga |
> | billingRoleDefinitions | Inga |
> | createBillingRoleAssignment | Inga |
> | enheten | Inga |
> | enrollmentAccounts | Inga |
> | fakturor | Inga |
> | överlåtelse | Inga |
> | överföringar/acceptTransfer | Inga |
> | överföringar/declineTransfer | Inga |
> | överföringar/operationStatus | Inga |
> | överföringar/validateTransfer | Inga |
> | validateAddress | Inga |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | mapApis | Ja |
> | updateCommunicationPreference | Inga |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blockchainMembers | Ja |
> | cordaMembers | Ja |
> | tittare | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blueprintAssignments | Inga |
> | blueprintAssignments / assignmentOperations | Inga |
> | blueprintAssignments/åtgärder | Inga |
> | modeller | Inga |
> | skisser/artefakter | Inga |
> | skisser/versioner | Inga |
> | skisser/versioner/artefakter | Inga |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | botServices | Ja |
> | botServices/kanaler | Inga |
> | botServices/anslutningar | Inga |
> | användning | Inga |
> | mallar | Inga |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Redis | Ja |
> | RedisConfigDefinition | Inga |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appliedReservations | Inga |
> | autoQuotaIncrease | Inga |
> | calculateExchange | Inga |
> | calculatePrice | Inga |
> | calculatePurchasePrice | Inga |
> | kataloger | Inga |
> | commercialReservationOrders | Inga |
> | exchange | Inga |
> | placePurchaseOrder | Inga |
> | reservationOrders | Inga |
> | reservationOrders / calculateRefund | Inga |
> | reservationOrders/slå samman | Inga |
> | reservationOrders/reservationer | Inga |
> | reservationOrders/reservationer/revisioner | Inga |
> | reservationOrders/retur | Inga |
> | reservationOrders/Split | Inga |
> | reservationOrders/växling | Inga |
> | reservera | Inga |
> | resourceProviders | Inga |
> | resurser | Inga |
> | validateReservationOrder | Inga |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Inga |
> | CdnWebApplicationFirewallPolicies | Ja |
> | edgenodes | Inga |
> | profiles | Ja |
> | profiler/slut punkter | Ja |
> | profiler/slut punkter/customdomains | Inga |
> | profiler/slut punkter/ursprung | Inga |
> | validateProbe | Inga |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certificateOrders | Ja |
> | certificateOrders/certifikat | Inga |
> | validateCertificateRegistrationInformation | Inga |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | trådlösa | Inga |
> | Domän namn | Ja |
> | Domän namn/funktioner | Inga |
> | Domän namn/internalLoadBalancers | Inga |
> | Domän namn/serviceCertificates | Inga |
> | Domän namn/platser | Inga |
> | Domän namn/platser/roller | Inga |
> | Domän namn/platser/roller/metricDefinitions | Inga |
> | Domän namn/platser/roller/mått | Inga |
> | moveSubscriptionResources | Inga |
> | operatingSystemFamilies | Inga |
> | operatingSystems | Inga |
> | quotas | Inga |
> | resourceTypes | Inga |
> | validateSubscriptionMoveAvailability | Inga |
> | virtualMachines | Ja |
> | virtualMachines / diagnosticSettings | Inga |
> | virtualMachines / metricDefinitions | Inga |
> | virtualMachines/mått | Inga |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicInfrastructureResources | Inga |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | trådlösa | Inga |
> | expressRouteCrossConnections | Inga |
> | expressRouteCrossConnections/peering | Inga |
> | gatewaySupportedDevices | Inga |
> | networkSecurityGroups | Ja |
> | quotas | Inga |
> | reservedIps | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Inga |
> | virtualNetworks/virtualNetworkPeerings | Inga |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | trådlösa | Inga |
> | disk | Inga |
> | images | Inga |
> | osImages | Inga |
> | osPlatformImages | Inga |
> | publicImages | Inga |
> | quotas | Inga |
> | storageAccounts | Ja |
> | storageAccounts/blobServices | Inga |
> | storageAccounts/fileServices | Inga |
> | storageAccounts/metricDefinitions | Inga |
> | storageAccounts/mått | Inga |
> | storageAccounts/queueServices | Inga |
> | storageAccounts/tjänster | Inga |
> | storageAccounts/tjänster/diagnosticSettings | Inga |
> | storageAccounts/tjänster/metricDefinitions | Inga |
> | storageAccounts/tjänster/mått | Inga |
> | storageAccounts/tableServices | Inga |
> | storageAccounts/vmImages | Inga |
> | vmImages | Inga |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | RateCard | Inga |
> | UsageAggregates | Inga |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilitySets | Ja |
> | diskEncryptionSets | Ja |
> | disk | Ja |
> | gallerier | Ja |
> | gallerier/program | Inga |
> | gallerier/program/versioner | Inga |
> | gallerier/bilder | Inga |
> | gallerier/avbildningar/versioner | Inga |
> | hostGroups | Ja |
> | hostGroups/värdar | Ja |
> | images | Ja |
> | proximityPlacementGroups | Ja |
> | restorePointCollections | Ja |
> | restorePointCollections / restorePoints | Inga |
> | sharedVMImages | Ja |
> | sharedVMImages/versioner | Inga |
> | snapshots | Ja |
> | virtualMachines | Ja |
> | virtualMachines/tillägg | Ja |
> | virtualMachines / metricDefinitions | Inga |
> | virtualMachineScaleSets | Ja |
> | virtualMachineScaleSets/tillägg | Inga |
> | virtualMachineScaleSets/networkInterfaces | Inga |
> | virtualMachineScaleSets/publicIPAddresses | Inga |
> | virtualMachineScaleSets/virtualMachines | Inga |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Inga |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | AggregatedCost | Inga |
> | Saldon | Inga |
> | Budgetar | Inga |
> | Avgifter | Inga |
> | CostTags | Inga |
> | krediter | Inga |
> | händelser | Inga |
> | Prognoser | Inga |
> | samtliga | Inga |
> | Marknads platser | Inga |
> | Pricesheets | Inga |
> | läkemedle | Inga |
> | ReservationDetails | Inga |
> | ReservationRecommendations | Inga |
> | ReservationSummaries | Inga |
> | ReservationTransactions | Inga |
> | Taggar | Inga |
> | klienter | Inga |
> | Villkor | Inga |
> | UsageDetails | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerGroups | Ja |
> | serviceAssociationLinks | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | register | Ja |
> | register/versioner | Inga |
> | register/versioner/Avbryt | Inga |
> | register/build/getLogLink | Inga |
> | register/buildTasks | Ja |
> | register/buildTasks/steg | Inga |
> | register/eventGridFilters | Inga |
> | register/generateCredentials | Inga |
> | register/getBuildSourceUploadUrl | Inga |
> | register/GetCredentials | Inga |
> | register/importImage | Inga |
> | register/queueBuild | Inga |
> | register/regenerateCredential | Inga |
> | register/regenerateCredentials | Inga |
> | register/replikeringar | Ja |
> | register/körningar | Inga |
> | register/körningar/Avbryt | Inga |
> | register/scheduleRun | Inga |
> | register/scopeMaps | Inga |
> | register/taskRuns | Ja |
> | register/uppgifter | Ja |
> | register/token | Inga |
> | register/updatePolicies | Inga |
> | register/Webhooks | Ja |
> | register/Webhooks/getCallbackConfig | Inga |
> | register/Webhooks/ping | Inga |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerServices | Ja |
> | managedClusters | Ja |
> | openShiftManagedClusters | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aviseringar | Inga |
> | billingAccounts | Inga |
> | Budgetar | Inga |
> | CloudConnectors | Inga |
> | Anslutningsprogram | Ja |
> | enheten | Inga |
> | Dimensioner | Inga |
> | EnrollmentAccounts | Inga |
> | Exporter | Inga |
> | ExternalBillingAccounts | Inga |
> | ExternalBillingAccounts/aviseringar | Inga |
> | ExternalBillingAccounts/dimensioner | Inga |
> | ExternalBillingAccounts/prognos | Inga |
> | ExternalBillingAccounts/fråga | Inga |
> | ExternalSubscriptions | Inga |
> | ExternalSubscriptions/aviseringar | Inga |
> | ExternalSubscriptions/dimensioner | Inga |
> | ExternalSubscriptions/prognos | Inga |
> | ExternalSubscriptions/fråga | Inga |
> | Prognostisering | Inga |
> | Fråga | Inga |
> | Registrera dig | Inga |
> | Reportconfigs | Inga |
> | Rapporter | Inga |
> | Inställningar | Inga |
> | showbackRules | Inga |
> | Vyer | Inga |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Begäranden | Inga |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | typer | Inga |
> | resourceProviders | Ja |

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
> | arbets ytor | Ja |
> | arbets ytor/dbWorkspaces | Inga |
> | arbets ytor/storageEncryption | Inga |
> | arbets ytor/virtualNetworkPeerings | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kataloger | Ja |
> | datacatalogs | Ja |
> | datacatalogs/data källor | Inga |
> | datacatalogs/data källor/-genomsökningar | Inga |
> | datacatalogs/data källor/genomsökningar/data uppsättningar | Inga |
> | datacatalogs/data källor/genomsökningar/utlösare | Inga |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataFactories | Ja |
> | dataFactories / diagnosticSettings | Inga |
> | dataFactories / metricDefinitions | Inga |
> | dataFactorySchema | Inga |
> | fabriker | Ja |
> | fabriker/integrationRuntimes | Inga |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/dataLakeStoreAccounts | Inga |
> | konton/storageAccounts | Inga |
> | konton/storageAccounts/behållare | Inga |
> | konton/transferAnalyticsUnits | Inga |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/eventGridFilters | Inga |
> | konton/firewallRules | Inga |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

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
> | konton/resurser | Inga |
> | konton/resurser/data uppsättningar | Inga |
> | konton/resurser/inbjudningar | Inga |
> | konton/resurser/providersharesubscriptions | Inga |
> | konton/resurser/synchronizationSettings | Inga |
> | konton/sharesubscriptions | Inga |
> | konton/sharesubscriptions/consumerSourceDataSets | Inga |
> | konton/sharesubscriptions/datasetmappings | Inga |
> | konton/sharesubscriptions/utlösare | Inga |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | servrar | Ja |
> | servrar/rådgivare | Inga |
> | servrar/nycklar | Inga |
> | servrar/privateEndpointConnectionProxies | Inga |
> | servrar/privateEndpointConnections | Inga |
> | servrar/privateLinkResources | Inga |
> | servrar/queryTexts | Inga |
> | servrar/recoverableServers | Inga |
> | servrar/topQueryStatistics | Inga |
> | servrar/virtualNetworkRules | Inga |
> | servrar/waitStatistics | Inga |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | servrar | Ja |
> | servrar/rådgivare | Inga |
> | servrar/nycklar | Inga |
> | servrar/privateEndpointConnectionProxies | Inga |
> | servrar/privateEndpointConnections | Inga |
> | servrar/privateLinkResources | Inga |
> | servrar/queryTexts | Inga |
> | servrar/recoverableServers | Inga |
> | servrar/topQueryStatistics | Inga |
> | servrar/virtualNetworkRules | Inga |
> | servrar/waitStatistics | Inga |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | serverGroups | Ja |
> | servrar | Ja |
> | servrar/rådgivare | Inga |
> | servrar/nycklar | Inga |
> | servrar/privateEndpointConnectionProxies | Inga |
> | servrar/privateEndpointConnections | Inga |
> | servrar/privateLinkResources | Inga |
> | servrar/queryTexts | Inga |
> | servrar/recoverableServers | Inga |
> | servrar/topQueryStatistics | Inga |
> | servrar/virtualNetworkRules | Inga |
> | servrar/waitStatistics | Inga |
> | serversv2 | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

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
> | applicationgroups/program | Inga |
> | applicationgroups/Station ära datorer | Inga |
> | applicationgroups / startmenuitems | Inga |
> | hostpools | Ja |
> | hostpools / sessionhosts | Inga |
> | hostpools / sessionhosts / usersessions | Inga |
> | hostpools / usersessions | Inga |
> | arbets ytor | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ElasticPools | Ja |
> | ElasticPools / IotHubTenants | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Inga |
> | IotHubs | Ja |
> | IotHubs / eventGridFilters | Inga |
> | IotHubs/securitySettings | Inga |
> | ProvisioningServices | Ja |
> | användningar | Inga |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | pipelines | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domänkontrollanter | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labcenters | Ja |
> | Laboration | Ja |
> | labb/miljöer | Ja |
> | labb/serviceRunners | Ja |
> | labb/virtualMachines | Ja |
> | scheman | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | databaseAccountNames | Inga |
> | databaseAccounts | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Ja |
> | domäner/domainOwnershipIdentifiers | Inga |
> | generateSsoRequest | Inga |
> | topLevelDomains | Inga |
> | validateDomainRegistrationInformation | Inga |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | lcsprojects | Inga |
> | lcsprojects / clouddeployments | Inga |
> | lcsprojects/kopplingar | Inga |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Ja |
> | domäner/ämnen | Inga |
> | eventSubscriptions | Inga |
> | extensionTopics | Inga |
> | partnerNamespaces | Ja |
> | partnerNamespaces/eventChannels | Inga |
> | partnerRegistrations | Ja |
> | partnerTopics | Ja |
> | systemTopics | Ja |
> | systemTopics / eventSubscriptions | Inga |
> | avsnitt | Ja |
> | topicTypes | Inga |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | namn områden | Ja |
> | namnrymder/authorizationrules | Inga |
> | namnrymder/disasterrecoveryconfigs | Inga |
> | namnrymder/eventhubs | Inga |
> | namnrymder/eventhubs/authorizationrules | Inga |
> | namnrymder/eventhubs/consumergroups | Inga |
> | namnrymder/networkrulesets | Inga |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | Inga |
> | Providers | Inga |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certifiering | Inga |
> | galleryitems | Inga |
> | generateartifactaccessuri | Inga |
> | områden | Inga |
> | område/områden | Inga |
> | område/områden/områden | Inga |
> | område/områden/områden/galleryitems | Inga |
> | områdets/områden/galleryitems | Inga |
> | områden/galleryitems | Inga |
> | Registrera dig | Inga |
> | resurser | Inga |
> | retrieveresourcesbyid | Inga |

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autoManagedAccounts | Ja |
> | autoManagedVmConfigurationProfiles | Ja |
> | configurationProfileAssignments | Inga |
> | guestConfigurationAssignments | Inga |
> | IntelliPoint | Inga |
> | softwareUpdateProfile | Inga |
> | softwareUpdates | Inga |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hanaInstances | Ja |
> | sapMonitors | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedHSMs | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | kluster/program | Inga |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | faxar | Ja |
> | datorer/tillägg | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataManagers | Ja |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | delarna | Ja |
> | networkScopes | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utskrifts | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Inga |
> | diagnosticSettingsCategories | Inga |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appTemplates | Inga |
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
> | deletedVaults | Inga |
> | hsmPools | Ja |
> | valv | Ja |
> | valv/accessPolicies | Inga |
> | valv/eventGridFilters | Inga |
> | valv/hemligheter | Inga |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | kluster/attacheddatabaseconfigurations | Inga |
> | kluster/databaser | Inga |
> | kluster/databaser/dataconnections | Inga |
> | kluster/databaser/eventhubconnections | Inga |
> | kluster/databaser/principalassignments | Inga |
> | kluster/principalassignments | Inga |
> | kluster/sharedidentities | Inga |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labaccounts | Ja |
> | användare | Inga |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hostingEnvironments | Ja |
> | integrationAccounts | Ja |
> | integrationServiceEnvironments | Ja |
> | integrationServiceEnvironments/managedApis | Ja |
> | isolatedEnvironments | Ja |
> | arbetsflöden | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

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
> | arbets ytor/beräkningar | Inga |
> | arbets ytor/eventGridFilters | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | identiteter | Inga |
> | userAssignedIdentities | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Inga |
> | registrationAssignments | Inga |
> | registrationDefinitions | Inga |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | getEntities | Inga |
> | managementGroups | Inga |
> | resurser | Inga |
> | startTenantBackfill | Inga |
> | tenantBackfillStatus | Inga |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/eventGridFilters | Inga |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | budgivning | Inga |
> | offerTypes | Inga |
> | offerTypes/utgivare | Inga |
> | offerTypes/utgivare/erbjudanden | Inga |
> | offerTypes/utgivare/erbjudanden/planer | Inga |
> | offerTypes/utgivare/erbjudanden/planer/avtal | Inga |
> | offerTypes/utgivare/erbjudanden/planer/konfigurationer | Inga |
> | offerTypes/utgivare/erbjudanden/planer/configs/importImage | Inga |
> | privategalleryitems | Inga |
> | privateStoreClient | Inga |
> | läkemedle | Inga |
> | Utgivare | Inga |
> | Utgivare/erbjudanden | Inga |
> | Utgivare/erbjudanden/ändringar | Inga |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicDevServices | Ja |
> | updateCommunicationPreference | Inga |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | villkor | Inga |
> | offertypes | Inga |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Media Services | Ja |
> | Media Services/accountFilters | Inga |
> | Media Services/till gångar | Inga |
> | Media Services/assets/assetFilters | Inga |
> | Media Services/contentKeyPolicies | Inga |
> | Media Services/eventGridFilters | Inga |
> | Media Services/liveEventOperations | Inga |
> | Media Services/liveEvents | Ja |
> | Media Services/liveEvents/liveOutputs | Inga |
> | Media Services/liveOutputOperations | Inga |
> | Media Services/mediaGraphs | Inga |
> | Media Services/streamingEndpointOperations | Inga |
> | Media Services/strömnings slut punkter | Ja |
> | Media Services/streamingLocators | Inga |
> | Media Services/streamingPolicies | Inga |
> | Media Services/transformeringar | Inga |
> | Media Services/transformeringar/jobb | Inga |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appClusters | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | assessmentProjects | Ja |
> | migrateprojects | Ja |
> | moveCollections | Ja |
> | samarbetsprojekt | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ja |
> | objectUnderstandingAccounts | Ja |
> | remoteRenderingAccounts | Ja |
> | spatialAnchorsAccounts | Ja |
> | surfaceReconstructionAccounts | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | netAppAccounts | Ja |
> | netAppAccounts / capacityPools | Ja |
> | netAppAccounts/capacityPools/Volumes | Ja |
> | netAppAccounts/capacityPools/Volumes/mountTargets | Ja |
> | netAppAccounts/capacityPools/volym/ögonblicks bilder | Ja |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationGateways | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja |
> | applicationSecurityGroups | Ja |
> | azureFirewallFqdnTags | Inga |
> | azureFirewalls | Ja |
> | bastionHosts | Ja |
> | bgpServiceCommunities | Inga |
> | anslutning | Ja |
> | ddosCustomPolicies | Ja |
> | ddosProtectionPlans | Ja |
> | dnsOperationStatuses | Inga |
> | dnszones | Ja |
> | dnszones/A | Inga |
> | dnszones/AAAA | Inga |
> | dnszones/alla | Inga |
> | dnszones/CAA | Inga |
> | dnszones/CNAME | Inga |
> | dnszones/MX | Inga |
> | dnszones/NS | Inga |
> | dnszones/PTR | Inga |
> | dnszones/Recordset | Inga |
> | dnszones/SOA | Inga |
> | dnszones/SRV | Inga |
> | dnszones/TXT | Inga |
> | expressRouteCircuits | Ja |
> | expressRouteCrossConnections | Ja |
> | expressRouteGateways | Ja |
> | expressRoutePorts | Ja |
> | expressRouteServiceProviders | Inga |
> | firewallPolicies | Ja |
> | frontdoors | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Inga |
> | frontdoorWebApplicationFirewallPolicies | Ja |
> | getDnsResourceReference | Inga |
> | internalNotify | Inga |
> | Belastningsutjämnare | Ja |
> | localNetworkGateways | Ja |
> | natGateways | Ja |
> | networkIntentPolicies | Ja |
> | networkInterfaces | Ja |
> | networkProfiles | Ja |
> | networkSecurityGroups | Ja |
> | networkWatchers | Ja |
> | networkWatchers / connectionMonitors | Ja |
> | networkWatchers/linser | Ja |
> | networkWatchers / pingMeshes | Ja |
> | p2sVpnGateways | Ja |
> | privateDnsOperationStatuses | Inga |
> | privateDnsZones | Ja |
> | privateDnsZones/A | Inga |
> | privateDnsZones/AAAA | Inga |
> | privateDnsZones/alla | Inga |
> | privateDnsZones/CNAME | Inga |
> | privateDnsZones/MX | Inga |
> | privateDnsZones/PTR | Inga |
> | privateDnsZones/SOA | Inga |
> | privateDnsZones/SRV | Inga |
> | privateDnsZones/TXT | Inga |
> | privateDnsZones / virtualNetworkLinks | Ja |
> | privateEndpoints | Ja |
> | privateLinkServices | Ja |
> | publicIPAddresses | Ja |
> | publicIPPrefixes | Ja |
> | routeFilters | Ja |
> | routeTables | Ja |
> | serviceEndpointPolicies | Ja |
> | trafficManagerGeographicHierarchies | Inga |
> | trafficmanagerprofiles | Ja |
> | trafficmanagerprofiles/termiska kartor | Inga |
> | trafficManagerUserMetricsKeys | Inga |
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

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | osNamespaces | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | HyperVSites | Ja |
> | ImportSites | Ja |
> | ServerSites | Ja |
> | VMwareSites | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | enheter | Inga |
> | linkTargets | Inga |
> | storageInsightConfigs | Inga |
> | arbets ytor | Ja |
> | arbets ytor/dataExports | Inga |
> | arbets ytor/data källor | Inga |
> | arbets ytor/linkedServices | Inga |
> | arbets ytor/privateEndpointConnectionProxies | Inga |
> | arbets ytor/privateEndpointConnections | Inga |
> | arbets ytor/privateLinkResources | Inga |
> | arbets ytor/fråga | Inga |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managementassociations | Inga |
> | managementconfigurations | Ja |
> | lösningar | Ja |
> | vyer | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | legacyPeerings | Inga |
> | peerAsns | Inga |
> | peerings | Ja |
> | peeringServiceProviders | Inga |
> | peeringServices | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | policyEvents | Inga |
> | policyMetadata | Inga |
> | policyStates | Inga |
> | policyTrackedResources | Inga |
> | reparationer | Inga |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konsoler | Inga |
> | instrument paneler | Ja |
> | userSettings | Inga |

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

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | backupProtectedItems | Inga |
> | valv | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja |
> | namnrymder/authorizationrules | Inga |
> | namnrymder/hybridconnections | Inga |
> | namnrymder/hybridconnections/authorizationrules | Inga |
> | namnrymder/wcfrelays | Inga |
> | namnrymder/wcfrelays/authorizationrules | Inga |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Inga |
> | Samlingar | Ja |
> | Samlingar/program | Inga |
> | Samlingar/securityprincipals | Inga |
> | templateImages | Inga |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | frågor | Ja |
> | resourceChangeDetails | Inga |
> | resourceChanges | Inga |
> | resurser | Inga |
> | resourcesHistory | Inga |
> | subscriptionsStatus | Inga |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilityStatuses | Inga |
> | childAvailabilityStatuses | Inga |
> | childResources | Inga |
> | emergingissues | Inga |
> | händelser | Inga |
> | impactedResources | Inga |
> | metadata | Inga |
> | Ansökningar | Inga |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | distributioner | Inga |
> | distributioner/åtgärder | Inga |
> | deploymentScripts | Ja |
> | deploymentScripts/loggar | Inga |
> | Länkar | Inga |
> | notifyResourceJobs | Inga |
> | Providers | Inga |
> | resourceGroups | Inga |
> | prenumerationer | Inga |
> | klienter | Inga |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | saasresources | Inga |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | förfrågningsåtgärder | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | resourceHealthMetadata | Inga |
> | searchServices | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Inga |
> | advancedThreatProtectionSettings | Inga |
> | aviseringar | Inga |
> | allowedConnections | Inga |
> | applicationWhitelistings | Inga |
> | assessmentMetadata | Inga |
> | utvärderingar | Inga |
> | autoDismissAlertsRules | Inga |
> | automatiseringar | Ja |
> | AutoProvisioningSettings | Inga |
> | Godkännanden | Inga |
> | dataCollectionAgents | Inga |
> | deviceSecurityGroups | Inga |
> | discoveredSecuritySolutions | Inga |
> | externalSecuritySolutions | Inga |
> | InformationProtectionPolicies | Inga |
> | iotSecuritySolutions | Ja |
> | iotSecuritySolutions / analyticsModels | Inga |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Inga |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Inga |
> | jitNetworkAccessPolicies | Inga |
> | networkData | Inga |
> | rikt | Inga |
> | prissättningar | Inga |
> | regulatoryComplianceStandards | Inga |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Inga |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Inga |
> | securityContacts | Inga |
> | securitySolutions | Inga |
> | securitySolutionsReferenceData | Inga |
> | securityStatuses | Inga |
> | securityStatusesSummaries | Inga |
> | serverVulnerabilityAssessments | Inga |
> | settings | Inga |
> | underbedömningar | Inga |
> | uppgifter | Inga |
> | topologier | Inga |
> | workspaceSettings | Inga |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Inga |
> | diagnosticSettingsCategories | Inga |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | agg regeringar | Inga |
> | alertRules | Inga |
> | alertRuleTemplates | Inga |
> | dina | Inga |
> | fall | Inga |
> | dataConnectors | Inga |
> | poster | Inga |
> | entityQueries | Inga |
> | officeConsents | Inga |
> | settings | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja |
> | namnrymder/authorizationrules | Inga |
> | namnrymder/disasterrecoveryconfigs | Inga |
> | namnrymder/eventgridfilters | Inga |
> | namnrymder/networkrulesets | Inga |
> | namnrymder/köer | Inga |
> | namnrymder/köer/authorizationrules | Inga |
> | namn områden/ämnen | Inga |
> | namnrymder/ämnen/authorizationrules | Inga |
> | namnrymder/ämnen/prenumerationer | Inga |
> | namn områden/ämnen/prenumerationer/regler | Inga |
> | premiumMessagingRegions | Inga |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | kluster | Ja |
> | kluster/program | Inga |
> | containerGroups | Ja |
> | containerGroupSets | Ja |
> | edgeclusters | Ja |
> | edgeclusters/program | Inga |
> | nätet | Ja |
> | secretstores | Ja |
> | secretstores/certifikat | Inga |
> | secretstores/hemligheter | Inga |
> | volumes | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | containerGroups | Ja |
> | gatewayer | Ja |
> | nätet | Ja |
> | secrets | Ja |
> | volumes | Ja |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | Inga |
> | providerRegistrations / resourceTypeRegistrations | Inga |
> | distributioner | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SignalR | Ja |
> | SignalR/eventGridFilters | Inga |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SiteRecoveryVault | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridUseBenefits | Inga |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationDefinitions | Ja |
> | program | Ja |
> | jitRequests | Ja |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedInstances | Ja |
> | managedInstances/databaser | Ja |
> | managedInstances/databaser/backupShortTermRetentionPolicies | Inga |
> | managedInstances/databaser/scheman/tabeller/kolumner/sensitivityLabels | Inga |
> | managedInstances/databaser/vulnerabilityAssessments | Inga |
> | managedInstances/databaser/vulnerabilityAssessments/regler/bas linjer | Inga |
> | managedInstances / encryptionProtector | Inga |
> | managedInstances/nycklar | Inga |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Inga |
> | managedInstances / vulnerabilityAssessments | Inga |
> | servrar | Ja |
> | servrar/administratörer | Inga |
> | servrar/communicationLinks | Inga |
> | servrar/databaser | Ja |
> | servrar/encryptionProtector | Inga |
> | servrar/firewallRules | Inga |
> | servrar/nycklar | Inga |
> | servrar/restorableDroppedDatabases | Inga |
> | servrar/serviceobjectives | Inga |
> | servrar/tdeCertificates | Inga |
> | virtualClusters | Inga |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Inga |
> | SqlVirtualMachines | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageAccounts | Ja |
> | storageAccounts/blobServices | Inga |
> | storageAccounts/fileServices | Inga |
> | storageAccounts/queueServices | Inga |
> | storageAccounts/tjänster | Inga |
> | storageAccounts/tjänster/metricDefinitions | Inga |
> | storageAccounts/tableServices | Inga |
> | användningar | Inga |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cacheminnen | Ja |
> | cache-/storageTargets | Inga |
> | usageModels | Inga |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | replicationGroups | Inga |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Inga |
> | storageSyncServices / syncGroups | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga |
> | storageSyncServices/arbets flöden | Inga |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Inga |
> | storageSyncServices / syncGroups | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga |
> | storageSyncServices/arbets flöden | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Inga |
> | storageSyncServices / syncGroups | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga |
> | storageSyncServices/arbets flöden | Inga |

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

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Avbryt | Inga |
> | CreateSubscription | Inga |
> | Använd | Inga |
> | ta | Inga |
> | SubscriptionDefinitions | Inga |
> | SubscriptionOperations | Inga |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Ja |
> | miljöer/accessPolicies | Inga |
> | miljöer/eventsources | Ja |
> | miljöer/referenceDataSets | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ja |
> | dedicatedCloudServices | Ja |
> | virtualMachines | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | enheter | Ja |
> | leverantören | Inga |
> | leverantörer/SKU: er | Inga |
> | vnfs | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | apiManagementAccounts | Inga |
> | apiManagementAccounts / apiAcls | Inga |
> | apiManagementAccounts/API: er | Inga |
> | apiManagementAccounts/API/apiAcls | Inga |
> | apiManagementAccounts/API/connectionAcls | Inga |
> | apiManagementAccounts/API/anslutningar | Inga |
> | apiManagementAccounts/API/Connections/connectionAcls | Inga |
> | apiManagementAccounts/API/localizedDefinitions | Inga |
> | apiManagementAccounts / connectionAcls | Inga |
> | apiManagementAccounts/anslutningar | Inga |
> | billingMeters | Inga |
> | intyg | Ja |
> | connectionGateways | Ja |
> | anslutning | Ja |
> | customApis | Ja |
> | deletedSites | Inga |
> | hostingEnvironments | Ja |
> | hostingEnvironments / eventGridFilters | Inga |
> | hostingEnvironments / multiRolePools | Inga |
> | hostingEnvironments / workerPools | Inga |
> | publishingUsers | Inga |
> | rekommenderade | Inga |
> | resourceHealthMetadata | Inga |
> | körningar | Inga |
> | serverFarms | Ja |
> | Server grupper/eventGridFilters | Inga |
> | stationer | Ja |
> | platser/konfig  | Inga |
> | platser/eventGridFilters | Inga |
> | platser/hostNameBindings | Inga |
> | platser/networkConfig | Inga |
> | platser/premieraddons | Ja |
> | platser/platser | Ja |
> | platser/platser/eventGridFilters | Inga |
> | platser/platser/hostNameBindings | Inga |
> | platser/platser/networkConfig | Inga |
> | sourceControls | Inga |
> | staticSites | Ja |
> | kontrollerar | Inga |
> | verifyHostingEnvironmentVnet | Inga |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Inga |
> | diagnosticSettingsCategories | Inga |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DeviceServices | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | delarna | Inga |
> | componentsSummary | Inga |
> | monitorInstances | Inga |
> | monitorInstancesSummary | Inga |
> | Övervakare | Inga |
> | notificationSettings | Inga |

## <a name="next-steps"></a>Nästa steg

Hämta samma data som en fil med kommaavgränsade värden genom att ladda ned [Complete-mode-deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
