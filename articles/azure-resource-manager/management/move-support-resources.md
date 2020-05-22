---
title: Flytta åtgärds stöd efter resurs typ
description: Visar en lista över de Azure-resurs typer som kan flyttas till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 4f2ed7f1cb24b9896b533fb5d18ac4e57db48e2c
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780331"
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
> - [Microsoft. BatchAI](#microsoftbatchai)
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
> - [Microsoft. Cognition](#microsoftcognition)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. förbrukning](#microsoftconsumption)
> - [Microsoft. container](#microsoftcontainer)
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
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
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
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
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
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
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
> - [Microsoft. SqlVM](#microsoftsqlvm)
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
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
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
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domainservices | Nej | Nej |
> | domainservices / oucontainer | Nej | Nej |
> | platser | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | privatelinkforazuread | Nej | Nej |
> | klienter | Nej | Nej |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | supportproviders | Nej | Nej |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Nej | Nej |
> | addsservices | Nej | Nej |
> | aktörer | Nej | Nej |
> | anonymousapiusers | Nej | Nej |
> | konfiguration | Nej | Nej |
> | loggar | Nej | Nej |
> | operations | Nej | Nej |
> | rapporter | Nej | Nej |
> | servicehealthmetrics | Nej | Nej |
> | services | Nej | Nej |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konfigurationer | Nej | Nej |
> | generaterecommendations | Nej | Nej |
> | metadata | Nej | Nej |
> | operations | Nej | Nej |
> | rekommendationer | Nej | Nej |
> | utelämningar | Nej | Nej |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | actionrules | Ja | Ja |
> | aviseringar | Nej | Nej |
> | alertslist | Nej | Nej |
> | alertsmetadata | Nej | Nej |
> | alertssummary | Nej | Nej |
> | alertssummarylist | Nej | Nej |
> | operations | Nej | Nej |
> | smartdetectoralertrules | Ja | Ja |
> | smartgroups | Nej | Nej |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |
> | brygghuvudservrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | Nej | Nej |
> | checknameavailability | Nej | Nej |
> | checkservicenameavailability | Nej | Nej |
> | operations | Nej | Nej |
> | reportfeedback | Nej | Nej |
> | tjänst | Ja | Ja |
> | validateservicename | Nej | Nej |

> [!IMPORTANT]
> Det går inte att flytta en API Management tjänst som är inställd på förbruknings-SKU: n.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | configurationstores | Ja | Ja |
> | configurationstores / eventgridfilters | Nej | Nej |
> | platser | Nej | Nej |
> | platser/operationsstatus | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/operationstatus | Nej | Nej |
> | operations | Nej | Nej |
> | upphängning | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | apiapps | Nej | Nej |
> | appidentities | Nej | Nej |
> | gatewayer | Nej | Nej |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft. attestering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Ja | Ja |
> | operations | Nej | Nej |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Access | Nej | Nej |
> | classicadministrators | Nej | Nej |
> | dataaliases | Nej | Nej |
> | denyassignments | Nej | Nej |
> | elevateaccess | Nej | Nej |
> | findorphanroleassignments | Nej | Nej |
> | hålls | Nej | Nej |
> | operations | Nej | Nej |
> | åtkomst | Nej | Nej |
> | policyassignments | Nej | Nej |
> | policydefinitions | Nej | Nej |
> | policysetdefinitions | Nej | Nej |
> | provideroperations | Nej | Nej |
> | RoleAssignments | Nej | Nej |
> | roleassignmentsusagemetrics | Nej | Nej |
> | roledefinitions | Nej | Nej |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ja | Ja |
> | automationaccounts/konfigurationer | Ja | Ja |
> | automationaccounts/jobb | Nej | Nej |
> | automationaccounts / privateendpointconnectionproxies | Nej | Nej |
> | automationaccounts / privateendpointconnections | Nej | Nej |
> | automationaccounts / privatelinkresources | Nej | Nej |
> | automationaccounts/Runbooks | Ja | Ja |
> | automationaccounts / softwareupdateconfigurations | Nej | Nej |
> | automationaccounts/Webhooks | Nej | Nej |
> | operations | Nej | Nej |

> [!IMPORTANT]
> Runbooks måste finnas i samma resurs grupp som Automation-kontot.

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/checkquotaavailability | Nej | Nej |
> | operations | Nej | Nej |
> | privateclouds | Ja | Ja |
> | privateclouds/kluster | Nej | Nej |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ja | Ja |
> | b2ctenants | Nej | Nej |
> | checknameavailability | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Nej | Nej |
> | hybriddatamanagers | Nej | Nej |
> | operations | Nej | Nej |
> | postgresinstances | Nej | Nej |
> | sqlinstances | Nej | Nej |
> | sqlmanagedinstances | Nej | Nej |
> | sqlserverinstances | Nej | Nej |
> | sqlserverregistrations | Ja | Ja |
> | sqlserverregistrations/SQLServer | Nej | Nej |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Nej | Nej |
> | operations | Nej | Nej |
> | registreringar | Ja | Ja |
> | registreringar/customersubscriptions | Nej | Nej |
> | registreringar/produkter | Nej | Nej |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |
> | platser | Nej | Nej |
> | platser/accountoperationresults | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/kvoter | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftbatchai"></a>Microsoft. BatchAI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Nej | Nej |
> | fileservers | Nej | Nej |
> | utskrifts | Nej | Nej |
> | operations | Nej | Nej |
> | arbetsytor | Nej | Nej |
> | arbets ytor/kluster | Nej | Nej |
> | arbets ytor/experiment | Nej | Nej |
> | arbets ytor/experiment/jobb | Nej | Nej |
> | arbets ytor/fileservers | Nej | Nej |

## <a name="microsoftbilling"></a>Microsoft. fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Nej | Nej |
> | billingaccounts/avtal | Nej | Nej |
> | billingaccounts / billingpermissions | Nej | Nej |
> | billingaccounts / billingprofiles | Nej | Nej |
> | billingaccounts / billingprofiles / availablebalance | Nej | Nej |
> | billingaccounts / billingprofiles / billingpermissions | Nej | Nej |
> | billingaccounts / billingprofiles / billingroleassignments | Nej | Nej |
> | billingaccounts / billingprofiles / billingroledefinitions | Nej | Nej |
> | billingaccounts / billingprofiles / billingsubscriptions | Nej | Nej |
> | billingaccounts / billingprofiles / createbillingroleassignment | Nej | Nej |
> | billingaccounts/billingprofiles/kunder | Nej | Nej |
> | billingaccounts/billingprofiles/instruktioner | Nej | Nej |
> | billingaccounts/billingprofiles/fakturor | Nej | Nej |
> | billingaccounts/billingprofiles/fakturor/pris dokument | Nej | Nej |
> | billingaccounts/billingprofiles/fakturor/transaktioner | Nej | Nej |
> | billingaccounts / billingprofiles / invoicesections | Nej | Nej |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | Nej | Nej |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | Nej | Nej |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | Nej | Nej |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | Nej | Nej |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | Nej | Nej |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | Nej | Nej |
> | billingaccounts/billingprofiles/invoicesections/Products | Nej | Nej |
> | billingaccounts/billingprofiles/invoicesections/Products/transfer | Nej | Nej |
> | billingaccounts/billingprofiles/invoicesections/Products/updateautorenew | Nej | Nej |
> | billingaccounts/billingprofiles/invoicesections/transaktioner | Nej | Nej |
> | billingaccounts/billingprofiles/invoicesections/transfers | Nej | Nej |
> | billingaccounts / billingprofiles / patchoperations | Nej | Nej |
> | billingaccounts / billingprofiles / paymentmethods | Nej | Nej |
> | billingaccounts/billingprofiles/policys | Nej | Nej |
> | billingaccounts/billingprofiles/pris dokument | Nej | Nej |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | Nej | Nej |
> | billingaccounts/billingprofiles/Products | Nej | Nej |
> | billingaccounts/billingprofiles/transaktioner | Nej | Nej |
> | billingaccounts / billingroleassignments | Nej | Nej |
> | billingaccounts / billingroledefinitions | Nej | Nej |
> | billingaccounts / billingsubscriptions | Nej | Nej |
> | billingaccounts/billingsubscriptions/fakturor | Nej | Nej |
> | billingaccounts / createbillingroleassignment | Nej | Nej |
> | billingaccounts / createinvoicesectionoperations | Nej | Nej |
> | billingaccounts/kunder | Nej | Nej |
> | billingaccounts/kunder/billingpermissions | Nej | Nej |
> | billingaccounts/kunder/billingsubscriptions | Nej | Nej |
> | billingaccounts/kunder/initiatetransfer | Nej | Nej |
> | billingaccounts/kunder/principer | Nej | Nej |
> | billingaccounts/kunder/produkter | Nej | Nej |
> | billingaccounts/kunder/transaktioner | Nej | Nej |
> | billingaccounts/kunder/överföringar | Nej | Nej |
> | billingaccounts/avdelningar | Nej | Nej |
> | billingaccounts / enrollmentaccounts | Nej | Nej |
> | billingaccounts/fakturor | Nej | Nej |
> | billingaccounts / invoicesections | Nej | Nej |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | Nej | Nej |
> | billingaccounts / invoicesections / billingsubscriptions | Nej | Nej |
> | billingaccounts/invoicesections/billingsubscriptions/transfer | Nej | Nej |
> | billingaccounts/invoicesections/höjning | Nej | Nej |
> | billingaccounts / invoicesections / initiatetransfer | Nej | Nej |
> | billingaccounts / invoicesections / patchoperations | Nej | Nej |
> | billingaccounts / invoicesections / productmoveoperations | Nej | Nej |
> | billingaccounts/invoicesections/Products | Nej | Nej |
> | billingaccounts/invoicesections/Products/transfer | Nej | Nej |
> | billingaccounts/invoicesections/Products/updateautorenew | Nej | Nej |
> | billingaccounts / invoicesections / producttransfersresults | Nej | Nej |
> | billingaccounts/invoicesections/transaktioner | Nej | Nej |
> | billingaccounts/invoicesections/överföringar | Nej | Nej |
> | billingaccounts / lineofcredit | Nej | Nej |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | Nej | Nej |
> | billingaccounts / operationresults | Nej | Nej |
> | billingaccounts / patchoperations | Nej | Nej |
> | billingaccounts / paymentmethods | Nej | Nej |
> | billingaccounts/produkter | Nej | Nej |
> | billingaccounts/transaktioner | Nej | Nej |
> | billingperiods | Nej | Nej |
> | billingpermissions | Nej | Nej |
> | billingproperty | Nej | Nej |
> | billingroleassignments | Nej | Nej |
> | billingroledefinitions | Nej | Nej |
> | createbillingroleassignment | Nej | Nej |
> | enheten | Nej | Nej |
> | enrollmentaccounts | Nej | Nej |
> | fakturor | Nej | Nej |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | operationstatus | Nej | Nej |
> | överlåtelse | Nej | Nej |
> | överföringar/accepttransfer | Nej | Nej |
> | överföringar/declinetransfer | Nej | Nej |
> | överföringar/operationstatus | Nej | Nej |
> | överföringar/validatetransfer | Nej | Nej |
> | validateaddress | Nej | Nej |

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | Nej | Nej |
> | mapapis | Nej | Nej |
> | operations | Nej | Nej |
> | updatecommunicationpreference | Nej | Nej |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | biztalk | Nej | Nej |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nej | Nej |
> | cordamembers | Nej | Nej |
> | platser | Nej | Nej |
> | platser/blockchainmemberoperationresults | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/listconsortiums | Nej | Nej |
> | platser/watcheroperationresults | Nej | Nej |
> | operations | Nej | Nej |
> | Övervakare | Nej | Nej |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Nej | Nej |
> | tokenservices | Nej | Nej |

## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nej | Nej |
> | blueprintassignments / assignmentoperations | Nej | Nej |
> | blueprintassignments/åtgärder | Nej | Nej |
> | modeller | Nej | Nej |
> | skisser/artefakter | Nej | Nej |
> | skisser/versioner | Nej | Nej |
> | skisser/versioner/artefakter | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | botservices | Ja | Ja |
> | botservices/kanaler | Nej | Nej |
> | botservices/anslutningar | Nej | Nej |
> | checknameavailability | Nej | Nej |
> | listauthserviceproviders | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | platser | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/operationsstatus | Nej | Nej |
> | operations | Nej | Nej |
> | Redis | Ja | Ja |
> | Redis/privateendpointconnections | Nej | Nej |
> | Redis/privatelinkresources | Nej | Nej |
> | redisenterprise | Nej | Nej |

> [!IMPORTANT]
> Om Azure cache för Redis-instansen har kon figurer ATS med ett virtuellt nätverk kan instansen inte flyttas till en annan prenumeration. Se [begränsningar för nätverks flytt](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Nej | Nej |
> | calculateexchange | Nej | Nej |
> | calculateprice | Nej | Nej |
> | calculatepurchaseprice | Nej | Nej |
> | kataloger | Nej | Nej |
> | checkoffers | Nej | Nej |
> | checkpurchasestatus | Nej | Nej |
> | checkscopes | Nej | Nej |
> | commercialreservationorders | Nej | Nej |
> | utväxla | Nej | Nej |
> | listbenefits | Nej | Nej |
> | operations | Nej | Nej |
> | placepurchaseorder | Nej | Nej |
> | reservationorders | Nej | Nej |
> | reservationorders / availablescopes | Nej | Nej |
> | reservationorders / calculaterefund | Nej | Nej |
> | reservationorders/slå samman | Nej | Nej |
> | reservationorders/reservationer | Nej | Nej |
> | reservationorders/reservationer/availablescopes | Nej | Nej |
> | reservationorders/reservationer/revisioner | Nej | Nej |
> | reservationorders/retur | Nej | Nej |
> | reservationorders/Split | Nej | Nej |
> | reservationorders/växling | Nej | Nej |
> | reservera | Nej | Nej |
> | resources | Nej | Nej |
> | validatereservationorder | Nej | Nej |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nej | Nej |
> | cdnwebapplicationfirewallpolicies | Ja | Ja |
> | checknameavailability | Nej | Nej |
> | checkresourceusage | Nej | Nej |
> | edgenodes | Nej | Nej |
> | operationresults | Nej | Nej |
> | operationresults / profileresults | Nej | Nej |
> | operationresults / profileresults / endpointresults | Nej | Nej |
> | operationresults / profileresults / endpointresults / customdomainresults | Nej | Nej |
> | operationresults / profileresults / endpointresults / origingroupresults | Nej | Nej |
> | operationresults / profileresults / endpointresults / originresults | Nej | Nej |
> | operations | Nej | Nej |
> | filer | Ja | Ja |
> | profiler/slut punkter | Ja | Ja |
> | profiler/slut punkter/customdomains | Nej | Nej |
> | profiler/slut punkter/origingroups | Nej | Nej |
> | profiler/slut punkter/ursprung | Nej | Nej |
> | validateprobe | Nej | Nej |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ja | Ja |
> | certificateorders/certifikat | Nej | Nej |
> | operations | Nej | Nej |
> | validatecertificateregistrationinformation | Nej | Nej |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Nej | Nej |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | funktioner eller behörigheter | Nej | Nej |
> | checkdomainnameavailability | Nej | Nej |
> | domän namn | Ja | Nej |
> | domän namn/funktioner | Nej | Nej |
> | domän namn/internalloadbalancers | Nej | Nej |
> | domän namn/servicecertificates | Nej | Nej |
> | domän namn/platser | Nej | Nej |
> | domän namn/platser/roller | Nej | Nej |
> | domän namn/platser/roller/metricdefinitions | Nej | Nej |
> | domän namn/platser/roller/mått | Nej | Nej |
> | movesubscriptionresources | Nej | Nej |
> | operatingsystemfamilies | Nej | Nej |
> | operatingsystems | Nej | Nej |
> | operations | Nej | Nej |
> | operationstatuses | Nej | Nej |
> | quotas | Nej | Nej |
> | resourcetypes | Nej | Nej |
> | validatesubscriptionmoveavailability | Nej | Nej |
> | virtualmachines | Ja | Nej |
> | virtualmachines / diagnosticsettings | Nej | Nej |
> | virtualmachines / metricdefinitions | Nej | Nej |
> | virtualmachines/mått | Nej | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Nej | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | funktioner eller behörigheter | Nej | Nej |
> | expressroutecrossconnections | Nej | Nej |
> | expressroutecrossconnections/peering | Nej | Nej |
> | gatewaysupporteddevices | Nej | Nej |
> | networksecuritygroups | Nej | Nej |
> | operations | Nej | Nej |
> | quotas | Nej | Nej |
> | reservedips | Nej | Nej |
> | virtualnetworks | Nej | Nej |
> | virtualnetworks/remotevirtualnetworkpeeringproxies | Nej | Nej |
> | virtualnetworks/virtualnetworkpeerings | Nej | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | funktioner eller behörigheter | Nej | Nej |
> | checkstorageaccountavailability | Nej | Nej |
> | disk | Nej | Nej |
> | images | Nej | Nej |
> | operations | Nej | Nej |
> | osimages | Nej | Nej |
> | osplatformimages | Nej | Nej |
> | publicimages | Nej | Nej |
> | quotas | Nej | Nej |
> | storageaccounts | Ja | Nej |
> | storageaccounts/blobservices | Nej | Nej |
> | storageaccounts/FileServices | Nej | Nej |
> | storageaccounts/metricdefinitions | Nej | Nej |
> | storageaccounts/mått | Nej | Nej |
> | storageaccounts/queueservices | Nej | Nej |
> | storageaccounts/tjänster | Nej | Nej |
> | storageaccounts/tjänster/diagnosticsettings | Nej | Nej |
> | storageaccounts/tjänster/metricdefinitions | Nej | Nej |
> | storageaccounts/tjänster/mått | Nej | Nej |
> | storageaccounts/tableservices | Nej | Nej |
> | storageaccounts/vmimages | Nej | Nej |
> | vmimages | Nej | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Nej | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftcognition"></a>Microsoft. Cognition

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | Nej | Nej |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | checkdomainavailability | Nej | Nej |
> | platser | Nej | Nej |
> | platser/checkskuavailability | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Nej | Nej |
> | ratecard | Nej | Nej |
> | usageaggregates | Nej | Nej |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ja | Ja |
> | diskaccesses | Nej | Nej |
> | diskencryptionsets | Nej | Nej |
> | disk | Ja | Ja |
> | gallerier | Nej | Nej |
> | gallerier/bilder | Nej | Nej |
> | gallerier/avbildningar/versioner | Nej | Nej |
> | hostgroups | Nej | Nej |
> | hostgroups/värdar | Nej | Nej |
> | images | Ja | Ja |
> | platser | Nej | Nej |
> | platser/artifactpublishers | Nej | Nej |
> | platser/capsoperations | Nej | Nej |
> | platser/diskoperations | Nej | Nej |
> | platser/loganalytics | Nej | Nej |
> | platser/åtgärder | Nej | Nej |
> | platser/utgivare | Nej | Nej |
> | platser/runcommands | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | platser/virtualmachines | Nej | Nej |
> | platser/tillåtna storlekar | Nej | Nej |
> | platser/vsmoperations | Nej | Nej |
> | operations | Nej | Nej |
> | proximityplacementgroups | Ja | Ja |
> | restorepointcollections | Nej | Nej |
> | restorepointcollections / restorepoints | Nej | Nej |
> | sharedvmextensions | Nej | Nej |
> | sharedvmimages | Nej | Nej |
> | sharedvmimages/versioner | Nej | Nej |
> | snapshots | Ja | Ja |
> | sshpublickeys | Nej | Nej |
> | virtualmachines | Ja | Ja |
> | virtualmachines/tillägg | Ja | Ja |
> | virtualmachines / metricdefinitions | Nej | Nej |
> | virtualmachines / runcommands | Nej | Nej |
> | virtualmachinescalesets | Ja | Ja |
> | virtualmachinescalesets/tillägg | Nej | Nej |
> | virtualmachinescalesets/NetworkInterfaces | Nej | Nej |
> | virtualmachinescalesets/publicipaddresses | Nej | Nej |
> | virtualmachinescalesets/virtualmachines | Nej | Nej |
> | virtualmachinescalesets/virtualmachines/NetworkInterfaces | Nej | Nej |

> [!IMPORTANT]
> Se [Virtual Machines flytta vägledning](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Nej | Nej |
> | balanserar | Nej | Nej |
> | budget | Nej | Nej |
> | utgifts | Nej | Nej |
> | costtags | Nej | Nej |
> | krediter | Nej | Nej |
> | händelser | Nej | Nej |
> | prognoser | Nej | Nej |
> | samtliga | Nej | Nej |
> | marknads platser | Nej | Nej |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | operationstatus | Nej | Nej |
> | pricesheets | Nej | Nej |
> | läkemedle | Nej | Nej |
> | reservationdetails | Nej | Nej |
> | reservationrecommendationdetails | Nej | Nej |
> | reservationrecommendations | Nej | Nej |
> | reservationsummaries | Nej | Nej |
> | reservationtransactions | Nej | Nej |
> | tags | Nej | Nej |
> | klienter | Nej | Nej |
> | begreppen | Nej | Nej |
> | usagedetails | Nej | Nej |

## <a name="microsoftcontainer"></a>Microsoft. container

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containergroups | Nej | Nej |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containergroups | Nej | Nej |
> | platser | Nej | Nej |
> | platser/cachedimages | Nej | Nej |
> | platser/funktioner | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | platser/åtgärder | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | operations | Nej | Nej |
> | serviceassociationlinks | Nej | Nej |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | platser | Nej | Nej |
> | platser/auktorisera | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/setupauth | Nej | Nej |
> | operations | Nej | Nej |
> | register | Ja | Ja |
> | register/agentpools | Ja | Ja |
> | register/agentpools/listqueuestatus | Nej | Nej |
> | register/versioner | Nej | Nej |
> | register/versioner/Avbryt | Nej | Nej |
> | register/build/getloglink | Nej | Nej |
> | register/buildtasks | Ja | Ja |
> | register/buildtasks/listsourcerepositoryproperties | Nej | Nej |
> | register/buildtasks/steg | Nej | Nej |
> | register/buildtasks/steg/listbuildarguments | Nej | Nej |
> | register/eventgridfilters | Nej | Nej |
> | register/exportpipelines | Nej | Nej |
> | register/generatecredentials | Nej | Nej |
> | register/getbuildsourceuploadurl | Nej | Nej |
> | register/getcredentials | Nej | Nej |
> | register/importimage | Nej | Nej |
> | register/importpipelines | Nej | Nej |
> | register/listbuildsourceuploadurl | Nej | Nej |
> | register/listcredentials | Nej | Nej |
> | register/ListPolicies | Nej | Nej |
> | register/listusages | Nej | Nej |
> | register/pipelineruns | Nej | Nej |
> | register/privateendpointconnectionproxies | Nej | Nej |
> | register/privateendpointconnectionproxies/validate | Nej | Nej |
> | register/privateendpointconnections | Nej | Nej |
> | register/privatelinkresources | Nej | Nej |
> | register/queuebuild | Nej | Nej |
> | register/regeneratecredential | Nej | Nej |
> | register/regeneratecredentials | Nej | Nej |
> | register/replikeringar | Ja | Ja |
> | register/körningar | Nej | Nej |
> | register/körningar/Avbryt | Nej | Nej |
> | register/körningar/listlogsasurl | Nej | Nej |
> | register/schedulerun | Nej | Nej |
> | register/scopemaps | Nej | Nej |
> | register/taskruns | Nej | Nej |
> | register/taskruns/listdetails | Nej | Nej |
> | register/uppgifter | Ja | Ja |
> | register/uppgifter/listdetails | Nej | Nej |
> | register/token | Nej | Nej |
> | register/updatepolicies | Nej | Nej |
> | register/Webhooks | Ja | Ja |
> | register/Webhooks/getcallbackconfig | Nej | Nej |
> | register/Webhooks/listevents | Nej | Nej |
> | register/Webhooks/ping | Nej | Nej |

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containerservices | Nej | Nej |
> | platser | Nej | Nej |
> | platser/openshiftclusters | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/åtgärder | Nej | Nej |
> | platser/Dirigerare | Nej | Nej |
> | managedclusters | Nej | Nej |
> | openshiftmanagedclusters | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Nej | Nej |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aviseringar | Nej | Nej |
> | billingaccounts | Nej | Nej |
> | budget | Nej | Nej |
> | cloudconnectors | Nej | Nej |
> | anslutningar | Ja | Ja |
> | enheten | Nej | Nej |
> | enheter | Nej | Nej |
> | enrollmentaccounts | Nej | Nej |
> | exporteras | Nej | Nej |
> | externalbillingaccounts | Nej | Nej |
> | externalbillingaccounts/aviseringar | Nej | Nej |
> | externalbillingaccounts/dimensioner | Nej | Nej |
> | externalbillingaccounts/prognos | Nej | Nej |
> | externalbillingaccounts/fråga | Nej | Nej |
> | externalsubscriptions | Nej | Nej |
> | externalsubscriptions/aviseringar | Nej | Nej |
> | externalsubscriptions/dimensioner | Nej | Nej |
> | externalsubscriptions/prognos | Nej | Nej |
> | externalsubscriptions/fråga | Nej | Nej |
> | forecast | Nej | Nej |
> | operations | Nej | Nej |
> | DocumentDB | Nej | Nej |
> | registrera | Nej | Nej |
> | reportconfigs | Nej | Nej |
> | rapporter | Nej | Nej |
> | settings | Nej | Nej |
> | showbackrules | Nej | Nej |
> | vyer | Nej | Nej |

## <a name="microsoftcostmanagementexports"></a>Microsoft. CostManagementExports

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Nej | Nej |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | nav | Nej | Nej |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Nej | Nej |
> | autentiseringsbegäran | Nej | Nej |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | typer | Nej | Nej |
> | platser | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |
> | resourceproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utskrifts | Nej | Nej |
> | platser | Nej | Nej |
> | platser/availableskus | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/regionconfiguration | Nej | Nej |
> | platser/validateaddress | Nej | Nej |
> | platser/validateinputs | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Nej | Nej |
> | databoxedgedevices / checknameavailability | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/getnetworkpolicies | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |
> | arbetsytor | Nej | Nej |
> | arbets ytor/dbworkspaces | Nej | Nej |
> | arbets ytor/virtualnetworkpeerings | Nej | Nej |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kataloger | Ja | Ja |
> | checknameavailability | Nej | Nej |
> | datacatalogs | Nej | Nej |
> | platser | Nej | Nej |
> | platser/jobb | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nej | Nej |

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | distributionspaket | Nej | Nej |
> | utgå | Nej | Nej |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | Nej | Nej |
> | checkdatafactorynameavailability | Nej | Nej |
> | datafactories | Ja | Ja |
> | datafactories / diagnosticsettings | Nej | Nej |
> | datafactories / metricdefinitions | Nej | Nej |
> | datafactoryschema | Nej | Nej |
> | fabriker | Ja | Ja |
> | fabriker/integrationruntimes | Nej | Nej |
> | platser | Nej | Nej |
> | platser/configurefactoryrepo | Nej | Nej |
> | platser/getfeaturevalue | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nej | Nej |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | konton/datalakestoreaccounts | Nej | Nej |
> | konton/storageaccounts | Nej | Nej |
> | konton/storageaccounts/behållare | Nej | Nej |
> | konton/storageaccounts/containers/listsastokens | Nej | Nej |
> | platser | Nej | Nej |
> | platser/kapacitet | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | konton/eventgridfilters | Nej | Nej |
> | konton/firewallrules | Nej | Nej |
> | platser | Nej | Nej |
> | platser/kapacitet | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |
> | services | Nej | Nej |
> | tjänster/projekt | Nej | Nej |
> | lots | Nej | Nej |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nej | Nej |
> | platser | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | konton/resurser | Nej | Nej |
> | konton/resurser/data uppsättningar | Nej | Nej |
> | konton/resurser/inbjudningar | Nej | Nej |
> | konton/resurser/providersharesubscriptions | Nej | Nej |
> | konton/resurser/synchronizationsettings | Nej | Nej |
> | konton/sharesubscriptions | Nej | Nej |
> | konton/sharesubscriptions/consumersourcedatasets | Nej | Nej |
> | konton/sharesubscriptions/datasetmappings | Nej | Nej |
> | konton/sharesubscriptions/utlösare | Nej | Nej |
> | listinvitations | Nej | Nej |
> | platser | Nej | Nej |
> | platser/consumerinvitations | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/rejectinvitation | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | platser | Nej | Nej |
> | platser/azureasyncoperation | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/performancetiers | Nej | Nej |
> | platser/privateendpointconnectionazureasyncoperation | Nej | Nej |
> | platser/privateendpointconnectionoperationresults | Nej | Nej |
> | platser/privateendpointconnectionproxyazureasyncoperation | Nej | Nej |
> | platser/privateendpointconnectionproxyoperationresults | Nej | Nej |
> | platser/recommendedactionsessionsazureasyncoperation | Nej | Nej |
> | platser/recommendedactionsessionsoperationresults | Nej | Nej |
> | platser/securityalertpoliciesazureasyncoperation | Nej | Nej |
> | platser/securityalertpoliciesoperationresults | Nej | Nej |
> | platser/serverkeyazureasyncoperation | Nej | Nej |
> | platser/serverkeyoperationresults | Nej | Nej |
> | operations | Nej | Nej |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Nej | Nej |
> | servrar/privateendpointconnectionproxies | Nej | Nej |
> | servrar/privateendpointconnections | Nej | Nej |
> | servrar/privatelinkresources | Nej | Nej |
> | servrar/querytexts | Nej | Nej |
> | servrar/recoverableservers | Nej | Nej |
> | servrar/topquerystatistics | Nej | Nej |
> | servrar/virtualnetworkrules | Nej | Nej |
> | servrar/waitstatistics | Nej | Nej |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | platser | Nej | Nej |
> | platser/administratorazureasyncoperation | Nej | Nej |
> | platser/administratoroperationresults | Nej | Nej |
> | platser/azureasyncoperation | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/performancetiers | Nej | Nej |
> | platser/privateendpointconnectionazureasyncoperation | Nej | Nej |
> | platser/privateendpointconnectionoperationresults | Nej | Nej |
> | platser/privateendpointconnectionproxyazureasyncoperation | Nej | Nej |
> | platser/privateendpointconnectionproxyoperationresults | Nej | Nej |
> | platser/recommendedactionsessionsazureasyncoperation | Nej | Nej |
> | platser/recommendedactionsessionsoperationresults | Nej | Nej |
> | platser/securityalertpoliciesazureasyncoperation | Nej | Nej |
> | platser/securityalertpoliciesoperationresults | Nej | Nej |
> | platser/serverkeyazureasyncoperation | Nej | Nej |
> | platser/serverkeyoperationresults | Nej | Nej |
> | operations | Nej | Nej |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Nej | Nej |
> | servrar/nycklar | Nej | Nej |
> | servrar/privateendpointconnectionproxies | Nej | Nej |
> | servrar/privateendpointconnections | Nej | Nej |
> | servrar/privatelinkresources | Nej | Nej |
> | servrar/querytexts | Nej | Nej |
> | servrar/recoverableservers | Nej | Nej |
> | servrar/topquerystatistics | Nej | Nej |
> | servrar/virtualnetworkrules | Nej | Nej |
> | servrar/waitstatistics | Nej | Nej |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | platser | Nej | Nej |
> | platser/administratorazureasyncoperation | Nej | Nej |
> | platser/administratoroperationresults | Nej | Nej |
> | platser/azureasyncoperation | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/performancetiers | Nej | Nej |
> | platser/privateendpointconnectionazureasyncoperation | Nej | Nej |
> | platser/privateendpointconnectionoperationresults | Nej | Nej |
> | platser/privateendpointconnectionproxyazureasyncoperation | Nej | Nej |
> | platser/privateendpointconnectionproxyoperationresults | Nej | Nej |
> | platser/recommendedactionsessionsazureasyncoperation | Nej | Nej |
> | platser/recommendedactionsessionsoperationresults | Nej | Nej |
> | platser/securityalertpoliciesazureasyncoperation | Nej | Nej |
> | platser/securityalertpoliciesoperationresults | Nej | Nej |
> | platser/serverkeyazureasyncoperation | Nej | Nej |
> | platser/serverkeyoperationresults | Nej | Nej |
> | operations | Nej | Nej |
> | servergroups | Nej | Nej |
> | brygghuvudservrar | Ja | Ja |
> | servrar/rådgivare | Nej | Nej |
> | servrar/nycklar | Nej | Nej |
> | servrar/privateendpointconnectionproxies | Nej | Nej |
> | servrar/privateendpointconnections | Nej | Nej |
> | servrar/privatelinkresources | Nej | Nej |
> | servrar/querytexts | Nej | Nej |
> | servrar/recoverableservers | Nej | Nej |
> | servrar/topquerystatistics | Nej | Nej |
> | servrar/virtualnetworkrules | Nej | Nej |
> | servrar/waitstatistics | Nej | Nej |
> | serversv2 | Ja | Ja |
> | singleservers | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ja | Ja |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |
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
> | applicationgroups/program | Nej | Nej |
> | applicationgroups/Station ära datorer | Nej | Nej |
> | applicationgroups / startmenuitems | Nej | Nej |
> | hostpools | Ja | Ja |
> | hostpools / sessionhosts | Nej | Nej |
> | hostpools / sessionhosts / usersessions | Nej | Nej |
> | hostpools / usersessions | Nej | Nej |
> | operations | Nej | Nej |
> | arbetsytor | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | checkprovisioningservicenameavailability | Nej | Nej |
> | elasticpools | Nej | Nej |
> | elasticpools / iothubtenants | Nej | Nej |
> | iothubs | Ja | Ja |
> | iothubs / eventgridfilters | Nej | Nej |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | provisioningservices | Ja | Ja |
> | användningar | Nej | Nej |

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
> | kontrollanter/listconnectiondetails | Nej | Nej |
> | platser | Nej | Nej |
> | platser/checkcontainerhostmapping | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labcenters | Nej | Nej |
> | Laboration | Ja | Nej |
> | labb/miljöer | Ja | Ja |
> | labb/servicerunners | Ja | Ja |
> | labb/virtualmachines | Ja | Nej |
> | platser | Nej | Nej |
> | platser/åtgärder | Nej | Nej |
> | operations | Nej | Nej |
> | scheman | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nej | Nej |
> | platser | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Nej | Nej |
> | databaseaccounts | Ja | Ja |
> | platser | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/operationsstatus | Nej | Nej |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | Nej | Nej |
> | domäner | Ja | Ja |
> | domäner/domainownershipidentifiers | Nej | Nej |
> | generatessorequest | Nej | Nej |
> | listdomainrecommendations | Nej | Nej |
> | operations | Nej | Nej |
> | topleveldomains | Nej | Nej |
> | validatedomainregistrationinformation | Nej | Nej |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domäner | Ja | Ja |
> | eventSubscriptions | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. |
> | domäner/ämnen | Nej | Nej |
> | eventsubscriptions | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. |
> | extensiontopics | Nej | Nej |
> | platser | Nej | Nej |
> | platser/eventsubscriptions | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/operationsstatus | Nej | Nej |
> | platser/topictypes | Nej | Nej |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | operationsstatus | Nej | Nej |
> | partnernamespaces | Ja | Ja |
> | partnernamespaces/eventchannels | Nej | Nej |
> | partnerregistrations | Nej | Nej |
> | partnertopics | Ja | Ja |
> | partnertopics / eventsubscriptions | Nej | Nej |
> | systemtopics | Ja | Ja |
> | systemtopics / eventsubscriptions | Nej | Nej |
> | avsnitt | Ja | Ja |
> | topictypes | Nej | Nej |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Nej | Nej |
> | checknameavailability | Nej | Nej |
> | checknamespaceavailability | Nej | Nej |
> | kluster | Ja | Ja |
> | platser | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Nej | Nej |
> | namnrymder/disasterrecoveryconfigs | Nej | Nej |
> | namnrymder/disasterrecoveryconfigs/checknameavailability | Nej | Nej |
> | namnrymder/eventhubs | Nej | Nej |
> | namnrymder/eventhubs/authorizationrules | Nej | Nej |
> | namnrymder/eventhubs/consumergroups | Nej | Nej |
> | namnrymder/networkrulesets | Nej | Nej |
> | operations | Nej | Nej |
> | sku | Nej | Nej |

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Nej | Nej |
> | platser | Nej | Nej |
> | platser/åtgärder | Nej | Nej |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | featureproviders | Nej | Nej |
> | funktioner | Nej | Nej |
> | operations | Nej | Nej |
> | finansiär | Nej | Nej |
> | subscriptionfeatureregistrations | Nej | Nej |

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Nej | Nej |
> | automanagedvmconfigurationprofiles | Nej | Nej |
> | guestconfigurationassignments | Nej | Nej |
> | operations | Nej | Nej |
> | IntelliPoint | Nej | Nej |
> | softwareupdateprofile | Nej | Nej |
> | softwareupdates | Nej | Nej |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nej | Nej |
> | platser | Nej | Nej |
> | platser/åtgärder | Nej | Nej |
> | platser/operationsstatus | Nej | Nej |
> | operations | Nej | Nej |
> | sapmonitors | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Nej | Nej |
> | platser | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |
> | kluster/program | Nej | Nej |
> | kluster/operationresults | Nej | Nej |
> | platser | Nej | Nej |
> | platser/azureasyncoperations | Nej | Nej |
> | platser/billingspecs | Nej | Nej |
> | platser/funktioner | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | platser/validatecreaterequest | Nej | Nej |
> | operations | Nej | Nej |

> [!IMPORTANT]
> Du kan flytta HDInsight-kluster till en ny prenumeration eller resurs grupp. Du kan dock inte flytta över prenumerationer som nätverks resurserna är länkade till HDInsight-klustret (till exempel det virtuella nätverket, NIC eller belastningsutjämnaren). Dessutom kan du inte flytta till en ny resurs grupp ett nätverkskort som är kopplat till en virtuell dator för klustret.
>
> När du flyttar ett HDInsight-kluster till en ny prenumeration måste du först flytta andra resurser (t. ex. lagrings kontot). Flytta sedan HDInsight-klustret själv.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | platser | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | services | Ja | Ja |
> | tjänster/privateendpointconnections | Nej | Nej |
> | tjänster/privatelinkresources | Nej | Nej |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | faxar | Ja | Ja |
> | datorer/tillägg | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |
> | operations | Nej | Nej |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | delarna | Nej | Nej |
> | platser | Nej | Nej |
> | networkscopes | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utskrifts | Ja | Ja |
> | platser | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ja | Ja |
> | activitylogalerts | Nej | Nej |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | planen | Nej | Nej |
> | calculatebaseline | Nej | Nej |
> | delarna | Ja | Ja |
> | komponenter/händelser | Nej | Nej |
> | komponenter/linkedstorageaccounts | Nej | Nej |
> | komponenter/mått | Nej | Nej |
> | komponenter/pricingplans | Nej | Nej |
> | komponenter/fråga | Nej | Nej |
> | datacollectionrules | Nej | Nej |
> | diagnosticsettings | Nej | Nej |
> | diagnosticsettingscategories | Nej | Nej |
> | eventcategories | Nej | Nej |
> | eventtypes | Nej | Nej |
> | extendeddiagnosticsettings | Nej | Nej |
> | guestdiagnosticsettings | Nej | Nej |
> | listmigrationdate | Nej | Nej |
> | platser | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | logdefinitions | Nej | Nej |
> | logprofiles | Nej | Nej |
> | loggar | Nej | Nej |
> | metricalerts | Nej | Nej |
> | metricbaselines | Nej | Nej |
> | metricbatch | Nej | Nej |
> | metricdefinitions | Nej | Nej |
> | metricnamespaces | Nej | Nej |
> | metrics | Nej | Nej |
> | migratealertrules | Nej | Nej |
> | migratetonewpricingmodel | Nej | Nej |
> | Mina arbets böcker | Nej | Nej |
> | notificationgroups | Nej | Nej |
> | operations | Nej | Nej |
> | privatelinkscopeoperationstatuses | Nej | Nej |
> | privatelinkscopes | Nej | Nej |
> | privatelinkscopes / privateendpointconnectionproxies | Nej | Nej |
> | privatelinkscopes / privateendpointconnections | Nej | Nej |
> | privatelinkscopes / scopedresources | Nej | Nej |
> | rollbacktolegacypricingmodel | Nej | Nej |
> | scheduledqueryrules | Ja | Ja |
> | topologi | Nej | Nej |
> | transaktioner | Nej | Nej |
> | vminsightsonboardingstatuses | Nej | Nej |
> | webbtester | Ja | Ja |
> | arbetsböcker | Ja | Ja |
> | workbooktemplates | Ja | Ja |

> [!IMPORTANT]
> Se till att flytta till den nya prenumerationen inte överskrider [prenumerations kvoterna](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | apptemplates | Nej | Nej |
> | checknameavailability | Nej | Nej |
> | checksubdomainavailability | Nej | Nej |
> | iotapps | Ja | Ja |
> | operations | Nej | Nej |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ja | Ja |
> | Rita | Ja | Ja |
> | operations | Nej | Nej |

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | deletedvaults | Nej | Nej |
> | hsmpools | Nej | Nej |
> | platser | Nej | Nej |
> | platser/deletedvaults | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | valv | Ja | Ja |
> | valv/accesspolicies | Nej | Nej |
> | valv/eventgridfilters | Nej | Nej |
> | valv/hemligheter | Nej | Nej |

> [!IMPORTANT]
> Nyckel valv som används för disk kryptering kan inte flyttas till en resurs grupp i samma prenumeration eller mellan prenumerationer.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ja | Ja |
> | platser | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |
> | registeredsubscriptions | Nej | Nej |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Nej | Nej |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |
> | kluster/attacheddatabaseconfigurations | Nej | Nej |
> | kluster/databaser | Nej | Nej |
> | kluster/databaser/dataconnections | Nej | Nej |
> | kluster/databaser/eventhubconnections | Nej | Nej |
> | kluster/databaser/principalassignments | Nej | Nej |
> | kluster/principalassignments | Nej | Nej |
> | platser | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nej | Nej |
> | platser | Nej | Nej |
> | platser/åtgärder | Nej | Nej |
> | operations | Nej | Nej |
> | användare | Nej | Nej |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftlocationservices"></a>Microsoft. filen LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nej | Nej |
> | integrationaccounts | Ja | Ja |
> | integrationserviceenvironments | Ja | Nej |
> | integrationserviceenvironments/managedapis | Ja | Nej |
> | isolatedenvironments | Nej | Nej |
> | platser | Nej | Nej |
> | platser/arbets flöden | Nej | Nej |
> | operations | Nej | Nej |
> | arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Nej | Nej |
> | platser | Nej | Nej |
> | platser/åtgärder | Nej | Nej |
> | platser/operationsstatus | Nej | Nej |
> | operations | Nej | Nej |
> | WebServices | Ja | Nej |
> | arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nej | Nej |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |
> | konton/arbets ytor | Nej | Nej |
> | konton/arbets ytor/projekt | Nej | Nej |
> | teamaccounts | Nej | Nej |
> | teamaccounts/arbets ytor | Nej | Nej |
> | teamaccounts/arbets ytor/projekt | Nej | Nej |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Nej | Nej |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/computeoperationsstatus | Nej | Nej |
> | platser/kvoter | Nej | Nej |
> | platser/updatequotas | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | platser/tillåtna storlekar | Nej | Nej |
> | platser/workspaceoperationsstatus | Nej | Nej |
> | operations | Nej | Nej |
> | arbetsytor | Nej | Nej |
> | arbets ytor/beräkningar | Nej | Nej |
> | arbets ytor/eventgridfilters | Nej | Nej |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applyupdates | Nej | Nej |
> | configurationassignments | Nej | Nej |
> | maintenanceconfigurations | Ja | Ja |
> | uppdateringar | Nej | Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | identiteter | Nej | Nej |
> | operations | Nej | Nej |
> | userassignedidentities | Nej | Nej |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | managednetworks | Nej | Nej |
> | managednetworks / managednetworkgroups | Nej | Nej |
> | managednetworks / managednetworkpeeringpolicies | Nej | Nej |
> | avisering | Nej | Nej |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Nej | Nej |
> | operations | Nej | Nej |
> | operationstatuses | Nej | Nej |
> | registrationassignments | Nej | Nej |
> | registrationdefinitions | Nej | Nej |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | getentities | Nej | Nej |
> | managementgroups | Nej | Nej |
> | managementgroups/inställningar | Nej | Nej |
> | operationresults | Nej | Nej |
> | operationresults / asyncoperation | Nej | Nej |
> | operations | Nej | Nej |
> | resources | Nej | Nej |
> | starttenantbackfill | Nej | Nej |
> | tenantbackfillstatus | Nej | Nej |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | konton/eventgridfilters | Nej | Nej |
> | konton/privateatlases | Ja | Ja |
> | operations | Nej | Nej |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Nej | Nej |
> | budgivning | Nej | Nej |
> | offertypes | Nej | Nej |
> | offertypes/utgivare | Nej | Nej |
> | offertypes/utgivare/erbjudanden | Nej | Nej |
> | offertypes/utgivare/erbjudanden/planer | Nej | Nej |
> | offertypes/utgivare/erbjudanden/planer/avtal | Nej | Nej |
> | offertypes/utgivare/erbjudanden/planer/konfigurationer | Nej | Nej |
> | offertypes/utgivare/erbjudanden/planer/configs/importimage | Nej | Nej |
> | operations | Nej | Nej |
> | privategalleryitems | Nej | Nej |
> | privatestoreclient | Nej | Nej |
> | privatestores | Nej | Nej |
> | privatestores/erbjudanden | Nej | Nej |
> | läkemedle | Nej | Nej |
> | Utgivare | Nej | Nej |
> | Utgivare/erbjudanden | Nej | Nej |
> | Utgivare/erbjudanden/ändringar | Nej | Nej |
> | registrera | Nej | Nej |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nej | Nej |
> | listcommunicationpreference | Nej | Nej |
> | operations | Nej | Nej |
> | updatecommunicationpreference | Nej | Nej |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | villkor | Nej | Nej |
> | offertypes | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | platser | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | Media Services | Ja | Ja |
> | Media Services/accountfilters | Nej | Nej |
> | Media Services/till gångar | Nej | Nej |
> | Media Services/assets/assetfilters | Nej | Nej |
> | Media Services/contentkeypolicies | Nej | Nej |
> | Media Services/eventgridfilters | Nej | Nej |
> | Media Services/liveeventoperations | Nej | Nej |
> | Media Services/liveevents | Ja | Ja |
> | Media Services/liveevents/liveoutputs | Nej | Nej |
> | Media Services/liveoutputoperations | Nej | Nej |
> | Media Services/streamingendpointoperations | Nej | Nej |
> | Media Services/strömnings slut punkter | Ja | Ja |
> | Media Services/streaminglocators | Nej | Nej |
> | Media Services/streamingpolicies | Nej | Nej |
> | Media Services/transformeringar | Nej | Nej |
> | Media Services/transformeringar/jobb | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | appclusters | Nej | Nej |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Ja | Ja |
> | platser | Nej | Nej |
> | platser/assessmentoptions | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | migrateprojects | Ja | Ja |
> | movecollections | Nej | Nej |
> | operations | Nej | Nej |
> | samarbetsprojekt | Nej | Nej |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nej | Nej |
> | platser | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | objectunderstandingaccounts | Nej | Nej |
> | operations | Nej | Nej |
> | remoterenderingaccounts | Ja | Ja |
> | spatialanchorsaccounts | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nej | Nej |
> | netappaccounts / backuppolicies | Nej | Nej |
> | netappaccounts / capacitypools | Nej | Nej |
> | netappaccounts/capacitypools/Volumes | Nej | Nej |
> | netappaccounts/capacitypools/Volumes/mounttargets | Nej | Nej |
> | netappaccounts/capacitypools/volym/ögonblicks bilder | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | Nej | Nej |
> | applicationgatewayavailableresponseheaders | Nej | Nej |
> | applicationgatewayavailableservervariables | Nej | Nej |
> | applicationgatewayavailablessloptions | Nej | Nej |
> | applicationgatewayavailablewafrulesets | Nej | Nej |
> | applicationgateways | Nej | Nej |
> | applicationgatewaywebapplicationfirewallpolicies | Nej | Nej |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewallfqdntags | Nej | Nej |
> | azurefirewalls | Nej | Nej |
> | bastionhosts | Nej | Nej |
> | bgpservicecommunities | Nej | Nej |
> | checkfrontdoornameavailability | Nej | Nej |
> | checktrafficmanagernameavailability | Nej | Nej |
> | anslutning | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Nej | Nej |
> | dnsoperationresults | Nej | Nej |
> | dnsoperationstatuses | Nej | Nej |
> | dnszones | Ja | Ja |
> | dnszones/a | Nej | Nej |
> | dnszones/AAAA | Nej | Nej |
> | dnszones/alla | Nej | Nej |
> | dnszones/CAA | Nej | Nej |
> | dnszones/CNAME | Nej | Nej |
> | dnszones/MX | Nej | Nej |
> | dnszones/ns | Nej | Nej |
> | dnszones/PTR | Nej | Nej |
> | dnszones/Recordset | Nej | Nej |
> | dnszones/SOA | Nej | Nej |
> | dnszones/SRV | Nej | Nej |
> | dnszones/txt | Nej | Nej |
> | expressroutecircuits | Nej | Nej |
> | expressroutegateways | Nej | Nej |
> | expressrouteserviceproviders | Nej | Nej |
> | firewallpolicies | Ja | Ja |
> | frontdooroperationresults | Nej | Nej |
> | frontdoors | Nej | Nej |
> | frontdoorwebapplicationfirewallmanagedrulesets | Nej | Nej |
> | frontdoorwebapplicationfirewallpolicies | Nej | Nej |
> | getdnsresourcereference | Nej | Nej |
> | internalnotify | Nej | Nej |
> | ipgroups | Ja | Ja |
> | belastningsutjämnare | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
> | localnetworkgateways | Ja | Ja |
> | platser | Nej | Nej |
> | platser/autoapprovedprivatelinkservices | Nej | Nej |
> | platser/availabledelegations | Nej | Nej |
> | platser/availableprivateendpointtypes | Nej | Nej |
> | platser/availableservicealiases | Nej | Nej |
> | platser/baremetaltenants | Nej | Nej |
> | platser/batchnotifyprivateendpointsforresourcemove | Nej | Nej |
> | platser/batchvalidateprivateendpointsforresourcemove | Nej | Nej |
> | platser/checkacceleratednetworkingsupport | Nej | Nej |
> | platser/checkdnsnameavailability | Nej | Nej |
> | platser/checkprivatelinkservicevisibility | Nej | Nej |
> | platser/commitinternalazurenetworkmanagerconfiguration | Nej | Nej |
> | platser/effectiveresourceownership | Nej | Nej |
> | platser/nfvoperationresults | Nej | Nej |
> | platser/nfvoperations | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/åtgärder | Nej | Nej |
> | platser/servicetags | Nej | Nej |
> | platser/setresourceownership | Nej | Nej |
> | platser/supportedvirtualmachinesizes | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | platser/validateresourceownership | Nej | Nej |
> | platser/virtualnetworkavailableendpointservices | Nej | Nej |
> | natgateways | Ja | Ja |
> | networkexperimentprofiles | Nej | Nej |
> | networkintentpolicies | Ja | Ja |
> | NetworkInterfaces | Ja | Ja |
> | networkprofiles | Nej | Nej |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Ja | Nej |
> | networkwatchers / connectionmonitors | Ja | Nej |
> | networkwatchers / flowlogs | Ja | Nej |
> | networkwatchers / pingmeshes | Ja | Nej |
> | operations | Nej | Nej |
> | p2svpngateways | Nej | Nej |
> | privatednsoperationresults | Nej | Nej |
> | privatednsoperationstatuses | Nej | Nej |
> | privatednszones | Ja | Ja |
> | privatednszones/a | Nej | Nej |
> | privatednszones/AAAA | Nej | Nej |
> | privatednszones/alla | Nej | Nej |
> | privatednszones/CNAME | Nej | Nej |
> | privatednszones/MX | Nej | Nej |
> | privatednszones/PTR | Nej | Nej |
> | privatednszones/SOA | Nej | Nej |
> | privatednszones/SRV | Nej | Nej |
> | privatednszones/txt | Nej | Nej |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privatednszonesinternal | Nej | Nej |
> | privateendpointredirectmaps | Nej | Nej |
> | privateendpoints | Ja | Ja |
> | privatelinkservices | Nej | Nej |
> | publicipaddresses | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
> | publicipprefixes | Ja | Ja |
> | routefilters | Nej | Nej |
> | routetables | Ja | Ja |
> | securitypartnerproviders | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagergeographichierarchies | Nej | Nej |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/termiska kartor | Nej | Nej |
> | trafficmanagerusermetricskeys | Nej | Nej |
> | virtualhubs | Nej | Nej |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Nej | Nej |
> | virtualrouters | Ja | Ja |
> | virtualwans | Nej | Nej |
> | vpngateways (virtuellt WAN) | Nej | Nej |
> | vpnserverconfigurations | Nej | Nej |
> | vpnsites (virtuellt WAN) | Nej | Nej |

> [!IMPORTANT]
> Se [rikt linjer för nätverks flytt](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | checknamespaceavailability | Nej | Nej |
> | namn områden | Ja | Ja |
> | namnrymder/notificationhubs | Ja | Ja |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hypervsites | Ja | Ja |
> | importsites | Ja | Ja |
> | operations | Nej | Nej |
> | serversites | Ja | Ja |
> | vmwaresites | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Nej | Nej |
> | deletedworkspaces | Nej | Nej |
> | linktargets | Nej | Nej |
> | platser | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |
> | storageinsightconfigs | Nej | Nej |
> | arbetsytor | Ja | Ja |
> | arbets ytor/data källor | Nej | Nej |
> | arbets ytor/linkedservices | Nej | Nej |
> | arbets ytor/linkedstorageaccounts | Nej | Nej |
> | arbets ytor/metadata | Nej | Nej |
> | arbets ytor/fråga | Nej | Nej |
> | arbets ytor/scopedprivatelinkproxies | Nej | Nej |

> [!IMPORTANT]
> Kontrol lera att flytta till en ny prenumeration inte överskrider [prenumerations kvoterna](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Det går inte att flytta arbets ytor som har ett länkat Automation-konto. Innan du påbörjar en flytt åtgärd måste du ta bort länken till eventuella Automation-konton.

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nej | Nej |
> | managementconfigurations | Ja | Ja |
> | operations | Nej | Nej |
> | lösningar | Ja | Ja |
> | vyer | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Nej | Nej |
> | legacypeerings | Nej | Nej |
> | operations | Nej | Nej |
> | peerasns | Nej | Nej |
> | peeringlocations | Nej | Nej |
> | peerings | Ja | Ja |
> | peeringservicecountries | Nej | Nej |
> | peeringservicelocations | Nej | Nej |
> | peeringserviceproviders | Nej | Nej |
> | peeringservices | Nej | Nej |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | Nej | Nej |
> | operations | Nej | Nej |
> | policyevents | Nej | Nej |
> | policystates | Nej | Nej |
> | policytrackedresources | Nej | Nej |
> | reparationer | Nej | Nej |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> |  -konsoler | Nej | Nej |
> | instrumentpaneler | Ja | Ja |
> | platser | Nej | Nej |
> | platser/konsoler | Nej | Nej |
> | platser/usersettings | Nej | Nej |
> | operations | Nej | Nej |
> | usersettings | Nej | Nej |

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | rootresources | Nej | Nej |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kapaciteter | Ja | Ja |
> | platser | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |
> | checknameavailability | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Nej | Nej |
> | providerregistrations | Nej | Nej |
> | providerregistrations / resourcetyperegistrations | Nej | Nej |
> | distributioner | Nej | Nej |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |
> | arbetsytor | Nej | Nej |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nej | Nej |
> | platser | Nej | Nej |
> | platser/allocatedstamp | Nej | Nej |
> | platser/allocatedstamp | Nej | Nej |
> | platser/backupaadproperties | Nej | Nej |
> | platser/backupcrossregionrestore | Nej | Nej |
> | platser/backupcrrjob | Nej | Nej |
> | platser/backupcrrjobs | Nej | Nej |
> | platser/backupcrroperationresults | Nej | Nej |
> | platser/backupcrroperationsstatus | Nej | Nej |
> | platser/backupprevalidateprotection | Nej | Nej |
> | platser/Backupstatus | Nej | Nej |
> | platser/backupvalidatefeatures | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | operations | Nej | Nej |
> | replicationeligibilityresults | Nej | Nej |
> | valv | Ja | Ja |

> [!IMPORTANT]
> Se [Recovery Services flytta vägledning](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/operationsstatus | Nej | Nej |
> | openshiftclusters | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Nej | Nej |
> | namnrymder/hybridconnections | Nej | Nej |
> | namnrymder/hybridconnections/authorizationrules | Nej | Nej |
> | namnrymder/privateendpointconnections | Nej | Nej |
> | namnrymder/wcfrelays | Nej | Nej |
> | namnrymder/wcfrelays/authorizationrules | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operations | Nej | Nej |
> | skickar | Ja | Ja |
> | resourcechangedetails | Nej | Nej |
> | resourcechanges | Nej | Nej |
> | resources | Nej | Nej |
> | resourceshistory | Nej | Nej |
> | subscriptionsstatus | Nej | Nej |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Nej | Nej |
> | childavailabilitystatuses | Nej | Nej |
> | childresources | Nej | Nej |
> | emergingissues | Nej | Nej |
> | händelser | Nej | Nej |
> | metadata | Nej | Nej |
> | meddelanden | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checkpolicycompliance | Nej | Nej |
> | checkresourcename | Nej | Nej |
> | distributioner | Nej | Nej |
> | distributioner/åtgärder | Nej | Nej |
> | deploymentscripts | Nej | Nej |
> | deploymentscripts/loggar | Nej | Nej |
> | Länkar | Nej | Nej |
> | platser | Nej | Nej |
> | platser/deploymentscriptoperationresults | Nej | Nej |
> | notifyresourcejobs | Nej | Nej |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | finansiär | Nej | Nej |
> | ResourceGroups | Nej | Nej |
> | resources | Nej | Nej |
> | prenumerationer | Nej | Nej |
> | prenumerationer/platser | Nej | Nej |
> | prenumerationer/operationresults | Nej | Nej |
> | prenumerationer/providers | Nej | Nej |
> | prenumerationer/ResourceGroups | Nej | Nej |
> | prenumerationer/ResourceGroups/resurser | Nej | Nej |
> | prenumerationer/resurser | Nej | Nej |
> | prenumerationer/tagNames | Nej | Nej |
> | prenumerationer/tagNames/tagvalues | Nej | Nej |
> | tags | Nej | Nej |
> | templatespecs | Nej | Nej |
> | templatespecs/versioner | Nej | Nej |
> | klienter | Nej | Nej |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Nej |
> | checkmoderneligibility | Nej | Nej |
> | checknameavailability | Nej | Nej |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | saasresources | Nej | Nej |

## <a name="microsoftsearch"></a>Microsoft. search

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | checkservicenameavailability | Nej | Nej |
> | operations | Nej | Nej |
> | resourcehealthmetadata | Nej | Nej |
> | searchservices | Ja | Ja |

> [!IMPORTANT]
> Du kan inte flytta flera Sök resurser i olika regioner i samma åtgärd. Flytta i stället dem i separata åtgärder.

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nej | Nej |
> | advancedthreatprotectionsettings | Nej | Nej |
> | aviseringar | Nej | Nej |
> | allowedconnections | Nej | Nej |
> | applicationwhitelistings | Nej | Nej |
> | assessmentmetadata | Nej | Nej |
> | utvärderingar | Nej | Nej |
> | autodismissalertsrules | Nej | Nej |
> | automatiseringar | Ja | Ja |
> | autoprovisioningsettings | Nej | Nej |
> | complianceresults | Nej | Nej |
> | godkännanden | Nej | Nej |
> | datacollectionagents | Nej | Nej |
> | devicesecuritygroups | Nej | Nej |
> | discoveredsecuritysolutions | Nej | Nej |
> | externalsecuritysolutions | Nej | Nej |
> | informationprotectionpolicies | Nej | Nej |
> | iotsecuritysolutions | Ja | Ja |
> | iotsecuritysolutions / analyticsmodels | Nej | Nej |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nej | Nej |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nej | Nej |
> | jitnetworkaccesspolicies | Nej | Nej |
> | platser | Nej | Nej |
> | platser/aviseringar | Nej | Nej |
> | platser/allowedconnections | Nej | Nej |
> | platser/applicationwhitelistings | Nej | Nej |
> | platser/discoveredsecuritysolutions | Nej | Nej |
> | platser/externalsecuritysolutions | Nej | Nej |
> | platser/jitnetworkaccesspolicies | Nej | Nej |
> | platser/securitysolutions | Nej | Nej |
> | platser/securitysolutionsreferencedata | Nej | Nej |
> | platser/uppgifter | Nej | Nej |
> | platser/topologier | Nej | Nej |
> | operations | Nej | Nej |
> | policies | Nej | Nej |
> | prissättningar | Nej | Nej |
> | regulatorycompliancestandards | Nej | Nej |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Nej | Nej |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nej | Nej |
> | securitycontacts | Nej | Nej |
> | securitysolutions | Nej | Nej |
> | securitysolutionsreferencedata | Nej | Nej |
> | securitystatuses | Nej | Nej |
> | securitystatusessummaries | Nej | Nej |
> | servervulnerabilityassessments | Nej | Nej |
> | settings | Nej | Nej |
> | underbedömningar | Nej | Nej |
> | uppgifter | Nej | Nej |
> | topologier | Nej | Nej |
> | workspacesettings | Nej | Nej |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | agg regeringar | Nej | Nej |
> | alertrules | Nej | Nej |
> | alertruletemplates | Nej | Nej |
> | bokmärken | Nej | Nej |
> | fall | Nej | Nej |
> | dataconnectors | Nej | Nej |
> | dataconnectorscheckrequirements | Nej | Nej |
> | poster | Nej | Nej |
> | entityqueries | Nej | Nej |
> | incidenter | Nej | Nej |
> | officeconsents | Nej | Nej |
> | operations | Nej | Nej |
> | settings | Nej | Nej |
> | threatintelligence | Nej | Nej |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | consoleservices | Nej | Nej |
> | platser | Nej | Nej |
> | platser/consoleservices | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | gatewayer | Nej | Nej |
> | artikelnoder | Nej | Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | checknamespaceavailability | Nej | Nej |
> | platser | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | namn områden | Ja | Ja |
> | namnrymder/authorizationrules | Nej | Nej |
> | namnrymder/disasterrecoveryconfigs | Nej | Nej |
> | namnrymder/disasterrecoveryconfigs/checknameavailability | Nej | Nej |
> | namnrymder/eventgridfilters | Nej | Nej |
> | namnrymder/networkrulesets | Nej | Nej |
> | namnrymder/köer | Nej | Nej |
> | namnrymder/köer/authorizationrules | Nej | Nej |
> | namn områden/ämnen | Nej | Nej |
> | namnrymder/ämnen/authorizationrules | Nej | Nej |
> | namnrymder/ämnen/prenumerationer | Nej | Nej |
> | namn områden/ämnen/prenumerationer/regler | Nej | Nej |
> | operations | Nej | Nej |
> | premiummessagingregions | Nej | Nej |
> | sku | Nej | Nej |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Nej | Nej |
> | kluster | Ja | Ja |
> | kluster/program | Nej | Nej |
> | containergroups | Nej | Nej |
> | containergroupsets | Nej | Nej |
> | edgeclusters | Nej | Nej |
> | platser | Nej | Nej |
> | platser/clusterversions | Nej | Nej |
> | platser/miljöer | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/åtgärder | Nej | Nej |
> | managedclusters | Nej | Nej |
> | nätet | Nej | Nej |
> | operations | Nej | Nej |
> | secretstores | Nej | Nej |
> | volumes | Nej | Nej |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Ja |
> | containergroups | Nej | Nej |
> | gatewayer | Ja | Ja |
> | platser | Nej | Nej |
> | platser/applicationoperations | Nej | Nej |
> | platser/gatewayoperations | Nej | Nej |
> | platser/networkoperations | Nej | Nej |
> | platser/secretoperations | Nej | Nej |
> | platser/volumeoperations | Nej | Nej |
> | nätet | Ja | Ja |
> | operations | Nej | Nej |
> | secrets | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nej | Nej |
> | providerregistrations / resourcetyperegistrations | Nej | Nej |
> | distributioner | Nej | Nej |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | operations | Nej | Nej |
> | signalr | Ja | Ja |
> | SignalR/eventgridfilters | Nej | Nej |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nej | Nej |
> | program | Nej | Nej |
> | jitrequests | Nej | Nej |
> | platser | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | instancepools | Nej | Nej |
> | platser | Ja | Ja |
> | platser | Nej | Nej |
> | platser/administratorazureasyncoperation | Nej | Nej |
> | platser/administratoroperationresults | Nej | Nej |
> | platser/auditingsettingsazureasyncoperation | Nej | Nej |
> | platser/auditingsettingsoperationresults | Nej | Nej |
> | platser/funktioner | Nej | Nej |
> | platser/databaseazureasyncoperation | Nej | Nej |
> | platser/databaseoperationresults | Nej | Nej |
> | platser/databaserestoreazureasyncoperation | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | platser/deletevirtualnetworkorsubnetsazureasyncoperation | Nej | Nej |
> | platser/deletevirtualnetworkorsubnetsoperationresults | Nej | Nej |
> | platser/dnsaliasasyncoperation | Nej | Nej |
> | platser/dnsaliasoperationresults | Nej | Nej |
> | platser/elasticpoolazureasyncoperation | Nej | Nej |
> | platser/elasticpooloperationresults | Nej | Nej |
> | platser/encryptionprotectorazureasyncoperation | Nej | Nej |
> | platser/encryptionprotectoroperationresults | Nej | Nej |
> | platser/extendedauditingsettingsazureasyncoperation | Nej | Nej |
> | platser/extendedauditingsettingsoperationresults | Nej | Nej |
> | platser/failovergroupazureasyncoperation | Nej | Nej |
> | platser/failovergroupoperationresults | Nej | Nej |
> | platser/firewallrulesazureasyncoperation | Nej | Nej |
> | platser/firewallrulesoperationresults | Nej | Nej |
> | platser/instancefailovergroupazureasyncoperation | Nej | Nej |
> | platser/instancefailovergroupoperationresults | Nej | Nej |
> | platser/instancefailovergroups | Nej | Nej |
> | platser/instancepoolazureasyncoperation | Nej | Nej |
> | platser/instancepooloperationresults | Nej | Nej |
> | platser/jobagentazureasyncoperation | Nej | Nej |
> | platser/jobagentoperationresults | Nej | Nej |
> | platser/longtermretentionbackupazureasyncoperation | Nej | Nej |
> | platser/longtermretentionbackupoperationresults | Nej | Nej |
> | platser/longtermretentionbackups | Nej | Nej |
> | platser/longtermretentionmanagedinstancebackupazureasyncoperation | Nej | Nej |
> | platser/longtermretentionmanagedinstancebackupoperationresults | Nej | Nej |
> | platser/longtermretentionmanagedinstancebackups | Nej | Nej |
> | platser/longtermretentionmanagedinstances | Nej | Nej |
> | platser/longtermretentionpolicyazureasyncoperation | Nej | Nej |
> | platser/longtermretentionpolicyoperationresults | Nej | Nej |
> | platser/longtermretentionservers | Nej | Nej |
> | platser/manageddatabaseazureasyncoperation | Nej | Nej |
> | platser/manageddatabasecompleterestoreazureasyncoperation | Nej | Nej |
> | platser/manageddatabasecompleterestoreoperationresults | Nej | Nej |
> | platser/manageddatabaseoperationresults | Nej | Nej |
> | platser/manageddatabaserestoreazureasyncoperation | Nej | Nej |
> | platser/manageddatabaserestoreoperationresults | Nej | Nej |
> | platser/managedinstanceazureasyncoperation | Nej | Nej |
> | platser/managedinstanceencryptionprotectorazureasyncoperation | Nej | Nej |
> | platser/managedinstanceencryptionprotectoroperationresults | Nej | Nej |
> | platser/managedinstancekeyazureasyncoperation | Nej | Nej |
> | platser/managedinstancekeyoperationresults | Nej | Nej |
> | platser/managedinstancelongtermretentionpolicyazureasyncoperation | Nej | Nej |
> | platser/managedinstancelongtermretentionpolicyoperationresults | Nej | Nej |
> | platser/managedinstanceoperationresults | Nej | Nej |
> | platser/managedinstancetdecertazureasyncoperation | Nej | Nej |
> | platser/managedinstancetdecertoperationresults | Nej | Nej |
> | platser/managedserversecurityalertpoliciesazureasyncoperation | Nej | Nej |
> | platser/managedserversecurityalertpoliciesoperationresults | Nej | Nej |
> | platser/managedshorttermretentionpolicyazureasyncoperation | Nej | Nej |
> | platser/managedshorttermretentionpolicyoperationresults | Nej | Nej |
> | platser/notifyazureasyncoperation | Nej | Nej |
> | platser/privateendpointconnectionazureasyncoperation | Nej | Nej |
> | platser/privateendpointconnectionoperationresults | Nej | Nej |
> | platser/privateendpointconnectionproxyazureasyncoperation | Nej | Nej |
> | platser/privateendpointconnectionproxyoperationresults | Nej | Nej |
> | platser/securityalertpoliciesazureasyncoperation | Nej | Nej |
> | platser/securityalertpoliciesoperationresults | Nej | Nej |
> | platser/serveradministratorazureasyncoperation | Nej | Nej |
> | platser/serveradministratoroperationresults | Nej | Nej |
> | platser/serverazureasyncoperation | Nej | Nej |
> | platser/serverkeyazureasyncoperation | Nej | Nej |
> | platser/serverkeyoperationresults | Nej | Nej |
> | platser/serveroperationresults | Nej | Nej |
> | platser/shorttermretentionpolicyazureasyncoperation | Nej | Nej |
> | platser/shorttermretentionpolicyoperationresults | Nej | Nej |
> | platser/syncagentoperationresults | Nej | Nej |
> | platser/syncdatabaseids | Nej | Nej |
> | platser/syncgroupoperationresults | Nej | Nej |
> | platser/syncmemberoperationresults | Nej | Nej |
> | platser/tdecertazureasyncoperation | Nej | Nej |
> | platser/tdecertoperationresults | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | platser/virtualclusterazureasyncoperation | Nej | Nej |
> | platser/virtualclusteroperationresults | Nej | Nej |
> | platser/virtualnetworkrulesazureasyncoperation | Nej | Nej |
> | platser/virtualnetworkrulesoperationresults | Nej | Nej |
> | platser/vulnerabilityassessmentscanazureasyncoperation | Nej | Nej |
> | platser/vulnerabilityassessmentscanoperationresults | Nej | Nej |
> | managedinstances | Nej | Nej |
> | managedinstances/administratörer | Nej | Nej |
> | managedinstances/databaser | Nej | Nej |
> | managedinstances/databaser/backuplongtermretentionpolicies | Nej | Nej |
> | managedinstances/databaser/vulnerabilityassessments | Nej | Nej |
> | managedinstances / metricdefinitions | Nej | Nej |
> | managedinstances/mått | Nej | Nej |
> | managedinstances / recoverabledatabases | Nej | Nej |
> | managedinstances / tdecertificates | Nej | Nej |
> | managedinstances / vulnerabilityassessments | Nej | Nej |
> | operations | Nej | Nej |
> | brygghuvudservrar | Ja | Ja |
> | servrar/administratoroperationresults | Nej | Nej |
> | servrar/administratörer | Nej | Nej |
> | servrar/rådgivare | Nej | Nej |
> | servrar/aggregateddatabasemetrics | Nej | Nej |
> | servrar/auditingpolicies | Nej | Nej |
> | servrar/auditingsettings | Nej | Nej |
> | servrar/automatictuning | Nej | Nej |
> | servrar/communicationlinks | Nej | Nej |
> | servrar/connectionpolicies | Nej | Nej |
> | servrar/databaser | Ja | Ja |
> | servrar/databaser/rådgivare | Nej | Nej |
> | servrar/databaser/auditingpolicies | Nej | Nej |
> | servrar/databaser/auditingsettings | Nej | Nej |
> | servrar/databaser/AuditRecords | Nej | Nej |
> | servrar/databaser/automatictuning | Nej | Nej |
> | servrar/databaser/backuplongtermretentionpolicies | Nej | Nej |
> | servrar/databaser/backupshorttermretentionpolicies | Nej | Nej |
> | servrar/databaser/connectionpolicies | Nej | Nej |
> | servrar/databaser/datamaskingpolicies | Nej | Nej |
> | servrar/databaser/datamaskingpolicies/regler | Nej | Nej |
> | servrar/databaser/tillägg | Nej | Nej |
> | servrar/databaser/geobackuppolicies | Nej | Nej |
> | servrar/databaser/metricdefinitions | Nej | Nej |
> | servrar/databaser/mått | Nej | Nej |
> | servrar/databaser/recommendedsensitivitylabels | Nej | Nej |
> | servrar/databaser/securityalertpolicies | Nej | Nej |
> | servrar/databaser/syncgroups | Nej | Nej |
> | servrar/databaser/syncgroups/syncmembers | Nej | Nej |
> | servrar/databaser/topqueries | Nej | Nej |
> | servrar/databaser/topqueries/querytext | Nej | Nej |
> | servrar/databaser/transparentdataencryption | Nej | Nej |
> | servrar/databaser/vulnerabilityassessment | Nej | Nej |
> | servrar/databaser/vulnerabilityassessments | Nej | Nej |
> | servrar/databaser/vulnerabilityassessmentscans | Nej | Nej |
> | servrar/databaser/vulnerabilityassessmentsettings | Nej | Nej |
> | servrar/databaser/workloadgroups | Nej | Nej |
> | servrar/databasesecuritypolicies | Nej | Nej |
> | servrar/disasterrecoveryconfiguration | Nej | Nej |
> | servrar/dnsaliases | Nej | Nej |
> | servrar/elasticpoolestimates | Nej | Nej |
> | servrar/elasticpools | Ja | Ja |
> | servrar/elasticpools/Advisor | Nej | Nej |
> | servrar/elasticpools/metricdefinitions | Nej | Nej |
> | servrar/elasticpools/mått | Nej | Nej |
> | servrar/encryptionprotector | Nej | Nej |
> | servrar/extendedauditingsettings | Nej | Nej |
> | servrar/failovergroups | Nej | Nej |
> | servrar/import | Nej | Nej |
> | servrar/importexportoperationresults | Nej | Nej |
> | servrar/jobaccounts | Ja | Ja |
> | servrar/jobagents | Ja | Ja |
> | servrar/jobagents/jobb | Nej | Nej |
> | servrar/jobagents/jobb/körningar | Nej | Nej |
> | servrar/jobagents/jobb/steg | Nej | Nej |
> | servrar/nycklar | Nej | Nej |
> | servrar/operationresults | Nej | Nej |
> | servrar/recommendedelasticpools | Nej | Nej |
> | servrar/recoverabledatabases | Nej | Nej |
> | servrar/restorabledroppeddatabases | Nej | Nej |
> | servrar/securityalertpolicies | Nej | Nej |
> | servrar/serviceobjectives | Nej | Nej |
> | servrar/syncagents | Nej | Nej |
> | servrar/tdecertificates | Nej | Nej |
> | servrar/användningar | Nej | Nej |
> | servrar/virtualnetworkrules | Nej | Nej |
> | servrar/vulnerabilityassessments | Nej | Nej |
> | virtualclusters | Ja | Ja |

> [!IMPORTANT]
> En databas och en server måste finnas i samma resurs grupp. När du flyttar en SQL-Server flyttas även alla databaserna. Detta gäller för Azure SQL Database-och Azure SQL Data Warehouse-databaser.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/availabilitygrouplisteneroperationresults | Nej | Nej |
> | platser/operationtypes | Nej | Nej |
> | platser/sqlvirtualmachinegroupoperationresults | Nej | Nej |
> | platser/sqlvirtualmachineoperationresults | Nej | Nej |
> | operations | Nej | Nej |
> | sqlvirtualmachinegroups | Ja | Ja |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | Nej | Nej |
> | sqlvirtualmachines | Ja | Ja |

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dwvm | Nej | Nej |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | platser | Nej | Nej |
> | platser/asyncoperations | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | operations | Nej | Nej |
> | storageaccounts | Ja | Ja |
> | storageaccounts/blobservices | Nej | Nej |
> | storageaccounts/FileServices | Nej | Nej |
> | storageaccounts/listaccountsas | Nej | Nej |
> | storageaccounts/listservicesas | Nej | Nej |
> | storageaccounts/queueservices | Nej | Nej |
> | storageaccounts/tjänster | Nej | Nej |
> | storageaccounts/tjänster/metricdefinitions | Nej | Nej |
> | storageaccounts/tableservices | Nej | Nej |
> | användningar | Nej | Nej |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cacheminnen | Nej | Nej |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/checknameavailability | Nej | Nej |
> | platser/arbets flöden | Nej | Nej |
> | operations | Nej | Nej |
> | storagesyncservices | Ja | Ja |
> | storagesyncservices / registeredservers | Nej | Nej |
> | storagesyncservices / syncgroups | Nej | Nej |
> | storagesyncservices / syncgroups / cloudendpoints | Nej | Nej |
> | storagesyncservices / syncgroups / serverendpoints | Nej | Nej |
> | storagesyncservices/arbets flöden | Nej | Nej |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nej | Nej |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nej | Nej |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hantera | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Nej | Nej |
> | platser | Nej | Nej |
> | platser/kvoter | Nej | Nej |
> | operations | Nej | Nej |
> | streamingjobs | Ja | Ja |

> [!IMPORTANT]
> Det går inte att flytta Stream Analytics jobb i körnings läge.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utrymmen | Nej | Nej |
> | miljöer/eventsources | Nej | Nej |
> | pipe | Nej | Nej |
> | instanser/miljöer | Nej | Nej |
> | instanser/miljöer/eventsources | Nej | Nej |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | avbryt | Nej | Nej |
> | createsubscription | Nej | Nej |
> | Aktivera | Nej | Nej |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | byt namn | Nej | Nej |
> | subscriptiondefinitions | Nej | Nej |
> | subscriptionoperations | Nej | Nej |
> | prenumerationer | Nej | Nej |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | operationresults | Nej | Nej |
> | operations | Nej | Nej |
> | operationsstatus | Nej | Nej |
> | services | Nej | Nej |
> | tjänster/problemclassifications | Nej | Nej |
> | supporttickets | Nej | Nej |

## <a name="microsoftsynapse"></a>Microsoft. Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nej | Nej |
> | operations | Nej | Nej |
> | arbetsytor | Ja | Ja |
> | arbets ytor/bigdatapools | Ja | Ja |
> | arbets ytor/operationresults | Nej | Nej |
> | arbets ytor/operationstatuses | Nej | Nej |
> | arbets ytor/sqlpools | Ja | Ja |

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nej | Nej |
> | resources | Nej | Nej |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utrymmen | Ja | Ja |
> | miljöer/accesspolicies | Nej | Nej |
> | miljöer/eventsources | Ja | Ja |
> | miljöer/referencedatasets | Ja | Ja |
> | operations | Nej | Nej |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Auktoriseringshanteraren | Ja | Ja |
> | butiker/accesspolicies | Nej | Nej |
> | butiker/tjänster | Nej | Nej |
> | butiker/tjänster/token | Nej | Nej |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konto | Nej | Nej |
> | konto/tillägg | Nej | Nej |
> | konto/projekt | Nej | Nej |
> | checknameavailability | Nej | Nej |
> | operations | Nej | Nej |

> [!IMPORTANT]
> Information om hur du ändrar prenumerationen för Azure-DevOps finns i [ändra den Azure-prenumeration som används för fakturering](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arczones | Nej | Nej |
> | platser | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |
> | resourcepools | Nej | Nej |
> | vCenter | Nej | Nej |
> | virtualmachines | Nej | Nej |
> | virtualmachinetemplates | Nej | Nej |
> | virtualnetworks | Nej | Nej |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nej | Nej |
> | dedicatedcloudservices | Nej | Nej |
> | platser | Nej | Nej |
> | platser/tillgänglighet | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/privateclouds | Nej | Nej |
> | platser/privateclouds/resourcepools | Nej | Nej |
> | platser/privateclouds/virtualmachinetemplates | Nej | Nej |
> | platser/privateclouds/virtualnetworks | Nej | Nej |
> | platser/användningar | Nej | Nej |
> | operations | Nej | Nej |
> | virtualmachines | Nej | Nej |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | devices | Nej | Nej |
> | platser | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | operations | Nej | Nej |
> | vnfs | Nej | Nej |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |
> | operations | Nej | Nej |
> | utgå | Nej | Nej |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availablestacks | Nej | Nej |
> | billingmeters | Nej | Nej |
> | certifikat | Nej | Ja |
> | checknameavailability | Nej | Nej |
> | connectiongateways | Ja | Ja |
> | anslutning | Ja | Ja |
> | customapis | Ja | Ja |
> | deletedsites | Nej | Nej |
> | deploymentlocations | Nej | Nej |
> | regioner | Nej | Nej |
> | hostingenvironments | Nej | Nej |
> | hostingenvironments / eventgridfilters | Nej | Nej |
> | hostingenvironments / multirolepools | Nej | Nej |
> | hostingenvironments / workerpools | Nej | Nej |
> | ishostingenvironmentnameavailable | Nej | Nej |
> | ishostnameavailable | Nej | Nej |
> | isusernameavailable | Nej | Nej |
> | kubeenvironments | Ja | Ja |
> | listsitesassignedtohostname | Nej | Nej |
> | platser | Nej | Nej |
> | platser/apioperations | Nej | Nej |
> | platser/connectiongatewayinstallations | Nej | Nej |
> | platser/deletedsites | Nej | Nej |
> | platser/deletevirtualnetworkorsubnets | Nej | Nej |
> | platser/extractapidefinitionfromwsdl | Nej | Nej |
> | platser/getnetworkpolicies | Nej | Nej |
> | platser/listwsdlinterfaces | Nej | Nej |
> | platser/managedapis | Nej | Nej |
> | platser/operationresults | Nej | Nej |
> | platser/åtgärder | Nej | Nej |
> | platser/körningar | Nej | Nej |
> | operations | Nej | Nej |
> | publishingusers | Nej | Nej |
> | rekommendationer | Nej | Nej |
> | resourcehealthmetadata | Nej | Nej |
> | körningar | Nej | Nej |
> | Server grupper | Ja | Ja |
> | Server grupper/eventgridfilters | Nej | Nej |
> | webbplatser | Ja | Ja |
> | platser/eventgridfilters | Nej | Nej |
> | platser/hostnamebindings | Nej | Nej |
> | platser/networkconfig | Nej | Nej |
> | platser/premieraddons | Ja | Ja |
> | platser/platser | Ja | Ja |
> | platser/platser/eventgridfilters | Nej | Nej |
> | platser/platser/hostnamebindings | Nej | Nej |
> | platser/platser/networkconfig | Nej | Nej |
> | sourcecontrols | Nej | Nej |
> | staticsites | Nej | Nej |
> | kontrollerar | Nej | Nej |
> | verifyhostingenvironmentvnet | Nej | Nej |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | platser | Nej | Nej |
> | platser/operationstatuses | Nej | Nej |
> | multipleactivationkeys | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nej | Nej |
> | operations | Nej | Nej |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | delarna | Nej | Nej |
> | componentssummary | Nej | Nej |
> | monitorinstances | Nej | Nej |
> | monitorinstancessummary | Nej | Nej |
> | Övervakare | Nej | Nej |
> | notificationsettings | Nej | Nej |
> | operations | Nej | Nej |

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.

## <a name="next-steps"></a>Nästa steg
För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](move-resource-group-and-subscription.md).

Hämta samma data som en fil med kommaavgränsade värden genom att ladda ned [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
