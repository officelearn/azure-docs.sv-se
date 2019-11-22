---
title: Stöd för att flytta Azure-resurser mellan regioner
description: Visar en lista över de Azure-resurs typer som kan flyttas mellan Azure-regioner
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 60b8708458e081d66514e092edc9ef9af7c33494
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308107"
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
> | domainservices | Nej | 
> | domainservices / replicasets | Nej | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Klienter | Nej |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | actionrules | Nej | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servrar | Nej |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | tjänst | Nej |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | configurationstores | Nej | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | apiapps | Nej | 
> | appidentities | Nej | 
> | gatewayer | Nej | 


## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | policyassignments | Nej |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | automationaccounts | Nej | 
> | automationaccounts/konfigurationer | Nej | 
> | automationaccounts/Runbooks | Nej | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | b2cdirectories | Nej | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | sqlserverregistrations | Nej |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | registreringar | Nej | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | batchaccounts | Nej |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Nej | 
> | fileservers | Nej | 
> | utskrifts | Nej | 
> | arbets ytor | Nej | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | mapapis | Nej | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | biztalk | Nej | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blockchainmembers | Nej |
> | tittare | Nej | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | blueprintassignments | Nej | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | botservices | Nej | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Redis | Nej | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nej |
> | profiles | Nej | 
> | profiler/slut punkter | Nej | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | certificateorders | Nej | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domän namn | Nej |  
> | virtualmachines | Nej | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | networksecuritygroups | Nej |
> | reservedips | Nej | 
> | virtualnetworks | Nej | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storageaccounts | Ja |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | availabilitysets | Nej | 
> | diskencryptionsets | Nej | 
> | disk | Nej | 
> | gallerier | Nej | 
> | gallerier/bilder | Nej | 
> | gallerier/avbildningar/versioner | Nej | 
> | hostgroups | Nej | 
> | hostgroups/värdar | Nej | 
> | images | Nej | 
> | proximityplacementgroups | Nej | 
> | restorepointcollections | Nej | 
> | sharedvmimages | Nej | 
> | sharedvmimages/versioner | Nej | 
> | snapshots | Nej | 
> | virtualmachines | Ja | 
> | virtualmachines/tillägg | Nej | 
> | virtualmachinescalesets | Nej | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containergroups | Nej | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containergroups | Nej | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | register | Nej |  
> | register/buildtasks | Nej |  
> | register/replikeringar | Nej | 
> | register/uppgifter | Nej |  
> | register/Webhooks | Nej | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | containerservices | Nej | 
> | managedclusters | Nej | 
> | openshiftmanagedclusters | Nej | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program | Nej | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | anslutningar | Nej |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | NAV | Nej |  

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | resourceproviders | Nej | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utskrifts | Nej | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | databoxedgedevices | Nej | 

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbets ytor | Nej | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kataloger | Nej | 
> | datacatalogs | Nej | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | connectionmanagers | Nej | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | distributionspaket | Nej | 
> | utgå | Nej | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datafactories | Nej | 
> | fabriker | Nej |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datalakeaccounts | Nej | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Nej | 
> | tjänster/projekt | Nej | 
> | lots | Nej | 

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servrar | Nej |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servrar | Nej |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | servergroups | Nej | 
> | servrar | Nej |  
> | serversv2 | Nej | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | artifactsources | Nej | 
> | distributioner | Nej |  
> | servicetopologies | Nej | 
> | servicetopologies/tjänster | Nej |  
> | servicetopologies/tjänster/serviceunits | Nej | 
> | steg | Nej | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | elasticpools | Nej | 
> | elasticpools / iothubtenants | Nej | 
> | iothubs | Ja | 
> | provisioningservices | Nej | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domänkontrollanter | Nej | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labcenters | Nej | 
> | Laboration | Nej | 
> | labb/miljöer | Nej |  
> | labb/servicerunners | Nej | 
> | labb/virtualmachines | Nej |  
> | scheman | Nej |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | databaseaccounts | Nej | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domäner | Nej | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Nej |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | domäner | Nej |  
> | avsnitt | Nej | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Nej |  
> | namn områden | Nej | 

## <a name="microsoftgenomics"></a>Microsoft. genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hanainstances | Nej | 
> | sapmonitors | Nej |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster | Nej | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | services | Nej |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | faxar | Nej | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | datamanagers |  Nej | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utskrifts |  Nej | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 
> | actiongroups |  Nej | 
> | activitylogalerts | Nej | 
> | alertrules |  Nej | 
> | autoscalesettings |  Nej | 
> | delarna |  Nej |  
> | guestdiagnosticsettings | Nej | 
> | metricalerts | Nej | 
> | notificationgroups | Nej | 
> | notificationrules | Nej | 
> | scheduledqueryrules |  Nej | 
> | webbtester |  Nej | 
> | arbets böcker |  Nej |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | iotapps |  Nej |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | checknameavailability |  Nej |  
> | graph |  Nej | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hsmpools | Nej | 
> | valv |  Nej | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kluster |  Nej |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | labaccounts | Nej | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hostingenvironments | Nej | 
> | integrationaccounts |  Nej |  
> | integrationserviceenvironments | Nej | 
> | isolatedenvironments | Nej | 
> | arbetsflöden |  Nej |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | commitmentplans |  Nej | 
> | WebServices |  Nej | 
> | arbets ytor |  Nej | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | operationalizationclusters |  Nej | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 
> | konton/arbets ytor | Nej | 
> | konton/arbets ytor/projekt | Nej | 
> | teamaccounts | Nej | 
> | teamaccounts/arbets ytor | Nej | 
> | teamaccounts/arbets ytor/projekt | Nej | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hostingaccounts | Nej | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbets ytor | Nej | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | userassignedidentities | Nej | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton |  Nej |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | classicdevservices | Nej | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Media Services |  Nej | 
> | Media Services/liveevents |  Nej | 
> | Media Services/strömnings slut punkter |  Nej | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | appclusters | Nej | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | assessmentprojects | Nej | 
> | migrateprojects | Nej | 
> | samarbetsprojekt | Nej | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | netappaccounts | Nej | 
> | netappaccounts / capacitypools | Nej | 
> | netappaccounts/capacitypools/Volumes | Nej | 
> | netappaccounts/capacitypools/Volumes/mounttargets | Nej | 
> | netappaccounts/capacitypools/volym/ögonblicks bilder | Nej | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | applicationgateways | Nej | 
> | applicationgatewaywebapplicationfirewallpolicies | Nej | 
> | applicationsecuritygroups |  Nej |  
> | azurefirewalls |  Nej |  
> | bastionhosts | Nej | 
> | anslutning |  Nej | 
> | ddoscustompolicies |  Nej | 
> | ddosprotectionplans | Nej | 
> | dnszones |  Nej | 
> | expressroutecircuits | Nej | 
> | expressroutecrossconnections | Nej | 
> | expressroutegateways | Nej | 
> | expressrouteports | Nej | 
> | frontdoors | Nej | 
> | frontdoorwebapplicationfirewallpolicies | Nej | 
> | belastningsutjämnare | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br> – Ja standard-SKU |
> | localnetworkgateways |  Nej | 
> | natgateways |  Nej | 
> | networkintentpolicies |  Nej | 
> | NetworkInterfaces | Ja | 
> | networkprofiles | Nej | 
> | networksecuritygroups | Ja | 
> | networkwatchers |  Nej |  
> | networkwatchers / connectionmonitors |  Nej | 
> | networkwatchers/linser |  Nej | 
> | networkwatchers / pingmeshes |  Nej | 
> | p2svpngateways | Nej | 
> | privatednszones |  Nej |  
> | privatednszones / virtualnetworklinks |  Nej |  
> | privateendpoints | Nej | 
> | privatelinkservices | Nej | 
> | publicipaddresses | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
> | publicipprefixes | Nej | 
> | routefilters | Nej | 
> | routetables |  Nej | 
> | serviceendpointpolicies |  Nej | 
> | trafficmanagerprofiles |  Nej | 
> | virtualhubs | Nej | 
> | virtualnetworkgateways |  Nej |  
> | virtualnetworks |  Nej | 
> | virtualnetworktaps | Nej | 
> | virtualwans | Nej | 
> | vpngateways (virtuellt WAN) | Nej | 
> | vpnsites (virtuellt WAN) | Nej | 
> | webapplicationfirewallpolicies |  Nej | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Nej | 
> | namnrymder/notificationhubs |  Nej |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | arbets ytor |  Nej | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | managementconfigurations |  Nej | 
> | vyer |  Nej | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | peerings | Nej | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | instrument paneler | Nej | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | rootresources | Nej | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | workspacecollections |  Nej | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | kapaciteter |  Nej | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konton | Nej | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | valv | Ja (för säkerhets kopierings valv jag tror? | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Nej | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | frågor |  Nej |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program |  Nej | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | planta |  Nej |  
> | förfrågningsåtgärder |  Nej | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | searchservices |  Nej | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Nej | 
> | playbookconfigurations | Nej | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | gatewayer | Nej | 
> | noder | Nej | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | namn områden |  Nej | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program | Nej | 
> | kluster |  Nej | 
> | kluster/program | Nej | 
> | containergroups | Nej | 
> | containergroupsets | Nej | 
> | edgeclusters | Nej | 
> | nätet | Nej | 
> | secretstores | Nej | 
> | volumes | Nej | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | program |  Nej | 
> | containergroups | Nej | 
> | gatewayer |  Nej | 
> | nätet |  Nej | 
> | secrets |  Nej | 
> | volumes |  Nej |  

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | SignalR |  Nej |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | appliancedefinitions | Nej | 
> | redskap | Nej | 
> | applicationdefinitions | Nej | 
> | program | Nej | 
> | jitrequests | Nej | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | instancepools | Nej | 
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
> | sqlvirtualmachinegroups |  Nej |  
> | sqlvirtualmachines |  Nej |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | dwvm | Nej | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storageaccounts | Ja?? | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | cacheminnen | Nej | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices |  Nej | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices | Nej | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | storagesyncservices | Nej | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | hantera | Nej | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | streamingjobs |  Nej |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utrymmen | Nej | 
> | miljöer/eventsources | Nej | 
> | instanser | Nej | 
> | instanser/miljöer | Nej | 
> | instanser/miljöer/eventsources | Nej | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | providerregistrations | Nej | 
> | resurser | Nej | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | utrymmen |  Nej | 
> | miljöer/eventsources |  Nej |  
> | miljöer/referencedatasets |  Nej | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | Auktoriseringshanteraren | Nej | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | imagetemplates | Nej | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | konto |  Nej | 
> | konto/tillägg |  Nej | 
> | konto/projekt |  Nej | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Nej | 
> | dedicatedcloudservices | Nej | 
> | virtualmachines | Nej | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | intyg | Nej | 
> | connectiongateways |  Nej |  
> | anslutning |  Nej |  
> | customapis |  Nej | 
> | hostingenvironments | Nej | 
> | Server grupper |  Nej |  
> | stationer |  Nej | 
> | platser/premieraddons |  Nej |  
> | platser/platser |  Nej |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | deviceservices | Nej | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Resurstyp | Flytta region | 
> | ------------- | ----------- |
> | applicationgroups | Nej | 
> | hostpools | Nej | 
> | arbets ytor | Nej | 

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.
