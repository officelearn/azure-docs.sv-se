---
title: Flytta åtgärds stöd efter resurs typ
description: Visar en lista över de Azure-resurs typer som kan flyttas till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 65f50fe63485c2538cdef3d144c4d77824d95f56
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659397"
---
# <a name="move-operation-support-for-resources"></a>Åtgärds stöd för flytt av resurser
Den här artikeln visar om en Azure-resurs har stöd för flytt åtgärden. Den innehåller också information om särskilda villkor att tänka på när du flyttar en resurs.

Hoppa till ett namn område för en resurs leverantör:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domainservices | Inga | Inga |
> | domainservices / replicasets | Inga | Inga |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | klienter | Inga | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | actionrules | Ja | Ja |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | servrar | Ja | Ja |

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

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | apiapps | Inga | Inga |
> | appidentities | Inga | Inga |
> | gatewayer | Inga | Inga |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | policyassignments | Inga | Inga |

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
> | sqlserverregistrations | Inga | Inga |

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
> | kluster | Inga | Inga |
> | fileservers | Inga | Inga |
> | utskrifts | Inga | Inga |
> | arbetsytor | Inga | Inga |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | mapapis | Inga | Inga |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | biztalk | Ja | Ja |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Ja | Ja |
> | tittare | Inga | Inga |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Inga | Inga |

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
> | cdnwebapplicationfirewallpolicies | Inga | Inga |
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
> | domän namn | Ja | Inga |
> | virtualmachines | Ja | Inga |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Inga | Inga |
> | reservedips | Inga | Inga |
> | virtualnetworks | Inga | Inga |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ja | Inga |

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
> | diskencryptionsets | Inga | Inga |
> | disk | Ja | Ja |
> | gallerier | Inga | Inga |
> | gallerier/bilder | Inga | Inga |
> | gallerier/avbildningar/versioner | Inga | Inga |
> | hostgroups | Inga | Inga |
> | hostgroups/värdar | Inga | Inga |
> | images | Ja | Ja |
> | proximityplacementgroups | Inga | Inga |
> | restorepointcollections | Inga | Inga |
> | sharedvmimages | Inga | Inga |
> | sharedvmimages/versioner | Inga | Inga |
> | snapshots | Ja | Ja |
> | virtualmachines | Ja | Ja |
> | virtualmachines/tillägg | Ja | Ja |
> | virtualmachinescalesets | Ja | Ja |

> [!IMPORTANT]
> Se [Virtual Machines flytta vägledning](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containergroups | Inga | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containergroups | Inga | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | register | Ja | Ja |
> | register/buildtasks | Ja | Ja |
> | register/replikeringar | Ja | Ja |
> | register/uppgifter | Ja | Ja |
> | register/Webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containerservices | Inga | Inga |
> | managedclusters | Inga | Inga |
> | openshiftmanagedclusters | Inga | Inga |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | anslutningar | Ja | Ja |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | nav | Ja | Ja |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | resourceproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utskrifts | Inga | Inga |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Inga | Inga |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Inga | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kataloger | Ja | Ja |
> | datacatalogs | Inga | Inga |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Inga | Inga |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | distributionspaket | Inga | Inga |
> | utgå | Inga | Inga |

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
> | datalakeaccounts | Inga | Inga |

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
> | services | Inga | Inga |
> | tjänster/projekt | Inga | Inga |
> | lots | Inga | Inga |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | servrar | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | servrar | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | servergroups | Inga | Inga |
> | servrar | Ja | Ja |
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
> | elasticpools | Inga | Inga |
> | elasticpools / iothubtenants | Inga | Inga |
> | iothubs | Ja | Ja |
> | provisioningservices | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domänkontrollanter | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labcenters | Inga | Inga |
> | Laboration | Ja | Inga |
> | labb/miljöer | Ja | Ja |
> | labb/servicerunners | Ja | Ja |
> | labb/virtualmachines | Ja | Inga |
> | scheman | Ja | Ja |

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
> | Avsnitt om | Ja | Ja |

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
> | konton | Inga | Inga |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hanainstances | Inga | Inga |
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
> | faxar | Inga | Inga |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | utskrifts | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |
> | actiongroups | Ja | Ja |
> | activitylogalerts | Inga | Inga |
> | alertrules | Ja | Ja |
> | autoscalesettings | Ja | Ja |
> | delarna | Ja | Ja |
> | guestdiagnosticsettings | Inga | Inga |
> | metricalerts | Inga | Inga |
> | notificationgroups | Inga | Inga |
> | notificationrules | Inga | Inga |
> | scheduledqueryrules | Ja | Ja |
> | webbtester | Ja | Ja |
> | arbetsböcker | Ja | Ja |

> [!IMPORTANT]
> Se till att flytta till den nya prenumerationen inte överskrider [prenumerations kvoterna](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits).

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
> | hsmpools | Inga | Inga |
> | valv | Ja | Ja |

> [!IMPORTANT]
> Nyckel valv som används för disk kryptering kan inte flyttas till en resurs grupp i samma prenumeration eller mellan prenumerationer.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kluster | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labaccounts | Inga | Inga |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Inga | Inga |
> | integrationaccounts | Ja | Ja |
> | integrationserviceenvironments | Inga | Inga |
> | isolatedenvironments | Inga | Inga |
> | arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Ja | Ja |
> | WebServices | Ja | Inga |
> | arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Ja | Ja |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |
> | konton/arbets ytor | Inga | Inga |
> | konton/arbets ytor/projekt | Inga | Inga |
> | teamaccounts | Inga | Inga |
> | teamaccounts/arbets ytor | Inga | Inga |
> | teamaccounts/arbets ytor/projekt | Inga | Inga |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Inga | Inga |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Inga | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | Inga | Inga |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Inga | Inga |

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
> | appclusters | Inga | Inga |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Inga | Inga |
> | migrateprojects | Inga | Inga |
> | projekt | Inga | Inga |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Inga | Inga |
> | netappaccounts / capacitypools | Inga | Inga |
> | netappaccounts/capacitypools/Volumes | Inga | Inga |
> | netappaccounts/capacitypools/Volumes/mounttargets | Inga | Inga |
> | netappaccounts/capacitypools/volym/ögonblicks bilder | Inga | Inga |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Inga | Inga |
> | applicationgatewaywebapplicationfirewallpolicies | Inga | Inga |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewalls | Ja | Ja |
> | bastionhosts | Inga | Inga |
> | anslutning | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Inga | Inga |
> | dnszones | Ja | Ja |
> | expressroutecircuits | Inga | Inga |
> | expressroutecrossconnections | Inga | Inga |
> | expressroutegateways | Inga | Inga |
> | expressrouteports | Inga | Inga |
> | frontdoors | Inga | Inga |
> | frontdoorwebapplicationfirewallpolicies | Inga | Inga |
> | belastningsutjämnare | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
> | localnetworkgateways | Ja | Ja |
> | natgateways | Ja | Ja |
> | networkintentpolicies | Ja | Ja |
> | NetworkInterfaces | Ja | Ja |
> | networkprofiles | Inga | Inga |
> | networksecuritygroups | Ja | Ja |
> | networkwatchers | Ja | Ja |
> | networkwatchers / connectionmonitors | Ja | Ja |
> | networkwatchers/linser | Ja | Ja |
> | networkwatchers / pingmeshes | Ja | Ja |
> | p2svpngateways | Inga | Inga |
> | privatednszones | Ja | Ja |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privateendpoints | Inga | Inga |
> | privatelinkservices | Inga | Inga |
> | publicipaddresses | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
> | publicipprefixes | Ja | Ja |
> | routefilters | Inga | Inga |
> | routetables | Ja | Ja |
> | securegateways | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagerprofiles | Ja | Ja |
> | virtualhubs | Inga | Inga |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Inga | Inga |
> | virtualwans | Inga | Inga |
> | vpngateways (virtuellt WAN) | Inga | Inga |
> | vpnsites (virtuellt WAN) | Inga | Inga |
> | webapplicationfirewallpolicies | Ja | Ja |

> [!IMPORTANT]
> Se [rikt linjer för nätverks flytt](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |
> | namnrymder/notificationhubs | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Ja | Ja |

> [!IMPORTANT]
> Se till att flytta till den nya prenumerationen inte överskrider [prenumerations kvoterna](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Ja | Ja |
> | lösningar | Ja | Ja |
> | visningar | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | peerings | Inga | Inga |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | instrumentpaneler | Ja | Ja |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | rootresources | Inga | Inga |

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

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
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

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Inga |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | flows | Ja | Ja |
> | förfrågningsåtgärder | Ja | Ja |

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
> | iotsecuritysolutions | Ja | Ja |
> | playbookconfigurations | Inga | Inga |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | gatewayer | Inga | Inga |
> | noder | Inga | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | namn områden | Ja | Ja |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Inga | Inga |
> | kluster | Ja | Ja |
> | kluster/program | Inga | Inga |
> | containergroups | Inga | Inga |
> | containergroupsets | Inga | Inga |
> | edgeclusters | Inga | Inga |
> | nätet | Inga | Inga |
> | secretstores | Inga | Inga |
> | volumes | Inga | Inga |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Ja |
> | containergroups | Inga | Inga |
> | gatewayer | Ja | Ja |
> | nätet | Ja | Ja |
> | secrets | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | SignalR | Ja | Ja |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Inga | Inga |
> | redskap | Inga | Inga |
> | applicationdefinitions | Inga | Inga |
> | program | Inga | Inga |
> | jitrequests | Inga | Inga |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | instancepools | Inga | Inga |
> | managedinstances | Inga | Inga |
> | managedinstances/databaser | Inga | Inga |
> | servrar | Ja | Ja |
> | servrar/databaser | Ja | Ja |
> | servrar/elasticpools | Ja | Ja |
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
> | dwvm | Inga | Inga |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ja | Ja |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cacheminnen | Inga | Inga |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Inga | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Inga | Inga |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hantera | Inga | Inga |

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
> | utrymmen | Inga | Inga |
> | miljöer/eventsources | Inga | Inga |
> | instanser | Inga | Inga |
> | instanser/miljöer | Inga | Inga |
> | instanser/miljöer/eventsources | Inga | Inga |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Inga | Inga |
> | resurser | Inga | Inga |

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
> | Auktoriseringshanteraren | Inga | Inga |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Inga | Inga |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Inga | Inga |
> | dedicatedcloudservices | Inga | Inga |
> | virtualmachines | Inga | Inga |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | certifikat | Inga | Ja |
> | connectiongateways | Ja | Ja |
> | anslutning | Ja | Ja |
> | customapis | Ja | Ja |
> | hostingenvironments | Inga | Inga |
> | Server grupper | Ja | Ja |
> | webbplatser | Ja | Ja |
> | platser/premieraddons | Ja | Ja |
> | platser/platser | Ja | Ja |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | deviceservices | Inga | Inga |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Inga | Inga |
> | hostpools | Inga | Inga |
> | arbetsytor | Inga | Inga |

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.

## <a name="next-steps"></a>Nästa steg
För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](move-resource-group-and-subscription.md).

Hämta samma data som en fil med kommaavgränsade värden genom att ladda ned [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
