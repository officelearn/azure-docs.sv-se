---
title: Flytta åtgärds stöd av Azure-resurs typ
description: Visar en lista över de Azure-resurs typer som kan flyttas till en ny resurs grupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 22493ad7998e9014c88c79e6ac5eee3bf1216119
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226802"
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
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
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
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
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
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
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
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Nej | Nej |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| klienter | Nej | Nej |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| actionrules | Ja | Ja |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Brygghuvudservrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tjänst | Ja | Ja |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| configurationstores | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| apiapps | Nej | Nej |
| appidentities | Nej | Nej |
| gatewayer | Nej | Nej |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft. Authorization
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Nej | Nej |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | Ja | Ja |
| automationaccounts/konfigurationer | Ja | Ja |
| automationaccounts/Runbooks | Ja | Ja |

> [!IMPORTANT]
> Runbooks måste finnas i samma resurs grupp som Automation-kontot.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| b2cdirectories | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registreringar | Ja | Ja |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| backupvault | Nej | Nej |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| batchaccounts | Ja | Ja |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kluster | Nej | Nej |
| fileservers | Nej | Nej |
| utskrifts | Nej | Nej |
| Arbets ytor | Nej | Nej |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mapapis | Nej | Nej |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztalk | Ja | Ja |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blockchainmembers | Ja | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Nej | Nej |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft.Cache
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Redis | Ja | Ja |

> [!IMPORTANT]
> Om Azure cache för Redis-instansen har kon figurer ATS med ett virtuellt nätverk kan instansen inte flyttas till en annan prenumeration. Se [begränsningar för flytt av virtuella nätverk](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| profiles | Ja | Ja |
| profiler/slut punkter | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificateorders | Ja | Ja |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domän namn | Ja | Nej |
| virtualmachines | Ja | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Nej | Nej |
| reservedips | Nej | Nej |
| virtualnetworks | Nej | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Ja | Nej |

> [!IMPORTANT]
> Läs mer i den [klassiska distributionen](./move-limitations/classic-model-move-limitations.md). Klassiska distributions resurser kan flyttas mellan prenumerationer med en åtgärd som är speciell för det scenariot.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | Ja | Ja |
| Disk | Ja | Ja |
| gallerier | Nej | Nej |
| gallerier/bilder | Nej | Nej |
| gallerier/avbildningar/versioner | Nej | Nej |
| hostgroups | Nej | Nej |
| hostgroups/värdar | Nej | Nej |
| images | Ja | Ja |
| proximityplacementgroups | Nej | Nej |
| restorepointcollections | Nej | Nej |
| sharedvmimages | Nej | Nej |
| sharedvmimages/versioner | Nej | Nej |
| Ögonblicks bilder | Ja | Ja |
| virtualmachines | Ja | Ja |
| virtualmachines/tillägg | Ja | Ja |
| virtualmachinescalesets | Ja | Ja |

> [!IMPORTANT]
> Se [Virtual Machines flytta vägledning](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Nej | Nej |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Nej | Nej |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| register | Ja | Ja |
| register/buildtasks | Ja | Ja |
| register/replikeringar | Ja | Ja |
| register/uppgifter | Ja | Ja |
| register/Webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containerservices | Nej | Nej |
| managedclusters | Nej | Nej |
| openshiftmanagedclusters | Nej | Nej |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| program | Ja | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Nej | Nej |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| anslutningar | Ja | Ja |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| NAV | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| utskrifts | Nej | Nej |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Nej | Nej |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Arbets ytor | Nej | Nej |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kataloger | Ja | Ja |
| datacatalogs | Nej | Nej |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectionmanagers | Nej | Nej |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| distributionspaket | Nej | Nej |
| utgå | Nej | Nej |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datafactories | Ja | Ja |
| fabriker | Ja | Ja |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Nej | Nej |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Nej | Nej |
| tjänster/projekt | Nej | Nej |
| lots | Nej | Nej |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Brygghuvudservrar | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Brygghuvudservrar | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Nej | Nej |
| Brygghuvudservrar | Ja | Ja |
| serversv2 | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | Ja | Ja |
| distributioner | Ja | Ja |
| servicetopologies | Ja | Ja |
| servicetopologies/tjänster | Ja | Ja |
| servicetopologies/tjänster/serviceunits | Ja | Ja |
| steg | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| elasticpools | Nej | Nej |
| elasticpools/iothubtenants | Nej | Nej |
| iothubs | Ja | Ja |
| provisioningservices | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Domänkontrollanter | Nej | Nej |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Nej | Nej |
| Laboration | Ja | Nej |
| labb/miljöer | Ja | Ja |
| labb/servicerunners | Ja | Ja |
| labb/virtualmachines | Ja | Nej |
| scheman | Ja | Ja |

## <a name="microsoftdns"></a>microsoft.dns
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Nej | Nej |
| dnszones/a | Nej | Nej |
| dnszones/AAAA | Nej | Nej |
| dnszones/CNAME | Nej | Nej |
| dnszones/MX | Nej | Nej |
| dnszones/PTR | Nej | Nej |
| dnszones/srv | Nej | Nej |
| dnszones/txt | Nej | Nej |
| trafficmanagerprofiles | Nej | Nej |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databaseaccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domäner | Ja | Ja |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domäner | Ja | Ja |
| Avsnitt | Ja | Ja |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kluster | Ja | Ja |
| namn områden | Ja | Ja |

## <a name="microsoftgenomics"></a>Microsoft. genomik
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Nej | Nej |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hanainstances | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kluster | Ja | Ja |

> [!IMPORTANT]
> Du kan flytta HDInsight-kluster till en ny prenumeration eller resursgrupp. Men kan inte du flytta mellan prenumerationer som nätverksresurser som är länkad till HDInsight-klustret (till exempel virtuella nätverk, nätverkskort eller belastningsutjämnare). Dessutom kan flytta du inte till en ny resursgrupp ett nätverkskort som är kopplad till en virtuell dator för klustret.
>
> När du flyttar ett HDInsight-kluster till en ny prenumeration först flytta andra resurser (t.ex. storage-konto). Flytta sedan HDInsight-klustret ensamt.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| faxar | Nej | Nej |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagers | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| utskrifts | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Nej | Nej |
| actiongroups | Ja | Ja |
| activitylogalerts | Nej | Nej |
| alertrules | Ja | Ja |
| autoscalesettings | Ja | Ja |
| Delarna | Ja | Ja |
| guestdiagnosticsettings | Nej | Nej |
| metricalerts | Nej | Nej |
| notificationgroups | Nej | Nej |
| notificationrules | Nej | Nej |
| scheduledqueryrules | Ja | Ja |
| webbtester | Ja | Ja |
| Arbets böcker | Ja | Ja |

> [!IMPORTANT]
> Se till att flytta till den nya prenumerationen inte överskrider [prenumerations kvoterna](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | Ja | Ja |
| graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Nej | Nej |
| valv | Ja | Ja |

> [!IMPORTANT]
> Nyckel valv som används för disk kryptering kan inte flyttas till en resurs grupp i samma prenumeration eller mellan prenumerationer.

## <a name="microsoftkusto"></a>Microsoft. Kusto
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kluster | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labaccounts | Nej | Nej |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Nej | Nej |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingenvironments | Nej | Nej |
| integrationaccounts | Ja | Ja |
| integrationserviceenvironments | Nej | Nej |
| isolatedenvironments | Nej | Nej |
| arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | Ja | Ja |
| WebServices | Ja | Nej |
| Arbets ytor | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Ja | Ja |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Nej | Nej |
| konton/arbets ytor | Nej | Nej |
| konton/arbets ytor/projekt | Nej | Nej |
| teamaccounts | Nej | Nej |
| teamaccounts/arbets ytor | Nej | Nej |
| teamaccounts/arbets ytor/projekt | Nej | Nej |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingaccounts | Nej | Nej |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Arbets ytor | Nej | Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| userassignedidentities | Nej | Nej |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| classicdevservices | Nej | Nej |

## <a name="microsoftmedia"></a>Microsoft.Media
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Media Services | Ja | Ja |
| Media Services/liveevents | Ja | Ja |
| Media Services/strömnings slut punkter | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| assessmentprojects | Nej | Nej |
| migrateprojects | Nej | Nej |
| samarbetsprojekt | Nej | Nej |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| netappaccounts | Nej | Nej |
| netappaccounts/capacitypools | Nej | Nej |
| netappaccounts/capacitypools/Volumes | Nej | Nej |
| netappaccounts/capacitypools/Volumes/mounttargets | Nej | Nej |
| netappaccounts/capacitypools/volym/ögonblicks bilder | Nej | Nej |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgateways | Nej | Nej |
| applicationgatewaywebapplicationfirewallpolicies | Nej | Nej |
| applicationsecuritygroups | Ja | Ja |
| azurefirewalls | Ja | Ja |
| bastionhosts | Nej | Nej |
| anslutning | Ja | Ja |
| ddoscustompolicies | Ja | Ja |
| ddosprotectionplans | Nej | Nej |
| dnszones | Ja | Ja |
| expressroutecircuits | Nej | Nej |
| expressroutecrossconnections | Nej | Nej |
| expressroutegateways | Nej | Nej |
| expressrouteports | Nej | Nej |
| frontdoors | Nej | Nej |
| frontdoorwebapplicationfirewallpolicies | Nej | Nej |
| belastningsutjämnare | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
| localnetworkgateways | Ja | Ja |
| natgateways | Ja | Ja |
| networkintentpolicies | Ja | Ja |
| NetworkInterfaces | Ja | Ja |
| networkprofiles | Nej | Nej |
| networksecuritygroups | Ja | Ja |
| networkwatchers | Ja | Ja |
| networkwatchers/connectionmonitors | Ja | Ja |
| networkwatchers/linser | Ja | Ja |
| networkwatchers/pingmeshes | Ja | Ja |
| p2svpngateways | Nej | Nej |
| privatednszones | Ja | Ja |
| privatednszones/virtualnetworklinks | Ja | Ja |
| privateendpoints | Nej | Nej |
| privatelinkservices | Nej | Nej |
| publicipaddresses | Ja – grundläggande SKU<br>Standard-SKU: n | Ja – grundläggande SKU<br>Standard-SKU: n |
| publicipprefixes | Ja | Ja |
| routefilters | Nej | Nej |
| routetables | Ja | Ja |
| securegateways | Ja | Ja |
| serviceendpointpolicies | Ja | Ja |
| trafficmanagerprofiles | Ja | Ja |
| virtualhubs | Nej | Nej |
| virtualnetworkgateways | Ja | Ja |
| virtualnetworks | Ja | Ja |
| virtualnetworktaps | Nej | Nej |
| virtualwans | Nej | Nej |
| vpngateways | Nej | Nej |
| vpnsites | Nej | Nej |
| webapplicationfirewallpolicies | Ja | Ja |

> [!IMPORTANT]
> Se hur du [flyttar vägledning för virtuella nätverk](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namn områden | Ja | Ja |
| namnrymder/notificationhubs | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Arbets ytor | Ja | Ja |

> [!IMPORTANT]
> Se till att flytta till den nya prenumerationen inte överskrider [prenumerations kvoterna](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | Ja | Ja |
| lösningar | Ja | Ja |
| Vyer | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| peerings | Nej | Nej |

## <a name="microsoftportal"></a>Microsoft.Portal
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Instrument paneler | Ja | Ja |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rootresources | Nej | Nej |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kapaciteter | Ja | Ja |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konton | Nej | Nej |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| valv | Ja | Ja |

> [!IMPORTANT]
> Se [Recovery Services flytta vägledning](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namn områden | Ja | Ja |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| program | Ja | Nej |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| planta | Ja | Ja |
| förfrågningsåtgärder | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | Ja | Ja |

> [!IMPORTANT]
> Du kan inte flytta flera Sök resurser i olika regioner i samma åtgärd. Flytta i stället dem i separata åtgärder.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Ja | Ja |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| gatewayer | Nej | Nej |
| noder | Nej | Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namn områden | Ja | Ja |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| program | Nej | Nej |
| kluster | Ja | Ja |
| containergroups | Nej | Nej |
| containergroupsets | Nej | Nej |
| edgeclusters | Nej | Nej |
| nätet | Nej | Nej |
| secretstores | Nej | Nej |
| volumes | Nej | Nej |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| program | Ja | Ja |
| containergroups | Nej | Nej |
| gatewayer | Ja | Ja |
| nätet | Ja | Ja |
| secrets | Ja | Ja |
| volumes | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| SignalR | Ja | Ja |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Nej | Nej |

> [!IMPORTANT]
> Se [Recovery Services flytta vägledning](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Nej | Nej |
| redskap | Nej | Nej |
| applicationdefinitions | Nej | Nej |
| program | Nej | Nej |
| jitrequests | Nej | Nej |

## <a name="microsoftsql"></a>Microsoft.Sql
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| instancepools | Nej | Nej |
| managedinstances | Nej | Nej |
| managedinstances/databaser | Nej | Nej |
| Brygghuvudservrar | Ja | Ja |
| servrar/databaser | Ja | Ja |
| servrar/elasticpools | Ja | Ja |
| virtualclusters | Ja | Ja |

> [!IMPORTANT]
> En databas och en server måste finnas i samma resurs grupp. Om du flyttar en SQLServer, flyttas även alla dess databaser. Det här beteendet gäller för Azure SQL Database och Azure SQL Data Warehouse-databaser.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Ja | Ja |
| sqlvirtualmachines | Ja | Ja |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dwvm | Nej | Nej |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Ja | Ja |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| cacheminnen | Nej | Nej |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nej | Nej |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nej | Nej |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hantera | Nej | Nej |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | Ja | Ja |

> [!IMPORTANT]
> Det går inte att flytta Stream Analytics jobb i körnings läge.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| utrymmen | Nej | Nej |
| miljöer/eventsources | Nej | Nej |
| instanser | Nej | Nej |
| instanser/miljöer | Nej | Nej |
| instanser/miljöer/eventsources | Nej | Nej |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Nej | Nej |
| Resurser | Nej | Nej |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| utrymmen | Ja | Ja |
| miljöer/eventsources | Ja | Ja |
| miljöer/referencedatasets | Ja | Ja |

## <a name="microsofttoken"></a>Microsoft.Token
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Auktoriseringshanteraren | Nej | Nej |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Nej | Nej |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| konto | Ja | Ja |
| konto/tillägg | Ja | Ja |
| konto/projekt | Ja | Ja |

> [!IMPORTANT]
> Information om hur du ändrar prenumerationen för Azure-DevOps finns i [ändra den Azure-prenumeration som används för fakturering](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Ja | Ja |
| dedicatedcloudservices | Ja | Ja |
| virtualmachines | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Intyg | Nej | Ja |
| connectiongateways | Ja | Ja |
| anslutning | Ja | Ja |
| customapis | Ja | Ja |
| hostingenvironments | Nej | Nej |
| Server grupper | Ja | Ja |
| stationer | Ja | Ja |
| platser/premieraddons | Ja | Ja |
| platser/platser | Ja | Ja |

> [!IMPORTANT]
> Se [App Service flytta vägledning](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Nej | Nej |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Resurstyp | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgroups | Nej | Nej |
| hostpools | Nej | Nej |
| Arbets ytor | Nej | Nej |

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flytt åtgärden.

## <a name="next-steps"></a>Nästa steg
För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](resource-group-move-resources.md).

Hämta samma data som en fil med kommaavgränsade värden genom att ladda ned [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
