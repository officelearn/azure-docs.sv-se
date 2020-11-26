---
title: Borttagning i complete-läget
description: Visar hur resurs typer hanterar fullständig borttagning av läge i Azure Resource Manager mallar.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: e0c67bfcda81ad128e0018c4ab37c4b0cbe680f0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184033"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Borttagning av Azure-resurser för komplett läges distributioner

I den här artikeln beskrivs hur resurs typer hanterar borttagning när de inte finns i en mall som distribueras i fullständigt läge.

De resurs typer som marker ATS med **Ja** tas bort när typen inte finns i mallen distribuerad med slutfört läge.

Resurs typerna som marker ATS med **Nej** tas inte bort automatiskt när de inte finns i mallen. de tas dock bort om den överordnade resursen tas bort. En fullständig beskrivning av beteendet finns i [Azure Resource Manager distributions lägen](deployment-modes.md).

Om du distribuerar till [fler än en resurs grupp i en mall](./deploy-to-resource-group.md)är resurserna i resurs gruppen som anges i distributions åtgärden tillgängliga för borttagning. Resurser i de sekundära resurs grupperna tas inte bort.

Resurserna visas i namn området för resurs leverantören. Information om hur du matchar ett namn område för en resurs leverantör med dess Azure-tjänst namn finns i [Resource providers för Azure-tjänster](../management/azure-services-resource-providers.md).

> [!NOTE]
> Använd alltid [åtgärden konsekvens](template-deploy-what-if.md) innan du distribuerar en mall i fullständigt läge. Vad händer om du ser vilka resurser som kommer att skapas, tas bort eller ändras. Använd what-if för att undvika oavsiktlig borttagning av resurser.
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
> - [Microsoft. BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
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
> - [Microsoft. Singularity](#microsoftsingularity)
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
> | DomainServices | Ja |
> | DomainServices / oucontainer | Nej |

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
> | rapporter | Nej |
> | servicehealthmetrics | Nej |
> | services | Nej |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | advisorScore | Nej |
> | konfigurationer | Nej |
> | generateRecommendations | Nej |
> | metadata | Nej |
> | rekommendationer | Nej |
> | utelämningar | Nej |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | farmBeats | Ja |

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
> | smartDetectorAlertRules | Ja |
> | smartGroups | Nej |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | brygghuvudservrar | Ja |

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
> | configurationStores / eventGridFilters | Nej |
> | configurationStores/-värde | Nej |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Spring | Ja |
> | Våren/appar | Nej |
> | Våren/appar/distributioner | Nej |

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attestationProviders | Ja |
> | defaultProviders | Nej |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | Nej |
> | accessReviewScheduleSettings | Nej |
> | classicAdministrators | Nej |
> | dataAliases | Nej |
> | denyAssignments | Nej |
> | elevateAccess | Nej |
> | findOrphanRoleAssignments | Nej |
> | hålls | Nej |
> | behörigheter | Nej |
> | policyAssignments | Nej |
> | policyDefinitions | Nej |
> | policyExemptions | Nej |
> | policySetDefinitions | Nej |
> | privateLinkAssociations | Nej |
> | providerOperations | Nej |
> | resourceManagementPrivateLinks | Ja |
> | roleAssignments | Nej |
> | roleAssignmentsUsageMetrics | Nej |
> | roleDefinitions | Nej |

## <a name="microsoftautomanage"></a>Microsoft. autohantera

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | configurationProfileAssignments | Nej |
> | configurationProfilePreferences | Ja |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | automationAccounts | Ja |
> | automationAccounts/konfigurationer | Ja |
> | automationAccounts/jobb | Nej |
> | automationAccounts / privateEndpointConnectionProxies | Nej |
> | automationAccounts / privateEndpointConnections | Nej |
> | automationAccounts / privateLinkResources | Nej |
> | automationAccounts/Runbooks | Ja |
> | automationAccounts / softwareUpdateConfigurations | Nej |
> | automationAccounts/Webhooks | Nej |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateClouds | Ja |
> | privateClouds/tillägg | Nej |
> | privateClouds/auktoriseringar | Nej |
> | privateClouds/kluster | Nej |
> | privateClouds / globalReachConnections | Nej |
> | privateClouds / hcxEnterpriseSites | Nej |
> | privateClouds / workloadNetworks | Nej |
> | privateClouds / workloadNetworks / dhcpConfigurations | Nej |
> | privateClouds/workloadNetworks/gateways | Nej |
> | privateClouds / workloadNetworks / portMirroringProfiles | Nej |
> | privateClouds/workloadNetworks/segment | Nej |
> | privateClouds / workloadNetworks / virtualMachines | Nej |
> | privateClouds / workloadNetworks / vmGroups | Nej |

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
> | guestUsages | Ja |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataControllers | Ja |
> | postgresInstances | Ja |
> | sqlManagedInstances | Ja |
> | sqlServerInstances | Ja |
> | sqlServerRegistrations | Ja |
> | sqlServerRegistrations/sqlServer | Nej |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cloudManifestFiles | Nej |
> | edgeSubscriptions | Ja |
> | linkedSubscriptions | Ja |
> | registreringar | Ja |
> | registreringar/customerSubscriptions | Nej |
> | registreringar/produkter | Nej |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | bareMetalInstances | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | batchAccounts | Ja |
> | batchAccounts/certifikat | Nej |
> | batchAccounts/pooler | Nej |

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | billingAccounts | Nej |
> | billingAccounts/avtal | Nej |
> | billingAccounts / billingPermissions | Nej |
> | billingAccounts / billingProfiles | Nej |
> | billingAccounts / billingProfiles / billingPermissions | Nej |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nej |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nej |
> | billingAccounts / billingProfiles / billingSubscriptions | Nej |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nej |
> | billingAccounts/billingProfiles/kunder | Nej |
> | billingAccounts/billingProfiles/instruktioner | Nej |
> | billingAccounts/billingProfiles/fakturor | Nej |
> | billingAccounts/billingProfiles/fakturor/pris dokument | Nej |
> | billingAccounts/billingProfiles/fakturor/transaktioner | Nej |
> | billingAccounts / billingProfiles / invoiceSections | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nej |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nej |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nej |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nej |
> | billingAccounts/billingProfiles/invoiceSections/Products | Nej |
> | billingAccounts/billingProfiles/invoiceSections/Products/transfer | Nej |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Nej |
> | billingAccounts/billingProfiles/invoiceSections/transaktioner | Nej |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nej |
> | billingAccounts / BillingProfiles / patchOperations | Nej |
> | billingAccounts / billingProfiles / paymentMethods | Nej |
> | billingAccounts/billingProfiles/policys | Nej |
> | billingAccounts/billingProfiles/pris dokument | Nej |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nej |
> | billingAccounts/billingProfiles/Products | Nej |
> | billingAccounts/billingProfiles/reservationer | Nej |
> | billingAccounts/billingProfiles/transaktioner | Nej |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Nej |
> | billingAccounts / billingRoleAssignments | Nej |
> | billingAccounts / billingRoleDefinitions | Nej |
> | billingAccounts / billingSubscriptions | Nej |
> | billingAccounts/billingSubscriptions/fakturor | Nej |
> | billingAccounts / createBillingRoleAssignment | Nej |
> | billingAccounts / createInvoiceSectionOperations | Nej |
> | billingAccounts/kunder | Nej |
> | billingAccounts/kunder/billingPermissions | Nej |
> | billingAccounts/kunder/billingSubscriptions | Nej |
> | billingAccounts/kunder/initiateTransfer | Nej |
> | billingAccounts/kunder/principer | Nej |
> | billingAccounts/kunder/produkter | Nej |
> | billingAccounts/kunder/transaktioner | Nej |
> | billingAccounts/kunder/överföringar | Nej |
> | billingAccounts/avdelningar | Nej |
> | billingAccounts/avdelningar/billingPermissions | Nej |
> | billingAccounts/avdelningar/billingRoleAssignments | Nej |
> | billingAccounts/avdelningar/billingRoleDefinitions | Nej |
> | billingAccounts / enrollmentAccounts | Nej |
> | billingAccounts / enrollmentAccounts / billingPermissions | Nej |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Nej |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Nej |
> | billingAccounts/fakturor | Nej |
> | billingAccounts/fakturor/transaktioner | Nej |
> | billingAccounts / invoiceSections | Nej |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nej |
> | billingAccounts / invoiceSections / billingSubscriptions | Nej |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nej |
> | billingAccounts/invoiceSections/höjning | Nej |
> | billingAccounts / invoiceSections / initiateTransfer | Nej |
> | billingAccounts / invoiceSections / patchOperations | Nej |
> | billingAccounts / invoiceSections / productMoveOperations | Nej |
> | billingAccounts/invoiceSections/Products | Nej |
> | billingAccounts/invoiceSections/Products/transfer | Nej |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Nej |
> | billingAccounts/invoiceSections/transaktioner | Nej |
> | billingAccounts/invoiceSections/överföringar | Nej |
> | billingAccounts / lineOfCredit | Nej |
> | billingAccounts / patchOperations | Nej |
> | billingAccounts / paymentMethods | Nej |
> | billingAccounts/produkter | Nej |
> | billingAccounts/reservationer | Nej |
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
> | Övervakare | Ja |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | TokenServices | Ja |
> | TokenServices / BlockchainNetworks | Nej |
> | TokenServices/grupper | Nej |
> | TokenServices/grupper/konton | Nej |
> | TokenServices / TokenTemplates | Nej |

## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blueprintAssignments | Nej |
> | blueprintAssignments / assignmentOperations | Nej |
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
> | språk | Nej |
> | mallar | Nej |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Redis | Ja |
> | Redis/EventGridFilters | Nej |
> | Redis/privateEndpointConnectionProxies | Nej |
> | Redis/privateEndpointConnectionProxies/validate | Nej |
> | Redis/privateEndpointConnections | Nej |
> | Redis/privateLinkResources | Nej |
> | redisEnterprise | Ja |
> | RedisEnterprise / privateEndpointConnectionProxies | Nej |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | Nej |
> | RedisEnterprise / privateEndpointConnections | Nej |
> | RedisEnterprise / privateLinkResources | Nej |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appliedReservations | Nej |
> | autoQuotaIncrease | Nej |
> | calculateExchange | Nej |
> | calculatePrice | Nej |
> | calculatePurchasePrice | Nej |
> | kataloger | Nej |
> | commercialReservationOrders | Nej |
> | utväxla | Nej |
> | ownReservations | Nej |
> | placePurchaseOrder | Nej |
> | reservationOrders | Nej |
> | reservationOrders / calculateRefund | Nej |
> | reservationOrders/slå samman | Nej |
> | reservationOrders/reservationer | Nej |
> | reservationOrders/reservationer/revisioner | Nej |
> | reservationOrders/retur | Nej |
> | reservationOrders/Split | Nej |
> | reservationOrders/växling | Nej |
> | reservera | Nej |
> | resourceProviders | Nej |
> | resources | Nej |
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
> | profiler/slut punkter/origingroups | Nej |
> | profiler/slut punkter/ursprung | Nej |
> | validateProbe | Nej |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certificateOrders | Ja |
> | certificateOrders/certifikat | Nej |
> | validateCertificateRegistrationInformation | Nej |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | profil | Nej |
> | resourceChanges | Nej |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner | Nej |
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
> | virtualMachines | Ja |
> | virtualMachines / diagnosticSettings | Nej |
> | virtualMachines / metricDefinitions | Nej |
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
> | funktioner | Nej |
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
> | funktioner | Nej |
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

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utgå | Ja |
> | registeredSubscriptions | Nej |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/privateEndpointConnectionProxies | Nej |
> | konton/privateEndpointConnections | Nej |
> | konton/privateLinkResources | Nej |

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
> | cloudServices | Ja |
> | cloudServices/networkInterfaces | Nej |
> | cloudServices/publicIPAddresses | Nej |
> | cloudServices / roleInstances | Nej |
> | cloudServices/roleInstances/networkInterfaces | Nej |
> | cloudServices/roller | Nej |
> | diskAccesses | Ja |
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
> | restorePointCollections / restorePoints | Nej |
> | sharedVMExtensions | Ja |
> | sharedVMExtensions/versioner | Nej |
> | sharedVMImages | Ja |
> | sharedVMImages/versioner | Nej |
> | snapshots | Ja |
> | sshPublicKeys | Ja |
> | virtualMachines | Ja |
> | virtualMachines/tillägg | Ja |
> | virtualMachines / metricDefinitions | Nej |
> | virtualMachines / runCommands | Ja |
> | virtualMachineScaleSets | Ja |
> | virtualMachineScaleSets/tillägg | Nej |
> | virtualMachineScaleSets/networkInterfaces | Nej |
> | virtualMachineScaleSets/publicIPAddresses | Nej |
> | virtualMachineScaleSets/virtualMachines | Nej |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nej |

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CacheNodes | Ja |

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
> | händelser | Nej |
> | Prognoser | Nej |
> | samtliga | Nej |
> | Marknads platser | Nej |
> | Prisdokument | Nej |
> | läkemedle | Nej |
> | ReservationDetails | Nej |
> | ReservationRecommendationDetails | Nej |
> | ReservationRecommendations | Nej |
> | ReservationSummaries | Nej |
> | ReservationTransactions | Nej |
> | Taggar | Nej |
> | klienter | Nej |
> | Termer | Nej |
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
> | register/agentPools | Ja |
> | register/versioner | Nej |
> | register/versioner/Avbryt | Nej |
> | register/build/getLogLink | Nej |
> | register/buildTasks | Ja |
> | register/buildTasks/steg | Nej |
> | register/eventGridFilters | Nej |
> | register/exportPipelines | Nej |
> | register/generateCredentials | Nej |
> | register/getBuildSourceUploadUrl | Nej |
> | register/GetCredentials | Nej |
> | register/importImage | Nej |
> | register/importPipelines | Nej |
> | register/pipelineRuns | Nej |
> | register/privateEndpointConnectionProxies | Nej |
> | register/privateEndpointConnectionProxies/validate | Nej |
> | register/privateEndpointConnections | Nej |
> | register/privateLinkResources | Nej |
> | register/queueBuild | Nej |
> | register/regenerateCredential | Nej |
> | register/regenerateCredentials | Nej |
> | register/replikeringar | Ja |
> | register/körningar | Nej |
> | register/körningar/Avbryt | Nej |
> | register/scheduleRun | Nej |
> | register/scopeMaps | Nej |
> | register/taskRuns | Nej |
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

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aviseringar | Nej |
> | BillingAccounts | Nej |
> | Budgetar | Nej |
> | CloudConnectors | Nej |
> | Anslutningar | Ja |
> | costAllocationRules | Nej |
> | Avdelningar | Nej |
> | Dimensioner | Nej |
> | EnrollmentAccounts | Nej |
> | Exporteras | Nej |
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
> | Prognos | Nej |
> | Insikter | Nej |
> | Fråga | Nej |
> | registrera | Nej |
> | Reportconfigs | Nej |
> | Rapporter | Nej |
> | Inställningar | Nej |
> | showbackRules | Nej |
> | Vyer | Nej |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autentiseringsbegäran | Nej |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | typer | Nej |
> | resourceProviders | Ja |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | pipe | Ja |

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
> | arbets ytor/dbWorkspaces | Nej |
> | arbets ytor/virtualNetworkPeerings | Nej |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kataloger | Ja |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataFactories | Ja |
> | dataFactories / diagnosticSettings | Nej |
> | dataFactories / metricDefinitions | Nej |
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
> | brygghuvudservrar | Ja |
> | servrar/rådgivare | Nej |
> | servrar/nycklar | Nej |
> | servrar/privateEndpointConnectionProxies | Nej |
> | servrar/privateEndpointConnections | Nej |
> | servrar/privateLinkResources | Nej |
> | servrar/queryTexts | Nej |
> | servrar/recoverableServers | Nej |
> | servrar/start | Nej |
> | servrar/stoppa | Nej |
> | servrar/topQueryStatistics | Nej |
> | servrar/virtualNetworkRules | Nej |
> | servrar/waitStatistics | Nej |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | flexibleServers | Ja |
> | brygghuvudservrar | Ja |
> | servrar/rådgivare | Nej |
> | servrar/nycklar | Nej |
> | servrar/privateEndpointConnectionProxies | Nej |
> | servrar/privateEndpointConnections | Nej |
> | servrar/privateLinkResources | Nej |
> | servrar/queryTexts | Nej |
> | servrar/recoverableServers | Nej |
> | servrar/start | Nej |
> | servrar/stoppa | Nej |
> | servrar/topQueryStatistics | Nej |
> | servrar/uppgradering | Nej |
> | servrar/virtualNetworkRules | Nej |
> | servrar/waitStatistics | Nej |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | flexibleServers | Ja |
> | serverGroups | Ja |
> | brygghuvudservrar | Ja |
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
> | applicationgroups / startmenuitems | Nej |
> | hostpools | Ja |
> | hostpools / msixpackages | Nej |
> | hostpools / sessionhosts | Nej |
> | hostpools / sessionhosts / usersessions | Nej |
> | hostpools / usersessions | Nej |
> | arbetsytor | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ElasticPools | Ja |
> | ElasticPools / IotHubTenants | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Nej |
> | IotHubs | Ja |
> | IotHubs / eventGridFilters | Nej |
> | IotHubs/securitySettings | Nej |
> | ProvisioningServices | Ja |
> | användningar | Nej |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/instanser | Ja |

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
> | digitalTwinsInstances/slut punkter | Nej |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | databaseAccountNames | Nej |
> | databaseAccounts | Ja |
> | restorableDatabaseAccounts | Nej |

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
> | lcsprojects / clouddeployments | Nej |
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
> | partnerNamespaces | Ja |
> | partnerNamespaces/eventChannels | Nej |
> | partnerRegistrations | Ja |
> | partnerTopics | Ja |
> | partnerTopics / eventSubscriptions | Nej |
> | systemTopics | Ja |
> | systemTopics / eventSubscriptions | Nej |
> | avsnitt | Ja |
> | topicTypes | Nej |

## <a name="microsofteventhub"></a>Microsoft. EventHub

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
> | namnrymder/networkrulesets | Nej |
> | namnrymder/privateEndpointConnections | Nej |

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
> | featureProviders | Nej |
> | funktioner | Nej |
> | finansiär | Nej |
> | subscriptionFeatureRegistrations | Nej |

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
> | registrera | Nej |
> | resources | Nej |
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
> | autoManagedAccounts | Ja |
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
> | tjänster/iomtconnectors | Nej |
> | tjänster/iomtconnectors/anslutningar | Nej |
> | tjänster/iomtconnectors/mappningar | Nej |
> | tjänster/privateEndpointConnectionProxies | Nej |
> | tjänster/privateEndpointConnections | Nej |
> | tjänster/privateLinkResources | Nej |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | faxar | Ja |
> | datorer/assessPatches | Nej |
> | datorer/tillägg | Ja |
> | datorer/installPatches | Nej |

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
> | networkFunctions | Ja |
> | networkFunctionVendors | Nej |
> | registeredSubscriptions | Nej |
> | leverantören | Nej |
> | leverantörer/vendorSkus | Nej |
> | leverantörer/vendorSkus/previewSubscriptions | Nej |
> | virtualNetworkFunctions | Ja |
> | virtualNetworkFunctionVendors | Nej |

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
> | managedHSMs | Ja |
> | valv | Ja |
> | valv/accessPolicies | Nej |
> | valv/eventGridFilters | Nej |
> | valv/nycklar | Nej |
> | valv/nycklar/versioner | Nej |
> | valv/hemligheter | Nej |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | connectedClusters | Ja |
> | registeredSubscriptions | Nej |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | tillägg | Nej |
> | sourceControlConfigurations | Nej |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | kluster/attacheddatabaseconfigurations | Nej |
> | kluster/databaser | Nej |
> | kluster/databaser/dataconnections | Nej |
> | kluster/databaser/eventhubconnections | Nej |
> | kluster/databaser/principalassignments | Nej |
> | kluster/dataconnections | Nej |
> | kluster/principalassignments | Nej |
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
> | arbetsytor | Ja |
> | arbets ytor/batchEndpoints | Ja |
> | arbets ytor/batchEndpoints/distributioner | Ja |
> | arbets ytor/koder | Nej |
> | arbets ytor/koder/versioner | Nej |
> | arbets ytor/beräkningar | Nej |
> | arbets ytor/data lager | Nej |
> | arbets ytor/eventGridFilters | Nej |
> | arbets ytor/jobb | Nej |
> | arbets ytor/labelingJobs | Nej |
> | arbets ytor/linkedServices | Nej |
> | arbets ytor/modeller | Nej |
> | arbets ytor/modeller/versioner | Nej |
> | arbets ytor/onlineEndpoints | Ja |
> | arbets ytor/onlineEndpoints/distributioner | Ja |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applyUpdates | Nej |
> | configurationAssignments | Nej |
> | maintenanceConfigurations | Ja |
> | publicMaintenanceConfigurations | Nej |
> | uppdateringar | Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Identiteter | Nej |
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
> | marketplaceRegistrationDefinitions | Nej |
> | registrationAssignments | Nej |
> | registrationDefinitions | Nej |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | getEntities | Nej |
> | managementGroups | Nej |
> | managementGroups/inställningar | Nej |
> | resources | Nej |
> | startTenantBackfill | Nej |
> | tenantBackfillStatus | Nej |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/eventGridFilters | Nej |
> | konton/privateAtlases | Ja |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | macc | Nej |
> | budgivning | Nej |
> | offerTypes | Nej |
> | offerTypes/utgivare | Nej |
> | offerTypes/utgivare/erbjudanden | Nej |
> | offerTypes/utgivare/erbjudanden/planer | Nej |
> | offerTypes/utgivare/erbjudanden/planer/avtal | Nej |
> | offerTypes/utgivare/erbjudanden/planer/konfigurationer | Nej |
> | offerTypes/utgivare/erbjudanden/planer/configs/importImage | Nej |
> | privategalleryitems | Nej |
> | privateStoreClient | Nej |
> | privateStores | Nej |
> | privateStores/erbjudanden | Nej |
> | läkemedle | Nej |
> | Utgivare | Nej |
> | Utgivare/erbjudanden | Nej |
> | Utgivare/erbjudanden/ändringar | Nej |
> | registrera | Nej |

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
> | Media Services/privateEndpointConnectionOperations | Nej |
> | Media Services/privateEndpointConnectionProxies | Nej |
> | Media Services/privateEndpointConnections | Nej |
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
> | netAppAccounts / accountBackups | Nej |
> | netAppAccounts / capacityPools | Ja |
> | netAppAccounts/capacityPools/Volumes | Ja |
> | netAppAccounts/capacityPools/volym/ögonblicks bilder | Nej |
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
> | ipGroups | Ja |
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
> | trafficmanagerprofiles/termiska kartor | Nej |
> | trafficManagerUserMetricsKeys | Nej |
> | virtualHubs | Ja |
> | virtualNetworkGateways | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks/undernät | Nej |
> | virtualNetworkTaps | Ja |
> | virtualWans | Ja |
> | vpnGateways | Ja |
> | vpnSites | Ja |
> | webApplicationFirewallPolicies | Ja |

## <a name="microsoftnotebooks"></a>Microsoft. Notebook

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | NotebookProxies | Nej |

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
> | MasterSites | Ja |
> | ServerSites | Ja |
> | VMwareSites | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kluster | Ja |
> | deletedWorkspaces | Nej |
> | linkTargets | Nej |
> | storageInsightConfigs | Nej |
> | arbetsytor | Ja |
> | arbets ytor/dataExports | Nej |
> | arbets ytor/data källor | Nej |
> | arbets ytor/linkedServices | Nej |
> | arbets ytor/linkedStorageAccounts | Nej |
> | arbets ytor/metadata | Nej |
> | arbets ytor/fråga | Nej |
> | arbets ytor/scopedPrivateLinkProxies | Nej |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managementassociations | Nej |
> | managementconfigurations | Ja |
> | lösningar | Ja |
> | vyer | Ja |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | legacyPeerings | Nej |
> | peerAsns | Nej |
> | peerings | Ja |
> | peeringServiceCountries | Nej |
> | peeringServiceProviders | Nej |
> | peeringServices | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attesteringar | Nej |
> | policyEvents | Nej |
> | policyMetadata | Nej |
> | policyStates | Nej |
> | policyTrackedResources | Nej |
> | reparationer | Nej |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> |  -konsoler | Nej |
> | instrumentpaneler | Ja |
> | userSettings | Nej |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Ja |
> | klienter | Ja |
> | innehavare/arbets ytor | Nej |
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
> | deletedAccounts | Nej |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | Nej |
> | providerRegistrations / defaultRollouts | Nej |
> | providerRegistrations / resourceTypeRegistrations | Nej |
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
> | backupProtectedItems | Nej |
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
> | namnrymder/authorizationrules | Nej |
> | namnrymder/hybridconnections | Nej |
> | namnrymder/hybridconnections/authorizationrules | Nej |
> | namnrymder/privateEndpointConnections | Nej |
> | namnrymder/wcfrelays | Nej |
> | namnrymder/wcfrelays/authorizationrules | Nej |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | skickar | Ja |
> | resourceChangeDetails | Nej |
> | resourceChanges | Nej |
> | resources | Nej |
> | resourcesHistory | Nej |
> | subscriptionsStatus | Nej |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | availabilityStatuses | Nej |
> | childAvailabilityStatuses | Nej |
> | childResources | Nej |
> | emergingissues | Nej |
> | händelser | Nej |
> | impactedResources | Nej |
> | metadata | Nej |
> | meddelanden | Nej |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | calculateTemplateHash | Nej |
> | distributioner | Nej |
> | distributioner/åtgärder | Nej |
> | deploymentScripts | Ja |
> | deploymentScripts/loggar | Nej |
> | Länkar | Nej |
> | notifyResourceJobs | Nej |
> | finansiär | Nej |
> | resourceGroups | Nej |
> | prenumerationer | Nej |
> | templateSpecs | Ja |
> | templateSpecs/versioner | Ja |
> | klienter | Nej |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | saasresources | Nej |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | moln | Ja |
> | VirtualMachines | Ja |
> | VirtualMachineTemplates | Ja |
> | VirtualNetworks | Ja |
> | vmmservers | Ja |

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
> | alertsSuppressionRules | Nej |
> | allowedConnections | Nej |
> | applicationWhitelistings | Nej |
> | assessmentMetadata | Nej |
> | utvärderingar | Nej |
> | autoDismissAlertsRules | Nej |
> | automatiseringar | Ja |
> | AutoProvisioningSettings | Nej |
> | Godkännanden | Nej |
> | anslutningar | Nej |
> | dataCollectionAgents | Nej |
> | deviceSecurityGroups | Nej |
> | discoveredSecuritySolutions | Nej |
> | externalSecuritySolutions | Nej |
> | InformationProtectionPolicies | Nej |
> | iotDefenderSettings | Nej |
> | iotSecuritySolutions | Ja |
> | iotSecuritySolutions / analyticsModels | Nej |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nej |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nej |
> | iotSecuritySolutions / iotAlerts | Nej |
> | iotSecuritySolutions / iotAlertTypes | Nej |
> | iotSecuritySolutions / iotRecommendations | Nej |
> | iotSecuritySolutions / iotRecommendationTypes | Nej |
> | iotSensors | Nej |
> | jitNetworkAccessPolicies | Nej |
> | jitPolicies | Nej |
> | policies | Nej |
> | prissättningar | Nej |
> | regulatoryComplianceStandards | Nej |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nej |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nej |
> | secureScoreControlDefinitions | Nej |
> | secureScoreControls | Nej |
> | secureScores | Nej |
> | secureScores / secureScoreControls | Nej |
> | securityContacts | Nej |
> | securitySolutions | Nej |
> | securitySolutionsReferenceData | Nej |
> | securityStatuses | Nej |
> | securityStatusesSummaries | Nej |
> | serverVulnerabilityAssessments | Nej |
> | inställningar | Nej |
> | sqlVulnerabilityAssessments | Nej |
> | underbedömningar | Nej |
> | uppgifter | Nej |
> | topologier | Nej |
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
> | automationRules | Nej |
> | bokmärken | Nej |
> | fall | Nej |
> | dataConnectors | Nej |
> | dataConnectorsCheckRequirements | Nej |
> | poster | Nej |
> | entityQueries | Nej |
> | incidenter | Nej |
> | officeConsents | Nej |
> | inställningar | Nej |
> | threatIntelligence | Nej |
> | watchlists | Nej |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | consoleServices | Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | namn områden | Ja |
> | namnrymder/authorizationrules | Nej |
> | namnrymder/disasterrecoveryconfigs | Nej |
> | namnrymder/eventgridfilters | Nej |
> | namnrymder/networkrulesets | Nej |
> | namnrymder/privateEndpointConnections | Nej |
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
> | managedclusters | Ja |
> | managedclusters/nodetypes | Nej |
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
> | secrets | Ja |
> | volumes | Ja |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | providerRegistrations | Nej |
> | providerRegistrations / resourceTypeRegistrations | Nej |
> | distributioner | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SignalR | Ja |
> | SignalR/eventGridFilters | Nej |

## <a name="microsoftsingularity"></a>Microsoft. Singularity

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton/accountQuotaPolicies | Nej |
> | konton/groupPolicies | Nej |
> | konton/jobb | Nej |
> | konton/storageContainers | Nej |

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
> | managedInstances / encryptionProtector | Nej |
> | managedInstances/nycklar | Nej |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nej |
> | managedInstances / vulnerabilityAssessments | Nej |
> | brygghuvudservrar | Ja |
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
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nej |
> | SqlVirtualMachines | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedAccounts | Nej |
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
> | storageSyncServices / registeredServers | Nej |
> | storageSyncServices / syncGroups | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej |
> | storageSyncServices/arbets flöden | Nej |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nej |
> | storageSyncServices / syncGroups | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej |
> | storageSyncServices/arbets flöden | Nej |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Nej |
> | storageSyncServices / syncGroups | Nej |
> | storageSyncServices / syncGroups / cloudEndpoints | Nej |
> | storageSyncServices / syncGroups / serverEndpoints | Nej |
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
> | kluster | Ja |
> | kluster/privateEndpoints | Nej |
> | streamingjobs | Ja |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | acceptChangeTenant | Nej |
> | alias | Nej |
> | avbryt | Nej |
> | changeTenantRequest | Nej |
> | changeTenantStatus | Nej |
> | CreateSubscription | Nej |
> | Aktivera | Nej |
> | byt namn | Nej |
> | SubscriptionDefinitions | Nej |
> | SubscriptionOperations | Nej |
> | prenumerationer | Nej |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | privateLinkHubs | Ja |
> | arbetsytor | Ja |
> | arbets ytor/bigDataPools | Ja |
> | arbets ytor/operationStatuses | Nej |
> | arbets ytor/sqlDatabases | Ja |
> | arbets ytor/sqlPools | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | utrymmen | Ja |
> | miljöer/accessPolicies | Nej |
> | miljöer/eventsources | Ja |
> | miljöer/referenceDataSets | Ja |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Auktoriseringshanteraren | Ja |
> | butiker/accessPolicies | Nej |
> | butiker/tjänster | Nej |
> | butiker/tjänster/token | Nej |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | imageTemplates | Ja |
> | imageTemplates / runOutputs | Nej |

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

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | devices | Ja |
> | registeredSubscriptions | Nej |
> | leverantören | Nej |
> | leverantörer/SKU: er | Nej |
> | leverantörer/vnfs | Nej |
> | virtualNetworkFunctionSkus | Nej |
> | vnfs | Ja |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | utgå | Ja |
> | registeredSubscriptions | Nej |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | apiManagementAccounts | Nej |
> | apiManagementAccounts / apiAcls | Nej |
> | apiManagementAccounts/API: er | Nej |
> | apiManagementAccounts/API/apiAcls | Nej |
> | apiManagementAccounts/API/connectionAcls | Nej |
> | apiManagementAccounts/API/anslutningar | Nej |
> | apiManagementAccounts/API/Connections/connectionAcls | Nej |
> | apiManagementAccounts/API/localizedDefinitions | Nej |
> | apiManagementAccounts / connectionAcls | Nej |
> | apiManagementAccounts/anslutningar | Nej |
> | billingMeters | Nej |
> | certifikat | Ja |
> | connectionGateways | Ja |
> | anslutning | Ja |
> | customApis | Ja |
> | deletedSites | Nej |
> | hostingEnvironments | Ja |
> | hostingEnvironments / eventGridFilters | Nej |
> | hostingEnvironments / multiRolePools | Nej |
> | hostingEnvironments / workerPools | Nej |
> | kubeEnvironments | Ja |
> | publishingUsers | Nej |
> | rekommendationer | Nej |
> | resourceHealthMetadata | Nej |
> | körningar | Nej |
> | Server grupper | Ja |
> | Server grupper/eventGridFilters | Nej |
> | Server grupper/firstPartyApps | Nej |
> | Server grupper/firstPartyApps/keyVaultSettings | Nej |
> | webbplatser | Ja |
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
> | staticSites | Ja |
> | kontrollerar | Nej |
> | verifyHostingEnvironmentVnet | Nej |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnosticSettings | Nej |
> | diagnosticSettingsCategories | Nej |

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
> | arbets belastningar/instanser | Nej |
> | arbets belastningar/versioner | Nej |
> | arbets belastningar/versioner/artefakter | Nej |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | delarna | Nej |
> | componentsSummary | Nej |
> | monitorInstances | Nej |
> | monitorInstancesSummary | Nej |
> | Övervakare | Nej |
> | notificationSettings | Nej |

## <a name="next-steps"></a>Nästa steg

Hämta [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)för att hämta samma data som en fil med kommaavgränsade värden.