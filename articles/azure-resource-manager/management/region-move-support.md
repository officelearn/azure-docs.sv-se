---
title: Stöd för att flytta Azure-resurser mellan regioner
description: Visar en lista över de Azure-resurs typer som kan flyttas mellan Azure-regioner
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760716"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Stöd för att flytta Azure-resurser mellan regioner

I den här artikeln bekräftas om en Azure-resurs har stöd för att flytta till en annan Azure-region. 

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
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- | 
> | domainservices | Inga | 
> | domainservices / replicasets | Inga | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | klienter | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | actionrules | Inga | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servrar | Inga |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | tjänst |  Ja | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | configurationstores | Inga | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | apiapps | Inga | 
> | appidentities | Inga | 
> | gatewayer | Inga | 


## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | policyassignments | Inga |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | automationaccounts | Inga | 
> | automationaccounts/konfigurationer | Inga | 
> | automationaccounts/Runbooks | Inga | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | b2cdirectories | Inga | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | sqlserverregistrations | Inga |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | registreringar | Inga | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | batchaccounts | Inga |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Inga | 
> | fileservers | Inga | 
> | utskrifts | Inga | 
> | arbets ytor | Inga | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | mapapis | Inga | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | biztalk | Inga | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blockchainmembers | Inga |
> | tittare | Inga | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blueprintassignments | Inga | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | botservices | Inga | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Redis | Inga | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Inga |
> | profiles | Inga | 
> | profiler/slut punkter | Inga | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | certificateorders | Inga | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domän namn | Inga |  
> | virtualmachines | Inga | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | networksecuritygroups | Inga |
> | reservedips | Inga | 
> | virtualnetworks | Inga | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storageaccounts | Ja |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | availabilitysets | Inga | 
> | diskencryptionsets | Inga | 
> | disk | Inga | 
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
> | virtualmachines | Ja | 
> | virtualmachines/tillägg | Inga | 
> | virtualmachinescalesets | Inga | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containergroups | Inga | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containergroups | Inga | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | register | Inga |  
> | register/buildtasks | Inga |  
> | register/replikeringar | Inga | 
> | register/uppgifter | Inga |  
> | register/Webhooks | Inga | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containerservices | Inga | 
> | managedclusters | Inga | 
> | openshiftmanagedclusters | Inga | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program | Inga | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | anslutningar | Inga |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | NAV | Inga |  

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | resourceproviders | Inga | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utskrifts | Inga | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | databoxedgedevices | Inga | 

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbets ytor | Inga | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kataloger | Inga | 
> | datacatalogs | Inga | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | connectionmanagers | Inga | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | distributionspaket | Inga | 
> | utgå | Inga | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datafactories | Inga | 
> | fabriker | Inga |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datalakeaccounts | Inga | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

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

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servrar | Inga |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servrar | Inga |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servergroups | Inga | 
> | servrar | Inga |  
> | serversv2 | Inga | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

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
> | elasticpools | Inga | 
> | elasticpools / iothubtenants | Inga | 
> | iothubs | Ja | 
> | provisioningservices | Inga | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domänkontrollanter | Inga | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

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

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domäner | Inga | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Inga |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domäner | Inga |  
> | avsnitt | Inga | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Inga |  
> | namn områden | Inga | 

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hanainstances | Inga | 
> | sapmonitors | Inga |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Inga | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Inga |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | faxar | Inga | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datamanagers |  Inga | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utskrifts |  Inga | 

## <a name="microsoftinsights"></a>microsoft.insights

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
> | arbets böcker |  Inga |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | iotapps |  Inga |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | checknameavailability |  Inga |  
> | graph |  Inga | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

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

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labaccounts | Inga | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hostingenvironments | Inga | 
> | integrationaccounts |  Inga |  
> | integrationserviceenvironments | Inga | 
> | isolatedenvironments | Inga | 
> | arbetsflöden |  Inga |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | commitmentplans |  Inga | 
> | WebServices |  Inga | 
> | arbets ytor |  Inga | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | operationalizationclusters |  Inga | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 
> | konton/arbets ytor | Inga | 
> | konton/arbets ytor/projekt | Inga | 
> | teamaccounts | Inga | 
> | teamaccounts/arbets ytor | Inga | 
> | teamaccounts/arbets ytor/projekt | Inga | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hostingaccounts | Inga | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbets ytor | Inga | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | userassignedidentities | Inga | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton |  Inga |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | classicdevservices | Inga | 

## <a name="microsoftmedia"></a>Microsoft.Media

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

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | assessmentprojects | Inga | 
> | migrateprojects | Inga | 
> | samarbetsprojekt | Inga | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

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
> | belastningsutjämnare | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br> – Ja standard-SKU |
> | localnetworkgateways |  Inga | 
> | natgateways |  Inga | 
> | networkintentpolicies |  Inga | 
> | NetworkInterfaces | Ja | 
> | networkprofiles | Inga | 
> | networksecuritygroups | Ja | 
> | networkwatchers |  Inga |  
> | networkwatchers / connectionmonitors |  Inga | 
> | networkwatchers/linser |  Inga | 
> | networkwatchers / pingmeshes |  Inga | 
> | p2svpngateways | Inga | 
> | privatednszones |  Inga |  
> | privatednszones / virtualnetworklinks |  Inga |  
> | privateendpoints | Inga | 
> | privatelinkservices | Inga | 
> | publicipaddresses | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
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


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Inga | 
> | namnrymder/notificationhubs |  Inga |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbets ytor |  Inga | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | managementconfigurations |  Inga | 
> | vyer |  Inga | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | peerings | Inga | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | instrument paneler | Inga | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | rootresources | Inga | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | workspacecollections |  Inga | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kapaciteter |  Inga | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | valv | Nej. [Inaktivera valv och återskapa](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) för Site Recovery  | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Inga | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | frågor |  Inga |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program |  Inga | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | planta |  Inga |  
> | förfrågningsåtgärder |  Inga | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | searchservices |  Inga | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Inga | 
> | playbookconfigurations | Inga | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | gatewayer | Inga | 
> | noder | Inga | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Inga | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

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

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

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

## <a name="microsoftsolutions"></a>Microsoft.Solutions

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
> | managedinstances | Ja | 
> | managedinstances/databaser | Ja | 
> | servrar | Ja | 
> | servrar/databaser | Ja | 
> | servrar/elasticpools | Ja | 
> | virtualclusters | Ja | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Inga |  
> | sqlvirtualmachines |  Inga |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | dwvm | Inga | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storageaccounts | Ja | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cacheminnen | Inga | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices |  Inga | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices | Inga | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices | Inga | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hantera | Inga | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | streamingjobs |  Inga |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utrymmen | Inga | 
> | miljöer/eventsources | Inga | 
> | instanser | Inga | 
> | instanser/miljöer | Inga | 
> | instanser/miljöer/eventsources | Inga | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | providerregistrations | Inga | 
> | resurser | Inga | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utrymmen |  Inga | 
> | miljöer/eventsources |  Inga |  
> | miljöer/referencedatasets |  Inga | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Auktoriseringshanteraren | Inga | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | imagetemplates | Inga | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konto |  Inga | 
> | konto/tillägg |  Inga | 
> | konto/projekt |  Inga | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Inga | 
> | dedicatedcloudservices | Inga | 
> | virtualmachines | Inga | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | intyg | Inga | 
> | connectiongateways |  Inga |  
> | anslutning |  Inga |  
> | customapis |  Inga | 
> | hostingenvironments | Inga | 
> | Server grupper |  Inga |  
> | stationer |  Inga | 
> | platser/premieraddons |  Inga |  
> | platser/platser |  Inga |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | deviceservices | Inga | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | applicationgroups | Inga | 
> | hostpools | Inga | 
> | arbets ytor | Inga | 

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.
