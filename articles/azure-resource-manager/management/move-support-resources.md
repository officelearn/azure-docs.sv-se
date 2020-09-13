---
title: Flytta åtgärds stöd efter resurs typ
description: Visar en lista över de Azure-resurs typer som kan flyttas till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 69fcb271ac94df3faf8e9e37c1ef30a6c0681441
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566134"
---
# <a name="move-operation-support-for-resources"></a>Resurser som kan flyttas

Den här artikeln visar om en Azure-resurs har stöd för flytt åtgärden. Den innehåller också information om särskilda villkor att tänka på när du flyttar en resurs.

Hoppa till ett namn område för en resurs leverantör:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. API Management](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft. attestering](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. fakturering](#microsoftbilling)
> - [Microsoft. Bingkartssökning](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
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
> - [Microsoft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. förbrukning](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. container service](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CostManagementExports](#microsoftcostmanagementexports)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. data-](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataConnect](#microsoftdataconnect)
> - [Microsoft. DataExchange](#microsoftdataexchange)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLake](#microsoftdatalake)
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
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. experimentering](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. features](#microsoftfeatures)
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
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. nyckel valv](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. filen LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
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
> - [Microsoft. PowerPlatform](#microsoftpowerplatform)
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
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. support](#microsoftsupport)
> - [Microsoft. Synapse](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domainservices | Inga | Inga |
> | domainservices / oucontainer | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Inga | Inga |
> | diagnosticsettingscategories | Inga | Inga |
> | operations | Inga | Inga |
> | privatelinkforazuread | Ja | Ja |
> | klienter | Ja | Ja |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | supportproviders | Inga | Inga |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Inga | Inga |
> | addsservices | Inga | Inga |
> | aktörer | Inga | Inga |
> | anonymousapiusers | Inga | Inga |
> | konfiguration | Inga | Inga |
> | loggar | Inga | Inga |
> | operations | Inga | Inga |
> | rapporter | Inga | Inga |
> | servicehealthmetrics | Inga | Inga |
> | services | Inga | Inga |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konfigurationer | Inga | Inga |
> | generaterecommendations | Inga | Inga |
> | metadata | Inga | Inga |
> | operations | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | utelämningar | Inga | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | actionrules | Ja | Ja |
> | aviseringar | Inga | Inga |
> | alertslist | Inga | Inga |
> | alertsmetadata | Inga | Inga |
> | alertssummary | Inga | Inga |
> | alertssummarylist | Inga | Inga |
> | operations | Inga | Inga |
> | smartdetectoralertrules | Ja | Ja |
> | smartgroups | Inga | Inga |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | brygghuvudservrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!IMPORTANT]
> Det går inte att flytta en API Management tjänst som är inställd på förbruknings-SKU: n.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | Inga | Inga |
> | checknameavailability | Inga | Inga |
> | checkservicenameavailability | Inga | Inga |
> | operations | Inga | Inga |
> | reportfeedback | Inga | Inga |
> | tjänst | Ja | Ja |
> | validateservicename | Inga | Inga |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | configurationstores | Ja | Ja |
> | configurationstores / eventgridfilters | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationsstatus | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/operationstatus | Inga | Inga |
> | operations | Inga | Inga |
> | upphängning | Ja | Ja |
> | våren/appar | Inga | Inga |
> | våren/appar/distributioner | Inga | Inga |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | apiapps | Inga | Inga |
> | appidentities | Inga | Inga |
> | gatewayer | Inga | Inga |

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Ja | Ja |
> | operations | Inga | Inga |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Access | Inga | Inga |
> | classicadministrators | Inga | Inga |
> | dataaliases | Inga | Inga |
> | denyassignments | Inga | Inga |
> | elevateaccess | Inga | Inga |
> | findorphanroleassignments | Inga | Inga |
> | hålls | Inga | Inga |
> | operations | Inga | Inga |
> | operationstatus | Inga | Inga |
> | behörigheter | Inga | Inga |
> | policyassignments | Inga | Inga |
> | policydefinitions | Inga | Inga |
> | policysetdefinitions | Inga | Inga |
> | privatelinkassociations | Inga | Inga |
> | provideroperations | Inga | Inga |
> | resourcemanagementprivatelinks | Inga | Inga |
> | RoleAssignments | Inga | Inga |
> | roleassignmentsusagemetrics | Inga | Inga |
> | roledefinitions | Inga | Inga |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!IMPORTANT]
> Runbooks måste finnas i samma resurs grupp som Automation-kontot.
>
> Mer information finns i [flytta ditt Azure Automation-konto till en annan prenumeration](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ja | Ja |
> | automationaccounts/konfigurationer | Ja | Ja |
> | automationaccounts/jobb | Inga | Inga |
> | automationaccounts / privateendpointconnectionproxies | Inga | Inga |
> | automationaccounts / privateendpointconnections | Inga | Inga |
> | automationaccounts / privatelinkresources | Inga | Inga |
> | automationaccounts/Runbooks | Ja | Ja |
> | automationaccounts / softwareupdateconfigurations | Inga | Inga |
> | automationaccounts/Webhooks | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/checkquotaavailability | Inga | Inga |
> | platser/checktrialavailability | Inga | Inga |
> | operations | Inga | Inga |
> | privateclouds | Ja | Ja |
> | privateclouds/kluster | Inga | Inga |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ja | Ja |
> | b2ctenants | Inga | Inga |
> | checknameavailability | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Inga | Inga |
> | hybriddatamanagers | Inga | Inga |
> | operations | Inga | Inga |
> | postgresinstances | Inga | Inga |
> | sqlinstances | Inga | Inga |
> | sqlmanagedinstances | Inga | Inga |
> | sqlserverinstances | Inga | Inga |
> | sqlserverregistrations | Ja | Ja |
> | sqlserverregistrations/SQLServer | Inga | Inga |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Inga | Inga |
> | operations | Inga | Inga |
> | registreringar | Ja | Ja |
> | registreringar/customersubscriptions | Inga | Inga |
> | registreringar/produkter | Inga | Inga |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |
> | platser | Inga | Inga |
> | platser/accountoperationresults | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/kvoter | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Inga | Inga |
> | billingaccounts/avtal | Inga | Inga |
> | billingaccounts / billingpermissions | Inga | Inga |
> | billingaccounts / billingprofiles | Inga | Inga |
> | billingaccounts / billingprofiles / availablebalance | Inga | Inga |
> | billingaccounts / billingprofiles / billingpermissions | Inga | Inga |
> | billingaccounts / billingprofiles / billingroleassignments | Inga | Inga |
> | billingaccounts / billingprofiles / billingroledefinitions | Inga | Inga |
> | billingaccounts / billingprofiles / billingsubscriptions | Inga | Inga |
> | billingaccounts / billingprofiles / createbillingroleassignment | Inga | Inga |
> | billingaccounts/billingprofiles/kunder | Inga | Inga |
> | billingaccounts/billingprofiles/instruktioner | Inga | Inga |
> | billingaccounts/billingprofiles/fakturor | Inga | Inga |
> | billingaccounts/billingprofiles/fakturor/pris dokument | Inga | Inga |
> | billingaccounts/billingprofiles/fakturor/transaktioner | Inga | Inga |
> | billingaccounts / billingprofiles / invoicesections | Inga | Inga |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | Inga | Inga |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | Inga | Inga |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | Inga | Inga |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | Inga | Inga |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | Inga | Inga |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | Inga | Inga |
> | billingaccounts/billingprofiles/invoicesections/Products | Inga | Inga |
> | billingaccounts/billingprofiles/invoicesections/Products/transfer | Inga | Inga |
> | billingaccounts/billingprofiles/invoicesections/Products/updateautorenew | Inga | Inga |
> | billingaccounts/billingprofiles/invoicesections/transaktioner | Inga | Inga |
> | billingaccounts/billingprofiles/invoicesections/transfers | Inga | Inga |
> | billingaccounts / billingprofiles / patchoperations | Inga | Inga |
> | billingaccounts / billingprofiles / paymentmethods | Inga | Inga |
> | billingaccounts/billingprofiles/policys | Inga | Inga |
> | billingaccounts/billingprofiles/pris dokument | Inga | Inga |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | Inga | Inga |
> | billingaccounts/billingprofiles/Products | Inga | Inga |
> | billingaccounts/billingprofiles/transaktioner | Inga | Inga |
> | billingaccounts / billingroleassignments | Inga | Inga |
> | billingaccounts / billingroledefinitions | Inga | Inga |
> | billingaccounts / billingsubscriptions | Inga | Inga |
> | billingaccounts/billingsubscriptions/fakturor | Inga | Inga |
> | billingaccounts / createbillingroleassignment | Inga | Inga |
> | billingaccounts / createinvoicesectionoperations | Inga | Inga |
> | billingaccounts/kunder | Inga | Inga |
> | billingaccounts/kunder/billingpermissions | Inga | Inga |
> | billingaccounts/kunder/billingsubscriptions | Inga | Inga |
> | billingaccounts/kunder/initiatetransfer | Inga | Inga |
> | billingaccounts/kunder/principer | Inga | Inga |
> | billingaccounts/kunder/produkter | Inga | Inga |
> | billingaccounts/kunder/transaktioner | Inga | Inga |
> | billingaccounts/kunder/överföringar | Inga | Inga |
> | billingaccounts/avdelningar | Inga | Inga |
> | billingaccounts / enrollmentaccounts | Inga | Inga |
> | billingaccounts/fakturor | Inga | Inga |
> | billingaccounts / invoicesections | Inga | Inga |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | Inga | Inga |
> | billingaccounts / invoicesections / billingsubscriptions | Inga | Inga |
> | billingaccounts/invoicesections/billingsubscriptions/transfer | Inga | Inga |
> | billingaccounts/invoicesections/höjning | Inga | Inga |
> | billingaccounts / invoicesections / initiatetransfer | Inga | Inga |
> | billingaccounts / invoicesections / patchoperations | Inga | Inga |
> | billingaccounts / invoicesections / productmoveoperations | Inga | Inga |
> | billingaccounts/invoicesections/Products | Inga | Inga |
> | billingaccounts/invoicesections/Products/transfer | Inga | Inga |
> | billingaccounts/invoicesections/Products/updateautorenew | Inga | Inga |
> | billingaccounts / invoicesections / producttransfersresults | Inga | Inga |
> | billingaccounts/invoicesections/transaktioner | Inga | Inga |
> | billingaccounts/invoicesections/överföringar | Inga | Inga |
> | billingaccounts / lineofcredit | Inga | Inga |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | Inga | Inga |
> | billingaccounts / operationresults | Inga | Inga |
> | billingaccounts / patchoperations | Inga | Inga |
> | billingaccounts / paymentmethods | Inga | Inga |
> | billingaccounts/produkter | Inga | Inga |
> | billingaccounts/transaktioner | Inga | Inga |
> | billingperiods | Inga | Inga |
> | billingpermissions | Inga | Inga |
> | billingproperty | Inga | Inga |
> | billingroleassignments | Inga | Inga |
> | billingroledefinitions | Inga | Inga |
> | createbillingroleassignment | Inga | Inga |
> | enheten | Inga | Inga |
> | enrollmentaccounts | Inga | Inga |
> | fakturor | Inga | Inga |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | operationstatus | Inga | Inga |
> | överlåtelse | Inga | Inga |
> | överföringar/accepttransfer | Inga | Inga |
> | överföringar/declinetransfer | Inga | Inga |
> | överföringar/operationstatus | Inga | Inga |
> | överföringar/validatetransfer | Inga | Inga |
> | validateaddress | Inga | Inga |

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | Inga | Inga |
> | mapapis | Inga | Inga |
> | operations | Inga | Inga |
> | updatecommunicationpreference | Inga | Inga |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | biztalk | Inga | Inga |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Inga | Inga |
> | cordamembers | Inga | Inga |
> | platser | Inga | Inga |
> | platser/blockchainmemberoperationresults | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/listconsortiums | Inga | Inga |
> | platser/watcheroperationresults | Inga | Inga |
> | operations | Inga | Inga |
> | Övervakare | Inga | Inga |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Inga | Inga |
> | tokenservices | Inga | Inga |

## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Inga | Inga |
> | blueprintassignments / assignmentoperations | Inga | Inga |
> | blueprintassignments/åtgärder | Inga | Inga |
> | modeller | Inga | Inga |
> | skisser/artefakter | Inga | Inga |
> | skisser/versioner | Inga | Inga |
> | skisser/versioner/artefakter | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | botservices | Ja | Ja |
> | botservices/kanaler | Inga | Inga |
> | botservices/anslutningar | Inga | Inga |
> | checknameavailability | Inga | Inga |
> | listauthserviceproviders | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftcache"></a>Microsoft. cache

> [!IMPORTANT]
> Om Azure cache för Redis-instansen har kon figurer ATS med ett virtuellt nätverk kan instansen inte flyttas till en annan prenumeration. Se [begränsningar för nätverks flytt](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/operationsstatus | Inga | Inga |
> | operations | Inga | Inga |
> | Redis | Ja | Ja |
> | Redis/eventgridfilters | Inga | Inga |
> | Redis/privatelinkresources | Inga | Inga |
> | redisenterprise | Inga | Inga |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Inga | Inga |
> | calculateexchange | Inga | Inga |
> | calculateprice | Inga | Inga |
> | calculatepurchaseprice | Inga | Inga |
> | kataloger | Inga | Inga |
> | checkoffers | Inga | Inga |
> | checkpurchasestatus | Inga | Inga |
> | checkscopes | Inga | Inga |
> | commercialreservationorders | Inga | Inga |
> | utväxla | Inga | Inga |
> | listbenefits | Inga | Inga |
> | operations | Inga | Inga |
> | placepurchaseorder | Inga | Inga |
> | reservationorders | Inga | Inga |
> | reservationorders / availablescopes | Inga | Inga |
> | reservationorders / calculaterefund | Inga | Inga |
> | reservationorders/slå samman | Inga | Inga |
> | reservationorders/reservationer | Inga | Inga |
> | reservationorders/reservationer/availablescopes | Inga | Inga |
> | reservationorders/reservationer/revisioner | Inga | Inga |
> | reservationorders/retur | Inga | Inga |
> | reservationorders/Split | Inga | Inga |
> | reservationorders/växling | Inga | Inga |
> | reservera | Inga | Inga |
> | resources | Inga | Inga |
> | validatereservationorder | Inga | Inga |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Inga | Inga |
> | cdnwebapplicationfirewallpolicies | Ja | Ja |
> | checknameavailability | Inga | Inga |
> | checkresourceusage | Inga | Inga |
> | edgenodes | Inga | Inga |
> | operationresults | Inga | Inga |
> | operationresults / profileresults | Inga | Inga |
> | operationresults / profileresults / endpointresults | Inga | Inga |
> | operationresults / profileresults / endpointresults / customdomainresults | Inga | Inga |
> | operationresults / profileresults / endpointresults / origingroupresults | Inga | Inga |
> | operationresults / profileresults / endpointresults / originresults | Inga | Inga |
> | operations | Inga | Inga |
> | filer | Ja | Ja |
> | profiler/slut punkter | Ja | Ja |
> | profiler/slut punkter/customdomains | Inga | Inga |
> | profiler/slut punkter/origingroups | Inga | Inga |
> | profiler/slut punkter/ursprung | Inga | Inga |
> | validateprobe | Inga | Inga |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ja | Ja |
> | certificateorders/certifikat | Inga | Inga |
> | operations | Inga | Inga |
> | validatecertificateregistrationinformation | Inga | Inga |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Inga | Inga |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | funktioner | Inga | Inga |
> | checkdomainnameavailability | Inga | Inga |
> | domän namn | Ja | Inga |
> | domän namn/funktioner | Inga | Inga |
> | domän namn/internalloadbalancers | Inga | Inga |
> | domän namn/servicecertificates | Inga | Inga |
> | domän namn/platser | Inga | Inga |
> | domän namn/platser/roller | Inga | Inga |
> | domän namn/platser/roller/metricdefinitions | Inga | Inga |
> | domän namn/platser/roller/mått | Inga | Inga |
> | movesubscriptionresources | Inga | Inga |
> | operatingsystemfamilies | Inga | Inga |
> | operatingsystems | Inga | Inga |
> | operations | Inga | Inga |
> | operationstatuses | Inga | Inga |
> | quotas | Inga | Inga |
> | resourcetypes | Inga | Inga |
> | validatesubscriptionmoveavailability | Inga | Inga |
> | virtualmachines | Ja | Ja |
> | virtualmachines / diagnosticsettings | Inga | Inga |
> | virtualmachines / metricdefinitions | Inga | Inga |
> | virtualmachines/mått | Inga | Inga |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Inga | Inga |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | funktioner | Inga | Inga |
> | expressroutecrossconnections | Inga | Inga |
> | expressroutecrossconnections/peering | Inga | Inga |
> | gatewaysupporteddevices | Inga | Inga |
> | networksecuritygroups | Inga | Inga |
> | operations | Inga | Inga |
> | quotas | Inga | Inga |
> | reservedips | Inga | Inga |
> | virtualnetworks | Inga | Inga |
> | virtualnetworks/remotevirtualnetworkpeeringproxies | Inga | Inga |
> | virtualnetworks/virtualnetworkpeerings | Inga | Inga |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | funktioner | Inga | Inga |
> | checkstorageaccountavailability | Inga | Inga |
> | disk | Inga | Inga |
> | images | Inga | Inga |
> | operations | Inga | Inga |
> | osimages | Inga | Inga |
> | osplatformimages | Inga | Inga |
> | publicimages | Inga | Inga |
> | quotas | Inga | Inga |
> | storageaccounts | Ja | Inga |
> | storageaccounts/blobservices | Inga | Inga |
> | storageaccounts/FileServices | Inga | Inga |
> | storageaccounts/metricdefinitions | Inga | Inga |
> | storageaccounts/mått | Inga | Inga |
> | storageaccounts/queueservices | Inga | Inga |
> | storageaccounts/tjänster | Inga | Inga |
> | storageaccounts/tjänster/diagnosticsettings | Inga | Inga |
> | storageaccounts/tjänster/metricdefinitions | Inga | Inga |
> | storageaccounts/tjänster/mått | Inga | Inga |
> | storageaccounts/tableservices | Inga | Inga |
> | storageaccounts/vmimages | Inga | Inga |
> | vmimages | Inga | Inga |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Inga | Inga |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | checkdomainavailability | Inga | Inga |
> | platser | Inga | Inga |
> | platser/checkskuavailability | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Inga | Inga |
> | ratecard | Inga | Inga |
> | usageaggregates | Inga | Inga |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Se [Virtual Machines flytta vägledning](./move-limitations/virtual-machines-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ja | Ja |
> | diskaccesses | Inga | Inga |
> | diskencryptionsets | Inga | Inga |
> | disk | Ja | Ja |
> | gallerier | Inga | Inga |
> | gallerier/bilder | Inga | Inga |
> | gallerier/avbildningar/versioner | Inga | Inga |
> | hostgroups | Inga | Inga |
> | hostgroups/värdar | Inga | Inga |
> | images | Ja | Ja |
> | platser | Inga | Inga |
> | platser/artifactpublishers | Inga | Inga |
> | platser/capsoperations | Inga | Inga |
> | platser/diskoperations | Inga | Inga |
> | platser/loganalytics | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | platser/utgivare | Inga | Inga |
> | platser/runcommands | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | platser/virtualmachines | Inga | Inga |
> | platser/tillåtna storlekar | Inga | Inga |
> | platser/vsmoperations | Inga | Inga |
> | operations | Inga | Inga |
> | proximityplacementgroups | Ja | Ja |
> | restorepointcollections | Inga | Inga |
> | restorepointcollections / restorepoints | Inga | Inga |
> | sharedvmextensions | Inga | Inga |
> | sharedvmimages | Inga | Inga |
> | sharedvmimages/versioner | Inga | Inga |
> | snapshots | Ja | Ja |
> | sshpublickeys | Inga | Inga |
> | virtualmachines | Ja | Ja |
> | virtualmachines/tillägg | Ja | Ja |
> | virtualmachines / metricdefinitions | Inga | Inga |
> | virtualmachines / runcommands | Inga | Inga |
> | virtualmachinescalesets | Ja | Ja |
> | virtualmachinescalesets/tillägg | Inga | Inga |
> | virtualmachinescalesets/NetworkInterfaces | Inga | Inga |
> | virtualmachinescalesets/publicipaddresses | Inga | Inga |
> | virtualmachinescalesets/virtualmachines | Inga | Inga |
> | virtualmachinescalesets/virtualmachines/NetworkInterfaces | Inga | Inga |

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Inga | Inga |
> | balanserar | Inga | Inga |
> | budget | Inga | Inga |
> | utgifts | Inga | Inga |
> | costtags | Inga | Inga |
> | krediter | Inga | Inga |
> | händelser | Inga | Inga |
> | prognoser | Inga | Inga |
> | samtliga | Inga | Inga |
> | marknads platser | Inga | Inga |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | operationstatus | Inga | Inga |
> | pricesheets | Inga | Inga |
> | läkemedle | Inga | Inga |
> | reservationdetails | Inga | Inga |
> | reservationrecommendationdetails | Inga | Inga |
> | reservationrecommendations | Inga | Inga |
> | reservationsummaries | Inga | Inga |
> | reservationtransactions | Inga | Inga |
> | tags | Inga | Inga |
> | klienter | Inga | Inga |
> | begreppen | Inga | Inga |
> | usagedetails | Inga | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containergroups | Inga | Inga |
> | platser | Inga | Inga |
> | platser/cachedimages | Inga | Inga |
> | platser/funktioner | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | operations | Inga | Inga |
> | serviceassociationlinks | Inga | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | platser | Inga | Inga |
> | platser/auktorisera | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/setupauth | Inga | Inga |
> | operations | Inga | Inga |
> | register | Ja | Ja |
> | register/agentpools | Ja | Ja |
> | register/agentpools/listqueuestatus | Inga | Inga |
> | register/versioner | Inga | Inga |
> | register/versioner/Avbryt | Inga | Inga |
> | register/build/getloglink | Inga | Inga |
> | register/buildtasks | Ja | Ja |
> | register/buildtasks/listsourcerepositoryproperties | Inga | Inga |
> | register/buildtasks/steg | Inga | Inga |
> | register/buildtasks/steg/listbuildarguments | Inga | Inga |
> | register/eventgridfilters | Inga | Inga |
> | register/exportpipelines | Inga | Inga |
> | register/generatecredentials | Inga | Inga |
> | register/getbuildsourceuploadurl | Inga | Inga |
> | register/getcredentials | Inga | Inga |
> | register/importimage | Inga | Inga |
> | register/importpipelines | Inga | Inga |
> | register/listbuildsourceuploadurl | Inga | Inga |
> | register/listcredentials | Inga | Inga |
> | register/ListPolicies | Inga | Inga |
> | register/listusages | Inga | Inga |
> | register/pipelineruns | Inga | Inga |
> | register/privateendpointconnectionproxies | Inga | Inga |
> | register/privateendpointconnectionproxies/validate | Inga | Inga |
> | register/privateendpointconnections | Inga | Inga |
> | register/privatelinkresources | Inga | Inga |
> | register/queuebuild | Inga | Inga |
> | register/regeneratecredential | Inga | Inga |
> | register/regeneratecredentials | Inga | Inga |
> | register/replikeringar | Ja | Ja |
> | register/körningar | Inga | Inga |
> | register/körningar/Avbryt | Inga | Inga |
> | register/körningar/listlogsasurl | Inga | Inga |
> | register/schedulerun | Inga | Inga |
> | register/scopemaps | Inga | Inga |
> | register/taskruns | Inga | Inga |
> | register/taskruns/listdetails | Inga | Inga |
> | register/uppgifter | Ja | Ja |
> | register/uppgifter/listdetails | Inga | Inga |
> | register/token | Inga | Inga |
> | register/updatepolicies | Inga | Inga |
> | register/Webhooks | Ja | Ja |
> | register/Webhooks/getcallbackconfig | Inga | Inga |
> | register/Webhooks/listevents | Inga | Inga |
> | register/Webhooks/ping | Inga | Inga |

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containerservices | Inga | Inga |
> | platser | Inga | Inga |
> | platser/openshiftclusters | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | platser/Dirigerare | Inga | Inga |
> | managedclusters | Inga | Inga |
> | openshiftmanagedclusters | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Inga | Inga |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aviseringar | Inga | Inga |
> | billingaccounts | Inga | Inga |
> | budget | Inga | Inga |
> | cloudconnectors | Inga | Inga |
> | anslutningar | Ja | Ja |
> | enheten | Inga | Inga |
> | enheter | Inga | Inga |
> | enrollmentaccounts | Inga | Inga |
> | exporteras | Inga | Inga |
> | externalbillingaccounts | Inga | Inga |
> | externalbillingaccounts/aviseringar | Inga | Inga |
> | externalbillingaccounts/dimensioner | Inga | Inga |
> | externalbillingaccounts/prognos | Inga | Inga |
> | externalbillingaccounts/fråga | Inga | Inga |
> | externalsubscriptions | Inga | Inga |
> | externalsubscriptions/aviseringar | Inga | Inga |
> | externalsubscriptions/dimensioner | Inga | Inga |
> | externalsubscriptions/prognos | Inga | Inga |
> | externalsubscriptions/fråga | Inga | Inga |
> | forecast (prognos) | Inga | Inga |
> | operations | Inga | Inga |
> | DocumentDB | Inga | Inga |
> | registrera | Inga | Inga |
> | reportconfigs | Inga | Inga |
> | rapporter | Inga | Inga |
> | inställningar | Inga | Inga |
> | showbackrules | Inga | Inga |
> | vyer | Inga | Inga |

## <a name="microsoftcostmanagementexports"></a>Microsoft. CostManagementExports

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Inga | Inga |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | nav | Inga | Inga |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Inga | Inga |
> | autentiseringsbegäran | Inga | Inga |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | typer | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | resourceproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utskrifts | Inga | Inga |
> | platser | Inga | Inga |
> | platser/availableskus | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/regionconfiguration | Inga | Inga |
> | platser/validateaddress | Inga | Inga |
> | platser/validateinputs | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availableskus | Inga | Inga |
> | databoxedgedevices | Ja | Ja |
> | databoxedgedevices / checknameavailability | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/getnetworkpolicies | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | arbetsytor | Inga | Inga |
> | arbets ytor/dbworkspaces | Inga | Inga |
> | arbets ytor/virtualnetworkpeerings | Inga | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kataloger | Ja | Ja |
> | checknameavailability | Inga | Inga |
> | datacatalogs | Inga | Inga |
> | platser | Inga | Inga |
> | platser/jobb | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Inga | Inga |

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | distributionspaket | Inga | Inga |
> | utgå | Inga | Inga |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | Inga | Inga |
> | checkdatafactorynameavailability | Inga | Inga |
> | datafactories | Ja | Ja |
> | datafactories / diagnosticsettings | Inga | Inga |
> | datafactories / metricdefinitions | Inga | Inga |
> | datafactoryschema | Inga | Inga |
> | fabriker | Ja | Ja |
> | fabriker/integrationruntimes | Inga | Inga |
> | platser | Inga | Inga |
> | platser/configurefactoryrepo | Inga | Inga |
> | platser/getfeaturevalue | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Inga | Inga |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | konton/datalakestoreaccounts | Inga | Inga |
> | konton/storageaccounts | Inga | Inga |
> | konton/storageaccounts/behållare | Inga | Inga |
> | konton/storageaccounts/containers/listsastokens | Inga | Inga |
> | platser | Inga | Inga |
> | platser/kapacitet | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | konton/eventgridfilters | Inga | Inga |
> | konton/firewallrules | Inga | Inga |
> | platser | Inga | Inga |
> | platser/kapacitet | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | services | Inga | Inga |
> | tjänster/projekt | Inga | Inga |
> | lots | Inga | Inga |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | backupvaults | Inga | Inga |
> | platser | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | konton/resurser | Inga | Inga |
> | konton/resurser/data uppsättningar | Inga | Inga |
> | konton/resurser/inbjudningar | Inga | Inga |
> | konton/resurser/providersharesubscriptions | Inga | Inga |
> | konton/resurser/synchronizationsettings | Inga | Inga |
> | konton/sharesubscriptions | Inga | Inga |
> | konton/sharesubscriptions/consumersourcedatasets | Inga | Inga |
> | konton/sharesubscriptions/datasetmappings | Inga | Inga |
> | konton/sharesubscriptions/utlösare | Inga | Inga |
> | listinvitations | Inga | Inga |
> | platser | Inga | Inga |
> | platser/consumerinvitations | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/rejectinvitation | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | platser | Inga | Inga |
> | platser/azureasyncoperation | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/performancetiers | Inga | Inga |
> | platser/privateendpointconnectionazureasyncoperation | Inga | Inga |
> | platser/privateendpointconnectionoperationresults | Inga | Inga |
> | platser/privateendpointconnectionproxyazureasyncoperation | Inga | Inga |
> | platser/privateendpointconnectionproxyoperationresults | Inga | Inga |
> | platser/recommendedactionsessionsazureasyncoperation | Inga | Inga |
> | platser/recommendedactionsessionsoperationresults | Inga | Inga |
> | platser/securityalertpoliciesazureasyncoperation | Inga | Inga |
> | platser/securityalertpoliciesoperationresults | Inga | Inga |
> | platser/serverkeyazureasyncoperation | Inga | Inga |
> | platser/serverkeyoperationresults | Inga | Inga |
> | operations | Inga | Inga |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Inga | Inga |
> | servrar/privateendpointconnectionproxies | Inga | Inga |
> | servrar/privateendpointconnections | Inga | Inga |
> | servrar/privatelinkresources | Inga | Inga |
> | servrar/querytexts | Inga | Inga |
> | servrar/recoverableservers | Inga | Inga |
> | servrar/topquerystatistics | Inga | Inga |
> | servrar/virtualnetworkrules | Inga | Inga |
> | servrar/waitstatistics | Inga | Inga |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | platser | Inga | Inga |
> | platser/administratorazureasyncoperation | Inga | Inga |
> | platser/administratoroperationresults | Inga | Inga |
> | platser/azureasyncoperation | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/performancetiers | Inga | Inga |
> | platser/privateendpointconnectionazureasyncoperation | Inga | Inga |
> | platser/privateendpointconnectionoperationresults | Inga | Inga |
> | platser/privateendpointconnectionproxyazureasyncoperation | Inga | Inga |
> | platser/privateendpointconnectionproxyoperationresults | Inga | Inga |
> | platser/recommendedactionsessionsazureasyncoperation | Inga | Inga |
> | platser/recommendedactionsessionsoperationresults | Inga | Inga |
> | platser/securityalertpoliciesazureasyncoperation | Inga | Inga |
> | platser/securityalertpoliciesoperationresults | Inga | Inga |
> | platser/serverkeyazureasyncoperation | Inga | Inga |
> | platser/serverkeyoperationresults | Inga | Inga |
> | operations | Inga | Inga |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Inga | Inga |
> | servrar/nycklar | Inga | Inga |
> | servrar/privateendpointconnectionproxies | Inga | Inga |
> | servrar/privateendpointconnections | Inga | Inga |
> | servrar/privatelinkresources | Inga | Inga |
> | servrar/querytexts | Inga | Inga |
> | servrar/recoverableservers | Inga | Inga |
> | servrar/topquerystatistics | Inga | Inga |
> | servrar/virtualnetworkrules | Inga | Inga |
> | servrar/waitstatistics | Inga | Inga |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | platser | Inga | Inga |
> | platser/administratorazureasyncoperation | Inga | Inga |
> | platser/administratoroperationresults | Inga | Inga |
> | platser/azureasyncoperation | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/performancetiers | Inga | Inga |
> | platser/privateendpointconnectionazureasyncoperation | Inga | Inga |
> | platser/privateendpointconnectionoperationresults | Inga | Inga |
> | platser/privateendpointconnectionproxyazureasyncoperation | Inga | Inga |
> | platser/privateendpointconnectionproxyoperationresults | Inga | Inga |
> | platser/recommendedactionsessionsazureasyncoperation | Inga | Inga |
> | platser/recommendedactionsessionsoperationresults | Inga | Inga |
> | platser/securityalertpoliciesazureasyncoperation | Inga | Inga |
> | platser/securityalertpoliciesoperationresults | Inga | Inga |
> | platser/serverkeyazureasyncoperation | Inga | Inga |
> | platser/serverkeyoperationresults | Inga | Inga |
> | operations | Inga | Inga |
> | servergroups | Inga | Inga |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Inga | Inga |
> | servrar/nycklar | Inga | Inga |
> | servrar/privateendpointconnectionproxies | Inga | Inga |
> | servrar/privateendpointconnections | Inga | Inga |
> | servrar/privatelinkresources | Inga | Inga |
> | servrar/querytexts | Inga | Inga |
> | servrar/recoverableservers | Inga | Inga |
> | servrar/topquerystatistics | Inga | Inga |
> | servrar/virtualnetworkrules | Inga | Inga |
> | servrar/waitstatistics | Inga | Inga |
> | serversv2 | Ja | Ja |
> | singleservers | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ja | Ja |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | distributioner | Ja | Ja |
> | servicetopologies | Ja | Ja |
> | servicetopologies/tjänster | Ja | Ja |
> | servicetopologies/tjänster/serviceunits | Ja | Ja |
> | steg | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Ja | Ja |
> | applicationgroups/program | Inga | Inga |
> | applicationgroups/Station ära datorer | Inga | Inga |
> | applicationgroups / startmenuitems | Inga | Inga |
> | hostpools | Ja | Ja |
> | hostpools / sessionhosts | Inga | Inga |
> | hostpools / sessionhosts / usersessions | Inga | Inga |
> | hostpools / usersessions | Inga | Inga |
> | operations | Inga | Inga |
> | arbetsytor | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | checkprovisioningservicenameavailability | Inga | Inga |
> | elasticpools | Inga | Inga |
> | elasticpools / iothubtenants | Inga | Inga |
> | iothubs | Ja | Ja |
> | iothubs / eventgridfilters | Inga | Inga |
> | iothubs/securitysettings | Inga | Inga |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | provisioningservices | Ja | Ja |
> | användningar | Inga | Inga |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | pipelines | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domänkontrollanter | Ja | Ja |
> | kontrollanter/listconnectiondetails | Inga | Inga |
> | platser | Inga | Inga |
> | platser/checkcontainerhostmapping | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labcenters | Inga | Inga |
> | Laboration | Ja | Inga |
> | labb/miljöer | Ja | Ja |
> | labb/servicerunners | Ja | Ja |
> | labb/virtualmachines | Ja | Inga |
> | platser | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | operations | Inga | Inga |
> | scheman | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Inga | Inga |
> | digitaltwinsinstances / operationresults | Inga | Inga |
> | platser | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Inga | Inga |
> | databaseaccounts | Ja | Ja |
> | platser | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/operationsstatus | Inga | Inga |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | Inga | Inga |
> | domäner | Ja | Ja |
> | domäner/domainownershipidentifiers | Inga | Inga |
> | generatessorequest | Inga | Inga |
> | listdomainrecommendations | Inga | Inga |
> | operations | Inga | Inga |
> | topleveldomains | Inga | Inga |
> | validatedomainregistrationinformation | Inga | Inga |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domäner | Ja | Ja |
> | domäner/ämnen | Inga | Inga |
> | eventsubscriptions | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. |
> | extensiontopics | Inga | Inga |
> | platser | Inga | Inga |
> | platser/eventsubscriptions | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/operationsstatus | Inga | Inga |
> | platser/topictypes | Inga | Inga |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | operationsstatus | Inga | Inga |
> | partnernamespaces | Ja | Ja |
> | partnernamespaces/eventchannels | Inga | Inga |
> | partnerregistrations | Inga | Inga |
> | partnertopics | Ja | Ja |
> | partnertopics / eventsubscriptions | Inga | Inga |
> | systemtopics | Ja | Ja |
> | systemtopics / eventsubscriptions | Inga | Inga |
> | avsnitt | Ja | Ja |
> | topictypes | Inga | Inga |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Inga | Inga |
> | checknameavailability | Inga | Inga |
> | checknamespaceavailability | Inga | Inga |
> | kluster | Ja | Ja |
> | platser | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Inga | Inga |
> | namnrymder/disasterrecoveryconfigs | Inga | Inga |
> | namnrymder/disasterrecoveryconfigs/checknameavailability | Inga | Inga |
> | namnrymder/eventhubs | Inga | Inga |
> | namnrymder/eventhubs/authorizationrules | Inga | Inga |
> | namnrymder/eventhubs/consumergroups | Inga | Inga |
> | namnrymder/networkrulesets | Inga | Inga |
> | operations | Inga | Inga |
> | sku | Inga | Inga |

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Inga | Inga |
> | platser | Inga | Inga |
> | platser/åtgärder | Inga | Inga |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | featureproviders | Inga | Inga |
> | funktioner | Inga | Inga |
> | operations | Inga | Inga |
> | finansiär | Inga | Inga |
> | subscriptionfeatureregistrations | Inga | Inga |

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Inga | Inga |
> | automanagedvmconfigurationprofiles | Inga | Inga |
> | guestconfigurationassignments | Inga | Inga |
> | operations | Inga | Inga |
> | IntelliPoint | Inga | Inga |
> | softwareupdateprofile | Inga | Inga |
> | softwareupdates | Inga | Inga |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hanainstances | Inga | Inga |
> | platser | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | platser/operationsstatus | Inga | Inga |
> | operations | Inga | Inga |
> | sapmonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Inga | Inga |
> | platser | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!IMPORTANT]
> Du kan flytta HDInsight-kluster till en ny prenumeration eller resurs grupp. Du kan dock inte flytta över prenumerationer som nätverks resurserna är länkade till HDInsight-klustret (till exempel det virtuella nätverket, NIC eller belastningsutjämnaren). Dessutom kan du inte flytta till en ny resurs grupp ett nätverkskort som är kopplat till en virtuell dator för klustret.
>
> När du flyttar ett HDInsight-kluster till en ny prenumeration måste du först flytta andra resurser (t. ex. lagrings kontot). Flytta sedan HDInsight-klustret själv.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |
> | kluster/program | Inga | Inga |
> | kluster/operationresults | Inga | Inga |
> | platser | Inga | Inga |
> | platser/azureasyncoperations | Inga | Inga |
> | platser/billingspecs | Inga | Inga |
> | platser/funktioner | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | platser/validatecreaterequest | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | services | Ja | Ja |
> | tjänster/privateendpointconnections | Inga | Inga |
> | tjänster/privatelinkresources | Inga | Inga |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/operationstatus | Inga | Inga |
> | faxar | Ja | Ja |
> | datorer/tillägg | Ja | Ja |
> | operations | Inga | Inga |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |
> | operations | Inga | Inga |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | devices | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | vnfs | Inga | Inga |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | delarna | Inga | Inga |
> | platser | Inga | Inga |
> | networkscopes | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utskrifts | Ja | Ja |
> | platser | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!IMPORTANT]
> Se till att flytta till den nya prenumerationen inte överskrider [prenumerations kvoterna](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ja | Ja |
> | activitylogalerts | Inga | Inga |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | baslinje | Inga | Inga |
> | calculatebaseline | Inga | Inga |
> | delarna | Ja | Ja |
> | komponenter/händelser | Inga | Inga |
> | komponenter/linkedstorageaccounts | Inga | Inga |
> | komponenter/metadata | Inga | Inga |
> | komponenter/mått | Inga | Inga |
> | komponenter/pricingplans | Inga | Inga |
> | komponenter/fråga | Inga | Inga |
> | datacollectionrules | Inga | Inga |
> | diagnosticsettings | Inga | Inga |
> | diagnosticsettingscategories | Inga | Inga |
> | eventcategories | Inga | Inga |
> | eventtypes | Inga | Inga |
> | extendeddiagnosticsettings | Inga | Inga |
> | guestdiagnosticsettings | Inga | Inga |
> | listmigrationdate | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | logdefinitions | Inga | Inga |
> | logprofiles | Inga | Inga |
> | loggar | Inga | Inga |
> | metricalerts | Inga | Inga |
> | metricbaselines | Inga | Inga |
> | metricbatch | Inga | Inga |
> | metricdefinitions | Inga | Inga |
> | metricnamespaces | Inga | Inga |
> | metrics | Inga | Inga |
> | migratealertrules | Inga | Inga |
> | migratetonewpricingmodel | Inga | Inga |
> | Mina arbets böcker | Inga | Inga |
> | notificationgroups | Inga | Inga |
> | operations | Inga | Inga |
> | privatelinkscopeoperationstatuses | Inga | Inga |
> | privatelinkscopes | Inga | Inga |
> | privatelinkscopes / privateendpointconnectionproxies | Inga | Inga |
> | privatelinkscopes / privateendpointconnections | Inga | Inga |
> | privatelinkscopes / scopedresources | Inga | Inga |
> | rollbacktolegacypricingmodel | Inga | Inga |
> | scheduledqueryrules | Ja | Ja |
> | topologi | Inga | Inga |
> | transaktioner | Inga | Inga |
> | vminsightsonboardingstatuses | Inga | Inga |
> | webbtester | Ja | Ja |
> | webbtester/gettestresultfile | Inga | Inga |
> | arbetsböcker | Ja | Ja |
> | workbooktemplates | Ja | Ja |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | apptemplates | Inga | Inga |
> | checknameavailability | Inga | Inga |
> | checksubdomainavailability | Inga | Inga |
> | iotapps | Ja | Ja |
> | operations | Inga | Inga |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ja | Ja |
> | Rita | Ja | Ja |
> | operations | Inga | Inga |

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!IMPORTANT]
> Nyckel valv som används för disk kryptering kan inte flyttas till en resurs grupp i samma prenumeration eller mellan prenumerationer.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | deletedvaults | Inga | Inga |
> | hsmpools | Inga | Inga |
> | platser | Inga | Inga |
> | platser/deletedvaults | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | managedhsms | Inga | Inga |
> | operations | Inga | Inga |
> | valv | Ja | Ja |
> | valv/accesspolicies | Inga | Inga |
> | valv/eventgridfilters | Inga | Inga |
> | valv/hemligheter | Inga | Inga |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ja | Ja |
> | platser | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | registeredsubscriptions | Inga | Inga |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Inga | Inga |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |
> | kluster/attacheddatabaseconfigurations | Inga | Inga |
> | kluster/databaser | Inga | Inga |
> | kluster/databaser/dataconnections | Inga | Inga |
> | kluster/databaser/eventhubconnections | Inga | Inga |
> | kluster/databaser/principalassignments | Inga | Inga |
> | kluster/principalassignments | Inga | Inga |
> | platser | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labaccounts | Inga | Inga |
> | platser | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | operations | Inga | Inga |
> | användare | Inga | Inga |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftlocationservices"></a>Microsoft. filen LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Inga | Inga |
> | integrationaccounts | Ja | Ja |
> | integrationserviceenvironments | Ja | Inga |
> | integrationserviceenvironments/managedapis | Ja | Inga |
> | isolatedenvironments | Inga | Inga |
> | platser | Inga | Inga |
> | platser/arbets flöden | Inga | Inga |
> | operations | Inga | Inga |
> | arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Inga | Inga |
> | platser | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | platser/operationsstatus | Inga | Inga |
> | operations | Inga | Inga |
> | WebServices | Ja | Inga |
> | arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Inga | Inga |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |
> | konton/arbets ytor | Inga | Inga |
> | konton/arbets ytor/projekt | Inga | Inga |
> | teamaccounts | Inga | Inga |
> | teamaccounts/arbets ytor | Inga | Inga |
> | teamaccounts/arbets ytor/projekt | Inga | Inga |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/computeoperationsstatus | Inga | Inga |
> | platser/kvoter | Inga | Inga |
> | platser/updatequotas | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | platser/tillåtna storlekar | Inga | Inga |
> | platser/workspaceoperationsstatus | Inga | Inga |
> | operations | Inga | Inga |
> | arbetsytor | Inga | Inga |
> | arbets ytor/beräkningar | Inga | Inga |
> | arbets ytor/eventgridfilters | Inga | Inga |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applyupdates | Inga | Inga |
> | configurationassignments | Inga | Inga |
> | maintenanceconfigurations | Ja | Ja |
> | uppdateringar | Inga | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | identiteter | Inga | Inga |
> | operations | Inga | Inga |
> | userassignedidentities | Inga | Inga |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | managednetworks | Inga | Inga |
> | managednetworks / managednetworkgroups | Inga | Inga |
> | managednetworks / managednetworkpeeringpolicies | Inga | Inga |
> | avisering | Inga | Inga |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Inga | Inga |
> | operations | Inga | Inga |
> | operationstatuses | Inga | Inga |
> | registrationassignments | Inga | Inga |
> | registrationdefinitions | Inga | Inga |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | getentities | Inga | Inga |
> | managementgroups | Inga | Inga |
> | managementgroups/inställningar | Inga | Inga |
> | operationresults | Inga | Inga |
> | operationresults / asyncoperation | Inga | Inga |
> | operations | Inga | Inga |
> | resources | Inga | Inga |
> | starttenantbackfill | Inga | Inga |
> | tenantbackfillstatus | Inga | Inga |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | konton/eventgridfilters | Inga | Inga |
> | konton/privateatlases | Ja | Ja |
> | operations | Inga | Inga |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Inga | Inga |
> | budgivning | Inga | Inga |
> | offertypes | Inga | Inga |
> | offertypes/utgivare | Inga | Inga |
> | offertypes/utgivare/erbjudanden | Inga | Inga |
> | offertypes/utgivare/erbjudanden/planer | Inga | Inga |
> | offertypes/utgivare/erbjudanden/planer/avtal | Inga | Inga |
> | offertypes/utgivare/erbjudanden/planer/konfigurationer | Inga | Inga |
> | offertypes/utgivare/erbjudanden/planer/configs/importimage | Inga | Inga |
> | operations | Inga | Inga |
> | privategalleryitems | Inga | Inga |
> | privatestoreclient | Inga | Inga |
> | privatestores | Inga | Inga |
> | privatestores/erbjudanden | Inga | Inga |
> | läkemedle | Inga | Inga |
> | Utgivare | Inga | Inga |
> | Utgivare/erbjudanden | Inga | Inga |
> | Utgivare/erbjudanden/ändringar | Inga | Inga |
> | registrera | Inga | Inga |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Inga | Inga |
> | listcommunicationpreference | Inga | Inga |
> | operations | Inga | Inga |
> | updatecommunicationpreference | Inga | Inga |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | villkor | Inga | Inga |
> | offertypes | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | platser | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | Media Services | Ja | Ja |
> | Media Services/accountfilters | Inga | Inga |
> | Media Services/till gångar | Inga | Inga |
> | Media Services/assets/assetfilters | Inga | Inga |
> | Media Services/contentkeypolicies | Inga | Inga |
> | Media Services/eventgridfilters | Inga | Inga |
> | Media Services/liveeventoperations | Inga | Inga |
> | Media Services/liveevents | Ja | Ja |
> | Media Services/liveevents/liveoutputs | Inga | Inga |
> | Media Services/liveoutputoperations | Inga | Inga |
> | Media Services/streamingendpointoperations | Inga | Inga |
> | Media Services/strömnings slut punkter | Ja | Ja |
> | Media Services/streaminglocators | Inga | Inga |
> | Media Services/streamingpolicies | Inga | Inga |
> | Media Services/transformeringar | Inga | Inga |
> | Media Services/transformeringar/jobb | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | appclusters | Inga | Inga |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Inga | Inga |
> | platser | Inga | Inga |
> | platser/assessmentoptions | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | migrateprojects | Inga | Inga |
> | movecollections | Inga | Inga |
> | operations | Inga | Inga |
> | samarbetsprojekt | Inga | Inga |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Inga | Inga |
> | platser | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | objectunderstandingaccounts | Inga | Inga |
> | operations | Inga | Inga |
> | remoterenderingaccounts | Ja | Ja |
> | spatialanchorsaccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Inga | Inga |
> | netappaccounts / backuppolicies | Inga | Inga |
> | netappaccounts / capacitypools | Inga | Inga |
> | netappaccounts/capacitypools/Volumes | Inga | Inga |
> | netappaccounts/capacitypools/Volumes/mounttargets | Inga | Inga |
> | netappaccounts/capacitypools/volym/ögonblicks bilder | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Se [rikt linjer för nätverks flytt](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | Inga | Inga |
> | applicationgatewayavailableresponseheaders | Inga | Inga |
> | applicationgatewayavailableservervariables | Inga | Inga |
> | applicationgatewayavailablessloptions | Inga | Inga |
> | applicationgatewayavailablewafrulesets | Inga | Inga |
> | applicationgateways | Inga | Inga |
> | applicationgatewaywebapplicationfirewallpolicies | Inga | Inga |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewallfqdntags | Inga | Inga |
> | azurefirewalls | Inga | Inga |
> | bastionhosts | Inga | Inga |
> | bgpservicecommunities | Inga | Inga |
> | checkfrontdoornameavailability | Inga | Inga |
> | checktrafficmanagernameavailability | Inga | Inga |
> | anslutning | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Inga | Inga |
> | dnsoperationresults | Inga | Inga |
> | dnsoperationstatuses | Inga | Inga |
> | dnszones | Ja | Ja |
> | dnszones/a | Inga | Inga |
> | dnszones/AAAA | Inga | Inga |
> | dnszones/alla | Inga | Inga |
> | dnszones/CAA | Inga | Inga |
> | dnszones/CNAME | Inga | Inga |
> | dnszones/MX | Inga | Inga |
> | dnszones/ns | Inga | Inga |
> | dnszones/PTR | Inga | Inga |
> | dnszones/Recordset | Inga | Inga |
> | dnszones/SOA | Inga | Inga |
> | dnszones/SRV | Inga | Inga |
> | dnszones/txt | Inga | Inga |
> | expressroutecircuits | Inga | Inga |
> | expressroutegateways | Inga | Inga |
> | expressrouteserviceproviders | Inga | Inga |
> | firewallpolicies | Ja | Ja |
> | frontdooroperationresults | Inga | Inga |
> | frontdoors | Inga | Inga |
> | frontdoors / frontendendpoints | Inga | Inga |
> | frontdoorwebapplicationfirewallmanagedrulesets | Inga | Inga |
> | frontdoorwebapplicationfirewallpolicies | Inga | Inga |
> | getdnsresourcereference | Inga | Inga |
> | internalnotify | Inga | Inga |
> | ipallocations | Ja | Ja |
> | ipgroups | Ja | Ja |
> | belastningsutjämnare | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
> | localnetworkgateways | Ja | Ja |
> | platser | Inga | Inga |
> | platser/autoapprovedprivatelinkservices | Inga | Inga |
> | platser/availabledelegations | Inga | Inga |
> | platser/availableprivateendpointtypes | Inga | Inga |
> | platser/availableservicealiases | Inga | Inga |
> | platser/baremetaltenants | Inga | Inga |
> | platser/batchnotifyprivateendpointsforresourcemove | Inga | Inga |
> | platser/batchvalidateprivateendpointsforresourcemove | Inga | Inga |
> | platser/checkacceleratednetworkingsupport | Inga | Inga |
> | platser/checkdnsnameavailability | Inga | Inga |
> | platser/checkprivatelinkservicevisibility | Inga | Inga |
> | platser/commitinternalazurenetworkmanagerconfiguration | Inga | Inga |
> | platser/effectiveresourceownership | Inga | Inga |
> | platser/nfvoperationresults | Inga | Inga |
> | platser/nfvoperations | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | platser/servicetags | Inga | Inga |
> | platser/setresourceownership | Inga | Inga |
> | platser/supportedvirtualmachinesizes | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | platser/validateresourceownership | Inga | Inga |
> | platser/virtualnetworkavailableendpointservices | Inga | Inga |
> | natgateways | Inga | Inga |
> | networkexperimentprofiles | Inga | Inga |
> | networkintentpolicies | Ja | Ja |
> | NetworkInterfaces | Ja | Ja |
> | networkprofiles | Inga | Inga |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Ja | Inga |
> | networkwatchers / connectionmonitors | Ja | Inga |
> | networkwatchers / flowlogs | Ja | Inga |
> | networkwatchers / pingmeshes | Ja | Inga |
> | operations | Inga | Inga |
> | p2svpngateways | Inga | Inga |
> | privatednsoperationresults | Inga | Inga |
> | privatednsoperationstatuses | Inga | Inga |
> | privatednszones | Ja | Ja |
> | privatednszones/a | Inga | Inga |
> | privatednszones/AAAA | Inga | Inga |
> | privatednszones/alla | Inga | Inga |
> | privatednszones/CNAME | Inga | Inga |
> | privatednszones/MX | Inga | Inga |
> | privatednszones/PTR | Inga | Inga |
> | privatednszones/SOA | Inga | Inga |
> | privatednszones/SRV | Inga | Inga |
> | privatednszones/txt | Inga | Inga |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privatednszonesinternal | Inga | Inga |
> | privateendpointredirectmaps | Inga | Inga |
> | privateendpoints | Ja | Ja |
> | privatelinkservices | Inga | Inga |
> | publicipaddresses | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
> | publicipprefixes | Ja | Ja |
> | routefilters | Inga | Inga |
> | routetables | Ja | Ja |
> | securitypartnerproviders | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagergeographichierarchies | Inga | Inga |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/termiska kartor | Inga | Inga |
> | trafficmanagerusermetricskeys | Inga | Inga |
> | virtualhubs | Inga | Inga |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Inga | Inga |
> | virtualrouters | Ja | Ja |
> | virtualwans | Inga | Inga |
> | vpngateways (virtuellt WAN) | Inga | Inga |
> | vpnserverconfigurations | Inga | Inga |
> | vpnsites (virtuellt WAN) | Inga | Inga |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | checknamespaceavailability | Inga | Inga |
> | namn områden | Ja | Ja |
> | namnrymder/notificationhubs | Ja | Ja |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hypervsites | Inga | Inga |
> | importsites | Inga | Inga |
> | operations | Inga | Inga |
> | serversites | Inga | Inga |
> | vmwaresites | Inga | Inga |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!IMPORTANT]
> Kontrol lera att flytta till en ny prenumeration inte överskrider [prenumerations kvoterna](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Det går inte att flytta arbets ytor som har ett länkat Automation-konto. Innan du påbörjar en flytt åtgärd måste du ta bort länken till eventuella Automation-konton.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Inga | Inga |
> | deletedworkspaces | Inga | Inga |
> | linktargets | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | storageinsightconfigs | Inga | Inga |
> | arbetsytor | Ja | Ja |
> | arbets ytor/data källor | Inga | Inga |
> | arbets ytor/linkedservices | Inga | Inga |
> | arbets ytor/linkedstorageaccounts | Inga | Inga |
> | arbets ytor/metadata | Inga | Inga |
> | arbets ytor/fråga | Inga | Inga |
> | arbets ytor/scopedprivatelinkproxies | Inga | Inga |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | managementassociations | Inga | Inga |
> | managementconfigurations | Ja | Ja |
> | operations | Inga | Inga |
> | lösningar | Ja | Ja |
> | vyer | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Inga | Inga |
> | legacypeerings | Inga | Inga |
> | operations | Inga | Inga |
> | peerasns | Inga | Inga |
> | peeringlocations | Inga | Inga |
> | peerings | Inga | Inga |
> | peeringservicecountries | Inga | Inga |
> | peeringservicelocations | Inga | Inga |
> | peeringserviceproviders | Inga | Inga |
> | peeringservices | Inga | Inga |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | Inga | Inga |
> | operations | Inga | Inga |
> | policyevents | Inga | Inga |
> | policystates | Inga | Inga |
> | policytrackedresources | Inga | Inga |
> | reparationer | Inga | Inga |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> |  -konsoler | Inga | Inga |
> | instrumentpaneler | Ja | Ja |
> | platser | Inga | Inga |
> | platser/konsoler | Inga | Inga |
> | platser/usersettings | Inga | Inga |
> | operations | Inga | Inga |
> | usersettings | Inga | Inga |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kapaciteter | Ja | Ja |
> | platser | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Inga | Inga |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |
> | checknameavailability | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Inga | Inga |
> | providerregistrations | Inga | Inga |
> | providerregistrations / resourcetyperegistrations | Inga | Inga |
> | distributioner | Inga | Inga |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | arbetsytor | Inga | Inga |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!IMPORTANT]
> Se [Recovery Services flytta vägledning](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Inga | Inga |
> | platser | Inga | Inga |
> | platser/allocatedstamp | Inga | Inga |
> | platser/allocatedstamp | Inga | Inga |
> | platser/backupaadproperties | Inga | Inga |
> | platser/backupcrossregionrestore | Inga | Inga |
> | platser/backupcrrjob | Inga | Inga |
> | platser/backupcrrjobs | Inga | Inga |
> | platser/backupcrroperationresults | Inga | Inga |
> | platser/backupcrroperationsstatus | Inga | Inga |
> | platser/backupprevalidateprotection | Inga | Inga |
> | platser/Backupstatus | Inga | Inga |
> | platser/backupvalidatefeatures | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | operations | Inga | Inga |
> | replicationeligibilityresults | Inga | Inga |
> | valv | Ja | Ja |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/operationsstatus | Inga | Inga |
> | openshiftclusters | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Inga | Inga |
> | namnrymder/hybridconnections | Inga | Inga |
> | namnrymder/hybridconnections/authorizationrules | Inga | Inga |
> | namnrymder/privateendpointconnections | Inga | Inga |
> | namnrymder/wcfrelays | Inga | Inga |
> | namnrymder/wcfrelays/authorizationrules | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Inga | Inga |
> | skickar | Ja | Ja |
> | resourcechangedetails | Inga | Inga |
> | resourcechanges | Inga | Inga |
> | resources | Inga | Inga |
> | resourceshistory | Inga | Inga |
> | subscriptionsstatus | Inga | Inga |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Inga | Inga |
> | childavailabilitystatuses | Inga | Inga |
> | childresources | Inga | Inga |
> | emergingissues | Inga | Inga |
> | händelser | Inga | Inga |
> | metadata | Inga | Inga |
> | meddelanden | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | Inga | Inga |
> | checkpolicycompliance | Inga | Inga |
> | checkresourcename | Inga | Inga |
> | distributioner | Inga | Inga |
> | distributioner/åtgärder | Inga | Inga |
> | deploymentscripts | Inga | Inga |
> | deploymentscripts/loggar | Inga | Inga |
> | Länkar | Inga | Inga |
> | platser | Inga | Inga |
> | platser/deploymentscriptoperationresults | Inga | Inga |
> | notifyresourcejobs | Inga | Inga |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | finansiär | Inga | Inga |
> | ResourceGroups | Inga | Inga |
> | resources | Inga | Inga |
> | prenumerationer | Inga | Inga |
> | prenumerationer/platser | Inga | Inga |
> | prenumerationer/operationresults | Inga | Inga |
> | prenumerationer/providers | Inga | Inga |
> | prenumerationer/ResourceGroups | Inga | Inga |
> | prenumerationer/ResourceGroups/resurser | Inga | Inga |
> | prenumerationer/resurser | Inga | Inga |
> | prenumerationer/tagNames | Inga | Inga |
> | prenumerationer/tagNames/tagvalues | Inga | Inga |
> | tags | Inga | Inga |
> | templatespecs | Inga | Inga |
> | templatespecs/versioner | Inga | Inga |
> | klienter | Inga | Inga |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Inga |
> | checkmoderneligibility | Inga | Inga |
> | checknameavailability | Inga | Inga |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | saasresources | Inga | Inga |

## <a name="microsoftsearch"></a>Microsoft. search

> [!IMPORTANT]
> Du kan inte flytta flera Sök resurser i olika regioner i samma åtgärd. Flytta i stället dem i separata åtgärder.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | checkservicenameavailability | Inga | Inga |
> | operations | Inga | Inga |
> | resourcehealthmetadata | Inga | Inga |
> | searchservices | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Inga | Inga |
> | advancedthreatprotectionsettings | Inga | Inga |
> | aviseringar | Inga | Inga |
> | allowedconnections | Inga | Inga |
> | applicationwhitelistings | Inga | Inga |
> | assessmentmetadata | Inga | Inga |
> | utvärderingar | Inga | Inga |
> | autodismissalertsrules | Inga | Inga |
> | automatiseringar | Ja | Ja |
> | autoprovisioningsettings | Inga | Inga |
> | complianceresults | Inga | Inga |
> | godkännanden | Inga | Inga |
> | datacollectionagents | Inga | Inga |
> | devicesecuritygroups | Inga | Inga |
> | discoveredsecuritysolutions | Inga | Inga |
> | externalsecuritysolutions | Inga | Inga |
> | informationprotectionpolicies | Inga | Inga |
> | iotsecuritysolutions | Ja | Ja |
> | iotsecuritysolutions / analyticsmodels | Inga | Inga |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Inga | Inga |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Inga | Inga |
> | jitnetworkaccesspolicies | Inga | Inga |
> | platser | Inga | Inga |
> | platser/aviseringar | Inga | Inga |
> | platser/allowedconnections | Inga | Inga |
> | platser/applicationwhitelistings | Inga | Inga |
> | platser/discoveredsecuritysolutions | Inga | Inga |
> | platser/externalsecuritysolutions | Inga | Inga |
> | platser/jitnetworkaccesspolicies | Inga | Inga |
> | platser/securitysolutions | Inga | Inga |
> | platser/securitysolutionsreferencedata | Inga | Inga |
> | platser/uppgifter | Inga | Inga |
> | platser/topologier | Inga | Inga |
> | operations | Inga | Inga |
> | policies | Inga | Inga |
> | prissättningar | Inga | Inga |
> | regulatorycompliancestandards | Inga | Inga |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Inga | Inga |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Inga | Inga |
> | securitycontacts | Inga | Inga |
> | securitysolutions | Inga | Inga |
> | securitysolutionsreferencedata | Inga | Inga |
> | securitystatuses | Inga | Inga |
> | securitystatusessummaries | Inga | Inga |
> | servervulnerabilityassessments | Inga | Inga |
> | inställningar | Inga | Inga |
> | underbedömningar | Inga | Inga |
> | uppgifter | Inga | Inga |
> | topologier | Inga | Inga |
> | workspacesettings | Inga | Inga |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | agg regeringar | Inga | Inga |
> | alertrules | Inga | Inga |
> | alertruletemplates | Inga | Inga |
> | automationrules | Inga | Inga |
> | bokmärken | Inga | Inga |
> | fall | Inga | Inga |
> | dataconnectors | Inga | Inga |
> | dataconnectorscheckrequirements | Inga | Inga |
> | poster | Inga | Inga |
> | entityqueries | Inga | Inga |
> | incidenter | Inga | Inga |
> | officeconsents | Inga | Inga |
> | operations | Inga | Inga |
> | inställningar | Inga | Inga |
> | threatintelligence | Inga | Inga |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | consoleservices | Inga | Inga |
> | platser | Inga | Inga |
> | platser/consoleservices | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | gatewayer | Inga | Inga |
> | artikelnoder | Inga | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | checknamespaceavailability | Inga | Inga |
> | platser | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Inga | Inga |
> | namnrymder/disasterrecoveryconfigs | Inga | Inga |
> | namnrymder/disasterrecoveryconfigs/checknameavailability | Inga | Inga |
> | namnrymder/eventgridfilters | Inga | Inga |
> | namnrymder/networkrulesets | Inga | Inga |
> | namnrymder/köer | Inga | Inga |
> | namnrymder/köer/authorizationrules | Inga | Inga |
> | namn områden/ämnen | Inga | Inga |
> | namnrymder/ämnen/authorizationrules | Inga | Inga |
> | namnrymder/ämnen/prenumerationer | Inga | Inga |
> | namn områden/ämnen/prenumerationer/regler | Inga | Inga |
> | operations | Inga | Inga |
> | premiummessagingregions | Inga | Inga |
> | sku | Inga | Inga |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Inga | Inga |
> | kluster | Ja | Ja |
> | kluster/program | Inga | Inga |
> | containergroups | Inga | Inga |
> | containergroupsets | Inga | Inga |
> | edgeclusters | Inga | Inga |
> | platser | Inga | Inga |
> | platser/clusterversions | Inga | Inga |
> | platser/miljöer | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | managedclusters | Inga | Inga |
> | nätet | Inga | Inga |
> | operations | Inga | Inga |
> | secretstores | Inga | Inga |
> | volumes | Inga | Inga |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Ja |
> | containergroups | Inga | Inga |
> | gatewayer | Ja | Ja |
> | platser | Inga | Inga |
> | platser/applicationoperations | Inga | Inga |
> | platser/gatewayoperations | Inga | Inga |
> | platser/networkoperations | Inga | Inga |
> | platser/secretoperations | Inga | Inga |
> | platser/volumeoperations | Inga | Inga |
> | nätet | Ja | Ja |
> | operations | Inga | Inga |
> | secrets | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | distributioner | Inga | Inga |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | operations | Inga | Inga |
> | SignalR | Ja | Ja |
> | SignalR/eventgridfilters | Inga | Inga |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Inga | Inga |
> | program | Inga | Inga |
> | jitrequests | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> En databas och en server måste finnas i samma resurs grupp. När du flyttar en SQL-Server flyttas även alla databaserna. Det här beteendet gäller för Azure SQL Database-och Azure Synapse Analytics-databaser.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | instancepools | Inga | Inga |
> | platser | Ja | Ja |
> | platser/administratorazureasyncoperation | Inga | Inga |
> | platser/administratoroperationresults | Inga | Inga |
> | platser/auditingsettingsazureasyncoperation | Inga | Inga |
> | platser/auditingsettingsoperationresults | Inga | Inga |
> | platser/funktioner | Inga | Inga |
> | platser/databaseazureasyncoperation | Inga | Inga |
> | platser/databaseoperationresults | Inga | Inga |
> | platser/databaserestoreazureasyncoperation | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | platser/deletevirtualnetworkorsubnetsazureasyncoperation | Inga | Inga |
> | platser/deletevirtualnetworkorsubnetsoperationresults | Inga | Inga |
> | platser/dnsaliasasyncoperation | Inga | Inga |
> | platser/dnsaliasoperationresults | Inga | Inga |
> | platser/elasticpoolazureasyncoperation | Inga | Inga |
> | platser/elasticpooloperationresults | Inga | Inga |
> | platser/encryptionprotectorazureasyncoperation | Inga | Inga |
> | platser/encryptionprotectoroperationresults | Inga | Inga |
> | platser/extendedauditingsettingsazureasyncoperation | Inga | Inga |
> | platser/extendedauditingsettingsoperationresults | Inga | Inga |
> | platser/failovergroupazureasyncoperation | Inga | Inga |
> | platser/failovergroupoperationresults | Inga | Inga |
> | platser/firewallrulesazureasyncoperation | Inga | Inga |
> | platser/firewallrulesoperationresults | Inga | Inga |
> | platser/instancefailovergroupazureasyncoperation | Inga | Inga |
> | platser/instancefailovergroupoperationresults | Inga | Inga |
> | platser/instancefailovergroups | Inga | Inga |
> | platser/instancepoolazureasyncoperation | Inga | Inga |
> | platser/instancepooloperationresults | Inga | Inga |
> | platser/jobagentazureasyncoperation | Inga | Inga |
> | platser/jobagentoperationresults | Inga | Inga |
> | platser/longtermretentionbackupazureasyncoperation | Inga | Inga |
> | platser/longtermretentionbackupoperationresults | Inga | Inga |
> | platser/longtermretentionbackups | Ja | Ja |
> | platser/longtermretentionmanagedinstancebackupazureasyncoperation | Inga | Inga |
> | platser/longtermretentionmanagedinstancebackupoperationresults | Inga | Inga |
> | platser/longtermretentionmanagedinstancebackups | Inga | Inga |
> | platser/longtermretentionmanagedinstances | Inga | Inga |
> | platser/longtermretentionpolicyazureasyncoperation | Inga | Inga |
> | platser/longtermretentionpolicyoperationresults | Inga | Inga |
> | platser/longtermretentionservers | Inga | Inga |
> | platser/manageddatabaseazureasyncoperation | Inga | Inga |
> | platser/manageddatabasecompleterestoreazureasyncoperation | Inga | Inga |
> | platser/manageddatabasecompleterestoreoperationresults | Inga | Inga |
> | platser/manageddatabaseoperationresults | Inga | Inga |
> | platser/manageddatabaserestoreazureasyncoperation | Inga | Inga |
> | platser/manageddatabaserestoreoperationresults | Inga | Inga |
> | platser/managedinstanceazureasyncoperation | Inga | Inga |
> | platser/managedinstanceencryptionprotectorazureasyncoperation | Inga | Inga |
> | platser/managedinstanceencryptionprotectoroperationresults | Inga | Inga |
> | platser/managedinstancekeyazureasyncoperation | Inga | Inga |
> | platser/managedinstancekeyoperationresults | Inga | Inga |
> | platser/managedinstancelongtermretentionpolicyazureasyncoperation | Inga | Inga |
> | platser/managedinstancelongtermretentionpolicyoperationresults | Inga | Inga |
> | platser/managedinstanceoperationresults | Inga | Inga |
> | platser/managedinstancetdecertazureasyncoperation | Inga | Inga |
> | platser/managedinstancetdecertoperationresults | Inga | Inga |
> | platser/managedserversecurityalertpoliciesazureasyncoperation | Inga | Inga |
> | platser/managedserversecurityalertpoliciesoperationresults | Inga | Inga |
> | platser/managedshorttermretentionpolicyazureasyncoperation | Inga | Inga |
> | platser/managedshorttermretentionpolicyoperationresults | Inga | Inga |
> | platser/notifyazureasyncoperation | Inga | Inga |
> | platser/privateendpointconnectionazureasyncoperation | Inga | Inga |
> | platser/privateendpointconnectionoperationresults | Inga | Inga |
> | platser/privateendpointconnectionproxyazureasyncoperation | Inga | Inga |
> | platser/privateendpointconnectionproxyoperationresults | Inga | Inga |
> | platser/securityalertpoliciesazureasyncoperation | Inga | Inga |
> | platser/securityalertpoliciesoperationresults | Inga | Inga |
> | platser/serveradministratorazureasyncoperation | Inga | Inga |
> | platser/serveradministratoroperationresults | Inga | Inga |
> | platser/serverazureasyncoperation | Inga | Inga |
> | platser/serverkeyazureasyncoperation | Inga | Inga |
> | platser/serverkeyoperationresults | Inga | Inga |
> | platser/serveroperationresults | Inga | Inga |
> | platser/shorttermretentionpolicyazureasyncoperation | Inga | Inga |
> | platser/shorttermretentionpolicyoperationresults | Inga | Inga |
> | platser/syncagentoperationresults | Inga | Inga |
> | platser/syncdatabaseids | Inga | Inga |
> | platser/syncgroupoperationresults | Inga | Inga |
> | platser/syncmemberoperationresults | Inga | Inga |
> | platser/tdecertazureasyncoperation | Inga | Inga |
> | platser/tdecertoperationresults | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | platser/virtualclusterazureasyncoperation | Inga | Inga |
> | platser/virtualclusteroperationresults | Inga | Inga |
> | platser/virtualnetworkrulesazureasyncoperation | Inga | Inga |
> | platser/virtualnetworkrulesoperationresults | Inga | Inga |
> | platser/vulnerabilityassessmentscanazureasyncoperation | Inga | Inga |
> | platser/vulnerabilityassessmentscanoperationresults | Inga | Inga |
> | managedinstances | Inga | Inga |
> | managedinstances/administratörer | Inga | Inga |
> | managedinstances/databaser | Inga | Inga |
> | managedinstances/databaser/backuplongtermretentionpolicies | Inga | Inga |
> | managedinstances/databaser/vulnerabilityassessments | Inga | Inga |
> | managedinstances / metricdefinitions | Inga | Inga |
> | managedinstances/mått | Inga | Inga |
> | managedinstances / recoverabledatabases | Inga | Inga |
> | managedinstances / tdecertificates | Inga | Inga |
> | managedinstances / vulnerabilityassessments | Inga | Inga |
> | operations | Inga | Inga |
> | brygghuvudservrar | Ja | Ja |
> | servrar/administratoroperationresults | Inga | Inga |
> | servrar/administratörer | Inga | Inga |
> | servrar/rådgivare | Inga | Inga |
> | servrar/aggregateddatabasemetrics | Inga | Inga |
> | servrar/auditingpolicies | Inga | Inga |
> | servrar/auditingsettings | Inga | Inga |
> | servrar/automatictuning | Inga | Inga |
> | servrar/communicationlinks | Inga | Inga |
> | servrar/connectionpolicies | Inga | Inga |
> | servrar/databaser | Ja | Ja |
> | servrar/databaser/rådgivare | Inga | Inga |
> | servrar/databaser/auditingpolicies | Inga | Inga |
> | servrar/databaser/auditingsettings | Inga | Inga |
> | servrar/databaser/AuditRecords | Inga | Inga |
> | servrar/databaser/automatictuning | Inga | Inga |
> | servrar/databaser/backuplongtermretentionpolicies | Ja | Ja |
> | servrar/databaser/backupshorttermretentionpolicies | Inga | Inga |
> | servrar/databaser/connectionpolicies | Inga | Inga |
> | servrar/databaser/datamaskingpolicies | Inga | Inga |
> | servrar/databaser/datamaskingpolicies/regler | Inga | Inga |
> | servrar/databaser/tillägg | Inga | Inga |
> | servrar/databaser/geobackuppolicies | Inga | Inga |
> | servrar/databaser/metricdefinitions | Inga | Inga |
> | servrar/databaser/mått | Inga | Inga |
> | servrar/databaser/recommendedsensitivitylabels | Inga | Inga |
> | servrar/databaser/securityalertpolicies | Inga | Inga |
> | servrar/databaser/syncgroups | Inga | Inga |
> | servrar/databaser/syncgroups/syncmembers | Inga | Inga |
> | servrar/databaser/topqueries | Inga | Inga |
> | servrar/databaser/topqueries/querytext | Inga | Inga |
> | servrar/databaser/transparentdataencryption | Inga | Inga |
> | servrar/databaser/vulnerabilityassessment | Inga | Inga |
> | servrar/databaser/vulnerabilityassessments | Inga | Inga |
> | servrar/databaser/vulnerabilityassessmentscans | Inga | Inga |
> | servrar/databaser/vulnerabilityassessmentsettings | Inga | Inga |
> | servrar/databaser/workloadgroups | Inga | Inga |
> | servrar/databasesecuritypolicies | Inga | Inga |
> | servrar/disasterrecoveryconfiguration | Inga | Inga |
> | servrar/dnsaliases | Inga | Inga |
> | servrar/elasticpoolestimates | Inga | Inga |
> | servrar/elasticpools | Ja | Ja |
> | servrar/elasticpools/Advisor | Inga | Inga |
> | servrar/elasticpools/metricdefinitions | Inga | Inga |
> | servrar/elasticpools/mått | Inga | Inga |
> | servrar/encryptionprotector | Inga | Inga |
> | servrar/extendedauditingsettings | Inga | Inga |
> | servrar/failovergroups | Inga | Inga |
> | servrar/import | Inga | Inga |
> | servrar/importexportoperationresults | Inga | Inga |
> | servrar/jobaccounts | Ja | Ja |
> | servrar/jobagents | Ja | Ja |
> | servrar/jobagents/jobb | Inga | Inga |
> | servrar/jobagents/jobb/körningar | Inga | Inga |
> | servrar/jobagents/jobb/steg | Inga | Inga |
> | servrar/nycklar | Inga | Inga |
> | servrar/operationresults | Inga | Inga |
> | servrar/recommendedelasticpools | Inga | Inga |
> | servrar/recoverabledatabases | Inga | Inga |
> | servrar/restorabledroppeddatabases | Inga | Inga |
> | servrar/securityalertpolicies | Inga | Inga |
> | servrar/serviceobjectives | Inga | Inga |
> | servrar/syncagents | Inga | Inga |
> | servrar/tdecertificates | Inga | Inga |
> | servrar/användningar | Inga | Inga |
> | servrar/virtualnetworkrules | Inga | Inga |
> | servrar/vulnerabilityassessments | Inga | Inga |
> | virtualclusters | Ja | Ja |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/availabilitygrouplisteneroperationresults | Inga | Inga |
> | platser/operationtypes | Inga | Inga |
> | platser/sqlvirtualmachinegroupoperationresults | Inga | Inga |
> | platser/sqlvirtualmachineoperationresults | Inga | Inga |
> | operations | Inga | Inga |
> | sqlvirtualmachinegroups | Ja | Ja |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | Inga | Inga |
> | sqlvirtualmachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | platser | Inga | Inga |
> | platser/asyncoperations | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | operations | Inga | Inga |
> | storageaccounts | Ja | Ja |
> | storageaccounts/blobservices | Inga | Inga |
> | storageaccounts/FileServices | Inga | Inga |
> | storageaccounts/listaccountsas | Inga | Inga |
> | storageaccounts/listservicesas | Inga | Inga |
> | storageaccounts/queueservices | Inga | Inga |
> | storageaccounts/tjänster | Inga | Inga |
> | storageaccounts/tjänster/metricdefinitions | Inga | Inga |
> | storageaccounts/tableservices | Inga | Inga |
> | användningar | Inga | Inga |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cacheminnen | Inga | Inga |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/checknameavailability | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | platser/arbets flöden | Inga | Inga |
> | operations | Inga | Inga |
> | storagesyncservices | Ja | Ja |
> | storagesyncservices / registeredservers | Inga | Inga |
> | storagesyncservices / syncgroups | Inga | Inga |
> | storagesyncservices / syncgroups / cloudendpoints | Inga | Inga |
> | storagesyncservices / syncgroups / serverendpoints | Inga | Inga |
> | storagesyncservices/arbets flöden | Inga | Inga |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Inga | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Inga | Inga |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hantera | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!IMPORTANT]
> Det går inte att flytta Stream Analytics jobb i körnings läge.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Inga | Inga |
> | platser | Inga | Inga |
> | platser/kvoter | Inga | Inga |
> | operations | Inga | Inga |
> | streamingjobs | Ja | Ja |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utrymmen | Inga | Inga |
> | miljöer/eventsources | Inga | Inga |
> | pipe | Inga | Inga |
> | instanser/miljöer | Inga | Inga |
> | instanser/miljöer/eventsources | Inga | Inga |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | avbryt | Inga | Inga |
> | createsubscription | Inga | Inga |
> | Aktivera | Inga | Inga |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | byt namn | Inga | Inga |
> | subscriptiondefinitions | Inga | Inga |
> | subscriptionoperations | Inga | Inga |
> | prenumerationer | Inga | Inga |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | operationresults | Inga | Inga |
> | operations | Inga | Inga |
> | operationsstatus | Inga | Inga |
> | services | Inga | Inga |
> | tjänster/problemclassifications | Inga | Inga |
> | supporttickets | Inga | Inga |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Inga | Inga |
> | operations | Inga | Inga |
> | arbetsytor | Ja | Ja |
> | arbets ytor/bigdatapools | Ja | Ja |
> | arbets ytor/operationresults | Inga | Inga |
> | arbets ytor/operationstatuses | Inga | Inga |
> | arbets ytor/sqlpools | Ja | Ja |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utrymmen | Ja | Ja |
> | miljöer/accesspolicies | Inga | Inga |
> | miljöer/eventsources | Ja | Ja |
> | miljöer/referencedatasets | Ja | Ja |
> | operations | Inga | Inga |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Auktoriseringshanteraren | Ja | Ja |
> | butiker/accesspolicies | Inga | Inga |
> | butiker/tjänster | Inga | Inga |
> | butiker/tjänster/token | Inga | Inga |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Inga | Inga |
> | imagetemplates / runoutputs | Inga | Inga |
> | platser | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!IMPORTANT]
> Information om hur du ändrar prenumerationen för Azure-DevOps finns i [ändra den Azure-prenumeration som används för fakturering](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konto | Inga | Inga |
> | konto/tillägg | Inga | Inga |
> | konto/projekt | Inga | Inga |
> | checknameavailability | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arczones | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | resourcepools | Inga | Inga |
> | vCenter | Inga | Inga |
> | virtualmachines | Inga | Inga |
> | virtualmachinetemplates | Inga | Inga |
> | virtualnetworks | Inga | Inga |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Inga | Inga |
> | dedicatedcloudservices | Inga | Inga |
> | platser | Inga | Inga |
> | platser/tillgänglighet | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/privateclouds | Inga | Inga |
> | platser/privateclouds/resourcepools | Inga | Inga |
> | platser/privateclouds/virtualmachinetemplates | Inga | Inga |
> | platser/privateclouds/virtualnetworks | Inga | Inga |
> | platser/användningar | Inga | Inga |
> | operations | Inga | Inga |
> | virtualmachines | Inga | Inga |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | devices | Inga | Inga |
> | platser | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | vnfs | Inga | Inga |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |
> | operations | Inga | Inga |
> | utgå | Inga | Inga |
> | registeredsubscriptions | Inga | Inga |

## <a name="microsoftweb"></a>Microsoft. Web

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availablestacks | Inga | Inga |
> | billingmeters | Inga | Inga |
> | certifikat | Inga | Ja |
> | checknameavailability | Inga | Inga |
> | connectiongateways | Ja | Ja |
> | anslutning | Ja | Ja |
> | customapis | Ja | Ja |
> | deletedsites | Inga | Inga |
> | deploymentlocations | Inga | Inga |
> | regioner | Inga | Inga |
> | hostingenvironments | Inga | Inga |
> | hostingenvironments / eventgridfilters | Inga | Inga |
> | hostingenvironments / multirolepools | Inga | Inga |
> | hostingenvironments / workerpools | Inga | Inga |
> | ishostingenvironmentnameavailable | Inga | Inga |
> | ishostnameavailable | Inga | Inga |
> | isusernameavailable | Inga | Inga |
> | kubeenvironments | Ja | Ja |
> | listsitesassignedtohostname | Inga | Inga |
> | platser | Inga | Inga |
> | platser/apioperations | Inga | Inga |
> | platser/connectiongatewayinstallations | Inga | Inga |
> | platser/deletedsites | Inga | Inga |
> | platser/deletevirtualnetworkorsubnets | Inga | Inga |
> | platser/extractapidefinitionfromwsdl | Inga | Inga |
> | platser/getnetworkpolicies | Inga | Inga |
> | platser/listwsdlinterfaces | Inga | Inga |
> | platser/managedapis | Inga | Inga |
> | platser/operationresults | Inga | Inga |
> | platser/åtgärder | Inga | Inga |
> | platser/körningar | Inga | Inga |
> | operations | Inga | Inga |
> | publishingusers | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | resourcehealthmetadata | Inga | Inga |
> | körningar | Inga | Inga |
> | Server grupper | Ja | Ja |
> | Server grupper/eventgridfilters | Inga | Inga |
> | webbplatser | Ja | Ja |
> | platser/eventgridfilters | Inga | Inga |
> | platser/hostnamebindings | Inga | Inga |
> | platser/networkconfig | Inga | Inga |
> | platser/premieraddons | Ja | Ja |
> | platser/platser | Ja | Ja |
> | platser/platser/eventgridfilters | Inga | Inga |
> | platser/platser/hostnamebindings | Inga | Inga |
> | platser/platser/networkconfig | Inga | Inga |
> | sourcecontrols | Inga | Inga |
> | staticsites | Inga | Inga |
> | kontrollerar | Inga | Inga |
> | verifyhostingenvironmentvnet | Inga | Inga |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | multipleactivationkeys | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | deviceservices | Inga | Inga |
> | operations | Inga | Inga |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Inga | Inga |
> | platser/operationstatuses | Inga | Inga |
> | operations | Inga | Inga |
> | arbets belastningar | Inga | Inga |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | delarna | Inga | Inga |
> | componentssummary | Inga | Inga |
> | monitorinstances | Inga | Inga |
> | monitorinstancessummary | Inga | Inga |
> | Övervakare | Inga | Inga |
> | notificationsettings | Inga | Inga |
> | operations | Inga | Inga |

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.

## <a name="next-steps"></a>Nästa steg
För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](move-resource-group-and-subscription.md).

Hämta [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)för att hämta samma data som en fil med kommaavgränsade värden.
