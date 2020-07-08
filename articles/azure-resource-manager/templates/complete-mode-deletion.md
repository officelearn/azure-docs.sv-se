---
title: Borttagning i complete-läget
description: Visar hur resurs typer hanterar fullständig borttagning av läge i Azure Resource Manager mallar.
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 5e247fc20a128b3dd8b3fe646ef956388e0d2c2d
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027834"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Borttagning av Azure-resurser för komplett läges distributioner

I den här artikeln beskrivs hur resurs typer hanterar borttagning när de inte finns i en mall som distribueras i fullständigt läge.

De resurs typer som marker ATS med **Ja** tas bort när typen inte finns i mallen distribuerad med slutfört läge.

Resurs typerna som marker ATS med **Nej** tas inte bort automatiskt när de inte finns i mallen. de tas dock bort om den överordnade resursen tas bort. En fullständig beskrivning av beteendet finns i [Azure Resource Manager distributions lägen](deployment-modes.md).

Om du distribuerar till [fler än en resurs grupp i en mall](cross-resource-group-deployment.md)är resurserna i resurs gruppen som anges i distributions åtgärden tillgängliga för borttagning. Resurser i de sekundära resurs grupperna tas inte bort.

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
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. Azure. Genève](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. fakturering](#microsoftbilling)
> - [Microsoft. Bingkartssökning](#microsoftbingmaps)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. skiss](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft. förbrukning](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. container service](#microsoftcontainerservice)
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
> - [Microsoft. DataProtection](#microsoftdataprotection)
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
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. experimentering](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
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
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. nyckel valv](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
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
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. Notebook](#microsoftnotebooks)
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
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. Quantum](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. Synapse](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VMwareOnAzure](#microsoftvmwareonazure)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DomainServices | Ja |
> | DomainServices / oucontainer | No |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | supportProviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | aadsupportcases | No |
> | addsservices | No |
> | aktörer | No |
> | anonymousapiusers | No |
> | konfiguration | No |
> | loggar | No |
> | rapporter | No |
> | servicehealthmetrics | No |
> | services | No |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konfigurationer | No |
> | generateRecommendations | No |
> | metadata | No |
> | rekommendationer | No |
> | utelämningar | No |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | actionRules | Ja |
> | aviseringar | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | smartDetectorAlertRules | Ja |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | brygghuvudservrar | Ja |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | reportFeedback | No |
> | tjänst | Ja |
> | validateServiceName | No |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Spring | Ja |
> | Våren/appar | No |
> | Våren/appar/distributioner | No |

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attestationProviders | Ja |
> | defaultProviders | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicAdministrators | No |
> | dataAliases | No |
> | denyAssignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
> | hålls | No |
> | behörigheter | No |
> | policyAssignments | No |
> | policyDefinitions | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | No |
> | roleAssignments | No |
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions | No |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | automationAccounts | Ja |
> | automationAccounts/konfigurationer | Ja |
> | automationAccounts/jobb | No |
> | automationAccounts / privateEndpointConnectionProxies | No |
> | automationAccounts / privateEndpointConnections | No |
> | automationAccounts / privateLinkResources | No |
> | automationAccounts/Runbooks | Ja |
> | automationAccounts / softwareUpdateConfigurations | No |
> | automationAccounts/Webhooks | No |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateClouds | Ja |
> | privateClouds/auktoriseringar | No |
> | privateClouds/kluster | No |
> | privateClouds / hcxEnterpriseSites | No |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genève

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | No |
> | miljöer/konton | No |
> | miljöer/konton/namn områden | No |
> | miljöer/konton/namn rymder/konfigurationer | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | b2cDirectories | Ja |
> | b2ctenants | No |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataControllers | Ja |
> | hybridDataManagers | Ja |
> | postgresInstances | Ja |
> | sqlInstances | Ja |
> | sqlManagedInstances | Ja |
> | sqlServerInstances | Ja |
> | sqlServerRegistrations | Ja |
> | sqlServerRegistrations/sqlServer | No |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions | Ja |
> | registreringar | Ja |
> | registreringar/customerSubscriptions | No |
> | registreringar/produkter | No |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | batchAccounts | Ja |

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/avtal | No |
> | billingAccounts / billingPermissions | No |
> | billingAccounts / billingProfiles | No |
> | billingAccounts / billingProfiles / billingPermissions | No |
> | billingAccounts / billingProfiles / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / billingSubscriptions | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/kunder | No |
> | billingAccounts/billingProfiles/instruktioner | No |
> | billingAccounts/billingProfiles/fakturor | No |
> | billingAccounts/billingProfiles/fakturor/pris dokument | No |
> | billingAccounts/billingProfiles/fakturor/transaktioner | No |
> | billingAccounts / billingProfiles / invoiceSections | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No |
> | billingAccounts/billingProfiles/invoiceSections/Products | No |
> | billingAccounts/billingProfiles/invoiceSections/Products/transfer | No |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | No |
> | billingAccounts/billingProfiles/invoiceSections/transaktioner | No |
> | billingAccounts/billingProfiles/invoiceSections/transfers | No |
> | billingAccounts / BillingProfiles / patchOperations | No |
> | billingAccounts / billingProfiles / paymentMethods | No |
> | billingAccounts/billingProfiles/policys | No |
> | billingAccounts/billingProfiles/pris dokument | No |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/Products | No |
> | billingAccounts/billingProfiles/transaktioner | No |
> | billingAccounts / billingRoleAssignments | No |
> | billingAccounts / billingRoleDefinitions | No |
> | billingAccounts / billingSubscriptions | No |
> | billingAccounts/billingSubscriptions/fakturor | No |
> | billingAccounts / createBillingRoleAssignment | No |
> | billingAccounts / createInvoiceSectionOperations | No |
> | billingAccounts/kunder | No |
> | billingAccounts/kunder/billingPermissions | No |
> | billingAccounts/kunder/billingSubscriptions | No |
> | billingAccounts/kunder/initiateTransfer | No |
> | billingAccounts/kunder/principer | No |
> | billingAccounts/kunder/produkter | No |
> | billingAccounts/kunder/transaktioner | No |
> | billingAccounts/kunder/överföringar | No |
> | billingAccounts/avdelningar | No |
> | billingAccounts/avdelningar/billingPermissions | No |
> | billingAccounts/avdelningar/billingRoleAssignments | No |
> | billingAccounts/avdelningar/billingRoleDefinitions | No |
> | billingAccounts / enrollmentAccounts | No |
> | billingAccounts / enrollmentAccounts / billingPermissions | No |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | No |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | No |
> | billingAccounts/fakturor | No |
> | billingAccounts/fakturor/transaktioner | No |
> | billingAccounts / invoiceSections | No |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | No |
> | billingAccounts / invoiceSections / billingSubscriptions | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No |
> | billingAccounts/invoiceSections/höjning | No |
> | billingAccounts / invoiceSections / initiateTransfer | No |
> | billingAccounts / invoiceSections / patchOperations | No |
> | billingAccounts / invoiceSections / productMoveOperations | No |
> | billingAccounts/invoiceSections/Products | No |
> | billingAccounts/invoiceSections/Products/transfer | No |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | No |
> | billingAccounts/invoiceSections/transaktioner | No |
> | billingAccounts/invoiceSections/överföringar | No |
> | billingAccounts / lineOfCredit | No |
> | billingAccounts / patchOperations | No |
> | billingAccounts / paymentMethods | No |
> | billingAccounts/produkter | No |
> | billingAccounts/transaktioner | No |
> | billingPeriods | No |
> | billingPermissions | No |
> | billingProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | enheten | No |
> | enrollmentAccounts | No |
> | fakturor | No |
> | överlåtelse | No |
> | överföringar/acceptTransfer | No |
> | överföringar/declineTransfer | No |
> | överföringar/operationStatus | No |
> | överföringar/validateTransfer | No |
> | validateAddress | No |

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | mapApis | Ja |
> | updateCommunicationPreference | No |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blockchainMembers | Ja |
> | cordaMembers | Ja |
> | Övervakare | Ja |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | TokenServices | Ja |
> | TokenServices / BlockchainNetworks | No |
> | TokenServices/grupper | No |
> | TokenServices/grupper/konton | No |
> | TokenServices / TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments / assignmentOperations | No |
> | blueprintAssignments/åtgärder | No |
> | modeller | No |
> | skisser/artefakter | No |
> | skisser/versioner | No |
> | skisser/versioner/artefakter | No |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | botServices | Ja |
> | botServices/kanaler | No |
> | botServices/anslutningar | No |
> | språk | No |
> | mallar | No |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Redis | Ja |
> | Redis/EventGridFilters | No |
> | Redis/privateEndpointConnectionProxies | No |
> | Redis/privateEndpointConnectionProxies/validate | No |
> | Redis/privateEndpointConnections | No |
> | Redis/privateLinkResources | No |
> | redisEnterprise | Ja |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appliedReservations | No |
> | autoQuotaIncrease | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | No |
> | kataloger | No |
> | commercialReservationOrders | No |
> | utväxla | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders / calculateRefund | No |
> | reservationOrders/slå samman | No |
> | reservationOrders/reservationer | No |
> | reservationOrders/reservationer/revisioner | No |
> | reservationOrders/retur | No |
> | reservationOrders/Split | No |
> | reservationOrders/växling | No |
> | reservera | No |
> | resourceProviders | No |
> | resources | No |
> | validateReservationOrder | No |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Ja |
> | edgenodes | No |
> | filer | Ja |
> | profiler/slut punkter | Ja |
> | profiler/slut punkter/customdomains | No |
> | profiler/slut punkter/origingroups | No |
> | profiler/slut punkter/ursprung | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certificateOrders | Ja |
> | certificateOrders/certifikat | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | profil | No |
> | resourceChanges | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | No |
> | Domän namn | Ja |
> | Domän namn/funktioner | No |
> | Domän namn/internalLoadBalancers | No |
> | Domän namn/serviceCertificates | No |
> | Domän namn/platser | No |
> | Domän namn/platser/roller | No |
> | Domän namn/platser/roller/metricDefinitions | No |
> | Domän namn/platser/roller/mått | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Ja |
> | virtualMachines / diagnosticSettings | No |
> | virtualMachines / metricDefinitions | No |
> | virtualMachines/mått | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections/peering | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Ja |
> | quotas | No |
> | reservedIps | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks/virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | No |
> | disk | No |
> | images | No |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | storageAccounts | Ja |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/metricDefinitions | No |
> | storageAccounts/mått | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/tjänster | No |
> | storageAccounts/tjänster/diagnosticSettings | No |
> | storageAccounts/tjänster/metricDefinitions | No |
> | storageAccounts/tjänster/mått | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/vmImages | No |
> | vmImages | No |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilitySets | Ja |
> | diskAccesses | Ja |
> | diskEncryptionSets | Ja |
> | disk | Ja |
> | gallerier | Ja |
> | gallerier/program | No |
> | gallerier/program/versioner | No |
> | gallerier/bilder | No |
> | gallerier/avbildningar/versioner | No |
> | hostGroups | Ja |
> | hostGroups/värdar | Ja |
> | images | Ja |
> | proximityPlacementGroups | Ja |
> | restorePointCollections | Ja |
> | restorePointCollections / restorePoints | No |
> | sharedVMExtensions | Ja |
> | sharedVMExtensions/versioner | No |
> | sharedVMImages | Ja |
> | sharedVMImages/versioner | No |
> | snapshots | Ja |
> | sshPublicKeys | Ja |
> | virtualMachines | Ja |
> | virtualMachines/tillägg | Ja |
> | virtualMachines / metricDefinitions | No |
> | virtualMachines / runCommands | Ja |
> | virtualMachineScaleSets | Ja |
> | virtualMachineScaleSets/tillägg | No |
> | virtualMachineScaleSets/networkInterfaces | No |
> | virtualMachineScaleSets/publicIPAddresses | No |
> | virtualMachineScaleSets/virtualMachines | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No |

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CacheNodes | Ja |

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | AggregatedCost | No |
> | Saldon | No |
> | Budgetar | No |
> | Avgifter | No |
> | CostTags | No |
> | krediter | No |
> | händelser | No |
> | Prognoser | No |
> | samtliga | No |
> | Marknads platser | No |
> | Prisdokument | No |
> | läkemedle | No |
> | ReservationDetails | No |
> | ReservationRecommendationDetails | No |
> | ReservationRecommendations | No |
> | ReservationSummaries | No |
> | ReservationTransactions | Nej |
> | Taggar | Nej |
> | klienter | No |
> | Termer | No |
> | UsageDetails | No |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerGroups | Ja |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | register | Ja |
> | register/agentPools | Ja |
> | register/versioner | No |
> | register/versioner/Avbryt | No |
> | register/build/getLogLink | No |
> | register/buildTasks | Ja |
> | register/buildTasks/steg | No |
> | register/eventGridFilters | No |
> | register/exportPipelines | No |
> | register/generateCredentials | No |
> | register/getBuildSourceUploadUrl | No |
> | register/GetCredentials | No |
> | register/importImage | No |
> | register/importPipelines | No |
> | register/pipelineRuns | No |
> | register/privateEndpointConnectionProxies | No |
> | register/privateEndpointConnectionProxies/validate | No |
> | register/privateEndpointConnections | No |
> | register/privateLinkResources | No |
> | register/queueBuild | No |
> | register/regenerateCredential | No |
> | register/regenerateCredentials | No |
> | register/replikeringar | Ja |
> | register/körningar | No |
> | register/körningar/Avbryt | No |
> | register/scheduleRun | No |
> | register/scopeMaps | No |
> | register/taskRuns | No |
> | register/uppgifter | Ja |
> | register/token | No |
> | register/updatePolicies | No |
> | register/Webhooks | Ja |
> | register/Webhooks/getCallbackConfig | No |
> | register/Webhooks/ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerServices | Ja |
> | managedClusters | Ja |
> | openShiftManagedClusters | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aviseringar | No |
> | BillingAccounts | No |
> | Budgetar | No |
> | CloudConnectors | No |
> | Anslutningar | Ja |
> | costAllocationRules | No |
> | Avdelningar | No |
> | Dimensioner | No |
> | EnrollmentAccounts | No |
> | Exporteras | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/aviseringar | No |
> | ExternalBillingAccounts/dimensioner | No |
> | ExternalBillingAccounts/prognos | No |
> | ExternalBillingAccounts/fråga | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions/aviseringar | No |
> | ExternalSubscriptions/dimensioner | No |
> | ExternalSubscriptions/prognos | No |
> | ExternalSubscriptions/fråga | No |
> | Prognos | No |
> | Söka i data | No |
> | registrera | No |
> | Reportconfigs | No |
> | Rapporter | No |
> | Inställningar | No |
> | showbackRules | No |
> | Vyer | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autentiseringsbegäran | No |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | typer | No |
> | resourceProviders | Ja |

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utskrifts | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Ja |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbetsytor | Ja |
> | arbets ytor/dbWorkspaces | No |
> | arbets ytor/storageEncryption | No |
> | arbets ytor/virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kataloger | Ja |
> | datacatalogs | Ja |
> | datacatalogs/data källor | No |
> | datacatalogs/data källor/-genomsökningar | No |
> | datacatalogs/data källor/genomsökningar/data uppsättningar | No |
> | datacatalogs/data källor/genomsökningar/utlösare | No |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataFactories | Ja |
> | dataFactories / diagnosticSettings | No |
> | dataFactories / metricDefinitions | No |
> | dataFactorySchema | No |
> | fabriker | Ja |
> | fabriker/integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/dataLakeStoreAccounts | No |
> | konton/storageAccounts | No |
> | konton/storageAccounts/behållare | No |
> | konton/transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/eventGridFilters | No |
> | konton/firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |
> | tjänster/projekt | Ja |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | BackupVaults | Ja |
> | ResourceOperationGateKeepers | Ja |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/resurser | No |
> | konton/resurser/data uppsättningar | No |
> | konton/resurser/inbjudningar | No |
> | konton/resurser/providersharesubscriptions | No |
> | konton/resurser/synchronizationSettings | No |
> | konton/sharesubscriptions | No |
> | konton/sharesubscriptions/consumerSourceDataSets | No |
> | konton/sharesubscriptions/datasetmappings | No |
> | konton/sharesubscriptions/utlösare | No |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | brygghuvudservrar | Ja |
> | servrar/rådgivare | No |
> | servrar/nycklar | No |
> | servrar/privateEndpointConnectionProxies | No |
> | servrar/privateEndpointConnections | No |
> | servrar/privateLinkResources | No |
> | servrar/queryTexts | No |
> | servrar/recoverableServers | No |
> | servrar/topQueryStatistics | No |
> | servrar/virtualNetworkRules | No |
> | servrar/waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | brygghuvudservrar | Ja |
> | servrar/rådgivare | No |
> | servrar/nycklar | No |
> | servrar/privateEndpointConnectionProxies | No |
> | servrar/privateEndpointConnections | No |
> | servrar/privateLinkResources | No |
> | servrar/queryTexts | No |
> | servrar/recoverableServers | No |
> | servrar/topQueryStatistics | No |
> | servrar/virtualNetworkRules | No |
> | servrar/waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | serverGroups | Ja |
> | brygghuvudservrar | Ja |
> | servrar/rådgivare | No |
> | servrar/nycklar | No |
> | servrar/privateEndpointConnectionProxies | No |
> | servrar/privateEndpointConnections | No |
> | servrar/privateLinkResources | No |
> | servrar/queryTexts | No |
> | servrar/recoverableServers | No |
> | servrar/topQueryStatistics | No |
> | servrar/virtualNetworkRules | No |
> | servrar/waitStatistics | No |
> | serversv2 | Ja |
> | singleServers | Ja |

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
> | applicationgroups/program | No |
> | applicationgroups/Station ära datorer | No |
> | applicationgroups / startmenuitems | No |
> | hostpools | Ja |
> | hostpools / sessionhosts | No |
> | hostpools / sessionhosts / usersessions | No |
> | hostpools / usersessions | No |
> | arbetsytor | Ja |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ElasticPools | Ja |
> | ElasticPools / IotHubTenants | Ja |
> | ElasticPools/IotHubTenants/securitySettings | No |
> | IotHubs | Ja |
> | IotHubs / eventGridFilters | No |
> | IotHubs/securitySettings | No |
> | ProvisioningServices | Ja |
> | användningar | No |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | pipelines | Ja |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domänkontrollanter | Ja |

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

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | digitalTwinsInstances | Ja |
> | digitalTwinsInstances/slut punkter | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | databaseAccountNames | No |
> | databaseAccounts | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Ja |
> | domäner/domainOwnershipIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects / clouddeployments | No |
> | lcsprojects/kopplingar | No |

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
> | domäner/ämnen | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | partnerNamespaces | Ja |
> | partnerNamespaces/eventChannels | No |
> | partnerRegistrations | Ja |
> | partnerTopics | Ja |
> | partnerTopics / eventSubscriptions | No |
> | systemTopics | Ja |
> | systemTopics / eventSubscriptions | No |
> | avsnitt | Ja |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | namn områden | Ja |
> | namnrymder/authorizationrules | No |
> | namnrymder/disasterrecoveryconfigs | No |
> | namnrymder/eventhubs | No |
> | namnrymder/eventhubs/authorizationrules | No |
> | namnrymder/eventhubs/consumergroups | No |
> | namnrymder/networkrulesets | No |

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | experimentWorkspaces | Ja |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | featureProviders | No |
> | funktioner | No |
> | finansiär | No |
> | subscriptionFeatureRegistrations | No |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certifiering | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | områden | No |
> | område/områden | No |
> | område/områden/områden | No |
> | område/områden/områden/galleryitems | No |
> | områdets/områden/galleryitems | No |
> | områden/galleryitems | No |
> | registrera | No |
> | resources | No |
> | retrieveresourcesbyid | No |

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autoManagedAccounts | Ja |
> | autoManagedVmConfigurationProfiles | Ja |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | IntelliPoint | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

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
> | kluster/program | No |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |
> | tjänster/iomtconnectors | No |
> | tjänster/iomtconnectors/anslutningar | No |
> | tjänster/iomtconnectors/mappningar | No |
> | tjänster/privateEndpointConnectionProxies | No |
> | tjänster/privateEndpointConnections | Ja |
> | tjänster/privateLinkResources | Ja |

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

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | devices | Ja |
> | registeredSubscriptions | No |
> | leverantören | No |
> | leverantörer/SKU: er | No |
> | leverantörer/vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | vnfs | Ja |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | delarna | Ja |
> | networkScopes | Ja |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utskrifts | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appTemplates | No |
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
> | deletedVaults | No |
> | hsmPools | Ja |
> | managedHSMs | Ja |
> | valv | Ja |
> | valv/accessPolicies | No |
> | valv/eventGridFilters | No |
> | valv/hemligheter | No |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | connectedClusters | Ja |
> | registeredSubscriptions | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | sourceControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | kluster/attacheddatabaseconfigurations | No |
> | kluster/databaser | No |
> | kluster/databaser/dataconnections | No |
> | kluster/databaser/eventhubconnections | No |
> | kluster/databaser/principalassignments | No |
> | kluster/dataconnections | No |
> | kluster/principalassignments | No |
> | kluster/sharedidentities | No |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labaccounts | Ja |
> | användare | No |

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
> | arbetsytor | Ja |
> | arbets ytor/beräkningar | No |
> | arbets ytor/eventGridFilters | No |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applyUpdates | No |
> | configurationAssignments | No |
> | maintenanceConfigurations | Ja |
> | uppdateringar | No |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Identiteter | No |
> | userAssignedIdentities | Ja |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedNetworks | Ja |
> | managedNetworks / managedNetworkGroups | Ja |
> | managedNetworks / managedNetworkPeeringPolicies | Ja |
> | avisering | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions | No |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | getEntities | No |
> | managementGroups | No |
> | managementGroups/inställningar | No |
> | resources | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/eventGridFilters | No |
> | konton/privateAtlases | Ja |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | budgivning | No |
> | offerTypes | No |
> | offerTypes/utgivare | No |
> | offerTypes/utgivare/erbjudanden | No |
> | offerTypes/utgivare/erbjudanden/planer | No |
> | offerTypes/utgivare/erbjudanden/planer/avtal | No |
> | offerTypes/utgivare/erbjudanden/planer/konfigurationer | No |
> | offerTypes/utgivare/erbjudanden/planer/configs/importImage | No |
> | privategalleryitems | No |
> | privateStoreClient | No |
> | privateStores | No |
> | privateStores/erbjudanden | No |
> | läkemedle | No |
> | Utgivare | No |
> | Utgivare/erbjudanden | No |
> | Utgivare/erbjudanden/ändringar | No |
> | registrera | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicDevServices | Ja |
> | updateCommunicationPreference | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | villkor | No |
> | offertypes | No |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Media Services | Ja |
> | Media Services/accountFilters | No |
> | Media Services/till gångar | No |
> | Media Services/assets/assetFilters | No |
> | Media Services/contentKeyPolicies | No |
> | Media Services/eventGridFilters | No |
> | Media Services/liveEventOperations | No |
> | Media Services/liveEvents | Ja |
> | Media Services/liveEvents/liveOutputs | No |
> | Media Services/liveEvents/privateEndpointConnectionProxies | No |
> | Media Services/liveOutputOperations | No |
> | Media Services/mediaGraphs | No |
> | Media Services/streamingEndpointOperations | No |
> | Media Services/strömnings slut punkter | Ja |
> | Media Services/strömnings slut punkter/privateEndpointConnectionProxies | No |
> | Media Services/streamingLocators | No |
> | Media Services/streamingPolicies | No |
> | Media Services/streamingPrivateEndpointConnectionProxyOperations | No |
> | Media Services/transformeringar | No |
> | Media Services/transformeringar/jobb | No |

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
> | moveCollections | Ja |
> | samarbetsprojekt | Ja |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ja |
> | objectUnderstandingAccounts | Ja |
> | remoteRenderingAccounts | Ja |
> | spatialAnchorsAccounts | Ja |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | netAppAccounts | Ja |
> | netAppAccounts / accountBackups | No |
> | netAppAccounts / capacityPools | Ja |
> | netAppAccounts/capacityPools/Volumes | Ja |
> | netAppAccounts/capacityPools/volym/ögonblicks bilder | Ja |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationGateways | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja |
> | applicationSecurityGroups | Ja |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Ja |
> | bastionHosts | Ja |
> | bgpServiceCommunities | No |
> | anslutning | Ja |
> | ddosCustomPolicies | Ja |
> | ddosProtectionPlans | Ja |
> | dnsOperationStatuses | No |
> | dnszones | Ja |
> | dnszones/A | No |
> | dnszones/AAAA | No |
> | dnszones/alla | No |
> | dnszones/CAA | No |
> | dnszones/CNAME | No |
> | dnszones/MX | No |
> | dnszones/NS | No |
> | dnszones/PTR | No |
> | dnszones/Recordset | No |
> | dnszones/SOA | No |
> | dnszones/SRV | No |
> | dnszones/TXT | No |
> | expressRouteCircuits | Ja |
> | expressRouteCrossConnections | Ja |
> | expressRouteGateways | Ja |
> | expressRoutePorts | Ja |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Ja |
> | frontdoors | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Ja |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | Belastningsutjämnare | Ja |
> | localNetworkGateways | Ja |
> | natGateways | Ja |
> | networkIntentPolicies | Ja |
> | networkInterfaces | Ja |
> | networkProfiles | Ja |
> | networkSecurityGroups | Ja |
> | networkWatchers | Ja |
> | networkWatchers / connectionMonitors | Ja |
> | networkWatchers / flowLogs | Ja |
> | networkWatchers/linser | Ja |
> | networkWatchers / pingMeshes | Ja |
> | p2sVpnGateways | Ja |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Ja |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/alla | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones / virtualNetworkLinks | Ja |
> | privateEndpoints | Ja |
> | privateLinkServices | Ja |
> | publicIPAddresses | Ja |
> | publicIPPrefixes | Ja |
> | routeFilters | Ja |
> | routeTables | Ja |
> | serviceEndpointPolicies | Ja |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Ja |
> | trafficmanagerprofiles/termiska kartor | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Ja |
> | virtualNetworkGateways | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks/undernät | No |
> | virtualNetworkTaps | Ja |
> | virtualWans | Ja |
> | vpnGateways | Ja |
> | vpnSites | Ja |
> | webApplicationFirewallPolicies | Ja |

## <a name="microsoftnotebooks"></a>Microsoft. Notebook

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | NotebookProxies | No |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

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
> | deletedWorkspaces | No |
> | linkTargets | No |
> | storageInsightConfigs | No |
> | arbetsytor | Ja |
> | arbets ytor/dataExports | No |
> | arbets ytor/data källor | No |
> | arbets ytor/linkedServices | No |
> | arbets ytor/linkedStorageAccounts | No |
> | arbets ytor/metadata | No |
> | arbets ytor/fråga | No |
> | arbets ytor/scopedPrivateLinkProxies | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations | Ja |
> | lösningar | Ja |
> | vyer | Ja |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | legacyPeerings | No |
> | peerAsns | No |
> | peerings | Ja |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | peeringServices | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | reparationer | No |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> |  -konsoler | No |
> | instrumentpaneler | Ja |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Ja |
> | klienter | Ja |
> | innehavare/arbets ytor | No |
> | workspaceCollections | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kapaciteter | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations / defaultRollouts | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | distributioner | Ja |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Arbetsytor | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | valv | Ja |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | OpenShiftClusters | Ja |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja |
> | namnrymder/authorizationrules | No |
> | namnrymder/hybridconnections | No |
> | namnrymder/hybridconnections/authorizationrules | No |
> | namnrymder/privateEndpointConnections | No |
> | namnrymder/wcfrelays | No |
> | namnrymder/wcfrelays/authorizationrules | No |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | skickar | Ja |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | resources | No |
> | resourcesHistory | No |
> | subscriptionsStatus | No |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilityStatuses | No |
> | childAvailabilityStatuses | No |
> | childResources | No |
> | emergingissues | No |
> | händelser | No |
> | impactedResources | No |
> | metadata | No |
> | meddelanden | No |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | calculateTemplateHash | No |
> | distributioner | No |
> | distributioner/åtgärder | No |
> | deploymentScripts | Ja |
> | deploymentScripts/loggar | No |
> | Länkar | No |
> | notifyResourceJobs | No |
> | finansiär | No |
> | resourceGroups | No |
> | prenumerationer | No |
> | templateSpecs | Ja |
> | templateSpecs/versioner | Ja |
> | klienter | No |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | saasresources | No |

## <a name="microsoftsearch"></a>Microsoft. search

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Ja |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | aviseringar | No |
> | alertsSuppressionRules | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | utvärderingar | No |
> | autoDismissAlertsRules | No |
> | automatiseringar | Ja |
> | AutoProvisioningSettings | No |
> | Godkännanden | No |
> | dataCollectionAgents | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | iotSecuritySolutions | Ja |
> | iotSecuritySolutions / analyticsModels | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No |
> | iotSecuritySolutions / iotAlerts | No |
> | iotSecuritySolutions / iotAlertTypes | No |
> | jitNetworkAccessPolicies | No |
> | policies | No |
> | prissättningar | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores | No |
> | secureScores / secureScoreControls | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | inställningar | No |
> | underbedömningar | No |
> | uppgifter | No |
> | topologier | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | agg regeringar | No |
> | alertRules | No |
> | alertRuleTemplates | No |
> | automationRules | No |
> | bokmärken | No |
> | fall | No |
> | dataConnectors | No |
> | dataConnectorsCheckRequirements | No |
> | poster | No |
> | entityQueries | No |
> | incidenter | No |
> | officeConsents | No |
> | inställningar | No |
> | threatIntelligence | No |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | consoleServices | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja |
> | namnrymder/authorizationrules | No |
> | namnrymder/disasterrecoveryconfigs | No |
> | namnrymder/eventgridfilters | No |
> | namnrymder/networkrulesets | No |
> | namnrymder/köer | No |
> | namnrymder/köer/authorizationrules | No |
> | namn områden/ämnen | No |
> | namnrymder/ämnen/authorizationrules | No |
> | namnrymder/ämnen/prenumerationer | No |
> | namn områden/ämnen/prenumerationer/regler | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | kluster | Ja |
> | kluster/program | No |
> | containerGroups | Ja |
> | containerGroupSets | Ja |
> | edgeclusters | Ja |
> | edgeclusters/program | No |
> | managedclusters | Ja |
> | managedclusters/nodetypes | No |
> | nätet | Ja |
> | secretstores | Ja |
> | secretstores/certifikat | No |
> | secretstores/hemligheter | No |
> | volumes | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

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
> | providerRegistrations | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | distributioner | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SignalR | Ja |
> | SignalR/eventGridFilters | No |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

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
> | managedInstances/databaser/backupShortTermRetentionPolicies | No |
> | managedInstances/databaser/scheman/tabeller/kolumner/sensitivityLabels | No |
> | managedInstances/databaser/vulnerabilityAssessments | No |
> | managedInstances/databaser/vulnerabilityAssessments/regler/bas linjer | No |
> | managedInstances / encryptionProtector | No |
> | managedInstances/nycklar | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No |
> | managedInstances / vulnerabilityAssessments | No |
> | brygghuvudservrar | Ja |
> | servrar/administratörer | No |
> | servrar/communicationLinks | No |
> | servrar/databaser | Ja |
> | servrar/encryptionProtector | No |
> | servrar/firewallRules | No |
> | servrar/nycklar | No |
> | servrar/restorableDroppedDatabases | No |
> | servrar/serviceobjectives | No |
> | servrar/tdeCertificates | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageAccounts | Ja |
> | storageAccounts/blobServices | No |
> | storageAccounts/fileServices | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/tjänster | No |
> | storageAccounts/tjänster/metricDefinitions | No |
> | storageAccounts/tableServices | No |
> | användningar | No |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cacheminnen | Ja |
> | cache-/storageTargets | No |
> | usageModels | No |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | replicationGroups | No |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/arbets flöden | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/arbets flöden | No |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/arbets flöden | No |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hantera | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | streamingjobs | Ja |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | avbryt | No |
> | CreateSubscription | No |
> | Aktivera | No |
> | byt namn | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |
> | prenumerationer | No |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateLinkHubs | Ja |
> | arbetsytor | Ja |
> | arbets ytor/bigDataPools | Ja |
> | arbets ytor/operationStatuses | No |
> | arbets ytor/sqlPools | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Ja |
> | miljöer/accessPolicies | No |
> | miljöer/eventsources | Ja |
> | miljöer/referenceDataSets | Ja |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Auktoriseringshanteraren | Ja |
> | butiker/accessPolicies | No |
> | butiker/tjänster | No |
> | butiker/tjänster/token | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | imageTemplates | Ja |
> | imageTemplates / runOutputs | No |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ArcZones | Ja |
> | ResourcePools | Ja |
> | VCenter | Ja |
> | VirtualMachines | Ja |
> | VirtualMachineTemplates | Ja |
> | VirtualNetworks | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ja |
> | dedicatedCloudServices | Ja |
> | virtualMachines | Ja |

## <a name="microsoftvmwareonazure"></a>Microsoft. VMwareOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateClouds | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | devices | Ja |
> | registeredSubscriptions | No |
> | leverantören | No |
> | leverantörer/SKU: er | No |
> | leverantörer/vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | vnfs | Ja |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | utgå | Ja |
> | registeredSubscriptions | No |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts / apiAcls | No |
> | apiManagementAccounts/API: er | No |
> | apiManagementAccounts/API/apiAcls | No |
> | apiManagementAccounts/API/connectionAcls | No |
> | apiManagementAccounts/API/anslutningar | No |
> | apiManagementAccounts/API/Connections/connectionAcls | No |
> | apiManagementAccounts/API/localizedDefinitions | No |
> | apiManagementAccounts / connectionAcls | No |
> | apiManagementAccounts/anslutningar | No |
> | billingMeters | No |
> | certifikat | Ja |
> | connectionGateways | Ja |
> | anslutning | Ja |
> | customApis | Ja |
> | deletedSites | No |
> | hostingEnvironments | Ja |
> | hostingEnvironments / eventGridFilters | No |
> | hostingEnvironments / multiRolePools | No |
> | hostingEnvironments / workerPools | No |
> | kubeEnvironments | Ja |
> | publishingUsers | No |
> | rekommendationer | No |
> | resourceHealthMetadata | No |
> | körningar | No |
> | Server grupper | Ja |
> | Server grupper/eventGridFilters | No |
> | webbplatser | Ja |
> | platser/konfig  | No |
> | platser/eventGridFilters | No |
> | platser/hostNameBindings | No |
> | platser/networkConfig | No |
> | platser/premieraddons | Ja |
> | platser/platser | Ja |
> | platser/platser/eventGridFilters | No |
> | platser/platser/hostNameBindings | No |
> | platser/platser/networkConfig | No |
> | sourceControls | No |
> | staticSites | Ja |
> | kontrollerar | No |
> | verifyHostingEnvironmentVnet | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | multipleActivationKeys | Ja |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DeviceServices | Ja |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbets belastningar | Ja |
> | arbets belastningar/instanser | No |
> | arbets belastningar/versioner | No |
> | arbets belastningar/versioner/artefakter | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | delarna | No |
> | componentsSummary | No |
> | monitorInstances | No |
> | monitorInstancesSummary | No |
> | Övervakare | No |
> | notificationSettings | Nej |

## <a name="next-steps"></a>Nästa steg

Hämta [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)för att hämta samma data som en fil med kommaavgränsade värden.
