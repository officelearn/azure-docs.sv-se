---
title: Stöd för att flytta Azure-resurser mellan regioner
description: Visar en lista över de Azure-resurs typer som kan flyttas mellan Azure-regioner
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 608d9511b14ef8dd3815d6f9b45cda31e6b38b94
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004314"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Stöd för att flytta Azure-resurser mellan regioner

I den här artikeln bekräftas om en Azure-resurs har stöd för att flytta till en annan Azure-region. 

Hoppa till ett namn område för en resurs leverantör:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. API Management](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft. Bingkartssökning](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. skiss](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
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
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. genomik](#microsoftgenomics)
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
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrate](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
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
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | domainservices | Inga | 
> | domainservices / replicasets | Inga | 

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | klienter | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | actionrules | Inga | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | brygghuvudservrar | Inga |

## <a name="microsoftapimanagement"></a>Microsoft. API Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | tjänst |  Ja (med mall) <br/><br/> [Flytta API Management över flera regioner](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | configurationstores | Inga | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | apiapps | Ja (med mall)<br/><br/> [Flytta en App Service app till en annan region](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Inga | 
> | gatewayer | Inga | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | policyassignments | Inga |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | automationaccounts | Ja (med mall) <br/><br/> [Använda geo-replikering](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfigurationer | Inga | 
> | automationaccounts/Runbooks | Inga | 



## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | b2cdirectories | Inga | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | sqlserverregistrations | Inga |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | registreringar | Inga | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | batchaccounts |  Batch-konton kan inte flyttas direkt från en region till en annan, men du kan använda en mall för att exportera en mall, ändra den och distribuera mallen till den nya regionen. <br/><br/> Lär dig mer om att [Flytta ett batch-konto över flera regioner](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Inga <br/><br/> Tjänsten Azure Batch AI har [dragits tillbaka](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai).
> | fileservers | Inga | 
> | utskrifts | Inga | 
> | arbetsytor | Inga | 

## <a name="microsoftbingmaps"></a>Microsoft. Bingkartssökning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | mapapis | Inga | 

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | biztalk | Inga | 

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blockchainmembers | Inga <br/><br/> Blockchain-nätverket kan inte ha noder i olika regioner. 
> | Övervakare | Inga | 

## <a name="microsoftblueprint"></a>Microsoft. skiss

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blueprintassignments | Inga | 

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | botservices | Inga | 

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Redis | Inga | 


## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Inga |
> | filer | Inga | 
> | profiler/slut punkter | Inga | 

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | certificateorders | Inga | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domän namn | Inget arbete har planer ATS för klassiska tjänster.
> | virtualmachines | Inga | 



## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | networksecuritygroups | Inget arbete har planer ATS för klassiska tjänster.
> | reservedips | Inga | 
> | virtualnetworks | Inga | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storageaccounts | Ja |  


## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 
> | Cognitive Search | Stöds med manuella steg.<br/><br/> Läs om hur du [flyttar din Azure kognitiv sökning-tjänst till en annan region](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | availabilitysets | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta tillgänglighets uppsättningar. | 
> | diskencryptionsets | Inga | 
> | disk | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta virtuella Azure-datorer och relaterade diskar. | 
> | gallerier | Inga | 
> | gallerier/bilder | Inga | 
> | gallerier/avbildningar/versioner | Inga | 
> | hostgroups | Inga | 
> | hostgroups/värdar | Inga | 
> | images | Inga | 
> | proximityplacementgroups | Inga | 
> | restorepointcollections | Inga | 
> | sharedvmimages | Inga | 
> | sharedvmimages/versioner | Inga | 
> | snapshots | Inga | 
> | virtualmachines | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta virtuella Azure-datorer. | 
> | virtualmachines/tillägg | Inga | 
> | virtualmachinescalesets | Inga | 

## <a name="microsoftcontainer"></a>Microsoft. container

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containergroups | Inga | 

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containergroups | Inga | 

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | register | Inga |  
> | register/buildtasks | Inga |  
> | register/replikeringar | Inga | 
> | register/uppgifter | Inga |  
> | register/Webhooks | Inga | 

## <a name="microsoftcontainerservice"></a>Microsoft. container service

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containerservices | Nej.<br/><br/> Tjänsten har [dragits tillbaka](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/).
> | managedclusters | Inga | 
> | openshiftmanagedclusters | Inga | 

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program | Inga | 

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | anslutningar | Inga |  

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | nav | Inga |  

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | resourceproviders | Inga | 

## <a name="microsoftdatabox"></a>Microsoft. data-

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utskrifts | Inga | 

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | databoxedgedevices | Inga | 

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytor | Inga | 

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kataloger | Inga | 
> | datacatalogs | Inga | 

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | connectionmanagers | Inga | 

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | distributionspaket | Inga | 
> | utgå | Inga | 

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datafactories | Inga | 
> | fabriker | Inga |  

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datalakeaccounts | Inga | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftdatamigration"></a>Microsoft. data migration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Inga | 
> | tjänster/projekt | Inga | 
> | lots | Inga | 

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | brygghuvudservrar | Du kan använda en skrivskyddad replik i flera regioner för att flytta en befintlig server. [Läs mer](../../postgresql/howto-move-regions-portal.md).<br/><br/> Om tjänsten är etablerad med Geo-redundant lagring, kan du använda geo-återställning för att återställa i andra regioner. [Läs mer](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | brygghuvudservrar | Du kan använda en skrivskyddad replik i flera regioner för att flytta en befintlig server. [Läs mer](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servergroups | Inga | 
> | brygghuvudservrar | Du kan använda en skrivskyddad replik i flera regioner för att flytta en befintlig server. [Lär dig mer](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Inga | 

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | artifactsources | Inga | 
> | distributioner | Inga |  
> | servicetopologies | Inga | 
> | servicetopologies/tjänster | Inga |  
> | servicetopologies/tjänster/serviceunits | Inga | 
> | steg | Inga | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | elasticpools | Nej. Resursen är inte exponerad.
> | elasticpools / iothubtenants | Nej. Resursen är inte exponerad.
> | iothubs | Ja. [Läs mer](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Inga | 

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domänkontrollanter | Inga | 
> | AKS-kluster | Inga<br/><br/> [Läs mer](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) om att flytta till en annan region.

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labcenters | Inga | 
> | Laboration | Inga | 
> | labb/miljöer | Inga |  
> | labb/servicerunners | Inga | 
> | labb/virtualmachines | Inga |  
> | scheman | Inga |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | databaseaccounts | Inga | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domäner | Inga | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Inga |  

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domäner | Inga |  
> | avsnitt | Inga | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Inga |  
> | namn områden | Ja (med mall)<br/><br/> [Flytta en Event Hub-namnrymd till en annan region](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hanainstances | Inga | 
> | sapmonitors | Inga |  

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Inga | 

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Inga |  

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | faxar | Inga | 

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datamanagers |  Inga | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utskrifts |  Inga | 

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 
> | actiongroups |  Inga | 
> | activitylogalerts | Inga | 
> | alertrules |  Inga | 
> | autoscalesettings |  Inga | 
> | delarna |  Inga |  
> | guestdiagnosticsettings | Inga | 
> | metricalerts | Inga | 
> | notificationgroups | Inga | 
> | notificationrules | Inga | 
> | scheduledqueryrules |  Inga | 
> | webbtester |  Inga | 
> | arbetsböcker |  Inga |  


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | checknameavailability |  Nej.<br/><br/> IoT Central fungerar med geografiska områden och inte regioner.
> | Rita | Inga

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> |  iothub |  Ja (klon hubb) <br/><br/> [Klona en IoT-hubb till en annan region](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | checknameavailability |  Inga |  
> | Rita |  Inga | 

## <a name="microsoftkeyvault"></a>Microsoft. nyckel valv

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hsmpools | Inga | 
> | valv |  Inga | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster |  Inga |  

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labaccounts | Inga | 

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftlocationservices"></a>Microsoft. filen LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej, det är en global tjänst.

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hostingenvironments | Inga | 
> | integrationaccounts |  Inga |  
> | integrationserviceenvironments | Inga | 
> | isolatedenvironments | Inga | 
> | arbetsflöden |  Inga |  

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | commitmentplans |  Inga | 
> | WebServices |  Inga | 
> | arbetsytor |  Inga | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | operationalizationclusters |  Inga | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 
> | konton/arbets ytor | Inga | 
> | konton/arbets ytor/projekt | Inga | 
> | teamaccounts | Inga | 
> | teamaccounts/arbets ytor | Inga | 
> | teamaccounts/arbets ytor/projekt | Inga | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hostingaccounts | Inga | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytor | Inga | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | userassignedidentities | Inga | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton |  Nej, Azure Maps är en Geospatial tjänst. 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | classicdevservices | Inget arbete har planer ATS för klassiska tjänster 

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Media Services |  Inga | 
> | Media Services/liveevents |  Inga | 
> | Media Services/strömnings slut punkter |  Inga | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | appclusters | Inga | 

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | assessmentprojects | Inga | 
> | migrateprojects | Inga | 
> | samarbetsprojekt | Inga | 

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | netappaccounts | Inga | 
> | netappaccounts / capacitypools | Inga | 
> | netappaccounts/capacitypools/Volumes | Inga | 
> | netappaccounts/capacitypools/Volumes/mounttargets | Inga | 
> | netappaccounts/capacitypools/volym/ögonblicks bilder | Inga | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | applicationgateways | Inga | 
> | applicationgatewaywebapplicationfirewallpolicies | Inga | 
> | applicationsecuritygroups |  Inga |  
> | azurefirewalls |  Inga |  
> | bastionhosts | Inga | 
> | anslutning |  Inga | 
> | ddoscustompolicies |  Inga | 
> | ddosprotectionplans | Inga | 
> | dnszones |  Inga | 
> | expressroutecircuits | Inga | 
> | expressroutecrossconnections | Inga | 
> | expressroutegateways | Inga | 
> | expressrouteports | Inga | 
> | frontdoors | Inga | 
> | frontdoorwebapplicationfirewallpolicies | Inga | 
> | belastningsutjämnare | Ja <br/><br/> Använd [Azure Resource](../../resource-mover/tutorial-move-region-virtual-machines.md) -arbetsbelastning för att flytta interna och externa belastningsutjämnare. |
> | localnetworkgateways |  Inga | 
> | natgateways |  Inga | 
> | networkintentpolicies |  Inga | 
> | NetworkInterfaces | Ja <br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta nätverkskort. | 
> | networkprofiles | Inga | 
> | networksecuritygroups | Ja <br/><br/> Använd [Azure resurs förflyttning](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta nätverks säkerhets grupper (NGSs). | 
> | networkwatchers |  Inga |  
> | networkwatchers / connectionmonitors |  Inga | 
> | networkwatchers/linser |  Inga | 
> | networkwatchers / pingmeshes |  Inga | 
> | p2svpngateways | Inga | 
> | privatednszones |  Inga |  
> | privatednszones / virtualnetworklinks |  Inga |  
> | privateendpoints | Inga | 
> | privatelinkservices | Inga | 
> | publicipaddresses | Ja<br/><br/> Använd [Azure Resource-arbetskraft](../../resource-mover/tutorial-move-region-virtual-machines.md) för att flytta offentliga IP-adresser. |
> | publicipprefixes | Inga | 
> | routefilters | Inga | 
> | routetables |  Inga | 
> | serviceendpointpolicies |  Inga | 
> | trafficmanagerprofiles |  Inga | 
> | virtualhubs | Inga | 
> | virtualnetworkgateways |  Inga |  
> | virtualnetworks |  Inga | 
> | virtualnetworktaps | Inga | 
> | virtualwans | Inga | 
> | vpngateways (virtuellt WAN) | Inga | 
> | vpnsites (virtuellt WAN) | Inga | 
> | webapplicationfirewallpolicies |  Inga | 


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Inga | 
> | namnrymder/notificationhubs |  Inga |  

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytor |  Inga | 



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | managementconfigurations |  Inga | 
> | vyer |  Inga | 

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | peerings | Inga | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | instrumentpaneler | Inga | 

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | rootresources | Inga | 

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | workspacecollections |  Inga | 

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kapaciteter |  Inga | 

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | valv | Nej.<br/><br/> Det finns inte stöd för att flytta Recovery Services valv för Azure Backup i Azure-regioner.<br/><br/> I Recovery Services valv för Azure Site Recovery kan du [inaktivera och återskapa valvet](../../site-recovery/move-vaults-across-regions.md) i mål regionen. | 


## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Inga | 

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | skickar |  Inga |  

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region |
> | ------------- | ----------- |
> | deploymentScripts |  Ja<br/><br/>[Flytta Microsoft. Resources-resurser till ny region](microsoft-resources-move-regions.md) |
> | templateSpecs |  Ja<br/><br/>[Flytta Microsoft. Resources-resurser till ny region](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program |  Inga | 

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | flows |  Inga |  
> | förfrågningsåtgärder |  Inga | 

## <a name="microsoftsearch"></a>Microsoft. search

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | searchservices |  Inga | 


## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Inga | 
> | playbookconfigurations | Inga | 

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | gatewayer | Inga | 
> | artikelnoder | Inga | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Inga | 

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program | Inga | 
> | kluster |  Inga | 
> | kluster/program | Inga | 
> | containergroups | Inga | 
> | containergroupsets | Inga | 
> | edgeclusters | Inga | 
> | nätet | Inga | 
> | secretstores | Inga | 
> | volumes | Inga | 

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program |  Inga | 
> | containergroups | Inga | 
> | gatewayer |  Inga | 
> | nätet |  Inga | 
> | secrets |  Inga | 
> | volumes |  Inga |  

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | SignalR |  Inga |  

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | appliancedefinitions | Inga | 
> | redskap | Inga | 
> | applicationdefinitions | Inga | 
> | program | Inga | 
> | jitrequests | Inga | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | instancepools | Inga | 
> | managedinstances | Ja <br/><br/> [Lär dig mer](/azure/azure-sql/database/move-resources-across-regions) om att flytta hanterade instanser i flera regioner. | 
> | managedinstances/databaser | Ja | 
> | brygghuvudservrar | Ja | 
> | servrar/databaser | Ja <br/><br/> [Lär dig mer](/azure/azure-sql/database/move-resources-across-regions) om att flytta databaser över flera regioner.<br/><br/> [Lär dig mer](../../resource-mover/tutorial-move-region-sql.md) om hur du använder Azure Resource-arbetskraft för att flytta Azure SQL-databaser.  | 
> | servrar/elasticpools | Ja <br/><br/> [Lär dig mer](/azure/azure-sql/database/move-resources-across-regions) om att flytta elastiska pooler över flera regioner.<br/><br/> [Läs mer](../../resource-mover/tutorial-move-region-sql.md) om hur du använder Azure Resource-arbetskraft för att flytta elastiska Azure SQL-pooler.  | 
> | virtualclusters | Ja | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Inga |  
> | sqlvirtualmachines |  Inga |  

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | dwvm | Inga | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storageaccounts | Ja<br/><br/> [Flytta ett Azure Storage-konto till en annan region](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cacheminnen | Inga | 

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices |  Inga | 

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices | Inga | 

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices | Inga | 

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hantera | Inga | 

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | streamingjobs |  Inga |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utrymmen | Inga | 
> | miljöer/eventsources | Inga | 
> | pipe | Inga | 
> | instanser/miljöer | Inga | 
> | instanser/miljöer/eventsources | Inga | 

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | providerregistrations | Inga | 
> | resources | Inga | 

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utrymmen |  Inga | 
> | miljöer/eventsources |  Inga |  
> | miljöer/referencedatasets |  Inga | 

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Auktoriseringshanteraren | Inga | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | imagetemplates | Inga | 

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konto |  Inga | 
> | konto/tillägg |  Inga | 
> | konto/projekt |  Inga | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Inga | 
> | dedicatedcloudservices | Inga | 
> | virtualmachines | Inga | 

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | certifikat | Inga | 
> | connectiongateways |  Inga |  
> | anslutning |  Inga |  
> | customapis |  Inga | 
> | hostingenvironments | Inga | 
> | Server grupper |  Inga |  
> | webbplatser |  Inga | 
> | platser/premieraddons |  Inga |  
> | platser/platser |  Inga |  


## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | deviceservices | Inga | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | applicationgroups | Inga | 
> | hostpools | Inga | 
> | arbetsytor | Inga | 

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.
