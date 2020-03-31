---
title: Stöd för att flytta Azure-resurser mellan regioner
description: Visar en lista över de Azure-resurstyper som kan flyttas över Azure-regioner
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760716"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Stöd för att flytta Azure-resurser mellan regioner

Den här artikeln bekräftar om en Azure-resurstyp stöds för att flytta till en annan Azure-region. 

Gå till ett namnområde för resursleverantören:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam (på nytt)](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiHanagement](#microsoftapimanagement)
> - [Microsoft.AppKonfigurering](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalk-tjänster](#microsoftbiztalkservices)
> - [Microsoft.Blockchain (på andra)](#microsoftblockchain)
> - [Microsoft.Blueprint (Microsoft.Blueprint)](#microsoftblueprint)
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
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.Container-registret](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics Microsoft.CortanaAnalytics Microsoft.CortanaAnalytics Microsoft.](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
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
> - [Microsoft.Genomik](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights microsoft.insights microsoft.insights microsoft.](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices Microsoft.LocationBasedServices Microsoft.LocationBasedServices Microsoft.](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLärning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization Microsoft.MachineLearningOperationalization Microsoft.MachineLearningOperationalization Microsoft.](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrera](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Sök](#microsoftsearch)
> - [Microsoft.Säkerhet](#microsoftsecurity)
> - [Microsoft.ServerHanagement](#microsoftservermanagement)
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
> - [Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.](#microsoftstreamanalytics)
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
> | domäntjänster | Inga | 
> | domäntjänster /replikuppsättningar | Inga | 

## <a name="microsoftaadiam"></a>microsoft.aadiam (på nytt)

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Hyresgäster | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | actionregler | Inga | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Servrar | Inga |

## <a name="microsoftapimanagement"></a>Microsoft.ApiHanagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | tjänst |  Ja | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppKonfigurering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | configurationstores | Inga | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | apiapps (apiapps) | Inga | 
> | appid entiteter | Inga | 
> | Gateways | Inga | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | policytilldelningar | Inga |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | automationskonton | Inga | 
> | automationskonton / konfigurationer | Inga | 
> | automationskonton / runbooks | Inga | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | b2kriktorier | Inga | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | sqlserverregistrations | Inga |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Registreringar | Inga | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | batchaccounts | Inga |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Kluster | Inga | 
> | filservrar | Inga | 
> | Jobb | Inga | 
> | arbetsytor | Inga | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | mapapis (mapapis) | Inga | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalk-tjänster

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | biztalk | Inga | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain (på andra)

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blockchainmembers | Inga |
> | Watchers | Inga | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint (Microsoft.Blueprint)

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | ritningartilldelningar | Inga | 

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
> | Profiler | Inga | 
> | profiler/slutpunkter | Inga | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | certifikatorder | Inga | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domännamn | Inga |  
> | virtualmachines (virtuellamaskiner) | Inga | 



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
> | lagringskonton | Ja |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | tillgänglighetsuppsättningar | Inga | 
> | diskencryptionsets | Inga | 
> | Diskar | Inga | 
> | Gallerier | Inga | 
> | gallerier / bilder | Inga | 
> | gallerier / bilder / versioner | Inga | 
> | värdgrupper | Inga | 
> | värdgrupper / värdar | Inga | 
> | images | Inga | 
> | närgrupper | Inga | 
> | återställningspunkter | Inga | 
> | sharedvmimages sharedvmimages sharedvmimages sharedv | Inga | 
> | sharedvmimages / versioner | Inga | 
> | snapshots | Inga | 
> | virtualmachines (virtuellamaskiner) | Ja | 
> | virtualmachines / tillägg | Inga | 
> | virtualmachinescalesets | Inga | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containergrupper | Inga | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containergrupper | Inga | 

## <a name="microsoftcontainerregistry"></a>Microsoft.Container-registret

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Register | Inga |  
> | register / buildtasks | Inga |  
> | register /replikeringar | Inga | 
> | register /uppgifter | Inga |  
> | register / webhooks | Inga | 

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

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics Microsoft.CortanaAnalytics Microsoft.CortanaAnalytics Microsoft.

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
> | nav | Inga |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | resurserproviders | Inga | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Jobb | Inga | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | databoxedgedevices | Inga | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytor | Inga | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Kataloger | Inga | 
> | datakategorier | Inga | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | connectionmanagers | Inga | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Paket | Inga | 
> | Planer | Inga | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datafakta | Inga | 
> | Fabriker | Inga |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datalakekonton | Inga | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.

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
> | tjänster /projekt | Inga | 
> | Platser | Inga | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Servrar | Inga |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Servrar | Inga |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servergrupper | Inga | 
> | Servrar | Inga |  
> | servrarv2 | Inga | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | artefaktkälla | Inga | 
> | Utbyggnader | Inga |  
> | servicetopologier | Inga | 
> | servicetopologier / tjänster | Inga |  
> | servicetopologier / tjänster / serviceunits | Inga | 
> | steg | Inga | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | elasticpools | Inga | 
> | elasticpools / iothubtenants | Inga | 
> | iothubs | Ja | 
> | etableringstjänster | Inga | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Styrenheter | Inga | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labcenters | Inga | 
> | Labs | Inga | 
> | laboratorier / miljöer | Inga |  
> | labb / servicerunners | Inga | 
> | laboratorier / virtualmachines | Inga |  
> | Scheman | Inga |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | databaskonton | Inga | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Domäner | Inga | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Inga |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Domäner | Inga |  
> | Ämnen | Inga | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Kluster | Inga |  
> | Namnområden | Inga | 

## <a name="microsoftgenomics"></a>Microsoft.Genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hanainstances (hanainstances) | Inga | 
> | sapmonitors (sapmonitors) | Inga |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Kluster | Inga | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Inga |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Maskiner | Inga | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datamanagers |  Inga | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Jobb |  Inga | 

## <a name="microsoftinsights"></a>microsoft.insights microsoft.insights microsoft.insights microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 
> | aktionsgrupper |  Inga | 
> | aktivitetslogalerts | Inga | 
> | varningsreglerna |  Inga | 
> | automatisk skalning |  Inga | 
> | Komponenter |  Inga |  
> | gästdiagnostik | Inga | 
> | metricalerter | Inga | 
> | anmälan grupper | Inga | 
> | anmälningsreglerna | Inga | 
> | schemalagdarurules |  Inga | 
> | webbtests |  Inga | 
> | arbetsböcker |  Inga |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | iotapps (iotapps) |  Inga |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kontrollera namntillgänglighet |  Inga |  
> | Diagram |  Inga | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hsmpools (hsmpools) | Inga | 
> | Valv |  Inga | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Kluster |  Inga |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labaccounts | Inga | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices Microsoft.LocationBasedServices Microsoft.LocationBasedServices Microsoft.

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
> | värdmiljöer | Inga | 
> | integrationskonton |  Inga |  
> | integrationstjänstermiljöer | Inga | 
> | isoleramiljöer | Inga | 
> | Arbetsflöden |  Inga |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLärning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | åtagandeplaner |  Inga | 
> | Webservices |  Inga | 
> | arbetsytor |  Inga | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | operationaliseringklustor |  Inga | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 
> | konton / arbetsytor | Inga | 
> | konton / arbetsytor / projekt | Inga | 
> | teamkonton | Inga | 
> | teamaccounts / arbetsytor | Inga | 
> | teamaccounts / arbetsytor / projekt | Inga | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization Microsoft.MachineLearningOperationalization Microsoft.MachineLearningOperationalization Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hostingkonton | Inga | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytor | Inga | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | userassignedid entiteter | Inga | 

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
> | medietjänster |  Inga | 
> | mediaservices / liveevents |  Inga | 
> | mediatjänster / streamingendpoints |  Inga | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | appclusters (appclusters) | Inga | 

## <a name="microsoftmigrate"></a>Microsoft.Migrera

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | bedömningsprojekt | Inga | 
> | migreraprojekt | Inga | 
> | Projekt | Inga | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | netappaccounts (netappaccounts) | Inga | 
> | netappaccounts / capacitypools | Inga | 
> | netappaccounts / capacitypools / volymer | Inga | 
> | netappaccounts / capacitypools / volymer / mounttargets | Inga | 
> | netappaccounts / capacitypools / volymer / ögonblicksbilder | Inga | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | applicationgateways (appliceringsvägar) | Inga | 
> | applicationgatewaywebapplicationfirewallpolicies | Inga | 
> | applicationsecuritygroups |  Inga |  
> | azurefirewalls (azurefirewalls) |  Inga |  
> | bastionhosts (bastionhosts) | Inga | 
> | Anslutningar |  Inga | 
> | ddoscustompolicies |  Inga | 
> | ddosprotectionplans | Inga | 
> | dnszones |  Inga | 
> | expressroutecircuits | Inga | 
> | expressroutecrossconnections | Inga | 
> | expressroutegateways | Inga | 
> | expressrouteports | Inga | 
> | frontdörrar | Inga | 
> | frontdoorwebapplicationfirewallpolicies | Inga | 
> | loadbalancers | Ja - Grundläggande SKU<br>Nej - Standard SKU | Ja - Grundläggande SKU<br> -Ja Standard SKU |
> | localnetworkgateways |  Inga | 
> | natgateways (natgateways) |  Inga | 
> | nätverkintentpolitik |  Inga | 
> | nätverksinterfaces | Ja | 
> | nätverksprofiler | Inga | 
> | networksecuritygroups | Ja | 
> | nätverkwatchers |  Inga |  
> | networkwatchers / connectionmonitors |  Inga | 
> | networkwatchers / linser |  Inga | 
> | networkwatchers / pingmeshes |  Inga | 
> | p2svpngateways | Inga | 
> | privatednszones |  Inga |  
> | privatednszones / virtualnetworklinks |  Inga |  
> | privateendpoints | Inga | 
> | privatelinkservices | Inga | 
> | offentliga ipaddresses | Ja - Grundläggande SKU<br>Nej - Standard SKU | Ja - Grundläggande SKU<br>Nej - Standard SKU |
> | offentliga offentliga korrigeringar | Inga | 
> | ruttfilter | Inga | 
> | rutttabeller |  Inga | 
> | serviceendpointpolicies |  Inga | 
> | trafficmanagerprofiles |  Inga | 
> | virtualhubs | Inga | 
> | virtualnetworkgateways |  Inga |  
> | virtualnetworks |  Inga | 
> | virtualnetworktaps | Inga | 
> | virtualwans (virtualwans) | Inga | 
> | vpngateways (VirtuellT WAN) | Inga | 
> | vpnsites (VirtuellT WAN) | Inga | 
> | webbapplikationfirewallpolicies |  Inga | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Namnområden |  Inga | 
> | namnområden / anmälanhubs |  Inga |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytor |  Inga | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hanteringskonfigurationer |  Inga | 
> | Visningar |  Inga | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | peerings | Inga | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | instrumentpaneler | Inga | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | rootresources | Inga | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbetsytesamlingar |  Inga | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Kapacitet |  Inga | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Inga | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Valv | Nej. [Inaktivera valv och återskapa](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) för webbplatsåterställning  | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Namnområden |  Inga | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Frågor |  Inga |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program |  Inga | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | flows |  Inga |  
> | jobbsamlingar |  Inga | 

## <a name="microsoftsearch"></a>Microsoft.Sök

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | söktjänster |  Inga | 


## <a name="microsoftsecurity"></a>Microsoft.Säkerhet

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Inga | 
> | playbookkonfigurationer | Inga | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerHanagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Gateways | Inga | 
> | Noder | Inga | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Namnområden |  Inga | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program | Inga | 
> | Kluster |  Inga | 
> | kluster / applikationer | Inga | 
> | containergrupper | Inga | 
> | containersgroupsets | Inga | 
> | kantklusor | Inga | 
> | Nätverk | Inga | 
> | hemliga butiker | Inga | 
> | volumes | Inga | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program |  Inga | 
> | containergrupper | Inga | 
> | Gateways |  Inga | 
> | Nätverk |  Inga | 
> | secrets |  Inga | 
> | volumes |  Inga |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | signalr |  Inga |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | definitioner av apparater | Inga | 
> | Apparater | Inga | 
> | applicationdefinitionss | Inga | 
> | program | Inga | 
> | jitrequests (påt) | Inga | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | instanspooler | Inga | 
> | managedinstances | Ja | 
> | managedinstances / databaser | Ja | 
> | Servrar | Ja | 
> | servrar / databaser | Ja | 
> | servrar / elasticpools | Ja | 
> | virtualclusters (virtuellakluster) | Ja | 

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
> | dwvm (dwvm) | Inga | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | lagringskonton | Ja | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Cachar | Inga | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | lagringssynkroniseringar |  Inga | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | lagringssynkroniseringar | Inga | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | lagringssynkroniseringar | Inga | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Chefer | Inga | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | streamingjobb |  Inga |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Miljöer | Inga | 
> | miljöer/eventsources | Inga | 
> | Instanser | Inga | 
> | instanser / miljöer | Inga | 
> | instanser / miljöer / eventsources | Inga | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | leverantörsregistreringar | Inga | 
> | resources | Inga | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Miljöer |  Inga | 
> | miljöer/eventsources |  Inga |  
> | miljöer / referensdataset |  Inga | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Butiker | Inga | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | bildskyltar | Inga | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konto |  Inga | 
> | konto / förlängning |  Inga | 
> | konto / projekt |  Inga | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | dedikerade molnen | Inga | 
> | dedikerade molntjänster | Inga | 
> | virtualmachines (virtuellamaskiner) | Inga | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | certifikat | Inga | 
> | connectiongateways |  Inga |  
> | Anslutningar |  Inga |  
> | customapis (customapis) |  Inga | 
> | värdmiljöer | Inga | 
> | serverfarms |  Inga |  
> | webbplatser |  Inga | 
> | platser / premieraddons |  Inga |  
> | platser / platser |  Inga |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | enhetstjänster | Inga | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | programgrupper | Inga | 
> | värdpooler | Inga | 
> | arbetsytor | Inga | 

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flyttåtgärden.
