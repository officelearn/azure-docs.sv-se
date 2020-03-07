---
title: Flytta åtgärds stöd efter resurs typ
description: Visar en lista över de Azure-resurs typer som kan flyttas till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 8ab194ad240e4f3e0994314ef9ade3bc7159cf81
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387702"
---
# <a name="move-operation-support-for-resources"></a>Åtgärds stöd för flytt av resurser
Den här artikeln visar om en Azure-resurs har stöd för flytt åtgärden. Den innehåller också information om särskilda villkor att tänka på när du flyttar en resurs.

Hoppa till ett namn område för en resurs leverantör:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. API Management](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
> - [Microsoft. fakturering](#microsoftbilling)
> - [Microsoft. Bingkartssökning](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. skiss](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Compute](#microsoftcompute)
> - [Microsoft. förbrukning](#microsoftconsumption)
> - [Microsoft. container](#microsoftcontainer)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. container service](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
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
> - [Microsoft. genomik](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. nyckel valv](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. filen LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrate](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
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
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft. Storage](#microsoftstorage)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. support](#microsoftsupport)
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domainservices | Nej | Nej |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | klienter | Nej | Nej |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konfigurationer | Nej | Nej |
> | rekommenderade | Nej | Nej |
> | utelämningar | Nej | Nej |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | actionrules | Ja | Ja |
> | aviseringar | Nej | Nej |
> | alertssummary | Nej | Nej |
> | smartdetectoralertrules | Ja | Ja |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | brygghuvudservrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | tjänst | Ja | Ja |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ja | Ja |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | upphängning | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | apiapps | Nej | Nej |
> | appidentities | Nej | Nej |
> | gatewayer | Nej | Nej |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Access | Nej | Nej |
> | denyassignments | Nej | Nej |
> | findorphanroleassignments | Nej | Nej |
> | hålls | Nej | Nej |
> | behörigheter | Nej | Nej |
> | policyassignments | Nej | Nej |
> | policydefinitions | Nej | Nej |
> | policysetdefinitions | Nej | Nej |
> | RoleAssignments | Nej | Nej |
> | roleassignmentsusagemetrics | Nej | Nej |
> | roledefinitions | Nej | Nej |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ja | Ja |
> | automationaccounts/konfigurationer | Ja | Ja |
> | automationaccounts/Runbooks | Ja | Ja |

> [!IMPORTANT]
> Runbooks måste finnas i samma resurs grupp som Automation-kontot.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ja | Ja |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Nej | Nej |
> | postgresinstances | Nej | Nej |
> | sqlbigdataclusters | Nej | Nej |
> | sqlinstances | Nej | Nej |
> | sqlserverregistrations | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | registreringar | Ja | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Nej | Nej |
> | fileservers | Nej | Nej |
> | Utskrifts | Nej | Nej |
> | arbets ytor | Nej | Nej |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | billingperiods | Nej | Nej |
> | billingpermissions | Nej | Nej |
> | billingroleassignments | Nej | Nej |
> | billingroledefinitions | Nej | Nej |
> | createbillingroleassignment | Nej | Nej |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | mapapis | Nej | Nej |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | biztalk | Nej | Nej |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nej | Nej |
> | Övervakare | Nej | Nej |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nej | Nej |
> | modeller | Nej | Nej |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Redis | Ja | Ja |

> [!IMPORTANT]
> Om Azure cache för Redis-instansen har kon figurer ATS med ett virtuellt nätverk kan instansen inte flyttas till en annan prenumeration. Se [begränsningar för nätverks flytt](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Ja | Ja |
> | profiles | Ja | Ja |
> | profiler/slut punkter | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ja | Ja |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domän namn | Ja | Nej |
> | virtualmachines | Ja | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nej | Nej |
> | reservedips | Nej | Nej |
> | virtualnetworks | Nej | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ja | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ja | Ja |
> | diskencryptionsets | Nej | Nej |
> | disk | Ja | Ja |
> | gallerier | Nej | Nej |
> | gallerier/bilder | Nej | Nej |
> | gallerier/avbildningar/versioner | Nej | Nej |
> | hostgroups | Nej | Nej |
> | hostgroups/värdar | Nej | Nej |
> | images | Ja | Ja |
> | proximityplacementgroups | Nej | Nej |
> | restorepointcollections | Nej | Nej |
> | sharedvmimages | Nej | Nej |
> | sharedvmimages/versioner | Nej | Nej |
> | snapshots | Ja | Ja |
> | virtualmachines | Ja | Ja |
> | virtualmachines/tillägg | Ja | Ja |
> | virtualmachinescalesets | Ja | Ja |

> [!IMPORTANT]
> Se [Virtual Machines flytta vägledning](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumption

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
> | Marknads platser | Nej | Nej |
> | operationresults | Nej | Nej |
> | operationstatus | Nej | Nej |
> | Pricesheets | Nej | Nej |
> | läkemedle | Nej | Nej |
> | reservationdetails | Nej | Nej |
> | reservationrecommendations | Nej | Nej |
> | reservationsummaries | Nej | Nej |
> | reservationtransactions | Nej | Nej |
> | taggar | Nej | Nej |
> | klienter | Nej | Nej |
> | begreppen | Nej | Nej |
> | usagedetails | Nej | Nej |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containergroups | Nej | Nej |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containergroups | Nej | Nej |
> | serviceassociationlinks | Nej | Nej |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | register | Ja | Ja |
> | register/buildtasks | Ja | Ja |
> | register/replikeringar | Ja | Ja |
> | register/taskruns | Ja | Ja |
> | register/uppgifter | Ja | Ja |
> | register/Webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containerservices | Nej | Nej |
> | managedclusters | Nej | Nej |
> | openshiftmanagedclusters | Nej | Nej |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Nej | Nej |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aviseringar | Nej | Nej |
> | budget | Nej | Nej |
> | anslutningar | Ja | Ja |
> | enheter | Nej | Nej |
> | Exporteras | Nej | Nej |
> | externalsubscriptions | Nej | Nej |
> | prognostisering | Nej | Nej |
> | query | Nej | Nej |
> | Reportconfigs | Nej | Nej |
> | rapporter | Nej | Nej |
> | showbackrules | Nej | Nej |
> | vyer | Nej | Nej |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | NAV | Nej | Nej |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | typer | Nej | Nej |
> | resourceproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Utskrifts | Nej | Nej |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Nej | Nej |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbets ytor | Nej | Nej |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kataloger | Ja | Ja |
> | datacatalogs | Nej | Nej |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nej | Nej |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | distributionspaket | Nej | Nej |
> | utgå | Nej | Nej |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datafactories | Ja | Ja |
> | fabriker | Ja | Ja |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nej | Nej |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | services | Nej | Nej |
> | tjänster/projekt | Nej | Nej |
> | lots | Nej | Nej |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nej | Nej |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | brygghuvudservrar | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | brygghuvudservrar | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | servergroups | Nej | Nej |
> | brygghuvudservrar | Ja | Ja |
> | serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ja | Ja |
> | distributioner | Ja | Ja |
> | servicetopologies | Ja | Ja |
> | servicetopologies/tjänster | Ja | Ja |
> | servicetopologies/tjänster/serviceunits | Ja | Ja |
> | steg | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nej | Nej |
> | elasticpools / iothubtenants | Nej | Nej |
> | iothubs | Ja | Ja |
> | provisioningservices | Ja | Ja |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | pipelines | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domänkontrollanter | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labcenters | Nej | Nej |
> | Laboration | Ja | Nej |
> | labb/miljöer | Ja | Ja |
> | labb/servicerunners | Ja | Ja |
> | labb/virtualmachines | Ja | Nej |
> | scheman | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nej | Nej |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domäner | Ja | Ja |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domäner | Ja | Ja |
> | eventSubscriptions | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. |
> | eventsubscriptions | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. | Nej-kan inte flyttas separat utan att automatiskt flyttas med den prenumererade resursen. |
> | extensiontopics | Nej | Nej |
> | avsnitt | Ja | Ja |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |
> | namn områden | Ja | Ja |

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | guestconfigurationassignments | Nej | Nej |
> | IntelliPoint | Nej | Nej |
> | softwareupdateprofile | Nej | Nej |
> | softwareupdates | Nej | Nej |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nej | Nej |
> | sapmonitors | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |

> [!IMPORTANT]
> Du kan flytta HDInsight-kluster till en ny prenumeration eller resursgrupp. Men kan inte du flytta mellan prenumerationer som nätverksresurser som är länkad till HDInsight-klustret (till exempel virtuella nätverk, nätverkskort eller belastningsutjämnare). Dessutom kan flytta du inte till en ny resursgrupp ett nätverkskort som är kopplad till en virtuell dator för klustret.
>
> När du flyttar ett HDInsight-kluster till en ny prenumeration först flytta andra resurser (t.ex. storage-konto). Flytta sedan HDInsight-klustret ensamt.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | faxar | Ja | Ja |
> | datorer/tillägg | Nej | Nej |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Utskrifts | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights

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
> | diagnosticsettings | Nej | Nej |
> | diagnosticsettingscategories | Nej | Nej |
> | eventtypes | Nej | Nej |
> | extendeddiagnosticsettings | Nej | Nej |
> | logdefinitions | Nej | Nej |
> | loggar | Nej | Nej |
> | metricalerts | Nej | Nej |
> | metricbaselines | Nej | Nej |
> | metricdefinitions | Nej | Nej |
> | metricnamespaces | Nej | Nej |
> | metrics | Nej | Nej |
> | Mina arbets böcker | Nej | Nej |
> | privatelinkscopes | Ja | Ja |
> | scheduledqueryrules | Ja | Ja |
> | topologi | Nej | Nej |
> | transaktioner | Nej | Nej |
> | vminsightsonboardingstatuses | Nej | Nej |
> | webbtester | Ja | Ja |
> | arbets böcker | Ja | Ja |
> | workbooktemplates | Ja | Ja |

> [!IMPORTANT]
> Se till att flytta till den nya prenumerationen inte överskrider [prenumerations kvoterna](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Ja | Ja |
> | graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | valv | Ja | Ja |

> [!IMPORTANT]
> Nyckel valv som används för disk kryptering kan inte flyttas till en resurs grupp i samma prenumeration eller mellan prenumerationer.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Nej | Nej |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nej | Nej |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nej | Nej |
> | integrationaccounts | Ja | Ja |
> | integrationserviceenvironments | Ja | Nej |
> | integrationserviceenvironments/managedapis | Ja | Nej |
> | isolatedenvironments | Nej | Nej |
> | arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Ja | Ja |
> | WebServices | Ja | Nej |
> | arbets ytor | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nej | Nej |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |
> | konton/arbets ytor | Nej | Nej |
> | konton/arbets ytor/projekt | Nej | Nej |
> | teamaccounts | Nej | Nej |
> | teamaccounts/arbets ytor | Nej | Nej |
> | teamaccounts/arbets ytor/projekt | Nej | Nej |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Nej | Nej |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbets ytor | Nej | Nej |
> | arbets ytor/beräkningar | Nej | Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | identiteter | Nej | Nej |
> | userassignedidentities | Nej | Nej |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | registrationassignments | Nej | Nej |
> | registrationdefinitions | Nej | Nej |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nej | Nej |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Media Services | Ja | Ja |
> | Media Services/liveevents | Ja | Ja |
> | Media Services/strömnings slut punkter | Ja | Ja |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | appclusters | Nej | Nej |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Ja | Ja |
> | migrateprojects | Ja | Ja |
> | samarbetsprojekt | Nej | Nej |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nej | Nej |
> | netappaccounts / backuppolicies | Nej | Nej |
> | netappaccounts / capacitypools | Nej | Nej |
> | netappaccounts/capacitypools/Volumes | Nej | Nej |
> | netappaccounts/capacitypools/Volumes/mounttargets | Nej | Nej |
> | netappaccounts/capacitypools/volym/ögonblicks bilder | Nej | Nej |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nej | Nej |
> | applicationgatewaywebapplicationfirewallpolicies | Nej | Nej |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewalls | Ja | Ja |
> | bastionhosts | Nej | Nej |
> | anslutning | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Nej | Nej |
> | dnszones | Ja | Ja |
> | expressroutecircuits | Nej | Nej |
> | expressroutegateways | Nej | Nej |
> | firewallpolicies | Ja | Ja |
> | frontdoors | Nej | Nej |
> | frontdoorwebapplicationfirewallpolicies | Nej | Nej |
> | ipgroups | Ja | Ja |
> | belastningsutjämnare | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
> | localnetworkgateways | Ja | Ja |
> | networkexperimentprofiles | Ja | Ja |
> | networkintentpolicies | Ja | Ja |
> | NetworkInterfaces | Ja | Ja |
> | networkprofiles | Nej | Nej |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Ja | Nej |
> | networkwatchers / connectionmonitors | Ja | Nej |
> | networkwatchers / flowlogs | Ja | Nej |
> | networkwatchers/linser | Ja | Nej |
> | networkwatchers / pingmeshes | Ja | Nej |
> | p2svpngateways | Nej | Nej |
> | privatednszones | Ja | Ja |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privateendpointredirectmaps | Nej | Nej |
> | privateendpoints | Nej | Nej |
> | privatelinkservices | Nej | Nej |
> | publicipaddresses | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
> | publicipprefixes | Ja | Ja |
> | routefilters | Nej | Nej |
> | routetables | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagerprofiles | Ja | Ja |
> | virtualhubs | Nej | Nej |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Nej | Nej |
> | virtualrouters | Ja | Ja |
> | virtualwans | Nej | Nej |
> | vpngateways (virtuellt WAN) | Nej | Nej |
> | vpnserverconfigurations | Nej | Nej |
> | vpnsites (virtuellt WAN) | Nej | Nej |
> | webapplicationfirewallpolicies | Ja | Ja |

> [!IMPORTANT]
> Se [rikt linjer för nätverks flytt](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |
> | namnrymder/notificationhubs | Ja | Ja |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | Nej | Nej |
> | arbets ytor | Ja | Ja |

> [!IMPORTANT]
> Se till att flytta till den nya prenumerationen inte överskrider [prenumerations kvoterna](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nej | Nej |
> | managementconfigurations | Ja | Ja |
> | lösningar | Ja | Ja |
> | vyer | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | peerings | Ja | Ja |
> | peeringservices | Nej | Nej |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | policyevents | Nej | Nej |
> | policystates | Nej | Nej |
> | policytrackedresources | Nej | Nej |
> | reparationer | Nej | Nej |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | instrument paneler | Ja | Ja |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | rootresources | Nej | Nej |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kapaciteter | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Nej | Nej |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | distributioner | Nej | Nej |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nej | Nej |
> | replicationeligibilityresults | Nej | Nej |
> | valv | Ja | Ja |

> [!IMPORTANT]
> Se [Recovery Services flytta vägledning](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | frågor | Ja | Ja |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Nej | Nej |
> | childavailabilitystatuses | Nej | Nej |
> | childresources | Nej | Nej |
> | händelser | Nej | Nej |
> | ansökningar | Nej | Nej |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Nej | Nej |
> | Länkar | Nej | Nej |
> | taggar | Nej | Nej |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Nej |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | searchservices | Ja | Ja |

> [!IMPORTANT]
> Du kan inte flytta flera Sök resurser i olika regioner i samma åtgärd. Flytta i stället dem i separata åtgärder.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nej | Nej |
> | advancedthreatprotectionsettings | Nej | Nej |
> | assessmentmetadata | Nej | Nej |
> | utvärderingar | Nej | Nej |
> | automatiseringar | Ja | Ja |
> | complianceresults | Nej | Nej |
> | Godkännanden | Nej | Nej |
> | datacollectionagents | Nej | Nej |
> | devicesecuritygroups | Nej | Nej |
> | informationprotectionpolicies | Nej | Nej |
> | iotsecuritysolutions | Ja | Ja |
> | servervulnerabilityassessments | Nej | Nej |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | agg regeringar | Nej | Nej |
> | alertrules | Nej | Nej |
> | alertruletemplates | Nej | Nej |
> | Dina | Nej | Nej |
> | fall | Nej | Nej |
> | dataconnectors | Nej | Nej |
> | dataconnectorscheckrequirements | Nej | Nej |
> | poster | Nej | Nej |
> | entityqueries | Nej | Nej |
> | incidenter | Nej | Nej |
> | officeconsents | Nej | Nej |
> | standardklient | Nej | Nej |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | gatewayer | Nej | Nej |
> | artikelnoder | Nej | Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Nej | Nej |
> | kluster | Ja | Ja |
> | kluster/program | Nej | Nej |
> | containergroups | Nej | Nej |
> | containergroupsets | Nej | Nej |
> | edgeclusters | Nej | Nej |
> | managedclusters | Nej | Nej |
> | nätet | Nej | Nej |
> | secretstores | Nej | Nej |
> | volymer | Nej | Nej |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Ja |
> | gatewayer | Ja | Ja |
> | nätet | Ja | Ja |
> | hemligheter | Ja | Ja |
> | volymer | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | distributioner | Nej | Nej |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | SignalR | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nej | Nej |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nej | Nej |
> | program | Nej | Nej |
> | jitrequests | Nej | Nej |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | instancepools | Nej | Nej |
> | managedinstances | Nej | Nej |
> | managedinstances/databaser | Nej | Nej |
> | brygghuvudservrar | Ja | Ja |
> | servrar/databaser | Ja | Ja |
> | servrar/elasticpools | Ja | Ja |
> | servrar/jobaccounts | Ja | Ja |
> | servrar/jobagents | Ja | Ja |
> | virtualclusters | Ja | Ja |

> [!IMPORTANT]
> En databas och en server måste finnas i samma resurs grupp. Om du flyttar en SQLServer, flyttas även alla dess databaser. Det här beteendet gäller för Azure SQL Database och Azure SQL Data Warehouse-databaser.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Ja | Ja |
> | sqlvirtualmachines | Ja | Ja |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dwvm | Nej | Nej |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ja | Ja |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nej | Nej |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nej | Nej |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hantera | Nej | Nej |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Ja | Ja |

> [!IMPORTANT]
> Det går inte att flytta Stream Analytics jobb i körnings läge.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utrymmen | Nej | Nej |
> | miljöer/eventsources | Nej | Nej |
> | pipe | Nej | Nej |
> | instanser/miljöer | Nej | Nej |
> | instanser/miljöer/eventsources | Nej | Nej |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | createsubscription | Nej | Nej |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | supporttickets | Nej | Nej |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nej | Nej |
> | resurser | Nej | Nej |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utrymmen | Ja | Ja |
> | miljöer/eventsources | Ja | Ja |
> | miljöer/referencedatasets | Ja | Ja |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Auktoriseringshanteraren | Ja | Ja |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konto | Nej | Nej |
> | konto/tillägg | Ja | Ja |
> | konto/projekt | Ja | Ja |

> [!IMPORTANT]
> Information om hur du ändrar prenumerationen för Azure-DevOps finns i [ändra den Azure-prenumeration som används för fakturering](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nej | Nej |
> | dedicatedcloudservices | Nej | Nej |
> | virtualmachines | Nej | Nej |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | utgå | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | intyg | Nej | Ja |
> | connectiongateways | Ja | Ja |
> | anslutning | Ja | Ja |
> | customapis | Ja | Ja |
> | hostingenvironments | Nej | Nej |
> | Server grupper | Ja | Ja |
> | stationer | Ja | Ja |
> | platser/premieraddons | Ja | Ja |
> | platser/platser | Ja | Ja |
> | staticsites | Nej | Nej |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nej | Nej |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | delarna | Nej | Nej |
> | monitorinstances | Nej | Nej |
> | Övervakare | Nej | Nej |
> | notificationsettings | Nej | Nej |

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.

## <a name="next-steps"></a>Nästa steg
För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](move-resource-group-and-subscription.md).

Hämta samma data som en fil med kommaavgränsade värden genom att ladda ned [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
