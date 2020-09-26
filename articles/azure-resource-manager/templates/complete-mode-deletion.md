---
title: Borttagning i complete-läget
description: Visar hur resurs typer hanterar fullständig borttagning av läge i Azure Resource Manager mallar.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 1e27960953aeb16486f893054c7653edb98bbdbc
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372469"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Borttagning av Azure-resurser för komplett läges distributioner

I den här artikeln beskrivs hur resurs typer hanterar borttagning när de inte finns i en mall som distribueras i fullständigt läge.

De resurs typer som marker ATS med **Ja** tas bort när typen inte finns i mallen distribuerad med slutfört läge.

Resurs typerna som marker ATS med **Nej** tas inte bort automatiskt när de inte finns i mallen. de tas dock bort om den överordnade resursen tas bort. En fullständig beskrivning av beteendet finns i [Azure Resource Manager distributions lägen](deployment-modes.md).

Om du distribuerar till [fler än en resurs grupp i en mall](cross-scope-deployment.md)är resurserna i resurs gruppen som anges i distributions åtgärden tillgängliga för borttagning. Resurser i de sekundära resurs grupperna tas inte bort.

Hoppa till ett namn område för en resurs leverantör:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. API Management](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. attestering](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. autohantera](#microsoftautomanage)
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
> - [Microsoft. Codespaces](#microsoftcodespaces)
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
> - [Microsoft. D365CustomerInsights](#microsoftd365customerinsights)
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
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DeviceUpdate](#microsoftdeviceupdate)
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
> - [Microsoft. PolicyInsights](#microsoftpolicyinsights)
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
> - [Microsoft. ScVmm](#microsoftscvmm)
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
> | DomainServices | Yes |
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
> | advisorScore | No |
> | konfigurationer | No |
> | generateRecommendations | No |
> | metadata | No |
> | rekommendationer | No |
> | utelämningar | No |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | farmBeats | Yes |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | actionRules | Yes |
> | aviseringar | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | smartDetectorAlertRules | Yes |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | brygghuvudservrar | Yes |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | reportFeedback | No |
> | tjänst | Yes |
> | validateServiceName | No |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Yes |
> | configurationStores / eventGridFilters | No |
> | configurationStores/-värde | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Spring | Yes |
> | Våren/appar | No |
> | Våren/appar/distributioner | No |

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attestationProviders | Yes |
> | defaultProviders | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | No |
> | accessReviewScheduleSettings | No |
> | classicAdministrators | No |
> | dataAliases | No |
> | denyAssignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
> | hålls | No |
> | behörigheter | No |
> | policyAssignments | No |
> | policyDefinitions | No |
> | policyExemptions | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | Yes |
> | roleAssignments | No |
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions | No |

## <a name="microsoftautomanage"></a>Microsoft. autohantera

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | configurationProfileAssignments | No |
> | configurationProfilePreferences | Yes |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | automationAccounts | Yes |
> | automationAccounts/konfigurationer | Yes |
> | automationAccounts/jobb | No |
> | automationAccounts / privateEndpointConnectionProxies | No |
> | automationAccounts / privateEndpointConnections | No |
> | automationAccounts / privateLinkResources | No |
> | automationAccounts/Runbooks | Yes |
> | automationAccounts / softwareUpdateConfigurations | No |
> | automationAccounts/Webhooks | No |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateClouds | Yes |
> | privateClouds/auktoriseringar | No |
> | privateClouds/kluster | No |
> | privateClouds / globalReachConnections | No |
> | privateClouds / hcxEnterpriseSites | No |
> | privateClouds / workloadNetworks | No |
> | privateClouds / workloadNetworks / dhcpConfigurations | No |
> | privateClouds/workloadNetworks/gateways | No |
> | privateClouds / workloadNetworks / portMirroringProfiles | No |
> | privateClouds/workloadNetworks/segment | No |
> | privateClouds / workloadNetworks / virtualMachines | No |
> | privateClouds / workloadNetworks / vmGroups | No |

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
> | b2cDirectories | Yes |
> | b2ctenants | No |
> | guestUsages | Yes |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataControllers | Yes |
> | postgresInstances | Yes |
> | sqlManagedInstances | Yes |
> | sqlServerInstances | Yes |
> | sqlServerRegistrations | Yes |
> | sqlServerRegistrations/sqlServer | No |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions | Yes |
> | linkedSubscriptions | Yes |
> | registreringar | Yes |
> | registreringar/customerSubscriptions | No |
> | registreringar/produkter | No |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | batchAccounts | Yes |

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
> | billingAccounts/billingProfiles/reservationer | No |
> | billingAccounts/billingProfiles/transaktioner | No |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | No |
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
> | billingAccounts/reservationer | No |
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
> | mapApis | Yes |
> | updateCommunicationPreference | No |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | cordaMembers | Yes |
> | Övervakare | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | TokenServices | Yes |
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
> | botServices | Yes |
> | botServices/kanaler | No |
> | botServices/anslutningar | No |
> | språk | No |
> | mallar | No |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Redis | Yes |
> | Redis/EventGridFilters | No |
> | Redis/privateEndpointConnectionProxies | No |
> | Redis/privateEndpointConnectionProxies/validate | No |
> | Redis/privateEndpointConnections | No |
> | Redis/privateLinkResources | No |
> | redisEnterprise | Yes |
> | RedisEnterprise / privateEndpointConnectionProxies | No |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | No |
> | RedisEnterprise / privateEndpointConnections | No |
> | RedisEnterprise / privateLinkResources | No |

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
> | ownReservations | No |
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
> | CdnWebApplicationFirewallPolicies | Yes |
> | edgenodes | No |
> | filer | Yes |
> | profiler/slut punkter | Yes |
> | profiler/slut punkter/customdomains | No |
> | profiler/slut punkter/origingroups | No |
> | profiler/slut punkter/ursprung | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certificateOrders | Yes |
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
> | Domän namn | Yes |
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
> | virtualMachines | Yes |
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
> | networkSecurityGroups | Yes |
> | quotas | No |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
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
> | storageAccounts | Yes |
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

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utgå | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | konton/privateEndpointConnectionProxies | No |
> | konton/privateEndpointConnections | No |
> | konton/privateLinkResources | No |

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
> | availabilitySets | Yes |
> | cloudServices | Yes |
> | cloudServices/networkInterfaces | No |
> | cloudServices/publicIPAddresses | No |
> | cloudServices / roleInstances | No |
> | cloudServices/roleInstances/networkInterfaces | No |
> | cloudServices/roller | No |
> | diskAccesses | Yes |
> | diskEncryptionSets | Yes |
> | disk | Yes |
> | gallerier | Yes |
> | gallerier/program | No |
> | gallerier/program/versioner | No |
> | gallerier/bilder | No |
> | gallerier/avbildningar/versioner | No |
> | hostGroups | Yes |
> | hostGroups/värdar | Yes |
> | images | Yes |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections / restorePoints | No |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions/versioner | No |
> | sharedVMImages | Yes |
> | sharedVMImages/versioner | No |
> | snapshots | Yes |
> | sshPublicKeys | Yes |
> | virtualMachines | Yes |
> | virtualMachines/tillägg | Yes |
> | virtualMachines / metricDefinitions | No |
> | virtualMachines / runCommands | Yes |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets/tillägg | No |
> | virtualMachineScaleSets/networkInterfaces | No |
> | virtualMachineScaleSets/publicIPAddresses | No |
> | virtualMachineScaleSets/virtualMachines | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No |

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CacheNodes | Yes |

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
> | containerGroups | Yes |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | register | Yes |
> | register/agentPools | Yes |
> | register/versioner | No |
> | register/versioner/Avbryt | No |
> | register/build/getLogLink | No |
> | register/buildTasks | Yes |
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
> | register/replikeringar | Yes |
> | register/körningar | No |
> | register/körningar/Avbryt | No |
> | register/scheduleRun | No |
> | register/scopeMaps | No |
> | register/taskRuns | No |
> | register/uppgifter | Yes |
> | register/token | No |
> | register/updatePolicies | No |
> | register/Webhooks | Yes |
> | register/Webhooks/getCallbackConfig | No |
> | register/Webhooks/ping | No |

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerServices | Yes |
> | managedClusters | Yes |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aviseringar | No |
> | BillingAccounts | No |
> | Budgetar | No |
> | CloudConnectors | No |
> | Anslutningar | Yes |
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
> | Insikter | No |
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
> | resourceProviders | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | pipe | Yes |

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utskrifts | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Yes |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbetsytor | Yes |
> | arbets ytor/dbWorkspaces | No |
> | arbets ytor/storageEncryption | No |
> | arbets ytor/virtualNetworkPeerings | No |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kataloger | Yes |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataFactories | Yes |
> | dataFactories / diagnosticSettings | No |
> | dataFactories / metricDefinitions | No |
> | dataFactorySchema | No |
> | fabriker | Yes |
> | fabriker/integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | konton/dataLakeStoreAccounts | No |
> | konton/storageAccounts | No |
> | konton/storageAccounts/behållare | No |
> | konton/transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | konton/eventGridFilters | No |
> | konton/firewallRules | No |

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Yes |
> | tjänster/projekt | Yes |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | BackupVaults | Yes |
> | ResourceOperationGateKeepers | Yes |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
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
> | brygghuvudservrar | Yes |
> | servrar/rådgivare | No |
> | servrar/nycklar | No |
> | servrar/privateEndpointConnectionProxies | No |
> | servrar/privateEndpointConnections | No |
> | servrar/privateLinkResources | No |
> | servrar/queryTexts | No |
> | servrar/recoverableServers | No |
> | servrar/start | No |
> | servrar/stoppa | No |
> | servrar/topQueryStatistics | No |
> | servrar/virtualNetworkRules | No |
> | servrar/waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | brygghuvudservrar | Yes |
> | servrar/rådgivare | No |
> | servrar/nycklar | No |
> | servrar/privateEndpointConnectionProxies | No |
> | servrar/privateEndpointConnections | No |
> | servrar/privateLinkResources | No |
> | servrar/queryTexts | No |
> | servrar/recoverableServers | No |
> | servrar/start | No |
> | servrar/stoppa | No |
> | servrar/topQueryStatistics | No |
> | servrar/uppgradering | No |
> | servrar/virtualNetworkRules | No |
> | servrar/waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | serverGroups | Yes |
> | brygghuvudservrar | Yes |
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
> | serversv2 | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | artifactSources | Yes |
> | distributioner | Yes |
> | serviceTopologies | Yes |
> | serviceTopologies/tjänster | Yes |
> | serviceTopologies/tjänster/serviceUnits | Yes |
> | steg | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationgroups | Yes |
> | applicationgroups/program | No |
> | applicationgroups/Station ära datorer | No |
> | applicationgroups / startmenuitems | No |
> | hostpools | Yes |
> | hostpools / sessionhosts | No |
> | hostpools / sessionhosts / usersessions | No |
> | hostpools / usersessions | No |
> | arbetsytor | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ElasticPools | Yes |
> | ElasticPools / IotHubTenants | Yes |
> | ElasticPools/IotHubTenants/securitySettings | No |
> | IotHubs | Yes |
> | IotHubs / eventGridFilters | No |
> | IotHubs/securitySettings | No |
> | ProvisioningServices | Yes |
> | användningar | No |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | konton/instanser | Yes |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | pipelines | Yes |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domänkontrollanter | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labcenters | Yes |
> | Laboration | Yes |
> | labb/miljöer | Yes |
> | labb/serviceRunners | Yes |
> | labb/virtualMachines | Yes |
> | scheman | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | digitalTwinsInstances | Yes |
> | digitalTwinsInstances/slut punkter | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | databaseAccountNames | No |
> | databaseAccounts | Yes |
> | restorableDatabaseAccounts | No |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Yes |
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
> | services | Yes |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | domäner | Yes |
> | domäner/ämnen | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | partnerNamespaces | Yes |
> | partnerNamespaces/eventChannels | No |
> | partnerRegistrations | Yes |
> | partnerTopics | Yes |
> | partnerTopics / eventSubscriptions | No |
> | systemTopics | Yes |
> | systemTopics / eventSubscriptions | No |
> | avsnitt | Yes |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Yes |
> | namn områden | Yes |
> | namnrymder/authorizationrules | No |
> | namnrymder/disasterrecoveryconfigs | No |
> | namnrymder/eventhubs | No |
> | namnrymder/eventhubs/authorizationrules | No |
> | namnrymder/eventhubs/consumergroups | No |
> | namnrymder/networkrulesets | No |
> | namnrymder/privateEndpointConnections | No |

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | experimentWorkspaces | Yes |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Yes |

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
> | konton | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autoManagedAccounts | Yes |
> | autoManagedVmConfigurationProfiles | Yes |
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | IntelliPoint | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitors | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedHSMs | Yes |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Yes |
> | kluster/program | No |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Yes |
> | tjänster/iomtconnectors | No |
> | tjänster/iomtconnectors/anslutningar | No |
> | tjänster/iomtconnectors/mappningar | No |
> | tjänster/privateEndpointConnectionProxies | No |
> | tjänster/privateEndpointConnections | No |
> | tjänster/privateLinkResources | No |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | faxar | Yes |
> | datorer/assessPatches | No |
> | datorer/tillägg | Yes |
> | datorer/installPatches | No |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataManagers | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | devices | Yes |
> | registeredSubscriptions | No |
> | leverantören | No |
> | leverantörer/vendorskus | No |
> | leverantörer/vendorskus/previewSubscriptions | No |
> | virtualnetworkfunctions | Yes |
> | virtualnetworkfunctionvendors | No |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | delarna | Yes |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utskrifts | Yes |

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
> | IoTApps | Yes |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Graph | Yes |

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedVaults | No |
> | hsmPools | Yes |
> | managedHSMs | Yes |
> | valv | Yes |
> | valv/accessPolicies | No |
> | valv/eventGridFilters | No |
> | valv/nycklar | No |
> | valv/nycklar/versioner | No |
> | valv/hemligheter | No |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | connectedClusters | Yes |
> | registeredSubscriptions | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | tillägg | No |
> | sourceControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Yes |
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
> | labaccounts | Yes |
> | användare | No |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | integrationAccounts | Yes |
> | integrationServiceEnvironments | Yes |
> | integrationServiceEnvironments/managedApis | Yes |
> | isolatedEnvironments | Yes |
> | arbetsflöden | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | commitmentPlans | Yes |
> | WebServices | Yes |
> | Arbetsytor | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbetsytor | Yes |
> | arbets ytor/beräkningar | No |
> | arbets ytor/eventGridFilters | No |
> | arbets ytor/linkedServices | No |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applyUpdates | No |
> | configurationAssignments | No |
> | maintenanceConfigurations | Yes |
> | publicMaintenanceConfigurations | No |
> | uppdateringar | No |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Identiteter | No |
> | userAssignedIdentities | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedNetworks | Yes |
> | managedNetworks / managedNetworkGroups | Yes |
> | managedNetworks / managedNetworkPeeringPolicies | Yes |
> | avisering | Yes |

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
> | konton | Yes |
> | konton/eventGridFilters | No |
> | konton/privateAtlases | Yes |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | macc | No |
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
> | classicDevServices | Yes |
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
> | Media Services | Yes |
> | Media Services/accountFilters | No |
> | Media Services/till gångar | No |
> | Media Services/assets/assetFilters | No |
> | Media Services/contentKeyPolicies | No |
> | Media Services/eventGridFilters | No |
> | Media Services/liveEventOperations | No |
> | Media Services/liveEvents | Yes |
> | Media Services/liveEvents/liveOutputs | No |
> | Media Services/liveOutputOperations | No |
> | Media Services/mediaGraphs | No |
> | Media Services/privateEndpointConnectionOperations | No |
> | Media Services/privateEndpointConnectionProxies | No |
> | Media Services/privateEndpointConnections | No |
> | Media Services/streamingEndpointOperations | No |
> | Media Services/strömnings slut punkter | Yes |
> | Media Services/streamingLocators | No |
> | Media Services/streamingPolicies | No |
> | Media Services/transformeringar | No |
> | Media Services/transformeringar/jobb | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appClusters | Yes |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | assessmentProjects | Yes |
> | migrateprojects | Yes |
> | moveCollections | Yes |
> | samarbetsprojekt | Yes |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | Yes |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts / accountBackups | No |
> | netAppAccounts / capacityPools | Yes |
> | netAppAccounts/capacityPools/Volumes | Yes |
> | netAppAccounts/capacityPools/volym/ögonblicks bilder | No |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applicationGateways | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Yes |
> | bastionHosts | Yes |
> | bgpServiceCommunities | No |
> | anslutning | Yes |
> | ddosCustomPolicies | Yes |
> | ddosProtectionPlans | Yes |
> | dnsOperationStatuses | No |
> | dnszones | Yes |
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
> | expressRouteCircuits | Yes |
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Yes |
> | frontdoors | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | ipGroups | Yes |
> | Belastningsutjämnare | Yes |
> | localNetworkGateways | Yes |
> | natGateways | Yes |
> | networkIntentPolicies | Yes |
> | networkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers / connectionMonitors | Yes |
> | networkWatchers / flowLogs | Yes |
> | networkWatchers/linser | Yes |
> | networkWatchers / pingMeshes | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Yes |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/alla | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones / virtualNetworkLinks | Yes |
> | privateEndpoints | Yes |
> | privateLinkServices | Yes |
> | publicIPAddresses | Yes |
> | publicIPPrefixes | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Yes |
> | trafficmanagerprofiles/termiska kartor | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/undernät | No |
> | virtualNetworkTaps | Yes |
> | virtualWans | Yes |
> | vpnGateways | Yes |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotebooks"></a>Microsoft. Notebook

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | NotebookProxies | No |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Yes |
> | namnrymder/notificationHubs | Yes |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | osNamespaces | Yes |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | HyperVSites | Yes |
> | ImportSites | Yes |
> | MasterSites | Yes |
> | ServerSites | Yes |
> | VMwareSites | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Yes |
> | deletedWorkspaces | No |
> | linkTargets | No |
> | storageInsightConfigs | No |
> | arbetsytor | Yes |
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
> | managementconfigurations | Yes |
> | lösningar | Yes |
> | vyer | Yes |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | legacyPeerings | No |
> | peerAsns | No |
> | peerings | Yes |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | peeringServices | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attesteringar | No |
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
> | instrumentpaneler | Yes |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Yes |
> | klienter | Yes |
> | innehavare/arbets ytor | No |
> | workspaceCollections | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kapaciteter | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | deletedAccounts | No |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations / defaultRollouts | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | distributioner | Yes |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Arbetsytor | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | backupProtectedItems | No |
> | valv | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | OpenShiftClusters | Yes |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Yes |
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
> | skickar | Yes |
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
> | deploymentScripts | Yes |
> | deploymentScripts/loggar | No |
> | Länkar | No |
> | notifyResourceJobs | No |
> | finansiär | No |
> | resourceGroups | No |
> | prenumerationer | No |
> | templateSpecs | Yes |
> | templateSpecs/versioner | Yes |
> | klienter | No |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Yes |
> | saasresources | No |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | moln | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |
> | vmmservers | Yes |

## <a name="microsoftsearch"></a>Microsoft. search

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Yes |

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
> | automatiseringar | Yes |
> | AutoProvisioningSettings | No |
> | Godkännanden | No |
> | anslutningar | No |
> | dataCollectionAgents | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | iotDefenderSettings | No |
> | iotSecuritySolutions | Yes |
> | iotSecuritySolutions / analyticsModels | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No |
> | iotSecuritySolutions / iotAlerts | No |
> | iotSecuritySolutions / iotAlertTypes | No |
> | iotSecuritySolutions / iotRecommendations | No |
> | iotSecuritySolutions / iotRecommendationTypes | No |
> | iotSensors | No |
> | jitNetworkAccessPolicies | No |
> | jitPolicies | No |
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
> | sqlVulnerabilityAssessments | No |
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
> | watchlists | No |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | consoleServices | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Yes |
> | namnrymder/authorizationrules | No |
> | namnrymder/disasterrecoveryconfigs | No |
> | namnrymder/eventgridfilters | No |
> | namnrymder/networkrulesets | No |
> | namnrymder/privateEndpointConnections | No |
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
> | program | Yes |
> | kluster | Yes |
> | kluster/program | No |
> | containerGroups | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters/program | No |
> | managedclusters | Yes |
> | managedclusters/nodetypes | No |
> | nätet | Yes |
> | secretstores | Yes |
> | secretstores/certifikat | No |
> | secretstores/hemligheter | No |
> | volumes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Yes |
> | containerGroups | Yes |
> | gatewayer | Yes |
> | nätet | Yes |
> | secrets | Yes |
> | volumes | Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | distributioner | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SignalR | Yes |
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
> | applicationDefinitions | Yes |
> | program | Yes |
> | jitRequests | Yes |

## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances/databaser | Yes |
> | managedInstances/databaser/backupShortTermRetentionPolicies | No |
> | managedInstances/databaser/scheman/tabeller/kolumner/sensitivityLabels | No |
> | managedInstances/databaser/vulnerabilityAssessments | No |
> | managedInstances/databaser/vulnerabilityAssessments/regler/bas linjer | No |
> | managedInstances / encryptionProtector | No |
> | managedInstances/nycklar | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No |
> | managedInstances / vulnerabilityAssessments | No |
> | brygghuvudservrar | Yes |
> | servrar/administratörer | No |
> | servrar/communicationLinks | No |
> | servrar/databaser | Yes |
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
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedAccounts | No |
> | storageAccounts | Yes |
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
> | cacheminnen | Yes |
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
> | storageSyncServices | Yes |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/arbets flöden | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/arbets flöden | No |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices / registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/arbets flöden | No |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hantera | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Yes |
> | streamingjobs | Yes |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | acceptChangeTenant | No |
> | alias | No |
> | avbryt | No |
> | changeTenantRequest | No |
> | changeTenantStatus | No |
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
> | privateLinkHubs | Yes |
> | arbetsytor | Yes |
> | arbets ytor/bigDataPools | Yes |
> | arbets ytor/operationStatuses | No |
> | arbets ytor/sqlDatabases | Yes |
> | arbets ytor/sqlPools | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Yes |
> | miljöer/accessPolicies | No |
> | miljöer/eventsources | Yes |
> | miljöer/referenceDataSets | Yes |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Auktoriseringshanteraren | Yes |
> | butiker/accessPolicies | No |
> | butiker/tjänster | No |
> | butiker/tjänster/token | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | imageTemplates | Yes |
> | imageTemplates / runOutputs | No |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ArcZones | Yes |
> | ResourcePools | Yes |
> | VCenter | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Yes |
> | dedicatedCloudServices | Yes |
> | virtualMachines | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | devices | Yes |
> | registeredSubscriptions | No |
> | leverantören | No |
> | leverantörer/SKU: er | No |
> | leverantörer/vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | vnfs | Yes |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Yes |
> | utgå | Yes |
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
> | certifikat | Yes |
> | connectionGateways | Yes |
> | anslutning | Yes |
> | customApis | Yes |
> | deletedSites | No |
> | hostingEnvironments | Yes |
> | hostingEnvironments / eventGridFilters | No |
> | hostingEnvironments / multiRolePools | No |
> | hostingEnvironments / workerPools | No |
> | kubeEnvironments | Yes |
> | publishingUsers | No |
> | rekommendationer | No |
> | resourceHealthMetadata | No |
> | körningar | No |
> | Server grupper | Yes |
> | Server grupper/eventGridFilters | No |
> | webbplatser | Yes |
> | platser/konfig  | No |
> | platser/eventGridFilters | No |
> | platser/hostNameBindings | No |
> | platser/networkConfig | No |
> | platser/premieraddons | Yes |
> | platser/platser | Yes |
> | platser/platser/eventGridFilters | No |
> | platser/platser/hostNameBindings | No |
> | platser/platser/networkConfig | No |
> | sourceControls | No |
> | staticSites | Yes |
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
> | multipleActivationKeys | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DeviceServices | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbets belastningar | Yes |
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
