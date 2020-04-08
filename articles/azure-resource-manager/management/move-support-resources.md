---
title: Flytta åtgärdsstöd efter resurstyp
description: Visar en lista över de Azure-resurstyper som kan flyttas till en ny resursgrupp eller prenumeration.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 90fbec4dc076feb1fee8c38cf9757d3c5ddbafaf
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804800"
---
# <a name="move-operation-support-for-resources"></a>Resurser som kan flyttas
I den här artikeln visas om en Azure-resurstyp stöder flyttåtgärden. Det ger också information om särskilda villkor att tänka på när du flyttar en resurs.

Gå till ett namnområde för resursleverantören:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam (på nytt)](#microsoftaadiam)
> - [Microsoft.Advisor (Av )](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiHanagement](#microsoftapimanagement)
> - [Microsoft.AppKonfigurering](#microsoftappconfiguration)
> - [Microsoft.AppPlattform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Fakturering](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalk-tjänster](#microsoftbiztalkservices)
> - [Microsoft.Blockchain (på andra)](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint (Microsoft.Blueprint)](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.Kognition](#microsoftcognition)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Förbrukning](#microsoftconsumption)
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
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experiment](#microsoftexperimentation)
> - [Microsoft.Falcon (olika)](#microsoftfalcon)
> - [Microsoft.Genomik](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra (på andra)](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights microsoft.insights microsoft.insights microsoft.](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
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
> - [Microsoft.Underhåll](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrera](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum (På andra)](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resurser](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Sök](#microsoftsearch)
> - [Microsoft.Säkerhet](#microsoftsecurity)
> - [Microsoft.SecurityInights](#microsoftsecurityinsights)
> - [Microsoft.ServerHanagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Tjänster](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
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
> - [Microsoft.Prenumeration](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domäntjänster | Inga | Inga |

## <a name="microsoftaadiam"></a>microsoft.aadiam (på nytt)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Hyresgäster | Inga | Inga |

## <a name="microsoftadvisor"></a>Microsoft.Advisor (Av )

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Konfigurationer | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | undertryckanden | Inga | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | actionregler | Ja | Ja |
> | aviseringar | Inga | Inga |
> | alertssummary | Inga | Inga |
> | smartdetectoralertrules | Ja | Ja |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Servrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiHanagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | tjänst | Ja | Ja |

> [!IMPORTANT]
> En API Management-tjänst som är inställd på SKU-förbrukning kan inte flyttas.

## <a name="microsoftappconfiguration"></a>Microsoft.AppKonfigurering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ja | Ja |

## <a name="microsoftappplatform"></a>Microsoft.AppPlattform

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Våren | Ja | Ja |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | apiapps (apiapps) | Inga | Inga |
> | appid entiteter | Inga | Inga |
> | Gateways | Inga | Inga |

> [!IMPORTANT]
> Se [riktlinjer för flyttvägledning för App-tjänsten](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | checktillgång | Inga | Inga |
> | denyassignments | Inga | Inga |
> | findorphanroleassignments | Inga | Inga |
> | Lås | Inga | Inga |
> | Behörigheter | Inga | Inga |
> | policytilldelningar | Inga | Inga |
> | policydefinitioner | Inga | Inga |
> | policysetdefinitioner | Inga | Inga |
> | rolltilldelningar | Inga | Inga |
> | rolltilldelningaranvändaremetri | Inga | Inga |
> | rolldefinitioner | Inga | Inga |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | automationskonton | Ja | Ja |
> | automationskonton / konfigurationer | Ja | Ja |
> | automationskonton / runbooks | Ja | Ja |

> [!IMPORTANT]
> Runbooks måste finnas i samma resursgrupp som Automation-kontot.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | b2kriktorier | Ja | Ja |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Inga | Inga |
> | eftergresinstances | Inga | Inga |
> | sqlinstances | Inga | Inga |
> | sqlserverregistrations | Ja | Ja |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Registreringar | Ja | Ja |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ja | Ja |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Kluster | Inga | Inga |
> | filservrar | Inga | Inga |
> | Jobb | Inga | Inga |
> | arbetsytor | Inga | Inga |

## <a name="microsoftbilling"></a>Microsoft.Fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | faktureringsperioder | Inga | Inga |
> | faktureringsuppdrag | Inga | Inga |
> | faktureringsanmälan | Inga | Inga |
> | faktureringroledefinitioner | Inga | Inga |
> | skapabillningsanmälan | Inga | Inga |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | mapapis (mapapis) | Inga | Inga |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalk-tjänster

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | biztalk | Inga | Inga |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain (på andra)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Inga | Inga |
> | cordamembers | Inga | Inga |
> | Watchers | Inga | Inga |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | tokentjänster | Inga | Inga |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint (Microsoft.Blueprint)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | ritningartilldelningar | Inga | Inga |
> | Ritningar | Inga | Inga |

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
> Om Azure Cache for Redis-instansen är konfigurerad med ett virtuellt nätverk kan instansen inte flyttas till en annan prenumeration. Se Begränsningar [för nätverksflyttning](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Ja | Ja |
> | Profiler | Ja | Ja |
> | profiler/slutpunkter | Ja | Ja |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | certifikatorder | Ja | Ja |

> [!IMPORTANT]
> Se [riktlinjer för flyttvägledning för App-tjänsten](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | domännamn | Ja | Inga |
> | virtualmachines (virtuellamaskiner) | Ja | Inga |

> [!IMPORTANT]
> Se [Vägledning för klassisk distributionsflyttning](./move-limitations/classic-model-move-limitations.md). Klassiska distributionsresurser kan flyttas över prenumerationer med en åtgärd som är specifik för det scenariot.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Inga | Inga |
> | reservedips | Inga | Inga |
> | virtualnetworks | Inga | Inga |

> [!IMPORTANT]
> Se [Vägledning för klassisk distributionsflyttning](./move-limitations/classic-model-move-limitations.md). Klassiska distributionsresurser kan flyttas över prenumerationer med en åtgärd som är specifik för det scenariot.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | lagringskonton | Ja | Inga |

> [!IMPORTANT]
> Se [Vägledning för klassisk distributionsflyttning](./move-limitations/classic-model-move-limitations.md). Klassiska distributionsresurser kan flyttas över prenumerationer med en åtgärd som är specifik för det scenariot.

## <a name="microsoftcognition"></a>Microsoft.Kognition

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | syntetmaterialserar | Inga | Inga |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | tillgänglighetsuppsättningar | Ja | Ja |
> | diskencryptionsets | Inga | Inga |
> | Diskar | Ja | Ja |
> | Gallerier | Inga | Inga |
> | gallerier / bilder | Inga | Inga |
> | gallerier / bilder / versioner | Inga | Inga |
> | värdgrupper | Inga | Inga |
> | värdgrupper / värdar | Inga | Inga |
> | images | Ja | Ja |
> | närgrupper | Ja | Ja |
> | återställningspunkter | Inga | Inga |
> | sharedvmextensions sharedvmextensions sharedvmextensions sharedv | Inga | Inga |
> | sharedvmimages sharedvmimages sharedvmimages sharedv | Inga | Inga |
> | sharedvmimages / versioner | Inga | Inga |
> | snapshots | Ja | Ja |
> | sshpublickeys (sshpublickeys) | Inga | Inga |
> | virtualmachines (virtuellamaskiner) | Ja | Ja |
> | virtualmachines / tillägg | Ja | Ja |
> | virtualmachinescalesets | Ja | Ja |

> [!IMPORTANT]
> Se [flytta vägledning för virtuella datorer](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aggregeradcost | Inga | Inga |
> | Saldon | Inga | Inga |
> | Budgetar | Inga | Inga |
> | Avgifter | Inga | Inga |
> | kostnadsbrickor | Inga | Inga |
> | Krediter | Inga | Inga |
> | händelser | Inga | Inga |
> | Prognoser | Inga | Inga |
> | Massor | Inga | Inga |
> | Marknadsplatser | Inga | Inga |
> | driftresultat | Inga | Inga |
> | funktionsstatus | Inga | Inga |
> | prisblad | Inga | Inga |
> | Produkter | Inga | Inga |
> | reservationdetails | Inga | Inga |
> | reservationrecommendations | Inga | Inga |
> | reservationer sammanfattningar | Inga | Inga |
> | reservationtransaktioner | Inga | Inga |
> | tags | Inga | Inga |
> | Hyresgäster | Inga | Inga |
> | Villkor | Inga | Inga |
> | användningsdetaljer | Inga | Inga |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containergrupper | Inga | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | containergrupper | Inga | Inga |
> | serviceassociationlänkar | Inga | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft.Container-registret

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Register | Ja | Ja |
> | register / agentpools | Inga | Inga |
> | register / buildtasks | Ja | Ja |
> | register /replikeringar | Ja | Ja |
> | register / aktivitetsformulär | Ja | Ja |
> | register /uppgifter | Ja | Ja |
> | register / webhooks | Ja | Ja |

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
> | program | Inga | Inga |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics Microsoft.CortanaAnalytics Microsoft.CortanaAnalytics Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aviseringar | Inga | Inga |
> | Budgetar | Inga | Inga |
> | anslutningar | Ja | Ja |
> | Dimensioner | Inga | Inga |
> | Export | Inga | Inga |
> | externa prenumerationer | Inga | Inga |
> | forecast | Inga | Inga |
> | DocumentDB | Inga | Inga |
> | rapportkonfigs | Inga | Inga |
> | rapporter | Inga | Inga |
> | showbackrules | Inga | Inga |
> | Visningar | Inga | Inga |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | nav | Inga | Inga |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Sammanslutningar | Inga | Inga |
> | resurserproviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Jobb | Inga | Inga |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Inga | Inga |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Inga | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Kataloger | Ja | Ja |
> | datakategorier | Inga | Inga |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Inga | Inga |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Paket | Inga | Inga |
> | Planer | Inga | Inga |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datafakta | Ja | Ja |
> | Fabriker | Ja | Ja |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datalakekonton | Inga | Inga |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.

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
> | tjänster /projekt | Inga | Inga |
> | Platser | Inga | Inga |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | backupvaults | Inga | Inga |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Servrar | Ja | Ja |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Servrar | Ja | Ja |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | servergrupper | Inga | Inga |
> | Servrar | Ja | Ja |
> | servrarv2 | Ja | Ja |
> | singleservers (singleservers) | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | artefaktkälla | Ja | Ja |
> | Utbyggnader | Ja | Ja |
> | servicetopologier | Ja | Ja |
> | servicetopologier / tjänster | Ja | Ja |
> | servicetopologier / tjänster / serviceunits | Ja | Ja |
> | steg | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | programgrupper | Inga | Inga |
> | värdpooler | Inga | Inga |
> | arbetsytor | Inga | Inga |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | elasticpools | Inga | Inga |
> | elasticpools / iothubtenants | Inga | Inga |
> | iothubs | Ja | Ja |
> | etableringstjänster | Ja | Ja |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Rörledningar | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Styrenheter | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labcenters | Inga | Inga |
> | Labs | Ja | Inga |
> | laboratorier / miljöer | Ja | Ja |
> | labb / servicerunners | Ja | Ja |
> | laboratorier / virtualmachines | Ja | Inga |
> | Scheman | Ja | Ja |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Inga | Inga |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | databaskonton | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Domäner | Ja | Ja |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Domäner | Ja | Ja |
> | händelseAbonnemang | Nej - kan inte flyttas oberoende av sig men flyttas automatiskt med prenumererad resurs. | Nej - kan inte flyttas oberoende av sig men flyttas automatiskt med prenumererad resurs. |
> | händelserteckningar | Nej - kan inte flyttas oberoende av sig men flyttas automatiskt med prenumererad resurs. | Nej - kan inte flyttas oberoende av sig men flyttas automatiskt med prenumererad resurs. |
> | extensiontopics | Inga | Inga |
> | partnernamnområden | Ja | Ja |
> | partnerregistreringar | Inga | Inga |
> | partnertopik | Ja | Ja |
> | systemtopik | Ja | Ja |
> | Ämnen | Ja | Ja |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Kluster | Ja | Ja |
> | Namnområden | Ja | Ja |

## <a name="microsoftexperimentation"></a>Microsoft.Experiment

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | experimentarbetsytor | Inga | Inga |

## <a name="microsoftfalcon"></a>Microsoft.Falcon (olika)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Namnområden | Ja | Ja |

## <a name="microsoftgenomics"></a>Microsoft.Genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Inga | Inga |
> | automanagedvmkonfigurationsprofiler | Inga | Inga |
> | gästkonfigurationertilldelningar | Inga | Inga |
> | Programvara | Inga | Inga |
> | softwareupdateprofile | Inga | Inga |
> | softwareupdates | Inga | Inga |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hanainstances (hanainstances) | Inga | Inga |
> | sapmonitors (sapmonitors) | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Kluster | Ja | Ja |

> [!IMPORTANT]
> Du kan flytta HDInsight-kluster till en ny prenumeration eller resursgrupp. Du kan dock inte flytta de nätverksresurser som är länkade till HDInsight-klustret över prenumerationer över prenumerationer (till exempel det virtuella nätverket, nätverkskortet eller belastningsutjämnaren). Dessutom kan du inte flytta ett nätverkskort som är kopplat till en virtuell dator för klustret till en ny resursgrupp.
>
> När du flyttar ett HDInsight-kluster till en ny prenumeration flyttar du först andra resurser (till exempel lagringskontot). Flytta sedan HDInsight-klustret av sig själv.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Maskiner | Ja | Ja |
> | maskiner / förlängningar | Inga | Inga |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | datamanagers | Ja | Ja |

## <a name="microsofthydra"></a>Microsoft.Hydra (på andra)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Komponenter | Inga | Inga |
> | nätverkskop | Inga | Inga |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Jobb | Ja | Ja |

## <a name="microsoftinsights"></a>microsoft.insights microsoft.insights microsoft.insights microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | aktionsgrupper | Ja | Ja |
> | aktivitetslogalerts | Inga | Inga |
> | varningsreglerna | Ja | Ja |
> | automatisk skalning | Ja | Ja |
> | Originalplan | Inga | Inga |
> | beräknabasrad | Inga | Inga |
> | Komponenter | Ja | Ja |
> | datasamlingsrules | Inga | Inga |
> | diagnostikinställningar | Inga | Inga |
> | diagnostikläggarekategorier | Inga | Inga |
> | händelsetyper | Inga | Inga |
> | utökade diagnoser | Inga | Inga |
> | gästdiagnostik | Inga | Inga |
> | logdefinitioner | Inga | Inga |
> | loggar | Inga | Inga |
> | metricalerter | Inga | Inga |
> | metricbaselines | Inga | Inga |
> | metriska definitioner | Inga | Inga |
> | metricnamespaces | Inga | Inga |
> | metrics | Inga | Inga |
> | myworkbooks | Inga | Inga |
> | anmälan grupper | Inga | Inga |
> | privatelinkscopes | Ja | Ja |
> | schemalagdarurules | Ja | Ja |
> | topologi | Inga | Inga |
> | transaktioner | Inga | Inga |
> | vminsightsonboardingstatuses vminsightsonboardingstatuses vminsightsonboardingstatuses vmins | Inga | Inga |
> | webbtests | Ja | Ja |
> | arbetsböcker | Ja | Ja |
> | arbetsboksmallar | Ja | Ja |

> [!IMPORTANT]
> Se till att övergången till ny prenumeration inte överskrider [prenumerationskvoterna.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | iotapps (iotapps) | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | kontrollera namntillgänglighet | Ja | Ja |
> | Diagram | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hsmpools (hsmpools) | Inga | Inga |
> | Valv | Ja | Ja |

> [!IMPORTANT]
> Nyckelvalv som används för diskkryptering kan inte flyttas till en resursgrupp i samma prenumeration eller mellan prenumerationer.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | anslutnakluster | Inga | Inga |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Kluster | Ja | Ja |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | labaccounts | Inga | Inga |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices Microsoft.LocationBasedServices Microsoft.LocationBasedServices Microsoft.

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
> | värdmiljöer | Inga | Inga |
> | integrationskonton | Ja | Ja |
> | integrationstjänstermiljöer | Ja | Inga |
> | integrationstjänstermiljöer / managedapis | Ja | Inga |
> | isoleramiljöer | Inga | Inga |
> | Arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLärning

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | åtagandeplaner | Inga | Inga |
> | Webservices | Ja | Inga |
> | arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | operationaliseringklustor | Inga | Inga |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |
> | konton / arbetsytor | Inga | Inga |
> | konton / arbetsytor / projekt | Inga | Inga |
> | teamkonton | Inga | Inga |
> | teamaccounts / arbetsytor | Inga | Inga |
> | teamaccounts / arbetsytor / projekt | Inga | Inga |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization Microsoft.MachineLearningOperationalization Microsoft.MachineLearningOperationalization Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hostingkonton | Inga | Inga |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Inga | Inga |
> | arbetsytor / beräkningar | Inga | Inga |

## <a name="microsoftmaintenance"></a>Microsoft.Underhåll

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applyupdates | Inga | Inga |
> | konfigurationtilldelningar | Inga | Inga |
> | underhållskonfigurationer | Ja | Ja |
> | uppdateringar | Inga | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Identiteter | Inga | Inga |
> | userassignedid entiteter | Inga | Inga |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | managednetworks | Inga | Inga |
> | managednetworks/managednetworkgroups | Inga | Inga |
> | managednetworks / managednetworkpeeringpolicies | Inga | Inga |
> | avisering | Inga | Inga |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | registreringstilldelningar | Inga | Inga |
> | registrationdefinitioner | Inga | Inga |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | konton / privateatlases | Ja | Ja |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Inga | Inga |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | medietjänster | Ja | Ja |
> | mediaservices / liveevents | Ja | Ja |
> | mediatjänster / streamingendpoints | Ja | Ja |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | appclusters (appclusters) | Inga | Inga |

## <a name="microsoftmigrate"></a>Microsoft.Migrera

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | bedömningsprojekt | Ja | Ja |
> | migreraprojekt | Ja | Ja |
> | flyttakolleringar | Inga | Inga |
> | Projekt | Inga | Inga |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | netappaccounts (netappaccounts) | Inga | Inga |
> | netappaccounts / backuppolicies | Inga | Inga |
> | netappaccounts / capacitypools | Inga | Inga |
> | netappaccounts / capacitypools / volymer | Inga | Inga |
> | netappaccounts / capacitypools / volymer / mounttargets | Inga | Inga |
> | netappaccounts / capacitypools / volymer / ögonblicksbilder | Inga | Inga |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationgateways (appliceringsvägar) | Inga | Inga |
> | applicationgatewaywebapplicationfirewallpolicies | Inga | Inga |
> | applicationsecuritygroups | Ja | Ja |
> | azurefirewalls (azurefirewalls) | Ja | Ja |
> | bastionhosts (bastionhosts) | Inga | Inga |
> | Anslutningar | Ja | Ja |
> | ddoscustompolicies | Ja | Ja |
> | ddosprotectionplans | Inga | Inga |
> | dnszones | Ja | Ja |
> | expressroutecircuits | Inga | Inga |
> | expressroutegateways | Inga | Inga |
> | brandväggar | Ja | Ja |
> | frontdörrar | Inga | Inga |
> | frontdoorwebapplicationfirewallpolicies | Inga | Inga |
> | ipgroups (ipgroups) | Ja | Ja |
> | loadbalancers | Ja - Grundläggande SKU<br>Nej - Standard SKU | Ja - Grundläggande SKU<br>Nej - Standard SKU |
> | localnetworkgateways | Ja | Ja |
> | natgateways (natgateways) | Ja | Ja |
> | nätverksexperimentprofiler | Ja | Ja |
> | nätverkintentpolitik | Ja | Ja |
> | nätverksinterfaces | Ja | Ja |
> | nätverksprofiler | Inga | Inga |
> | networksecuritygroups | Ja | Ja |
> | nätverkwatchers | Ja | Inga |
> | networkwatchers / connectionmonitors | Ja | Inga |
> | networkwatchers / flödesloggar | Ja | Inga |
> | networkwatchers / pingmeshes | Ja | Inga |
> | p2svpngateways | Inga | Inga |
> | privatednszones | Ja | Ja |
> | privatednszones / virtualnetworklinks | Ja | Ja |
> | privataredirectredirectmaps | Inga | Inga |
> | privateendpoints | Ja | Ja |
> | privatelinkservices | Inga | Inga |
> | offentliga ipaddresses | Ja - Grundläggande SKU<br>Nej - Standard SKU | Ja - Grundläggande SKU<br>Nej - Standard SKU |
> | offentliga offentliga korrigeringar | Ja | Ja |
> | ruttfilter | Inga | Inga |
> | rutttabeller | Ja | Ja |
> | serviceendpointpolicies | Ja | Ja |
> | trafficmanagerprofiles | Ja | Ja |
> | virtualhubs | Inga | Inga |
> | virtualnetworkgateways | Ja | Ja |
> | virtualnetworks | Ja | Ja |
> | virtualnetworktaps | Inga | Inga |
> | virtualrouters (virtualrouters) | Ja | Ja |
> | virtualwans (virtualwans) | Inga | Inga |
> | vpngateways (VirtuellT WAN) | Inga | Inga |
> | vpnserverkonfigurationer | Inga | Inga |
> | vpnsites (VirtuellT WAN) | Inga | Inga |
> | webbapplikationfirewallpolicies | Ja | Ja |

> [!IMPORTANT]
> Se [Vägledning för nätverksflyttning](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Namnområden | Ja | Ja |
> | namnområden / anmälanhubs | Ja | Ja |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Kluster | Inga | Inga |
> | lagringsplatsernas ljuskonfigs | Inga | Inga |
> | arbetsytor | Ja | Ja |

> [!IMPORTANT]
> Se till att övergången till ny prenumeration inte överskrider [prenumerationskvoterna.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | managementassociations | Inga | Inga |
> | hanteringskonfigurationer | Ja | Ja |
> | lösningar | Ja | Ja |
> | Visningar | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | peerings | Ja | Ja |
> | peeringtjänster | Inga | Inga |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | policyevents | Inga | Inga |
> | policystates | Inga | Inga |
> | policytrackedresources | Inga | Inga |
> | avhjälpande åtgärder | Inga | Inga |

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
> | arbetsytesamlingar | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Kapacitet | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Inga | Inga |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Utbyggnader | Inga | Inga |

## <a name="microsoftquantum"></a>Microsoft.Quantum (På andra)

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Inga | Inga |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | backupskyddadeobjekt | Inga | Inga |
> | replikeringsberättigande resultat | Inga | Inga |
> | Valv | Ja | Ja |

> [!IMPORTANT]
> Se [vägledning för återställningstjänster.](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Inga | Inga |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Namnområden | Ja | Ja |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Frågor | Ja | Ja |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | tillgänglighetstatusar | Inga | Inga |
> | barntillgänglighetstatusar | Inga | Inga |
> | barnresurser | Inga | Inga |
> | händelser | Inga | Inga |
> | meddelanden | Inga | Inga |

## <a name="microsoftresources"></a>Microsoft.Resurser

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Inga | Inga |
> | Länkar | Inga | Inga |
> | tags | Inga | Inga |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Inga |

## <a name="microsoftsearch"></a>Microsoft.Sök

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | söktjänster | Ja | Ja |

> [!IMPORTANT]
> Du kan inte flytta flera sökresurser i olika regioner i en åtgärd. Flytta dem i stället i separata operationer.

## <a name="microsoftsecurity"></a>Microsoft.Säkerhet

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Inga | Inga |
> | avanceradereatskyddssinställningar | Inga | Inga |
> | bedömningsmetadata | Inga | Inga |
> | utvärderingar | Inga | Inga |
> | automatiseringar | Ja | Ja |
> | resultat av efterlevnad | Inga | Inga |
> | efterlevnad | Inga | Inga |
> | datainsamlingsagenter | Inga | Inga |
> | enheter säkerhetsgrupper | Inga | Inga |
> | informationsskyddspolitik | Inga | Inga |
> | iotsecuritysolutions | Ja | Ja |
> | serversårbarhetsbesörningar | Inga | Inga |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Aggregeringar | Inga | Inga |
> | varningsreglerna | Inga | Inga |
> | alertruletemplates | Inga | Inga |
> | bokmärken | Inga | Inga |
> | Fall | Inga | Inga |
> | dataanknyter | Inga | Inga |
> | dataconnectorscheckerkquirements | Inga | Inga |
> | Enheter | Inga | Inga |
> | entityqueries | Inga | Inga |
> | Incidenter | Inga | Inga |
> | kontorskonsenter | Inga | Inga |
> | settings | Inga | Inga |

## <a name="microsoftservermanagement"></a>Microsoft.ServerHanagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Gateways | Inga | Inga |
> | Noder | Inga | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Namnområden | Ja | Ja |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Inga | Inga |
> | Kluster | Ja | Ja |
> | kluster / applikationer | Inga | Inga |
> | containergrupper | Inga | Inga |
> | containersgroupsets | Inga | Inga |
> | kantklusor | Inga | Inga |
> | managedclusters | Inga | Inga |
> | Nätverk | Inga | Inga |
> | hemliga butiker | Inga | Inga |
> | volumes | Inga | Inga |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | program | Ja | Ja |
> | containergrupper | Inga | Inga |
> | Gateways | Ja | Ja |
> | Nätverk | Ja | Ja |
> | secrets | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft.Tjänster

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Utbyggnader | Inga | Inga |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | signalr | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | hybridanvändningspassningar | Inga | Inga |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | applicationdefinitionss | Inga | Inga |
> | program | Inga | Inga |
> | jitrequests (påt) | Inga | Inga |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | instanspooler | Inga | Inga |
> | managedinstances | Inga | Inga |
> | managedinstances / databaser | Inga | Inga |
> | Servrar | Ja | Ja |
> | servrar / databaser | Ja | Ja |
> | servrar / elasticpools | Ja | Ja |
> | servrar / jobbkonton | Ja | Ja |
> | servrar / jobagents | Ja | Ja |
> | virtualclusters (virtuellakluster) | Ja | Ja |

> [!IMPORTANT]
> En databas och server måste finnas i samma resursgrupp. När du flyttar en SQL-server flyttas även alla dess databaser. Det här problemet gäller Azure SQL Database och Azure SQL Data Warehouse-databaser.

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
> | dwvm (dwvm) | Inga | Inga |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | lagringskonton | Ja | Ja |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Cachar | Inga | Inga |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | lagringssynkroniseringar | Ja | Ja |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | lagringssynkroniseringar | Inga | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | lagringssynkroniseringar | Inga | Inga |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Chefer | Inga | Inga |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | streamingjobb | Ja | Ja |

> [!IMPORTANT]
> Stream Analytics-jobb kan inte flyttas när de körs.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Miljöer | Inga | Inga |
> | miljöer/eventsources | Inga | Inga |
> | Instanser | Inga | Inga |
> | instanser / miljöer | Inga | Inga |
> | instanser / miljöer / eventsources | Inga | Inga |

## <a name="microsoftsubscription"></a>Microsoft.Prenumeration

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | skaparteckning | Inga | Inga |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | supportbiljetter | Inga | Inga |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | arbetsytor | Inga | Inga |
> | arbetsytor / bigdatapools | Inga | Inga |
> | arbetsytor / sqlpools | Inga | Inga |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | leverantörsregistreringar | Inga | Inga |
> | resources | Inga | Inga |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Miljöer | Ja | Ja |
> | miljöer/eventsources | Ja | Ja |
> | miljöer / referensdataset | Ja | Ja |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Butiker | Ja | Ja |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | bildskyltar | Inga | Inga |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konto | Inga | Inga |
> | konto / förlängning | Ja | Ja |
> | konto / projekt | Ja | Ja |

> [!IMPORTANT]
> Information om hur du ändrar prenumerationen för Azure DevOps läser du [ändra Azure-prenumerationen som används för fakturering](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | dedikerade molnen | Inga | Inga |
> | dedikerade molntjänster | Inga | Inga |
> | virtualmachines (virtuellamaskiner) | Inga | Inga |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | enheter | Inga | Inga |
> | vnfs (vnfs) | Inga | Inga |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | konton | Ja | Ja |
> | Planer | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | certifikat | Inga | Ja |
> | connectiongateways | Ja | Ja |
> | Anslutningar | Ja | Ja |
> | customapis (customapis) | Ja | Ja |
> | värdmiljöer | Inga | Inga |
> | kubeenvironments | Ja | Ja |
> | serverfarms | Ja | Ja |
> | webbplatser | Ja | Ja |
> | platser / premieraddons | Ja | Ja |
> | platser / platser | Ja | Ja |
> | statikplatser | Inga | Inga |

> [!IMPORTANT]
> Se [riktlinjer för flyttvägledning för App-tjänsten](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | fleraaktiveringsnycklar | Inga | Inga |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | enhetstjänster | Inga | Inga |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Resursgrupp | Prenumeration |
> | ------------- | ----------- | ---------- |
> | Komponenter | Inga | Inga |
> | monitorintances | Inga | Inga |
> | Bildskärmar | Inga | Inga |
> | meddelanden | Inga | Inga |

## <a name="third-party-services"></a>Tjänster från tredje part

Tjänster från tredje part stöder för närvarande inte flyttåtgärden.

## <a name="next-steps"></a>Nästa steg
Kommandon som ska flyttas finns i [Flytta resurser till ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).

Om du vill hämta samma data som en fil med kommaavgränsade värden hämtar du [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
