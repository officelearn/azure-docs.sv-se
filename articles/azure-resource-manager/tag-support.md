---
title: Tag support for resources
description: Shows which Azure resource types support tags. Provides details for all Azure services.
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 5b3e26d914887496eedde609404eaf0b380dbcc0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422136"
---
# <a name="tag-support-for-azure-resources"></a>Tag support for Azure resources
This article describes whether a resource type supports [tags](resource-group-using-tags.md). The column labeled **Supports tags** indicates whether the resource type has a property for the tag. The column labeled **Tag in cost report** indicates whether that resource type passes the tag to the cost report. You can view costs by tags in the [Cost Management cost analysis](../cost-management/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) and the [Azure billing invoice and daily usage data](../billing/billing-download-azure-invoice-daily-usage-date.md).

To get the same data as a file of comma-separated values, download [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices / oucontainer | Nej | Nej |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nej | Nej |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nej | Nej |
> | addsservices | Nej | Nej |
> | agents | Nej | Nej |
> | anonymousapiusers | Nej | Nej |
> | konfiguration | Nej | Nej |
> | loggar | Nej | Nej |
> | reports | Nej | Nej |
> | servicehealthmetrics | Nej | Nej |
> | services | Nej | Nej |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurations | Nej | Nej |
> | generateRecommendations | Nej | Nej |
> | metadata | Nej | Nej |
> | recommendations | Nej | Nej |
> | suppressions | Nej | Nej |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | actionRules | Ja | Ja |
> | aviseringar | Nej | Nej |
> | alertsList | Nej | Nej |
> | alertsMetaData | Nej | Nej |
> | alertsSummary | Nej | Nej |
> | alertsSummaryList | Nej | Nej |
> | feedback | Nej | Nej |
> | smartDetectorAlertRules | Ja | Ja |
> | smartDetectorRuntimeEnvironments | Nej | Nej |
> | smartGroups | Nej | Nej |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nej | Nej |
> | tjänst | Ja | Ja |
> | validateServiceName | Nej | Nej |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores / eventGridFilters | Nej | Nej |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Nej | Nej |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Nej | Nej |
> | dataAliases | Nej | Nej |
> | denyAssignments | Nej | Nej |
> | elevateAccess | Nej | Nej |
> | findOrphanRoleAssignments | Nej | Nej |
> | locks | Nej | Nej |
> | permissions | Nej | Nej |
> | policyAssignments | Nej | Nej |
> | policyDefinitions | Nej | Nej |
> | policySetDefinitions | Nej | Nej |
> | providerOperations | Nej | Nej |
> | roleAssignments | Nej | Nej |
> | roleDefinitions | Nej | Nej |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ja | Ja |
> | automationAccounts / configurations | Ja | Ja |
> | automationAccounts / jobs | Nej | Nej |
> | automationAccounts / runbooks | Ja | Ja |
> | automationAccounts / softwareUpdateConfigurations | Nej | Nej |
> | automationAccounts / webhooks | Nej | Nej |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores / eventGridFilters | Nej | Nej |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Nej | Nej |
> | environments / accounts | Nej | Nej |
> | environments / accounts / namespaces | Nej | Nej |
> | environments / accounts / namespaces / configurations | Nej | Nej |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ja | Nej |
> | b2ctenants | Nej | Nej |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Ja | Ja |
> | postgresInstances | Ja | Ja |
> | sqlBigDataClusters | Ja | Ja |
> | sqlInstances | Ja | Ja |
> | sqlServerRegistrations | Ja | Ja |
> | sqlServerRegistrations / sqlServers | Nej | Nej |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registrations | Ja | Ja |
> | registrations / customerSubscriptions | Nej | Nej |
> | registrations / products | Nej | Nej |
> | verificationKeys | Nej | Nej |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nej | Nej |
> | billingAccounts / agreements | Nej | Nej |
> | billingAccounts / billingPermissions | Nej | Nej |
> | billingAccounts / billingProfiles | Nej | Nej |
> | billingAccounts / billingProfiles / billingPermissions | Nej | Nej |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nej | Nej |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nej | Nej |
> | billingAccounts / billingProfiles / billingSubscriptions | Nej | Nej |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nej | Nej |
> | billingAccounts / billingProfiles / customers | Nej | Nej |
> | billingAccounts / billingProfiles / invoices | Nej | Nej |
> | billingAccounts / billingProfiles / invoices / pricesheet | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / products | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Nej | Nej |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nej | Nej |
> | billingAccounts / BillingProfiles / patchOperations | Nej | Nej |
> | billingAccounts / billingProfiles / paymentMethods | Nej | Nej |
> | billingAccounts / billingProfiles / policies | Nej | Nej |
> | billingAccounts / billingProfiles / pricesheet | Nej | Nej |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nej | Nej |
> | billingAccounts / billingProfiles / products | Nej | Nej |
> | billingAccounts / billingProfiles / transactions | Nej | Nej |
> | billingAccounts / billingRoleAssignments | Nej | Nej |
> | billingAccounts / billingRoleDefinitions | Nej | Nej |
> | billingAccounts / billingSubscriptions | Nej | Nej |
> | billingAccounts / billingSubscriptions / invoices | Nej | Nej |
> | billingAccounts / createBillingRoleAssignment | Nej | Nej |
> | billingAccounts / createInvoiceSectionOperations | Nej | Nej |
> | billingAccounts / customers | Nej | Nej |
> | billingAccounts / customers / billingPermissions | Nej | Nej |
> | billingAccounts / customers / billingSubscriptions | Nej | Nej |
> | billingAccounts / customers / initiateTransfer | Nej | Nej |
> | billingAccounts / customers / policies | Nej | Nej |
> | billingAccounts / customers / products | Nej | Nej |
> | billingAccounts / customers / transactions | Nej | Nej |
> | billingAccounts / customers / transfers | Nej | Nej |
> | billingAccounts / departments | Nej | Nej |
> | billingAccounts / enrollmentAccounts | Nej | Nej |
> | billingAccounts / invoices | Nej | Nej |
> | billingAccounts / invoiceSections | Nej | Nej |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nej | Nej |
> | billingAccounts / invoiceSections / billingSubscriptions | Nej | Nej |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Nej | Nej |
> | billingAccounts / invoiceSections / elevate | Nej | Nej |
> | billingAccounts / invoiceSections / initiateTransfer | Nej | Nej |
> | billingAccounts / invoiceSections / patchOperations | Nej | Nej |
> | billingAccounts / invoiceSections / productMoveOperations | Nej | Nej |
> | billingAccounts / invoiceSections / products | Nej | Nej |
> | billingAccounts / invoiceSections / products / transfer | Nej | Nej |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Nej | Nej |
> | billingAccounts / invoiceSections / transactions | Nej | Nej |
> | billingAccounts / invoiceSections / transfers | Nej | Nej |
> | billingAccounts / lineOfCredit | Nej | Nej |
> | billingAccounts / patchOperations | Nej | Nej |
> | billingAccounts / paymentMethods | Nej | Nej |
> | billingAccounts / products | Nej | Nej |
> | billingAccounts / transactions | Nej | Nej |
> | billingPeriods | Nej | Nej |
> | billingPermissions | Nej | Nej |
> | billingProperty | Nej | Nej |
> | billingRoleAssignments | Nej | Nej |
> | billingRoleDefinitions | Nej | Nej |
> | createBillingRoleAssignment | Nej | Nej |
> | departments | Nej | Nej |
> | enrollmentAccounts | Nej | Nej |
> | invoices | Nej | Nej |
> | transfers | Nej | Nej |
> | transfers / acceptTransfer | Nej | Nej |
> | transfers / declineTransfer | Nej | Nej |
> | transfers / operationStatus | Nej | Nej |
> | transfers / validateTransfer | Nej | Nej |
> | validateAddress | Nej | Nej |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mapApis | Ja | Ja |
> | updateCommunicationPreference | Nej | Nej |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ja | Ja |
> | cordaMembers | Ja | Ja |
> | tittare | Ja | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nej | Nej |
> | blueprintAssignments / assignmentOperations | Nej | Nej |
> | blueprintAssignments / operations | Nej | Nej |
> | blueprints | Nej | Nej |
> | blueprints / artifacts | Nej | Nej |
> | blueprints / versions | Nej | Nej |
> | blueprints / versions / artifacts | Nej | Nej |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices / channels | Nej | Nej |
> | botServices / connections | Nej | Nej |
> | languages | Nej | Nej |
> | mallar | Nej | Nej |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |
> | RedisConfigDefinition | Nej | Nej |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nej | Nej |
> | calculateExchange | Nej | Nej |
> | calculatePrice | Nej | Nej |
> | calculatePurchasePrice | Nej | Nej |
> | catalogs | Nej | Nej |
> | commercialReservationOrders | Nej | Nej |
> | exchange | Nej | Nej |
> | placePurchaseOrder | Nej | Nej |
> | reservationOrders | Nej | Nej |
> | reservationOrders / calculateRefund | Nej | Nej |
> | reservationOrders / merge | Nej | Nej |
> | reservationOrders / reservations | Nej | Nej |
> | reservationOrders / reservations / revisions | Nej | Nej |
> | reservationOrders / return | Nej | Nej |
> | reservationOrders / split | Nej | Nej |
> | reservationOrders / swap | Nej | Nej |
> | reservations | Nej | Nej |
> | resources | Nej | Nej |
> | validateReservationOrder | Nej | Nej |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nej | Nej |
> | CdnWebApplicationFirewallPolicies | Ja | Ja |
> | edgenodes | Nej | Nej |
> | profiles | Ja | Ja |
> | profiles / endpoints | Ja | Ja |
> | profiles / endpoints / customdomains | Nej | Nej |
> | profiles / endpoints / origins | Nej | Nej |
> | validateProbe | Nej | Nej |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ja | Ja |
> | certificateOrders / certificates | Nej | Nej |
> | validateCertificateRegistrationInformation | Nej | Nej |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nej | Nej |
> | domainNames | Nej | Nej |
> | domainNames / capabilities | Nej | Nej |
> | domainNames / internalLoadBalancers | Nej | Nej |
> | domainNames / serviceCertificates | Nej | Nej |
> | domainNames / slots | Nej | Nej |
> | domainNames / slots / roles | Nej | Nej |
> | domainNames / slots / roles / metricDefinitions | Nej | Nej |
> | domainNames / slots / roles / metrics | Nej | Nej |
> | moveSubscriptionResources | Nej | Nej |
> | operatingSystemFamilies | Nej | Nej |
> | operatingSystems | Nej | Nej |
> | quotas | Nej | Nej |
> | resourceTypes | Nej | Nej |
> | validateSubscriptionMoveAvailability | Nej | Nej |
> | virtualMachines | Nej | Nej |
> | virtualMachines / diagnosticSettings | Nej | Nej |
> | virtualMachines / metricDefinitions | Nej | Nej |
> | virtualMachines / metrics | Nej | Nej |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nej | Nej |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nej | Nej |
> | expressRouteCrossConnections | Nej | Nej |
> | expressRouteCrossConnections / peerings | Nej | Nej |
> | gatewaySupportedDevices | Nej | Nej |
> | networkSecurityGroups | Nej | Nej |
> | quotas | Nej | Nej |
> | reservedIps | Nej | Nej |
> | virtualNetworks | Nej | Nej |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nej | Nej |
> | virtualNetworks / virtualNetworkPeerings | Nej | Nej |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nej | Nej |
> | disks | Nej | Nej |
> | images | Nej | Nej |
> | osImages | Nej | Nej |
> | osPlatformImages | Nej | Nej |
> | publicImages | Nej | Nej |
> | quotas | Nej | Nej |
> | storageAccounts | Nej | Nej |
> | storageAccounts / blobServices | Nej | Nej |
> | storageAccounts / fileServices | Nej | Nej |
> | storageAccounts / metricDefinitions | Nej | Nej |
> | storageAccounts / metrics | Nej | Nej |
> | storageAccounts / queueServices | Nej | Nej |
> | storageAccounts / services | Nej | Nej |
> | storageAccounts / services / diagnosticSettings | Nej | Nej |
> | storageAccounts / services / metricDefinitions | Nej | Nej |
> | storageAccounts / services / metrics | Nej | Nej |
> | storageAccounts / tableServices | Nej | Nej |
> | storageAccounts / vmImages | Nej | Nej |
> | vmImages | Nej | Nej |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | RateCard | Nej | Nej |
> | UsageAggregates | Nej | Nej |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | disks | Ja | Ja |
> | galleries | Ja | Ja |
> | galleries / applications | Nej | Nej |
> | galleries / applications / versions | Nej | Nej |
> | galleries / images | Nej | Nej |
> | galleries / images / versions | Nej | Nej |
> | hostGroups | Ja | Ja |
> | hostGroups / hosts | Ja | Ja |
> | images | Ja | Ja |
> | proximityPlacementGroups | Ja | Ja |
> | restorePointCollections | Ja | Ja |
> | restorePointCollections / restorePoints | Nej | Nej |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages / versions | Nej | Nej |
> | snapshots | Ja | Ja |
> | virtualMachines | Ja | Ja |
> | virtualMachines / extensions | Ja | Ja |
> | virtualMachines / metricDefinitions | Nej | Nej |
> | virtualMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets / extensions | Nej | Nej |
> | virtualMachineScaleSets / networkInterfaces | Nej | Nej |
> | virtualMachineScaleSets / publicIPAddresses | Nej | Nej |
> | virtualMachineScaleSets / virtualMachines | Nej | Nej |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nej | Nej |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nej | Nej |
> | Saldon | Nej | Nej |
> | Budgetar | Nej | Nej |
> | Avgifter | Nej | Nej |
> | CostTags | Nej | Nej |
> | credits | Nej | Nej |
> | evenemang | Nej | Nej |
> | Prognoser | Nej | Nej |
> | lots | Nej | Nej |
> | Marketplaces | Nej | Nej |
> | Pricesheets | Nej | Nej |
> | products | Nej | Nej |
> | ReservationDetails | Nej | Nej |
> | ReservationRecommendations | Nej | Nej |
> | ReservationSummaries | Nej | Nej |
> | ReservationTransactions | Nej | Nej |
> | Taggar | Nej | Nej |
> | tenants | Nej | Nej |
> | Villkor | Nej | Nej |
> | UsageDetails | Nej | Nej |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ja | Ja |
> | serviceAssociationLinks | Nej | Nej |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registries | Ja | Ja |
> | registries / builds | Nej | Nej |
> | registries / builds / cancel | Nej | Nej |
> | registries / builds / getLogLink | Nej | Nej |
> | registries / buildTasks | Ja | Ja |
> | registries / buildTasks / steps | Nej | Nej |
> | registries / eventGridFilters | Nej | Nej |
> | registries / generateCredentials | Nej | Nej |
> | registries / getBuildSourceUploadUrl | Nej | Nej |
> | registries / GetCredentials | Nej | Nej |
> | registries / importImage | Nej | Nej |
> | registries / queueBuild | Nej | Nej |
> | registries / regenerateCredential | Nej | Nej |
> | registries / regenerateCredentials | Nej | Nej |
> | registries / replications | Ja | Ja |
> | registries / runs | Nej | Nej |
> | registries / runs / cancel | Nej | Nej |
> | registries / scheduleRun | Nej | Nej |
> | registries / scopeMaps | Nej | Nej |
> | registries / taskRuns | Ja | Ja |
> | registries / tasks | Ja | Ja |
> | registries / tokens | Nej | Nej |
> | registries / updatePolicies | Nej | Nej |
> | registries / webhooks | Ja | Ja |
> | registries / webhooks / getCallbackConfig | Nej | Nej |
> | registries / webhooks / ping | Nej | Nej |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | openShiftManagedClusters | Ja | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Aviseringar | Nej | Nej |
> | BillingAccounts | Nej | Nej |
> | Budgetar | Nej | Nej |
> | CloudConnectors | Nej | Nej |
> | Anslutningsprogram | Ja | Ja |
> | Departments | Nej | Nej |
> | Dimensioner | Nej | Nej |
> | EnrollmentAccounts | Nej | Nej |
> | Exporter | Nej | Nej |
> | ExternalBillingAccounts | Nej | Nej |
> | ExternalBillingAccounts / Alerts | Nej | Nej |
> | ExternalBillingAccounts / Dimensions | Nej | Nej |
> | ExternalBillingAccounts / Forecast | Nej | Nej |
> | ExternalBillingAccounts / Query | Nej | Nej |
> | ExternalSubscriptions | Nej | Nej |
> | ExternalSubscriptions / Alerts | Nej | Nej |
> | ExternalSubscriptions / Dimensions | Nej | Nej |
> | ExternalSubscriptions / Forecast | Nej | Nej |
> | ExternalSubscriptions / Query | Nej | Nej |
> | Forecast | Nej | Nej |
> | Söka i data | Nej | Nej |
> | register | Nej | Nej |
> | Reportconfigs | Nej | Nej |
> | Rapporter | Nej | Nej |
> | Inställningar | Nej | Nej |
> | showbackRules | Nej | Nej |
> | Vyer | Nej | Nej |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | requests | Nej | Nej |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | associations | Nej | Nej |
> | resourceProviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Ja | Nej |
> | workspaces / virtualNetworkPeerings | Nej | Nej |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | catalogs | Ja | Ja |
> | datacatalogs | Ja | Ja |
> | datacatalogs / datasources | Nej | Nej |
> | datacatalogs / datasources / scans | Nej | Nej |
> | datacatalogs / datasources / scans / datasets | Nej | Nej |
> | datacatalogs / datasources / scans / triggers | Nej | Nej |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataFactories | Ja | Nej |
> | dataFactories / diagnosticSettings | Nej | Nej |
> | dataFactories / metricDefinitions | Nej | Nej |
> | dataFactorySchema | Nej | Nej |
> | factories | Ja | Nej |
> | factories / integrationRuntimes | Nej | Nej |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | accounts / dataLakeStoreAccounts | Nej | Nej |
> | accounts / storageAccounts | Nej | Nej |
> | accounts / storageAccounts / containers | Nej | Nej |
> | accounts / transferAnalyticsUnits | Nej | Nej |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | accounts / eventGridFilters | Nej | Nej |
> | accounts / firewallRules | Nej | Nej |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Nej | Nej |
> | services / projects | Nej | Nej |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | accounts / shares | Nej | Nej |
> | accounts / shares / datasets | Nej | Nej |
> | accounts / shares / invitations | Nej | Nej |
> | accounts / shares / providersharesubscriptions | Nej | Nej |
> | accounts / shares / synchronizationSettings | Nej | Nej |
> | accounts / sharesubscriptions | Nej | Nej |
> | accounts / sharesubscriptions / consumerSourceDataSets | Nej | Nej |
> | accounts / sharesubscriptions / datasetmappings | Nej | Nej |
> | accounts / sharesubscriptions / triggers | Nej | Nej |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servrar | Ja | Ja |
> | servers / advisors | Nej | Nej |
> | servers / privateEndpointConnectionProxies | Nej | Nej |
> | servers / privateEndpointConnections | Nej | Nej |
> | servers / privateLinkResources | Nej | Nej |
> | servers / queryTexts | Nej | Nej |
> | servers / recoverableServers | Nej | Nej |
> | servers / topQueryStatistics | Nej | Nej |
> | servers / virtualNetworkRules | Nej | Nej |
> | servers / waitStatistics | Nej | Nej |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servrar | Ja | Ja |
> | servers / advisors | Nej | Nej |
> | servers / privateEndpointConnectionProxies | Nej | Nej |
> | servers / privateEndpointConnections | Nej | Nej |
> | servers / privateLinkResources | Nej | Nej |
> | servers / queryTexts | Nej | Nej |
> | servers / recoverableServers | Nej | Nej |
> | servers / topQueryStatistics | Nej | Nej |
> | servers / virtualNetworkRules | Nej | Nej |
> | servers / waitStatistics | Nej | Nej |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serverGroups | Ja | Ja |
> | servrar | Ja | Ja |
> | servers / advisors | Nej | Nej |
> | servers / keys | Nej | Nej |
> | servers / privateEndpointConnectionProxies | Nej | Nej |
> | servers / privateEndpointConnections | Nej | Nej |
> | servers / privateLinkResources | Nej | Nej |
> | servers / queryTexts | Nej | Nej |
> | servers / recoverableServers | Nej | Nej |
> | servers / topQueryStatistics | Nej | Nej |
> | servers / virtualNetworkRules | Nej | Nej |
> | servers / waitStatistics | Nej | Nej |
> | serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | artifactSources | Ja | Ja |
> | rollouts | Ja | Ja |
> | serviceTopologies | Ja | Ja |
> | serviceTopologies / services | Ja | Ja |
> | serviceTopologies / services / serviceUnits | Ja | Ja |
> | steg | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Ja | Ja |
> | applicationgroups / applications | Nej | Nej |
> | applicationgroups / desktops | Nej | Nej |
> | applicationgroups / startmenuitems | Nej | Nej |
> | hostpools | Ja | Ja |
> | hostpools / sessionhosts | Nej | Nej |
> | hostpools / sessionhosts / usersessions | Nej | Nej |
> | hostpools / usersessions | Nej | Nej |
> | workspaces | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools / IotHubTenants | Ja | Ja |
> | IotHubs | Ja | Ja |
> | IotHubs / eventGridFilters | Nej | Nej |
> | ProvisioningServices | Ja | Ja |
> | usages | Nej | Nej |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | pipelines | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | controllers | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labcenters | Ja | Ja |
> | labs | Ja | Ja |
> | labs / environments | Ja | Ja |
> | labs / serviceRunners | Ja | Ja |
> | labs / virtualMachines | Ja | Ja |
> | schedules | Ja | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nej | Nej |
> | databaseAccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Ja | Ja |
> | domains / domainOwnershipIdentifiers | Nej | Nej |
> | generateSsoRequest | Nej | Nej |
> | topLevelDomains | Nej | Nej |
> | validateDomainRegistrationInformation | Nej | Nej |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nej | Nej |
> | lcsprojects / clouddeployments | Nej | Nej |
> | lcsprojects / connectors | Nej | Nej |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Ja | Ja |
> | domains / topics | Nej | Nej |
> | eventSubscriptions | Nej | Nej |
> | extensionTopics | Nej | Nej |
> | topics | Ja | Ja |
> | topicTypes | Nej | Nej |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | namespaces | Ja | Ja |
> | namespaces / authorizationrules | Nej | Nej |
> | namespaces / disasterrecoveryconfigs | Nej | Nej |
> | namespaces / eventhubs | Nej | Nej |
> | namespaces / eventhubs / authorizationrules | Nej | Nej |
> | namespaces / eventhubs / consumergroups | Nej | Nej |
> | namespaces / networkrulesets | Nej | Nej |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | funktioner | Nej | Nej |
> | providers | Nej | Nej |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | enroll | Nej | Nej |
> | galleryitems | Nej | Nej |
> | generateartifactaccessuri | Nej | Nej |
> | myareas | Nej | Nej |
> | myareas / areas | Nej | Nej |
> | myareas / areas / areas | Nej | Nej |
> | myareas / areas / areas / galleryitems | Nej | Nej |
> | myareas / areas / galleryitems | Nej | Nej |
> | myareas / galleryitems | Nej | Nej |
> | register | Nej | Nej |
> | resources | Nej | Nej |
> | retrieveresourcesbyid | Nej | Nej |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | Nej | Nej |
> | guestConfigurationAssignments | Nej | Nej |
> | software | Nej | Nej |
> | softwareUpdateProfile | Nej | Nej |
> | softwareUpdates | Nej | Nej |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ja | Ja |
> | sapMonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters / applications | Nej | Nej |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | machines | Ja | Ja |
> | machines / extensions | Ja | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataManagers | Ja | Ja |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Ja | Ja |
> | networkScopes | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nej | Nej |
> | diagnosticSettingsCategories | Nej | Nej |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nej | Nej |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nej | Nej |
> | hsmPools | Ja | Ja |
> | vaults | Ja | Ja |
> | vaults / accessPolicies | Nej | Nej |
> | vaults / eventGridFilters | Nej | Nej |
> | vaults / secrets | Nej | Nej |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | clusters / attacheddatabaseconfigurations | Nej | Nej |
> | clusters / databases | Nej | Nej |
> | clusters / databases / dataconnections | Nej | Nej |
> | clusters / databases / eventhubconnections | Nej | Nej |
> | clusters / sharedidentities | Nej | Nej |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Ja |
> | användare | Nej | Nej |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ja | Ja |
> | integrationAccounts | Ja | Ja |
> | integrationServiceEnvironments | Ja | Ja |
> | integrationServiceEnvironments / managedApis | Ja | Ja |
> | isolatedEnvironments | Ja | Ja |
> | workflows | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ja | Ja |
> | webServices | Ja | Ja |
> | Arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Ja | Ja |
> | workspaces / computes | Nej | Nej |
> | workspaces / eventGridFilters | Nej | Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Identities | Nej | Nej |
> | userAssignedIdentities | Ja | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nej | Nej |
> | registrationAssignments | Nej | Nej |
> | registrationDefinitions | Nej | Nej |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | getEntities | Nej | Nej |
> | managementGroups | Nej | Nej |
> | resources | Nej | Nej |
> | startTenantBackfill | Nej | Nej |
> | tenantBackfillStatus | Nej | Nej |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | accounts / eventGridFilters | Nej | Nej |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | offers | Nej | Nej |
> | offerTypes | Nej | Nej |
> | offerTypes / publishers | Nej | Nej |
> | offerTypes / publishers / offers | Nej | Nej |
> | offerTypes / publishers / offers / plans | Nej | Nej |
> | offerTypes / publishers / offers / plans / agreements | Nej | Nej |
> | offerTypes / publishers / offers / plans / configs | Nej | Nej |
> | offerTypes / publishers / offers / plans / configs / importImage | Nej | Nej |
> | privategalleryitems | Nej | Nej |
> | products | Nej | Nej |
> | publishers | Nej | Nej |
> | publishers / offers | Nej | Nej |
> | publishers / offers / amendments | Nej | Nej |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | updateCommunicationPreference | Nej | Nej |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | agreements | Nej | Nej |
> | offertypes | Nej | Nej |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mediaservices | Ja | Ja |
> | mediaservices / accountFilters | Nej | Nej |
> | mediaservices / assets | Nej | Nej |
> | mediaservices / assets / assetFilters | Nej | Nej |
> | mediaservices / contentKeyPolicies | Nej | Nej |
> | mediaservices / eventGridFilters | Nej | Nej |
> | mediaservices / liveEventOperations | Nej | Nej |
> | mediaservices / liveEvents | Ja | Ja |
> | mediaservices / liveEvents / liveOutputs | Nej | Nej |
> | mediaservices / liveOutputOperations | Nej | Nej |
> | mediaservices / mediaGraphs | Nej | Nej |
> | mediaservices / streamingEndpointOperations | Nej | Nej |
> | mediaservices / streamingEndpoints | Ja | Ja |
> | mediaservices / streamingLocators | Nej | Nej |
> | mediaservices / streamingPolicies | Nej | Nej |
> | mediaservices / transforms | Nej | Nej |
> | mediaservices / transforms / jobs | Nej | Nej |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appClusters | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Ja | Ja |
> | migrateprojects | Ja | Ja |
> | projects | Ja | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ja | Ja |
> | objectUnderstandingAccounts | Ja | Ja |
> | remoteRenderingAccounts | Ja | Ja |
> | spatialAnchorsAccounts | Ja | Ja |
> | surfaceReconstructionAccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ja | Nej |
> | netAppAccounts / capacityPools | Ja | Nej |
> | netAppAccounts / capacityPools / volumes | Ja | Nej |
> | netAppAccounts / capacityPools / volumes / mountTargets | Ja | Nej |
> | netAppAccounts / capacityPools / volumes / snapshots | Ja | Nej |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ja | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Nej | Nej |
> | azureFirewalls | Ja | Nej |
> | bastionHosts | Ja | Ja |
> | bgpServiceCommunities | Nej | Nej |
> | connections | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatuses | Nej | Nej |
> | dnszones | Ja | Ja |
> | dnszones / A | Nej | Nej |
> | dnszones / AAAA | Nej | Nej |
> | dnszones / all | Nej | Nej |
> | dnszones / CAA | Nej | Nej |
> | dnszones / CNAME | Nej | Nej |
> | dnszones / MX | Nej | Nej |
> | dnszones / NS | Nej | Nej |
> | dnszones / PTR | Nej | Nej |
> | dnszones / recordsets | Nej | Nej |
> | dnszones / SOA | Nej | Nej |
> | dnszones / SRV | Nej | Nej |
> | dnszones / TXT | Nej | Nej |
> | expressRouteCircuits | Ja | Ja |
> | expressRouteCrossConnections | Ja | Ja |
> | expressRouteGateways | Ja | Ja |
> | expressRoutePorts | Ja | Ja |
> | expressRouteServiceProviders | Nej | Nej |
> | firewallPolicies | Ja | Ja |
> | frontdoors | Yes, but limited (see [note below](#frontdoor)) | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Yes, but limited (see [note below](#frontdoor)) | Nej |
> | frontdoorWebApplicationFirewallPolicies | Yes, but limited (see [note below](#frontdoor)) | Ja |
> | getDnsResourceReference | Nej | Nej |
> | internalNotify | Nej | Nej |
> | loadBalancers | Ja | Nej |
> | localNetworkGateways | Ja | Ja |
> | natGateways | Ja | Ja |
> | networkIntentPolicies | Ja | Ja |
> | networkInterfaces | Ja | Ja |
> | networkProfiles | Ja | Ja |
> | networkSecurityGroups | Ja | Ja |
> | networkWatchers | Ja | Nej |
> | networkWatchers / connectionMonitors | Ja | Nej |
> | networkWatchers / lenses | Ja | Nej |
> | networkWatchers / pingMeshes | Ja | Nej |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatuses | Nej | Nej |
> | privateDnsZones | Ja | Ja |
> | privateDnsZones / A | Nej | Nej |
> | privateDnsZones / AAAA | Nej | Nej |
> | privateDnsZones / all | Nej | Nej |
> | privateDnsZones / CNAME | Nej | Nej |
> | privateDnsZones / MX | Nej | Nej |
> | privateDnsZones / PTR | Nej | Nej |
> | privateDnsZones / SOA | Nej | Nej |
> | privateDnsZones / SRV | Nej | Nej |
> | privateDnsZones / TXT | Nej | Nej |
> | privateDnsZones / virtualNetworkLinks | Ja | Ja |
> | privateEndpoints | Ja | Ja |
> | privateLinkServices | Ja | Ja |
> | publicIPAddresses | Ja | Ja |
> | publicIPPrefixes | Ja | Ja |
> | routeFilters | Ja | Ja |
> | routeTables | Ja | Ja |
> | serviceEndpointPolicies | Ja | Ja |
> | trafficManagerGeographicHierarchies | Nej | Nej |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/heatMaps | Nej | Nej |
> | trafficManagerUserMetricsKeys | Nej | Nej |
> | virtualHubs | Ja | Ja |
> | virtualNetworkGateways | Ja | Ja |
> | virtualNetworks | Ja | Ja |
> | virtualNetworkTaps | Ja | Ja |
> | virtualWans | Ja | Ja |
> | vpnGateways | Ja | Nej |
> | vpnSites | Ja | Ja |
> | webApplicationFirewallPolicies | Ja | Ja |

<a id="frontdoor" />

> [!NOTE]
> For Azure Front Door Service, you can apply tags when creating the resource, but updating or adding tags is not currently supported.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Ja | Nej |
> | namespaces / notificationHubs | Ja | Nej |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ja | Ja |
> | ImportSites | Ja | Ja |
> | ServerSites | Ja | Ja |
> | VMwareSites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ja | Ja |
> | enheter | Nej | Nej |
> | linkTargets | Nej | Nej |
> | storageInsightConfigs | Nej | Nej |
> | workspaces | Ja | Ja |
> | workspaces / dataSources | Nej | Nej |
> | workspaces / linkedServices | Nej | Nej |
> | workspaces / query | Nej | Nej |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nej | Nej |
> | managementconfigurations | Ja | Ja |
> | lösningar | Ja | Ja |
> | views | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nej | Nej |
> | peerAsns | Nej | Nej |
> | peerings | Ja | Ja |
> | peeringServiceProviders | Nej | Nej |
> | peeringServices | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | policyEvents | Nej | Nej |
> | policyMetadata | Nej | Nej |
> | policyStates | Nej | Nej |
> | policyTrackedResources | Nej | Nej |
> | remediations | Nej | Nej |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | consoles | Nej | Nej |
> | dashboards | Ja | Ja |
> | userSettings | Nej | Nej |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capacities | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nej | Nej |
> | vaults | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Ja | Ja |
> | namespaces / authorizationrules | Nej | Nej |
> | namespaces / hybridconnections | Nej | Nej |
> | namespaces / hybridconnections / authorizationrules | Nej | Nej |
> | namespaces / wcfrelays | Nej | Nej |
> | namespaces / wcfrelays / authorizationrules | Nej | Nej |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | konton | Nej | Nej |
> | collections | Ja | Ja |
> | collections / applications | Nej | Nej |
> | collections / securityprincipals | Nej | Nej |
> | templateImages | Nej | Nej |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | frågor | Ja | Ja |
> | resourceChangeDetails | Nej | Nej |
> | resourceChanges | Nej | Nej |
> | resources | Nej | Nej |
> | resourcesHistory | Nej | Nej |
> | subscriptionsStatus | Nej | Nej |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nej | Nej |
> | childAvailabilityStatuses | Nej | Nej |
> | childResources | Nej | Nej |
> | evenemang | Nej | Nej |
> | impactedResources | Nej | Nej |
> | metadata | Nej | Nej |
> | notifications | Nej | Nej |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deployments | Ja | Nej |
> | deployments / operations | Nej | Nej |
> | deploymentScripts | Ja | Ja |
> | deploymentScripts / logs | Nej | Nej |
> | links | Nej | Nej |
> | notifyResourceJobs | Nej | Nej |
> | providers | Nej | Nej |
> | resourceGroups | Ja | Nej |
> | subscriptions | Nej | Nej |
> | tenants | Nej | Nej |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | saasresources | Nej | Nej |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobcollections | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nej | Nej |
> | searchServices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nej | Nej |
> | advancedThreatProtectionSettings | Nej | Nej |
> | aviseringar | Nej | Nej |
> | allowedConnections | Nej | Nej |
> | applicationWhitelistings | Nej | Nej |
> | assessmentMetadata | Nej | Nej |
> | assessments | Nej | Nej |
> | autoDismissAlertsRules | Nej | Nej |
> | automations | Ja | Ja |
> | AutoProvisioningSettings | Nej | Nej |
> | Compliances | Nej | Nej |
> | dataCollectionAgents | Nej | Nej |
> | deviceSecurityGroups | Nej | Nej |
> | discoveredSecuritySolutions | Nej | Nej |
> | externalSecuritySolutions | Nej | Nej |
> | InformationProtectionPolicies | Nej | Nej |
> | iotSecuritySolutions | Ja | Ja |
> | iotSecuritySolutions / analyticsModels | Nej | Nej |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nej | Nej |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nej | Nej |
> | jitNetworkAccessPolicies | Nej | Nej |
> | networkData | Nej | Nej |
> | policies | Nej | Nej |
> | pricings | Nej | Nej |
> | regulatoryComplianceStandards | Nej | Nej |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nej | Nej |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nej | Nej |
> | securityContacts | Nej | Nej |
> | securitySolutions | Nej | Nej |
> | securitySolutionsReferenceData | Nej | Nej |
> | securityStatuses | Nej | Nej |
> | securityStatusesSummaries | Nej | Nej |
> | serverVulnerabilityAssessments | Nej | Nej |
> | settings | Nej | Nej |
> | subAssessments | Nej | Nej |
> | uppgifter | Nej | Nej |
> | topologies | Nej | Nej |
> | workspaceSettings | Nej | Nej |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nej | Nej |
> | diagnosticSettingsCategories | Nej | Nej |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aggregations | Nej | Nej |
> | alertRules | Nej | Nej |
> | alertRuleTemplates | Nej | Nej |
> | bookmarks | Nej | Nej |
> | cases | Nej | Nej |
> | dataConnectors | Nej | Nej |
> | entities | Nej | Nej |
> | entityQueries | Nej | Nej |
> | officeConsents | Nej | Nej |
> | settings | Nej | Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Ja | Nej |
> | namespaces / authorizationrules | Nej | Nej |
> | namespaces / disasterrecoveryconfigs | Nej | Nej |
> | namespaces / eventgridfilters | Nej | Nej |
> | namespaces / networkrulesets | Nej | Nej |
> | namespaces / queues | Nej | Nej |
> | namespaces / queues / authorizationrules | Nej | Nej |
> | namespaces / topics | Nej | Nej |
> | namespaces / topics / authorizationrules | Nej | Nej |
> | namespaces / topics / subscriptions | Nej | Nej |
> | namespaces / topics / subscriptions / rules | Nej | Nej |
> | premiumMessagingRegions | Nej | Nej |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | clusters | Ja | Ja |
> | clusters / applications | Nej | Nej |
> | containerGroups | Ja | Ja |
> | containerGroupSets | Ja | Ja |
> | edgeclusters | Ja | Ja |
> | edgeclusters / applications | Nej | Nej |
> | networks | Ja | Ja |
> | secretstores | Ja | Ja |
> | secretstores / certificates | Nej | Nej |
> | secretstores / secrets | Nej | Nej |
> | volumes | Ja | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | containerGroups | Ja | Ja |
> | gateways | Ja | Ja |
> | networks | Ja | Ja |
> | hemligheter | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nej | Nej |
> | providerRegistrations / resourceTypeRegistrations | Nej | Nej |
> | rollouts | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | SignalR / eventGridFilters | Nej | Nej |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nej | Nej |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ja | Ja |
> | program | Ja | Ja |
> | jitRequests | Ja | Ja |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managedInstances | Ja | Ja |
> | managedInstances / databases | Nej | Nej |
> | managedInstances / databases / backupShortTermRetentionPolicies | Nej | Nej |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Nej | Nej |
> | managedInstances / databases / vulnerabilityAssessments | Nej | Nej |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Nej | Nej |
> | managedInstances / encryptionProtector | Nej | Nej |
> | managedInstances / keys | Nej | Nej |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nej | Nej |
> | managedInstances / vulnerabilityAssessments | Nej | Nej |
> | servrar | Ja | Ja |
> | servers / administrators | Nej | Nej |
> | servers / communicationLinks | Nej | Nej |
> | servers / databases | Yes (see [note below](#sqlnote)) | Ja |
> | servers / encryptionProtector | Nej | Nej |
> | servers / firewallRules | Nej | Nej |
> | servers / keys | Nej | Nej |
> | servers / restorableDroppedDatabases | Nej | Nej |
> | servers / serviceobjectives | Nej | Nej |
> | servers / tdeCertificates | Nej | Nej |
> | virtualClusters | Nej | Nej |

<a id="sqlnote" />

> [!NOTE]
> The Master database doesn't support tags, but other databases, including Azure SQL Data Warehouse databases, support tags. Azure SQL Data Warehouse databases must be in Active (not Paused) state.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ja | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nej | Nej |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Ja | Ja |
> | storageAccounts / blobServices | Nej | Nej |
> | storageAccounts / fileServices | Nej | Nej |
> | storageAccounts / queueServices | Nej | Nej |
> | storageAccounts / services | Nej | Nej |
> | storageAccounts / services / metricDefinitions | Nej | Nej |
> | storageAccounts / tableServices | Nej | Nej |
> | usages | Nej | Nej |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | caches | Ja | Ja |
> | caches / storageTargets | Nej | Nej |
> | usageModels | Nej | Nej |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nej | Nej |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nej | Nej |
> | storageSyncServices / syncGroups | Nej | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej | Nej |
> | storageSyncServices / workflows | Nej | Nej |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nej | Nej |
> | storageSyncServices / syncGroups | Nej | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej | Nej |
> | storageSyncServices / workflows | Nej | Nej |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Nej | Nej |
> | storageSyncServices / syncGroups | Nej | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej | Nej |
> | storageSyncServices / workflows | Nej | Nej |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managers | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Yes (see note below) | Ja |

> [!NOTE]
> You can't add a tag when streamingjobs is running. Stop the resource to add a tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | cancel | Nej | Nej |
> | CreateSubscription | Nej | Nej |
> | enable | Nej | Nej |
> | rename | Nej | Nej |
> | SubscriptionDefinitions | Nej | Nej |
> | SubscriptionOperations | Nej | Nej |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Ja | Nej |
> | environments / accessPolicies | Nej | Nej |
> | environments / eventsources | Ja | Nej |
> | environments / referenceDataSets | Ja | Nej |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ja | Ja |
> | dedicatedCloudServices | Ja | Ja |
> | virtualMachines | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nej | Nej |
> | apiManagementAccounts / apiAcls | Nej | Nej |
> | apiManagementAccounts / apis | Nej | Nej |
> | apiManagementAccounts / apis / apiAcls | Nej | Nej |
> | apiManagementAccounts / apis / connectionAcls | Nej | Nej |
> | apiManagementAccounts / apis / connections | Nej | Nej |
> | apiManagementAccounts / apis / connections / connectionAcls | Nej | Nej |
> | apiManagementAccounts / apis / localizedDefinitions | Nej | Nej |
> | apiManagementAccounts / connectionAcls | Nej | Nej |
> | apiManagementAccounts / connections | Nej | Nej |
> | billingMeters | Nej | Nej |
> | certificates | Ja | Ja |
> | connectionGateways | Ja | Ja |
> | connections | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Nej | Nej |
> | functions | Nej | Nej |
> | hostingEnvironments | Ja | Ja |
> | hostingEnvironments / multiRolePools | Nej | Nej |
> | hostingEnvironments / workerPools | Nej | Nej |
> | publishingUsers | Nej | Nej |
> | recommendations | Nej | Nej |
> | resourceHealthMetadata | Nej | Nej |
> | runtimes | Nej | Nej |
> | serverFarms | Ja | Ja |
> | serverFarms / eventGridFilters | Nej | Nej |
> | sites | Ja | Ja |
> | sites / config  | Nej | Nej |
> | sites / eventGridFilters | Nej | Nej |
> | sites / hostNameBindings | Nej | Nej |
> | sites / networkConfig | Nej | Nej |
> | sites / premieraddons | Ja | Ja |
> | sites / slots | Ja | Ja |
> | sites / slots / eventGridFilters | Nej | Nej |
> | sites / slots / hostNameBindings | Nej | Nej |
> | sites / slots / networkConfig | Nej | Nej |
> | sourceControls | Nej | Nej |
> | validate | Nej | Nej |
> | verifyHostingEnvironmentVnet | Nej | Nej |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nej | Nej |
> | diagnosticSettingsCategories | Nej | Nej |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ja | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Nej | Nej |
> | componentsSummary | Nej | Nej |
> | monitorInstances | Nej | Nej |
> | monitorInstancesSummary | Nej | Nej |
> | monitors | Nej | Nej |
> | notificationSettings | Nej | Nej |

## <a name="next-steps"></a>Nästa steg

To learn how to apply tags to resources, see [Use tags to organize your Azure resources](resource-group-using-tags.md).
