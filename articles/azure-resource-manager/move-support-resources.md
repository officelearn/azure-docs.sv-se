---
title: Flytta stöd genom Azure-resursgrupp
description: Visar en lista över vilka typer av Azure-resurs som kan flyttas till en ny resursgrupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 2/13/2019
ms.author: tomfitz
ms.openlocfilehash: f82c3adc0049c7386206fb4be10d3e634627a25a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236897"
---
# <a name="move-operation-support-for-resources"></a>Flytta åtgärden stöd för resurser
Den här artikeln visar om en Azure-resurstypen stöder flyttåtgärden. Även om en resurstyp stöder flyttåtgärden, kan det finnas villkor som hindrar resursen flyttas. Mer information om villkor som påverkar flyttåtgärder finns i [flytta resurser till ny resursgrupp eller prenumeration](resource-group-move-resources.md).

För att få samma data som en fil med kommaavgränsade värden kan hämta [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| domainservices | Nej | Nej |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Klienter | Nej | Nej |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| servrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| tjänst | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| apiapps | Nej | Nej |
| appidentities | Nej | Nej |
| Gateways | Nej | Nej |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| policyassignments | Nej | Nej |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| automationaccounts | Ja | Ja |
| automationaccounts/konfigurationer | Ja | Ja |
| automationaccounts/runbooks | Ja | Ja |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| b2cdirectories | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| registreringar | Ja | Ja |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| backupvault | Nej | Nej |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| batchaccounts | Ja | Ja |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Kluster | Nej | Nej |
| fileservers | Nej | Nej |
| jobb | Nej | Nej |
| arbetsytor | Nej | Nej |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| mapapis | Nej | Nej |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| biztalk | Ja | Ja |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| blockchainmembers | Nej | Nej |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| blueprintassignments | Nej | Nej |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| botservices | Ja | Ja |

## <a name="microsoftcache"></a>Microsoft.Cache
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Redis | Ja | Ja |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Profiler | Ja | Ja |
| profiler-slutpunkter | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| certificateorders | Ja | Ja |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| domainnames | Ja | Nej |
| virtuella datorer | Ja | Nej |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Nej | Nej |
| reservedips | Nej | Nej |
| virtualnetworks | Nej | Nej |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| storageaccounts | Ja | Nej |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| availabilitysets | Ja | Ja |
| Diskar | Ja | Ja |
| gallerier | Nej | Nej |
| gallerier/avbildningar | Nej | Nej |
| gallerier/bilder/versioner | Nej | Nej |
| images | Ja | Ja |
| proximityplacementgroups | Nej | Nej |
| restorepointcollections | Nej | Nej |
| sharedvmimages | Nej | Nej |
| sharedvmimages/versions | Nej | Nej |
| ögonblicksbilder | Ja | Ja |
| virtuella datorer | Ja | Ja |
| virtuella datorer /-tillägg | Ja | Ja |
| virtualmachinescalesets | Ja | Ja |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| containergroups | Nej | Nej |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| containergroups | Nej | Nej |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| register | Ja | Ja |
| register/buildtasks | Ja | Ja |
| register/replikeringar | Nej | Nej |
| register/uppgifter | Ja | Ja |
| register/webhooks | Ja | Ja |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| containerservices | Nej | Nej |
| managedclusters | Nej | Nej |
| openshiftmanagedclusters | Nej | Nej |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| program | Ja | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Nej | Nej |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| anslutningar | Ja | Ja |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| hubbar | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| jobb | Nej | Nej |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Nej | Nej |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| arbetsytor | Nej | Nej |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| kataloger | Ja | Ja |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| connectionmanagers | Nej | Nej |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Paket | Nej | Nej |
| Planer | Nej | Nej |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| datafactories | Ja | Ja |
| fabriker | Ja | Ja |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Nej | Nej |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| services | Nej | Nej |
| Services-projekt | Nej | Nej |
| fack | Nej | Nej |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| servrar | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| servrar | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| servergroups | Nej | Nej |
| servrar | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| artifactsources | Nej | Nej |
| Distributioner | Nej | Nej |
| servicetopologies | Nej | Nej |
| servicetopologies/tjänster | Nej | Nej |
| servicetopologies/services/serviceunits | Nej | Nej |
| steg | Nej | Nej |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| elasticpools | Nej | Nej |
| elasticpools/iothubtenants | Nej | Nej |
| iothubs | Ja | Ja |
| provisioningservices | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Domänkontrollanter | Nej | Nej |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| labcenters | Nej | Nej |
| Labs | Ja | Nej |
| Labs/servicerunners | Ja | Ja |
| Labs/virtuella datorer | Ja | Nej |
| Scheman | Nej | Nej |

## <a name="microsoftdns"></a>microsoft.dns
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| dnszones | Nej | Nej |
| dnszones/en | Nej | Nej |
| dnszones/aaaa | Nej | Nej |
| dnszones/cname | Nej | Nej |
| dnszones/mx | Nej | Nej |
| dnszones/ptr | Nej | Nej |
| dnszones/srv | Nej | Nej |
| dnszones/txt | Nej | Nej |
| trafficmanagerprofiles | Nej | Nej |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| databaseaccounts | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| domäner | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| domäner | Ja | Ja |
| ämnen | Ja | Ja |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Kluster | Ja | Ja |
| namnrymder | Ja | Ja |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Nej | Nej |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| hanainstances | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Kluster | Ja | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| datamanagers | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| jobb | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Nej | Nej |
| actiongroups | Ja | Ja |
| activitylogalerts | Nej | Nej |
| alertrules | Ja | Ja |
| autoscalesettings | Ja | Ja |
| Komponenter | Ja | Ja |
| guestdiagnosticsettings | Nej | Nej |
| metricalerts | Nej | Nej |
| notificationgroups | Nej | Nej |
| notificationrules | Nej | Nej |
| scheduledqueryrules | Nej | Nej |
| webbtester | Ja | Ja |
| arbetsböcker | Ja | Ja |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| iotapps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| checknameavailability | Ja | Ja |
| graf | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| hsmpools | Nej | Nej |
| Valv | Ja | Ja |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Kluster | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| labaccounts | Ja | Ja |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| hostingenvironments | Nej | Nej |
| integrationaccounts | Ja | Ja |
| integrationserviceenvironments | Nej | Nej |
| isolatedenvironments | Nej | Nej |
| Arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| commitmentplans | Ja | Ja |
| WebServices | Ja | Nej |
| arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Ja | Ja |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |
| konton/arbetsytor | Ja | Ja |
| konton-arbetsytor-projekt | Ja | Ja |
| teamaccounts | Ja | Ja |
| teamaccounts/arbetsytor | Ja | Ja |
| teamaccounts-arbetsytor-projekt | Ja | Ja |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| hostingaccounts | Nej | Nej |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| arbetsytor | Nej | Nej |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| userassignedidentities | Ja | Ja |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Ja | Ja |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| classicdevservices | Nej | Nej |

## <a name="microsoftmedia"></a>Microsoft.Media
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| mediaservices | Ja | Ja |
| mediaservices/liveevents | Ja | Ja |
| mediaservices/strömningsslutpunkter | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| assessmentprojects | Nej | Nej |
| migrateprojects | Nej | Nej |
| projekt | Nej | Nej |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| netappaccounts | Nej | Nej |
| netappaccounts/capacitypools | Nej | Nej |
| netappaccounts/capacitypools/volumes | Nej | Nej |
| netappaccounts/capacitypools/volumes/mounttargets | Nej | Nej |
| netappaccounts/capacitypools/volumes/snapshots | Nej | Nej |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| applicationgateways | Nej | Nej |
| applicationsecuritygroups | Ja | Ja |
| azurefirewalls | Ja | Ja |
| bastionhosts | Nej | Nej |
| anslutningar | Ja | Ja |
| ddoscustompolicies | Ja | Ja |
| ddosprotectionplans | Nej | Nej |
| dnszones | Ja | Ja |
| expressroutecircuits | Nej | Nej |
| expressroutecrossconnections | Nej | Nej |
| expressroutegateways | Nej | Nej |
| expressrouteports | Nej | Nej |
| frontdoors | Ja | Ja |
| frontdoorwebapplicationfirewallpolicies | Ja | Ja |
| interfaceendpoints | Nej | Nej |
| loadbalancers | Ja | Ja |
| localnetworkgateways | Ja | Ja |
| natgateways | Ja | Ja |
| networkintentpolicies | Ja | Ja |
| networkinterfaces | Ja | Ja |
| networkprofiles | Nej | Nej |
| networksecuritygroups | Ja | Ja |
| networkwatchers | Ja | Ja |
| networkwatchers/connectionmonitors | Ja | Ja |
| networkwatchers/linser | Ja | Ja |
| networkwatchers/pingmeshes | Ja | Ja |
| p2svpngateways | Nej | Nej |
| privatelinkservices | Nej | Nej |
| publicipaddresses | Ja | Ja |
| publicipprefixes | Ja | Ja |
| routefilters | Nej | Nej |
| routetables | Ja | Ja |
| securegateways | Nej | Nej |
| serviceendpointpolicies | Ja | Ja |
| trafficmanagerprofiles | Ja | Ja |
| virtualhubs | Nej | Nej |
| virtualnetworkgateways | Ja | Ja |
| virtualnetworks | Ja | Ja |
| virtualnetworktaps | Nej | Nej |
| virtualwans | Nej | Nej |
| vpngateways | Nej | Nej |
| vpnsites | Ja | Ja |
| webapplicationfirewallpolicies | Ja | Ja |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| namnrymder | Ja | Ja |
| namnområden/notificationhubs | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| arbetsytor | Ja | Ja |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| managementconfigurations | Ja | Ja |
| lösningar | Ja | Ja |
| visningar | Ja | Ja |

## <a name="microsoftportal"></a>Microsoft.Portal
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Instrumentpaneler | Ja | Ja |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| rootresources | Nej | Nej |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| workspacecollections | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Kapaciteter | Ja | Ja |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konton | Nej | Nej |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Valv | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| namnrymder | Ja | Ja |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| program | Ja | Nej |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| flöden | Ja | Ja |
| förfrågningsåtgärder | Ja | Ja |

## <a name="microsoftsearch"></a>Microsoft.Search
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| searchservices | Ja | Ja |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Gateways | Nej | Nej |
| noder | Nej | Nej |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| namnrymder | Ja | Ja |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| program | Nej | Nej |
| Kluster | Ja | Ja |
| containergroups | Nej | Nej |
| containergroupsets | Nej | Nej |
| edgeclusters | Nej | Nej |
| Nätverk | Nej | Nej |
| secretstores | Nej | Nej |
| volumes | Nej | Nej |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| program | Ja | Ja |
| containergroups | Nej | Nej |
| Gateways | Ja | Ja |
| Nätverk | Ja | Ja |
| secrets | Ja | Ja |
| volumes | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| signalr | Ja | Ja |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Nej | Nej |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Nej | Nej |
| installationer | Nej | Nej |
| applicationdefinitions | Nej | Nej |
| program | Nej | Nej |
| jitrequests | Nej | Nej |

## <a name="microsoftsql"></a>Microsoft.Sql
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| managedinstances | Ja | Ja |
| managedinstances/databaser | Ja | Ja |
| servrar | Ja | Ja |
| servrar/databaser | Ja | Ja |
| servrar/elasticpools | Ja | Ja |
| servrar/jobaccounts | Nej | Nej |
| servrar/jobagents | Nej | Nej |
| virtualclusters | Ja | Ja |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Ja | Ja |
| sqlvirtualmachines | Ja | Ja |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| dwvm | Nej | Nej |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| storageaccounts | Ja | Ja |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| storagesyncservices | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nej | Nej |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nej | Nej |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| chefer | Nej | Nej |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| streamingjobs | Ja | Ja |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Miljöer | Nej | Nej |
| miljöer/eventsources | Nej | Nej |
| instanser | Nej | Nej |
| instanser/miljöer | Nej | Nej |
| instanser/miljöer/eventsources | Nej | Nej |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| providerregistrations | Nej | Nej |
| resurser | Nej | Nej |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| Miljöer | Ja | Ja |
| miljöer/eventsources | Ja | Ja |
| miljöer/referencedatasets | Ja | Ja |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| imagetemplates | Nej | Nej |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| konto | Ja | Ja |
| tillägget och Account | Ja | Ja |
| kontot/projektet | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| certifikat | Nej | Ja |
| connectiongateways | Ja | Ja |
| anslutningar | Ja | Ja |
| customapis | Ja | Ja |
| hostingenvironments | Nej | Nej |
| servergrupper | Ja | Ja |
| Platser | Ja | Ja |
| sites/premieraddons | Ja | Ja |
| platser/platser | Ja | Ja |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Resurstyp | Resursgrupp | Prenumeration |
| ------------- | ----------- | ---------- |
| deviceservices | Ja | Ja |

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flyttåtgärden.

## <a name="next-steps"></a>Nästa steg
Kommandon att flytta resurser finns i [flytta resurser till ny resursgrupp eller prenumeration](resource-group-move-resources.md).
