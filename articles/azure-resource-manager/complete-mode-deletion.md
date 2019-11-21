---
title: Borttagning i complete-läget
description: Shows how resource types handle complete mode deletion in Azure Resource Manager templates.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232694"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Deletion of Azure resources for complete mode deployments

This article describes how resource types handle deletion when not in a template that is deployed in complete mode.

The resource types marked with **Yes** are deleted when the type isn't in the template deployed with complete mode.

The resource types marked with **No** aren't automatically deleted when not in the template; however, they're deleted if the parent resource is deleted. For a full description of the behavior, see [Azure Resource Manager deployment modes](deployment-modes.md).

If you deploy to [more than one resource group in a template](resource-manager-cross-resource-group-deployment.md), resources in the resource group specified in the deployment operation are eligible to be deleted. Resources in the secondary resource groups aren't deleted.

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
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DomainServices | Ja |
> | DomainServices / oucontainer | Nej |

## <a name="microsoftaddons"></a>Microsoft.Addons

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
> | agents | Nej |
> | anonymousapiusers | Nej |
> | konfiguration | Nej |
> | loggar | Nej |
> | reports | Nej |
> | servicehealthmetrics | Nej |
> | services | Nej |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurations | Nej |
> | generateRecommendations | Nej |
> | metadata | Nej |
> | recommendations | Nej |
> | suppressions | Nej |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | actionRules | Ja |
> | aviseringar | Nej |
> | alertsList | Nej |
> | alertsMetaData | Nej |
> | alertsSummary | Nej |
> | alertsSummaryList | Nej |
> | feedback | Nej |
> | smartDetectorAlertRules | Ja |
> | smartDetectorRuntimeEnvironments | Nej |
> | smartGroups | Nej |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | servrar | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | reportFeedback | Nej |
> | tjänst | Ja |
> | validateServiceName | Nej |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Nej |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Spring | Ja |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attestationProviders | Nej |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicAdministrators | Nej |
> | dataAliases | Nej |
> | denyAssignments | Nej |
> | elevateAccess | Nej |
> | findOrphanRoleAssignments | Nej |
> | locks | Nej |
> | permissions | Nej |
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
> | automationAccounts | Ja |
> | automationAccounts / configurations | Ja |
> | automationAccounts / jobs | Nej |
> | automationAccounts / runbooks | Ja |
> | automationAccounts / softwareUpdateConfigurations | Nej |
> | automationAccounts / webhooks | Nej |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Nej |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | environments | Nej |
> | environments / accounts | Nej |
> | environments / accounts / namespaces | Nej |
> | environments / accounts / namespaces / configurations | Nej |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | b2cDirectories | Ja |
> | b2ctenants | Nej |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridDataManagers | Ja |
> | postgresInstances | Ja |
> | sqlBigDataClusters | Ja |
> | sqlInstances | Ja |
> | sqlServerRegistrations | Ja |
> | sqlServerRegistrations / sqlServers | Nej |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | registrations | Ja |
> | registrations / customerSubscriptions | Nej |
> | registrations / products | Nej |
> | verificationKeys | Nej |

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
> | billingAccounts / agreements | Nej |
> | billingAccounts / billingPermissions | Nej |
> | billingAccounts / billingProfiles | Nej |
> | billingAccounts / billingProfiles / billingPermissions | Nej |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nej |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nej |
> | billingAccounts / billingProfiles / billingSubscriptions | Nej |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nej |
> | billingAccounts / billingProfiles / customers | Nej |
> | billingAccounts / billingProfiles / invoices | Nej |
> | billingAccounts / billingProfiles / invoices / pricesheet | Nej |
> | billingAccounts / billingProfiles / invoiceSections | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nej |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nej |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nej |
> | billingAccounts / billingProfiles / invoiceSections / products | Nej |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nej |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nej |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Nej |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nej |
> | billingAccounts / BillingProfiles / patchOperations | Nej |
> | billingAccounts / billingProfiles / paymentMethods | Nej |
> | billingAccounts / billingProfiles / policies | Nej |
> | billingAccounts / billingProfiles / pricesheet | Nej |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nej |
> | billingAccounts / billingProfiles / products | Nej |
> | billingAccounts / billingProfiles / transactions | Nej |
> | billingAccounts / billingRoleAssignments | Nej |
> | billingAccounts / billingRoleDefinitions | Nej |
> | billingAccounts / billingSubscriptions | Nej |
> | billingAccounts / billingSubscriptions / invoices | Nej |
> | billingAccounts / createBillingRoleAssignment | Nej |
> | billingAccounts / createInvoiceSectionOperations | Nej |
> | billingAccounts / customers | Nej |
> | billingAccounts / customers / billingPermissions | Nej |
> | billingAccounts / customers / billingSubscriptions | Nej |
> | billingAccounts / customers / initiateTransfer | Nej |
> | billingAccounts / customers / policies | Nej |
> | billingAccounts / customers / products | Nej |
> | billingAccounts / customers / transactions | Nej |
> | billingAccounts / customers / transfers | Nej |
> | billingAccounts / departments | Nej |
> | billingAccounts / enrollmentAccounts | Nej |
> | billingAccounts / invoices | Nej |
> | billingAccounts / invoiceSections | Nej |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nej |
> | billingAccounts / invoiceSections / billingSubscriptions | Nej |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Nej |
> | billingAccounts / invoiceSections / elevate | Nej |
> | billingAccounts / invoiceSections / initiateTransfer | Nej |
> | billingAccounts / invoiceSections / patchOperations | Nej |
> | billingAccounts / invoiceSections / productMoveOperations | Nej |
> | billingAccounts / invoiceSections / products | Nej |
> | billingAccounts / invoiceSections / products / transfer | Nej |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Nej |
> | billingAccounts / invoiceSections / transactions | Nej |
> | billingAccounts / invoiceSections / transfers | Nej |
> | billingAccounts / lineOfCredit | Nej |
> | billingAccounts / patchOperations | Nej |
> | billingAccounts / paymentMethods | Nej |
> | billingAccounts / products | Nej |
> | billingAccounts / transactions | Nej |
> | billingPeriods | Nej |
> | billingPermissions | Nej |
> | billingProperty | Nej |
> | billingRoleAssignments | Nej |
> | billingRoleDefinitions | Nej |
> | createBillingRoleAssignment | Nej |
> | departments | Nej |
> | enrollmentAccounts | Nej |
> | invoices | Nej |
> | transfers | Nej |
> | transfers / acceptTransfer | Nej |
> | transfers / declineTransfer | Nej |
> | transfers / operationStatus | Nej |
> | transfers / validateTransfer | Nej |
> | validateAddress | Nej |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | mapApis | Ja |
> | updateCommunicationPreference | Nej |

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
> | blueprintAssignments | Nej |
> | blueprintAssignments / assignmentOperations | Nej |
> | blueprintAssignments / operations | Nej |
> | blueprints | Nej |
> | blueprints / artifacts | Nej |
> | blueprints / versions | Nej |
> | blueprints / versions / artifacts | Nej |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | botServices | Ja |
> | botServices / channels | Nej |
> | botServices / connections | Nej |
> | languages | Nej |
> | mallar | Nej |

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
> | calculateExchange | Nej |
> | calculatePrice | Nej |
> | calculatePurchasePrice | Nej |
> | catalogs | Nej |
> | commercialReservationOrders | Nej |
> | exchange | Nej |
> | placePurchaseOrder | Nej |
> | reservationOrders | Nej |
> | reservationOrders / calculateRefund | Nej |
> | reservationOrders / merge | Nej |
> | reservationOrders / reservations | Nej |
> | reservationOrders / reservations / revisions | Nej |
> | reservationOrders / return | Nej |
> | reservationOrders / split | Nej |
> | reservationOrders / swap | Nej |
> | reservations | Nej |
> | resources | Nej |
> | validateReservationOrder | Nej |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nej |
> | CdnWebApplicationFirewallPolicies | Ja |
> | edgenodes | Nej |
> | profiles | Ja |
> | profiles / endpoints | Ja |
> | profiles / endpoints / customdomains | Nej |
> | profiles / endpoints / origins | Nej |
> | validateProbe | Nej |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certificateOrders | Ja |
> | certificateOrders / certificates | Nej |
> | validateCertificateRegistrationInformation | Nej |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | capabilities | Nej |
> | domainNames | Ja |
> | domainNames / capabilities | Nej |
> | domainNames / internalLoadBalancers | Nej |
> | domainNames / serviceCertificates | Nej |
> | domainNames / slots | Nej |
> | domainNames / slots / roles | Nej |
> | domainNames / slots / roles / metricDefinitions | Nej |
> | domainNames / slots / roles / metrics | Nej |
> | moveSubscriptionResources | Nej |
> | operatingSystemFamilies | Nej |
> | operatingSystems | Nej |
> | quotas | Nej |
> | resourceTypes | Nej |
> | validateSubscriptionMoveAvailability | Nej |
> | virtualMachines | Ja |
> | virtualMachines / diagnosticSettings | Nej |
> | virtualMachines / metricDefinitions | Nej |
> | virtualMachines / metrics | Nej |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nej |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | capabilities | Nej |
> | expressRouteCrossConnections | Nej |
> | expressRouteCrossConnections / peerings | Nej |
> | gatewaySupportedDevices | Nej |
> | networkSecurityGroups | Ja |
> | quotas | Nej |
> | reservedIps | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nej |
> | virtualNetworks / virtualNetworkPeerings | Nej |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | capabilities | Nej |
> | disks | Nej |
> | images | Nej |
> | osImages | Nej |
> | osPlatformImages | Nej |
> | publicImages | Nej |
> | quotas | Nej |
> | storageAccounts | Ja |
> | storageAccounts / blobServices | Nej |
> | storageAccounts / fileServices | Nej |
> | storageAccounts / metricDefinitions | Nej |
> | storageAccounts / metrics | Nej |
> | storageAccounts / queueServices | Nej |
> | storageAccounts / services | Nej |
> | storageAccounts / services / diagnosticSettings | Nej |
> | storageAccounts / services / metricDefinitions | Nej |
> | storageAccounts / services / metrics | Nej |
> | storageAccounts / tableServices | Nej |
> | storageAccounts / vmImages | Nej |
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
> | diskEncryptionSets | Ja |
> | disks | Ja |
> | galleries | Ja |
> | galleries / applications | Nej |
> | galleries / applications / versions | Nej |
> | galleries / images | Nej |
> | galleries / images / versions | Nej |
> | hostGroups | Ja |
> | hostGroups / hosts | Ja |
> | images | Ja |
> | proximityPlacementGroups | Ja |
> | restorePointCollections | Ja |
> | restorePointCollections / restorePoints | Nej |
> | sharedVMImages | Ja |
> | sharedVMImages / versions | Nej |
> | snapshots | Ja |
> | virtualMachines | Ja |
> | virtualMachines / extensions | Ja |
> | virtualMachines / metricDefinitions | Nej |
> | virtualMachineScaleSets | Ja |
> | virtualMachineScaleSets / extensions | Nej |
> | virtualMachineScaleSets / networkInterfaces | Nej |
> | virtualMachineScaleSets / publicIPAddresses | Nej |
> | virtualMachineScaleSets / virtualMachines | Nej |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nej |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | AggregatedCost | Nej |
> | Saldon | Nej |
> | Budgetar | Nej |
> | Avgifter | Nej |
> | CostTags | Nej |
> | credits | Nej |
> | evenemang | Nej |
> | Prognoser | Nej |
> | lots | Nej |
> | Marketplaces | Nej |
> | Pricesheets | Nej |
> | products | Nej |
> | ReservationDetails | Nej |
> | ReservationRecommendations | Nej |
> | ReservationSummaries | Nej |
> | ReservationTransactions | Nej |
> | Taggar | Nej |
> | tenants | Nej |
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
> | registries | Ja |
> | registries / builds | Nej |
> | registries / builds / cancel | Nej |
> | registries / builds / getLogLink | Nej |
> | registries / buildTasks | Ja |
> | registries / buildTasks / steps | Nej |
> | registries / eventGridFilters | Nej |
> | registries / generateCredentials | Nej |
> | registries / getBuildSourceUploadUrl | Nej |
> | registries / GetCredentials | Nej |
> | registries / importImage | Nej |
> | registries / queueBuild | Nej |
> | registries / regenerateCredential | Nej |
> | registries / regenerateCredentials | Nej |
> | registries / replications | Ja |
> | registries / runs | Nej |
> | registries / runs / cancel | Nej |
> | registries / scheduleRun | Nej |
> | registries / scopeMaps | Nej |
> | registries / taskRuns | Ja |
> | registries / tasks | Ja |
> | registries / tokens | Nej |
> | registries / updatePolicies | Nej |
> | registries / webhooks | Ja |
> | registries / webhooks / getCallbackConfig | Nej |
> | registries / webhooks / ping | Nej |

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
> | Aviseringar | Nej |
> | BillingAccounts | Nej |
> | Budgetar | Nej |
> | CloudConnectors | Nej |
> | Anslutningsprogram | Ja |
> | Departments | Nej |
> | Dimensioner | Nej |
> | EnrollmentAccounts | Nej |
> | Exporter | Nej |
> | ExternalBillingAccounts | Nej |
> | ExternalBillingAccounts / Alerts | Nej |
> | ExternalBillingAccounts / Dimensions | Nej |
> | ExternalBillingAccounts / Forecast | Nej |
> | ExternalBillingAccounts / Query | Nej |
> | ExternalSubscriptions | Nej |
> | ExternalSubscriptions / Alerts | Nej |
> | ExternalSubscriptions / Dimensions | Nej |
> | ExternalSubscriptions / Forecast | Nej |
> | ExternalSubscriptions / Query | Nej |
> | Forecast | Nej |
> | Söka i data | Nej |
> | register | Nej |
> | Reportconfigs | Nej |
> | Rapporter | Nej |
> | Inställningar | Nej |
> | showbackRules | Nej |
> | Vyer | Nej |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | requests | Nej |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | associations | Nej |
> | resourceProviders | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | jobs | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | workspaces | Ja |
> | workspaces / virtualNetworkPeerings | Nej |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | catalogs | Ja |
> | datacatalogs | Ja |
> | datacatalogs / datasources | Nej |
> | datacatalogs / datasources / scans | Nej |
> | datacatalogs / datasources / scans / datasets | Nej |
> | datacatalogs / datasources / scans / triggers | Nej |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataFactories | Ja |
> | dataFactories / diagnosticSettings | Nej |
> | dataFactories / metricDefinitions | Nej |
> | dataFactorySchema | Nej |
> | factories | Ja |
> | factories / integrationRuntimes | Nej |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | accounts / dataLakeStoreAccounts | Nej |
> | accounts / storageAccounts | Nej |
> | accounts / storageAccounts / containers | Nej |
> | accounts / transferAnalyticsUnits | Nej |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | accounts / eventGridFilters | Nej |
> | accounts / firewallRules | Nej |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |
> | services / projects | Ja |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | accounts / shares | Nej |
> | accounts / shares / datasets | Nej |
> | accounts / shares / invitations | Nej |
> | accounts / shares / providersharesubscriptions | Nej |
> | accounts / shares / synchronizationSettings | Nej |
> | accounts / sharesubscriptions | Nej |
> | accounts / sharesubscriptions / consumerSourceDataSets | Nej |
> | accounts / sharesubscriptions / datasetmappings | Nej |
> | accounts / sharesubscriptions / triggers | Nej |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | servrar | Ja |
> | servers / advisors | Nej |
> | servers / privateEndpointConnectionProxies | Nej |
> | servers / privateEndpointConnections | Nej |
> | servers / privateLinkResources | Nej |
> | servers / queryTexts | Nej |
> | servers / recoverableServers | Nej |
> | servers / topQueryStatistics | Nej |
> | servers / virtualNetworkRules | Nej |
> | servers / waitStatistics | Nej |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | servrar | Ja |
> | servers / advisors | Nej |
> | servers / privateEndpointConnectionProxies | Nej |
> | servers / privateEndpointConnections | Nej |
> | servers / privateLinkResources | Nej |
> | servers / queryTexts | Nej |
> | servers / recoverableServers | Nej |
> | servers / topQueryStatistics | Nej |
> | servers / virtualNetworkRules | Nej |
> | servers / waitStatistics | Nej |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | serverGroups | Ja |
> | servrar | Ja |
> | servers / advisors | Nej |
> | servers / keys | Nej |
> | servers / privateEndpointConnectionProxies | Nej |
> | servers / privateEndpointConnections | Nej |
> | servers / privateLinkResources | Nej |
> | servers / queryTexts | Nej |
> | servers / recoverableServers | Nej |
> | servers / topQueryStatistics | Nej |
> | servers / virtualNetworkRules | Nej |
> | servers / waitStatistics | Nej |
> | serversv2 | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | artifactSources | Ja |
> | rollouts | Ja |
> | serviceTopologies | Ja |
> | serviceTopologies / services | Ja |
> | serviceTopologies / services / serviceUnits | Ja |
> | steg | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationgroups | Ja |
> | applicationgroups / applications | Nej |
> | applicationgroups / desktops | Nej |
> | applicationgroups / startmenuitems | Nej |
> | hostpools | Ja |
> | hostpools / sessionhosts | Nej |
> | hostpools / sessionhosts / usersessions | Nej |
> | hostpools / usersessions | Nej |
> | workspaces | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ElasticPools | Ja |
> | ElasticPools / IotHubTenants | Ja |
> | IotHubs | Ja |
> | IotHubs / eventGridFilters | Nej |
> | ProvisioningServices | Ja |
> | usages | Nej |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | pipelines | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | controllers | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labcenters | Ja |
> | labs | Ja |
> | labs / environments | Ja |
> | labs / serviceRunners | Ja |
> | labs / virtualMachines | Ja |
> | schedules | Ja |

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
> | domains | Ja |
> | domains / domainOwnershipIdentifiers | Nej |
> | generateSsoRequest | Nej |
> | topLevelDomains | Nej |
> | validateDomainRegistrationInformation | Nej |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | lcsprojects | Nej |
> | lcsprojects / clouddeployments | Nej |
> | lcsprojects / connectors | Nej |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domains | Ja |
> | domains / topics | Nej |
> | eventSubscriptions | Nej |
> | extensionTopics | Nej |
> | topics | Ja |
> | topicTypes | Nej |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | clusters | Ja |
> | namespaces | Ja |
> | namespaces / authorizationrules | Nej |
> | namespaces / disasterrecoveryconfigs | Nej |
> | namespaces / eventhubs | Nej |
> | namespaces / eventhubs / authorizationrules | Nej |
> | namespaces / eventhubs / consumergroups | Nej |
> | namespaces / networkrulesets | Nej |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | Nej |
> | providers | Nej |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | enroll | Nej |
> | galleryitems | Nej |
> | generateartifactaccessuri | Nej |
> | myareas | Nej |
> | myareas / areas | Nej |
> | myareas / areas / areas | Nej |
> | myareas / areas / areas / galleryitems | Nej |
> | myareas / areas / galleryitems | Nej |
> | myareas / galleryitems | Nej |
> | register | Nej |
> | resources | Nej |
> | retrieveresourcesbyid | Nej |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationProfileAssignments | Nej |
> | guestConfigurationAssignments | Nej |
> | software | Nej |
> | softwareUpdateProfile | Nej |
> | softwareUpdates | Nej |

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
> | clusters | Ja |
> | clusters / applications | Nej |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | machines | Ja |
> | machines / extensions | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataManagers | Ja |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | components | Ja |
> | networkScopes | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | jobs | Ja |

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
> | appTemplates | Nej |
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
> | hsmPools | Ja |
> | vaults | Ja |
> | vaults / accessPolicies | Nej |
> | vaults / eventGridFilters | Nej |
> | vaults / secrets | Nej |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | clusters | Ja |
> | clusters / attacheddatabaseconfigurations | Nej |
> | clusters / databases | Nej |
> | clusters / databases / dataconnections | Nej |
> | clusters / databases / eventhubconnections | Nej |
> | clusters / sharedidentities | Nej |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labaccounts | Ja |
> | användare | Nej |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hostingEnvironments | Ja |
> | integrationAccounts | Ja |
> | integrationServiceEnvironments | Ja |
> | integrationServiceEnvironments / managedApis | Ja |
> | isolatedEnvironments | Ja |
> | workflows | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | commitmentPlans | Ja |
> | webServices | Ja |
> | Arbetsytor | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | workspaces | Ja |
> | workspaces / computes | Nej |
> | workspaces / eventGridFilters | Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Identities | Nej |
> | userAssignedIdentities | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Nej |
> | registrationAssignments | Nej |
> | registrationDefinitions | Nej |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | getEntities | Nej |
> | managementGroups | Nej |
> | resources | Nej |
> | startTenantBackfill | Nej |
> | tenantBackfillStatus | Nej |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | accounts / eventGridFilters | Nej |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | offers | Nej |
> | offerTypes | Nej |
> | offerTypes / publishers | Nej |
> | offerTypes / publishers / offers | Nej |
> | offerTypes / publishers / offers / plans | Nej |
> | offerTypes / publishers / offers / plans / agreements | Nej |
> | offerTypes / publishers / offers / plans / configs | Nej |
> | offerTypes / publishers / offers / plans / configs / importImage | Nej |
> | privategalleryitems | Nej |
> | products | Nej |
> | publishers | Nej |
> | publishers / offers | Nej |
> | publishers / offers / amendments | Nej |

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
> | agreements | Nej |
> | offertypes | Nej |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | mediaservices | Ja |
> | mediaservices / accountFilters | Nej |
> | mediaservices / assets | Nej |
> | mediaservices / assets / assetFilters | Nej |
> | mediaservices / contentKeyPolicies | Nej |
> | mediaservices / eventGridFilters | Nej |
> | mediaservices / liveEventOperations | Nej |
> | mediaservices / liveEvents | Ja |
> | mediaservices / liveEvents / liveOutputs | Nej |
> | mediaservices / liveOutputOperations | Nej |
> | mediaservices / mediaGraphs | Nej |
> | mediaservices / streamingEndpointOperations | Nej |
> | mediaservices / streamingEndpoints | Ja |
> | mediaservices / streamingLocators | Nej |
> | mediaservices / streamingPolicies | Nej |
> | mediaservices / transforms | Nej |
> | mediaservices / transforms / jobs | Nej |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

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
> | projects | Ja |

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
> | netAppAccounts / capacityPools / volumes | Ja |
> | netAppAccounts / capacityPools / volumes / mountTargets | Ja |
> | netAppAccounts / capacityPools / volumes / snapshots | Ja |
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
> | connections | Ja |
> | ddosCustomPolicies | Ja |
> | ddosProtectionPlans | Ja |
> | dnsOperationStatuses | Nej |
> | dnszones | Ja |
> | dnszones / A | Nej |
> | dnszones / AAAA | Nej |
> | dnszones / all | Nej |
> | dnszones / CAA | Nej |
> | dnszones / CNAME | Nej |
> | dnszones / MX | Nej |
> | dnszones / NS | Nej |
> | dnszones / PTR | Nej |
> | dnszones / recordsets | Nej |
> | dnszones / SOA | Nej |
> | dnszones / SRV | Nej |
> | dnszones / TXT | Nej |
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
> | loadBalancers | Ja |
> | localNetworkGateways | Ja |
> | natGateways | Ja |
> | networkIntentPolicies | Ja |
> | networkInterfaces | Ja |
> | networkProfiles | Ja |
> | networkSecurityGroups | Ja |
> | networkWatchers | Ja |
> | networkWatchers / connectionMonitors | Ja |
> | networkWatchers / lenses | Ja |
> | networkWatchers / pingMeshes | Ja |
> | p2sVpnGateways | Ja |
> | privateDnsOperationStatuses | Nej |
> | privateDnsZones | Ja |
> | privateDnsZones / A | Nej |
> | privateDnsZones / AAAA | Nej |
> | privateDnsZones / all | Nej |
> | privateDnsZones / CNAME | Nej |
> | privateDnsZones / MX | Nej |
> | privateDnsZones / PTR | Nej |
> | privateDnsZones / SOA | Nej |
> | privateDnsZones / SRV | Nej |
> | privateDnsZones / TXT | Nej |
> | privateDnsZones / virtualNetworkLinks | Ja |
> | privateEndpoints | Ja |
> | privateLinkServices | Ja |
> | publicIPAddresses | Ja |
> | publicIPPrefixes | Ja |
> | routeFilters | Ja |
> | routeTables | Ja |
> | serviceEndpointPolicies | Ja |
> | trafficManagerGeographicHierarchies | Nej |
> | trafficmanagerprofiles | Ja |
> | trafficmanagerprofiles / heatMaps | Nej |
> | trafficManagerUserMetricsKeys | Nej |
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
> | namespaces | Ja |
> | namespaces / notificationHubs | Ja |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

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
> | clusters | Ja |
> | enheter | Nej |
> | linkTargets | Nej |
> | storageInsightConfigs | Nej |
> | workspaces | Ja |
> | workspaces / dataSources | Nej |
> | workspaces / linkedServices | Nej |
> | workspaces / query | Nej |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managementassociations | Nej |
> | managementconfigurations | Ja |
> | lösningar | Ja |
> | views | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | legacyPeerings | Nej |
> | peerAsns | Nej |
> | peerings | Ja |
> | peeringServiceProviders | Nej |
> | peeringServices | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | policyEvents | Nej |
> | policyMetadata | Nej |
> | policyStates | Nej |
> | policyTrackedResources | Nej |
> | remediations | Nej |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | consoles | Nej |
> | dashboards | Ja |
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
> | capacities | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | backupProtectedItems | Nej |
> | vaults | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namespaces | Ja |
> | namespaces / authorizationrules | Nej |
> | namespaces / hybridconnections | Nej |
> | namespaces / hybridconnections / authorizationrules | Nej |
> | namespaces / wcfrelays | Nej |
> | namespaces / wcfrelays / authorizationrules | Nej |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Nej |
> | collections | Ja |
> | collections / applications | Nej |
> | collections / securityprincipals | Nej |
> | templateImages | Nej |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | frågor | Ja |
> | resourceChangeDetails | Nej |
> | resourceChanges | Nej |
> | resources | Nej |
> | resourcesHistory | Nej |
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
> | metadata | Nej |
> | notifications | Nej |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deployments | Nej |
> | deployments / operations | Nej |
> | deploymentScripts | Ja |
> | deploymentScripts / logs | Nej |
> | links | Nej |
> | notifyResourceJobs | Nej |
> | providers | Nej |
> | resourceGroups | Nej |
> | resources | Nej |
> | subscriptions | Nej |
> | subscriptions / providers | Nej |
> | subscriptions / resources | Nej |
> | subscriptions / tagnames | Nej |
> | subscriptions / tagNames / tagValues | Nej |
> | tenants | Nej |

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
> | jobcollections | Ja |

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
> | adaptiveNetworkHardenings | Nej |
> | advancedThreatProtectionSettings | Nej |
> | aviseringar | Nej |
> | allowedConnections | Nej |
> | applicationWhitelistings | Nej |
> | assessmentMetadata | Nej |
> | assessments | Nej |
> | autoDismissAlertsRules | Nej |
> | automations | Ja |
> | AutoProvisioningSettings | Nej |
> | Compliances | Nej |
> | dataCollectionAgents | Nej |
> | deviceSecurityGroups | Nej |
> | discoveredSecuritySolutions | Nej |
> | externalSecuritySolutions | Nej |
> | InformationProtectionPolicies | Nej |
> | iotSecuritySolutions | Ja |
> | iotSecuritySolutions / analyticsModels | Nej |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nej |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nej |
> | jitNetworkAccessPolicies | Nej |
> | networkData | Nej |
> | policies | Nej |
> | pricings | Nej |
> | regulatoryComplianceStandards | Nej |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nej |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nej |
> | securityContacts | Nej |
> | securitySolutions | Nej |
> | securitySolutionsReferenceData | Nej |
> | securityStatuses | Nej |
> | securityStatusesSummaries | Nej |
> | serverVulnerabilityAssessments | Nej |
> | settings | Nej |
> | subAssessments | Nej |
> | uppgifter | Nej |
> | topologies | Nej |
> | workspaceSettings | Nej |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Nej |
> | diagnosticSettingsCategories | Nej |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | aggregations | Nej |
> | alertRules | Nej |
> | alertRuleTemplates | Nej |
> | bookmarks | Nej |
> | cases | Nej |
> | dataConnectors | Nej |
> | entities | Nej |
> | entityQueries | Nej |
> | officeConsents | Nej |
> | settings | Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namespaces | Ja |
> | namespaces / authorizationrules | Nej |
> | namespaces / disasterrecoveryconfigs | Nej |
> | namespaces / eventgridfilters | Nej |
> | namespaces / networkrulesets | Nej |
> | namespaces / queues | Nej |
> | namespaces / queues / authorizationrules | Nej |
> | namespaces / topics | Nej |
> | namespaces / topics / authorizationrules | Nej |
> | namespaces / topics / subscriptions | Nej |
> | namespaces / topics / subscriptions / rules | Nej |
> | premiumMessagingRegions | Nej |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | clusters | Ja |
> | clusters / applications | Nej |
> | containerGroups | Ja |
> | containerGroupSets | Ja |
> | edgeclusters | Ja |
> | edgeclusters / applications | Nej |
> | networks | Ja |
> | secretstores | Ja |
> | secretstores / certificates | Nej |
> | secretstores / secrets | Nej |
> | volumes | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | containerGroups | Ja |
> | gateways | Ja |
> | networks | Ja |
> | hemligheter | Ja |
> | volumes | Ja |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | Nej |
> | providerRegistrations / resourceTypeRegistrations | Nej |
> | rollouts | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SignalR | Ja |
> | SignalR / eventGridFilters | Nej |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SiteRecoveryVault | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridUseBenefits | Nej |

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
> | managedInstances / databases | Ja |
> | managedInstances / databases / backupShortTermRetentionPolicies | Nej |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Nej |
> | managedInstances / databases / vulnerabilityAssessments | Nej |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Nej |
> | managedInstances / encryptionProtector | Nej |
> | managedInstances / keys | Nej |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nej |
> | managedInstances / vulnerabilityAssessments | Nej |
> | servrar | Ja |
> | servers / administrators | Nej |
> | servers / communicationLinks | Nej |
> | servers / databases | Ja |
> | servers / encryptionProtector | Nej |
> | servers / firewallRules | Nej |
> | servers / keys | Nej |
> | servers / restorableDroppedDatabases | Nej |
> | servers / serviceobjectives | Nej |
> | servers / tdeCertificates | Nej |
> | virtualClusters | Nej |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nej |
> | SqlVirtualMachines | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageAccounts | Ja |
> | storageAccounts / blobServices | Nej |
> | storageAccounts / fileServices | Nej |
> | storageAccounts / queueServices | Nej |
> | storageAccounts / services | Nej |
> | storageAccounts / services / metricDefinitions | Nej |
> | storageAccounts / tableServices | Nej |
> | usages | Nej |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | caches | Ja |
> | caches / storageTargets | Nej |
> | usageModels | Nej |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | replicationGroups | Nej |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nej |
> | storageSyncServices / syncGroups | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej |
> | storageSyncServices / workflows | Nej |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nej |
> | storageSyncServices / syncGroups | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej |
> | storageSyncServices / workflows | Nej |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nej |
> | storageSyncServices / syncGroups | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej |
> | storageSyncServices / workflows | Nej |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managers | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | streamingjobs | Ja |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cancel | Nej |
> | CreateSubscription | Nej |
> | enable | Nej |
> | rename | Nej |
> | SubscriptionDefinitions | Nej |
> | SubscriptionOperations | Nej |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | environments | Ja |
> | environments / accessPolicies | Nej |
> | environments / eventsources | Ja |
> | environments / referenceDataSets | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ja |
> | dedicatedCloudServices | Ja |
> | virtualMachines | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | apiManagementAccounts | Nej |
> | apiManagementAccounts / apiAcls | Nej |
> | apiManagementAccounts / apis | Nej |
> | apiManagementAccounts / apis / apiAcls | Nej |
> | apiManagementAccounts / apis / connectionAcls | Nej |
> | apiManagementAccounts / apis / connections | Nej |
> | apiManagementAccounts / apis / connections / connectionAcls | Nej |
> | apiManagementAccounts / apis / localizedDefinitions | Nej |
> | apiManagementAccounts / connectionAcls | Nej |
> | apiManagementAccounts / connections | Nej |
> | billingMeters | Nej |
> | certificates | Ja |
> | connectionGateways | Ja |
> | connections | Ja |
> | customApis | Ja |
> | deletedSites | Nej |
> | functions | Nej |
> | hostingEnvironments | Ja |
> | hostingEnvironments / multiRolePools | Nej |
> | hostingEnvironments / workerPools | Nej |
> | publishingUsers | Nej |
> | recommendations | Nej |
> | resourceHealthMetadata | Nej |
> | runtimes | Nej |
> | serverFarms | Ja |
> | serverFarms / eventGridFilters | Nej |
> | sites | Ja |
> | sites/config  | Nej |
> | sites / eventGridFilters | Nej |
> | sites / hostNameBindings | Nej |
> | sites / networkConfig | Nej |
> | sites / premieraddons | Ja |
> | sites / slots | Ja |
> | sites / slots / eventGridFilters | Nej |
> | sites / slots / hostNameBindings | Nej |
> | sites / slots / networkConfig | Nej |
> | sourceControls | Nej |
> | validate | Nej |
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
> | components | Nej |
> | componentsSummary | Nej |
> | monitorInstances | Nej |
> | monitorInstancesSummary | Nej |
> | monitors | Nej |
> | notificationSettings | Nej |

## <a name="next-steps"></a>Nästa steg

To get the same data as a file of comma-separated values, download [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
