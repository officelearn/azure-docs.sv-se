---
title: Taggstöd för resurser
description: Visar vilka Azure-resurstyper som stöder taggar. Innehåller information om alla Azure-tjänster.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: c971d3af102faf99f97aac261882797460d71f37
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255035"
---
# <a name="tag-support-for-azure-resources"></a>Taggstöd för Azure-resurser
I den här artikeln beskrivs om en resurstyp stöder [taggar](tag-resources.md). Kolumnen med etiketten **Stöder taggar** anger om resurstypen har en egenskap för taggen. Kolumnen Tagg **i kostnadsrapport** anger om resurstypen skickar taggen till kostnadsrapporten. Du kan visa kostnader efter taggar i [kostnadshanteringskostnadsanalysen](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) och [Azure-faktureringsfakturan och dagliga användningsdata](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Om du vill hämta samma data som en fil med kommaavgränsade värden hämtar [du tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Gå till ett namnområde för resursleverantören:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor (Av )](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiHanagement](#microsoftapimanagement)
> - [Microsoft.AppKonfigurering](#microsoftappconfiguration)
> - [Microsoft.AppPlattform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Fakturering](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain (på andra)](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint (Microsoft.Blueprint)](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Kapacitet](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastrukturMigera](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Förbrukning](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.Container-registret](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
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
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Falcon (olika)](#microsoftfalcon)
> - [Microsoft.Funktioner](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomik](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra (på andra)](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLärning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Underhåll](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management (På)Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceBeställa](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrera](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.Quantum (På andra)](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resurser](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Sök](#microsoftsearch)
> - [Microsoft.Säkerhet](#microsoftsecurity)
> - [Microsoft.SecurityGraph Microsoft.SecurityGraph Microsoft.SecurityGraph Microsoft.](#microsoftsecuritygraph)
> - [Microsoft.SecurityInights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Tjänster](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SpoolService](#microsoftspoolservice)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.](#microsoftstreamanalytics)
> - [Microsoft.Prenumeration](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ja | Ja |
> | DomainServices / oucontainer | Inga | Inga |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | supportProviders | Inga | Inga |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Inga | Inga |
> | adderartjänster | Inga | Inga |
> | Agenter | Inga | Inga |
> | anonymapiusers | Inga | Inga |
> | konfiguration | Inga | Inga |
> | loggar | Inga | Inga |
> | rapporter | Inga | Inga |
> | servicehealthmetrics | Inga | Inga |
> | services | Inga | Inga |

## <a name="microsoftadvisor"></a>Microsoft.Advisor (Av )

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Konfigurationer | Inga | Inga |
> | genereraKommendationer | Inga | Inga |
> | metadata | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | undertryckanden | Inga | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | actionRules | Ja | Ja |
> | aviseringar | Inga | Inga |
> | aviseringarLista | Inga | Inga |
> | varningarMetaData | Inga | Inga |
> | varningarSumma | Inga | Inga |
> | aviseringarSummaryList | Inga | Inga |
> | smartDetectorAlertRules | Ja | Ja |
> | smarta grupper | Inga | Inga |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Servrar | Ja | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiHanagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | rapportFeedback | Inga | Inga |
> | tjänst | Ja | Ja |
> | verifieraServiceName | Inga | Inga |

## <a name="microsoftappconfiguration"></a>Microsoft.AppKonfigurering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores / eventGridFilters | Inga | Inga |

## <a name="microsoftappplatform"></a>Microsoft.AppPlattform

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Spring | Ja | Ja |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Inga | Inga |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | klassiskaLäggare | Inga | Inga |
> | dataAliases | Inga | Inga |
> | nekaTilldelningar | Inga | Inga |
> | höjaAccess | Inga | Inga |
> | findOrphanRoleAssignments | Inga | Inga |
> | Lås | Inga | Inga |
> | Behörigheter | Inga | Inga |
> | policyAssignments | Inga | Inga |
> | policyDefinitioner | Inga | Inga |
> | policySetDefinitions | Inga | Inga |
> | providerOperations | Inga | Inga |
> | rollTilldelningar | Inga | Inga |
> | rollTilldelningarAnvändarnametri | Inga | Inga |
> | rollDefinitioner | Inga | Inga |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | automationKonton | Ja | Ja |
> | automationKonton / konfigurationer | Ja | Ja |
> | automationKonton / jobb | Inga | Inga |
> | automationAlän / privateEndpointConnectionProxies | Inga | Inga |
> | automationKonton / privateEndpointAnslutningar | Inga | Inga |
> | automationKonton / privateLinkResources | Inga | Inga |
> | automationKonton / runbooks | Ja | Ja |
> | automationKonto/programvaraUppdämpningar | Inga | Inga |
> | automationKonton / webhooks | Inga | Inga |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ja | Ja |
> | configurationStores / eventGridFilters | Inga | Inga |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Miljöer | Inga | Inga |
> | miljöer / konton | Inga | Inga |
> | miljöer / konton / namnområden | Inga | Inga |
> | miljöer / konton / namnrymd / konfigurationer | Inga | Inga |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | b2cKataloger | Ja | Inga |
> | b2ctenants (b2ctenants) | Inga | Inga |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Ja | Ja |
> | postgresInstances | Ja | Ja |
> | sqlInstances | Ja | Ja |
> | sqlServerRegistrations | Ja | Ja |
> | sqlServerRegistrations / sqlServers | Inga | Inga |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiler | Inga | Inga |
> | Registreringar | Ja | Ja |
> | registreringar / kundAbonnemang | Inga | Inga |
> | registreringar / produkter | Inga | Inga |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | batchKonton | Ja | Ja |

## <a name="microsoftbilling"></a>Microsoft.Fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | faktureringKonton | Inga | Inga |
> | faktureringKonton/avtal | Inga | Inga |
> | faktureringKonton /faktureringPermissions | Inga | Inga |
> | faktureringKonto/faktureringProfiler | Inga | Inga |
> | faktureringKonton / faktureringProfiler / faktureringPermissions | Inga | Inga |
> | faktureringKonto/faktureringProfiler / faktureringRolleAstilldelningar | Inga | Inga |
> | faktureringKonto / faktureringProfiler / faktureringRoleDefinitioner | Inga | Inga |
> | faktureringKonto/faktureringProfiler / faktureringAbonnemang | Inga | Inga |
> | faktureringKonto/faktureringProfiler / skapaFaktureringRollAstilldelning | Inga | Inga |
> | faktureringKonton / faktureringProfiler / kunder | Inga | Inga |
> | faktureringKonton / faktureringProfiler / instruktioner | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturor | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturor / prisblad | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturor / transaktioner | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / faktureringPermissions | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / faktureringRolEAstilldelningar | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / faktureringRoleDefinitioner | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / faktureringAbonnemang | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / skapaFakturering | Inga | Inga |
> | faktureringKonton /faktureringProfiler / fakturaAvsnitt / initieraTransfer | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / produkter | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / produkter / överföring | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / produkter / uppdateringAutoRenew | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / transaktioner | Inga | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / överföringar | Inga | Inga |
> | billingAccounts / BillingProfiles / patchOperations | Inga | Inga |
> | faktureringKonton / faktureringProfiler / betalningMethods | Inga | Inga |
> | faktureringKonton / faktureringProfiler / policyer | Inga | Inga |
> | faktureringKonton / faktureringProfiler / prisblad | Inga | Inga |
> | faktureringKonton / faktureringProfiler / prisbladLadda nedOperationer | Inga | Inga |
> | faktureringKonton / faktureringProfiler / produkter | Inga | Inga |
> | faktureringKonton / faktureringProfiler / transaktioner | Inga | Inga |
> | faktureringKonton /faktureringRollAtilldelningar | Inga | Inga |
> | faktureringKonto / faktureringRoleDefinitioner | Inga | Inga |
> | faktureringKonto/faktureringAbonnemang | Inga | Inga |
> | faktureringKonton / faktureringAbonnemang /fakturor | Inga | Inga |
> | billingAccounts / createBillingRoleAssignment | Inga | Inga |
> | billingAccounts / createInvoiceSectionOperations | Inga | Inga |
> | faktureringKonton / kunder | Inga | Inga |
> | faktureringKonton / kunder / faktureringPermissions | Inga | Inga |
> | faktureringKonton / kunder / faktureringAbonnemang | Inga | Inga |
> | faktureringKonton / kunder / initieraTransfer | Inga | Inga |
> | faktureringKonton / kunder / policyer | Inga | Inga |
> | faktureringKonton / kunder / produkter | Inga | Inga |
> | faktureringKonton / kunder / transaktioner | Inga | Inga |
> | faktureringKonton / kunder / överföringar | Inga | Inga |
> | faktureringKonton / avdelningar | Inga | Inga |
> | faktureringKonton / registreringKonton | Inga | Inga |
> | faktureringKonton/fakturor | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt / faktureringAbonnemangFlyttaOperationer | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt / faktureringAbonnemang | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt / faktureringAbonnemang /överföring | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt / höj | Inga | Inga |
> | faktureringKonton / fakturaAvsnitt / initieraTransfer | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt / patchOperations | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt / productMoveOperations | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt / produkter | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt / produkter / överföring | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt / produkter / uppdateringAutoRenew | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt /transaktioner | Inga | Inga |
> | faktureringKonton / fakturorAvsnitt /överföringar | Inga | Inga |
> | billingAccounts / lineOfCredit | Inga | Inga |
> | billingAccounts / patchOperations | Inga | Inga |
> | faktureringKonton / betalningMethods | Inga | Inga |
> | faktureringKonton / produkter | Inga | Inga |
> | faktureringKonton /transaktioner | Inga | Inga |
> | faktureringPerioder | Inga | Inga |
> | faktureringPermissions | Inga | Inga |
> | faktureringSförsörstighet | Inga | Inga |
> | billingRoleAstilldelningar | Inga | Inga |
> | faktureringRoleDefinitioner | Inga | Inga |
> | skapaBillingRoleAssignment | Inga | Inga |
> | Avdelningar | Inga | Inga |
> | registreringKonto | Inga | Inga |
> | Fakturor | Inga | Inga |
> | Överföringar | Inga | Inga |
> | överföringar / accepteraÖversändande | Inga | Inga |
> | överföringar / avböjTransfer | Inga | Inga |
> | överföringar /operationStatus | Inga | Inga |
> | överföringar/valideraÖverförflyttning | Inga | Inga |
> | valideraAdress | Inga | Inga |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | kartaApis | Ja | Ja |
> | uppdateraKommunikationsövertolkning | Inga | Inga |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain (på andra)

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ja | Ja |
> | cordaMembers | Ja | Ja |
> | Watchers | Ja | Ja |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | TokenServices | Ja | Ja |
> | TokenServices / BlockchainNetworks | Inga | Inga |
> | TokenServices / Grupper | Inga | Inga |
> | TokenServices / Grupper / Konton | Inga | Inga |
> | TokenServices / TokenTemplates | Inga | Inga |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint (Microsoft.Blueprint)

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | ritningarTilldelningar | Inga | Inga |
> | skissTilldelningar/tilldelningOperationer | Inga | Inga |
> | blueprintAssignments /operationer | Inga | Inga |
> | Ritningar | Inga | Inga |
> | ritningar / artefakter | Inga | Inga |
> | ritningar / versioner | Inga | Inga |
> | ritningar / versioner / artefakter | Inga | Inga |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | botServices | Ja | Ja |
> | botServices / kanaler | Inga | Inga |
> | botServices / anslutningar | Inga | Inga |
> | språk | Inga | Inga |
> | mallar | Inga | Inga |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Redis | Ja | Ja |

## <a name="microsoftcapacity"></a>Microsoft.Kapacitet

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Inga | Inga |
> | autoQuotaIncrease | Inga | Inga |
> | beräknaUtnytt på | Inga | Inga |
> | beräknaPris | Inga | Inga |
> | beräknaKöpPrit | Inga | Inga |
> | Kataloger | Inga | Inga |
> | kommersiellaReservationOrder | Inga | Inga |
> | Exchange | Inga | Inga |
> | platsKöpOrder | Inga | Inga |
> | reservationOrder | Inga | Inga |
> | reservationOrder / beräknaRefund | Inga | Inga |
> | reservationOrder / sammanfogning | Inga | Inga |
> | reservationOrder / reservationer | Inga | Inga |
> | reservationOrder / reservationer / revideringar | Inga | Inga |
> | reservationOrder / retur | Inga | Inga |
> | reservationOrder / split | Inga | Inga |
> | reservationOrder / swap | Inga | Inga |
> | Reservationer | Inga | Inga |
> | resurserProviders | Inga | Inga |
> | resources | Inga | Inga |
> | valideraReservationOrder | Inga | Inga |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSet | Inga | Inga |
> | CdnWebApplicationFirewallPolicies | Ja | Ja |
> | kantnoder | Inga | Inga |
> | Profiler | Ja | Ja |
> | profiler/slutpunkter | Ja | Ja |
> | profiler/slutpunkter / customdomains | Inga | Inga |
> | profiler/slutpunkter/ursprungsgrupper | Inga | Inga |
> | profiler/slutpunkter/ursprung | Inga | Inga |
> | valideraProbe | Inga | Inga |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | certifikatOrdar | Ja | Ja |
> | certifikatOrder/certifikat | Inga | Inga |
> | valideraCertificateRegistrationInformation | Inga | Inga |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | funktioner eller behörigheter | Inga | Inga |
> | domainNames (domainNames) | Inga | Inga |
> | domainNames /capabilities | Inga | Inga |
> | domainNames / internalLoadBalancers | Inga | Inga |
> | domainNames / serviceCertificates | Inga | Inga |
> | domainNames / slots | Inga | Inga |
> | domainNames / slots / roller | Inga | Inga |
> | domainNames / slots / roller / metricDefinitions | Inga | Inga |
> | domainNames / slots / roller / mått | Inga | Inga |
> | moveSubscriptionResources | Inga | Inga |
> | driftSystemFamilier | Inga | Inga |
> | driftSystem | Inga | Inga |
> | quotas | Inga | Inga |
> | resourceTypes | Inga | Inga |
> | valideraAbonnemangFlyttaTillgänglighet | Inga | Inga |
> | virtuellaMaskiner | Inga | Inga |
> | virtualMachines / diagnosticSettings | Inga | Inga |
> | virtualMachines / metricDefinitions | Inga | Inga |
> | virtualMachines / mått | Inga | Inga |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastrukturMigera

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | klassiskaInfrastrukturResurser | Inga | Inga |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | funktioner eller behörigheter | Inga | Inga |
> | expressRouteCrossAnslutningar | Inga | Inga |
> | expressRouteCrossConnections / peerings | Inga | Inga |
> | gatewaySupportedDevices | Inga | Inga |
> | nätverkSäkerhetsgrupper | Inga | Inga |
> | quotas | Inga | Inga |
> | reservedIps | Inga | Inga |
> | virtualNetworks | Inga | Inga |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Inga | Inga |
> | virtualNetworks / virtualNetworkPeerings | Inga | Inga |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | funktioner eller behörigheter | Inga | Inga |
> | Diskar | Inga | Inga |
> | images | Inga | Inga |
> | osImages (osImages) | Inga | Inga |
> | osPlatformImager | Inga | Inga |
> | offentligaBilder | Inga | Inga |
> | quotas | Inga | Inga |
> | lagringKonton | Inga | Inga |
> | lagringKonton / blobServices | Inga | Inga |
> | lagringKonton / filTjänster | Inga | Inga |
> | lagringKonto/ måttDefinitioner | Inga | Inga |
> | lagringKonto/mått | Inga | Inga |
> | lagringKonton /queueServices | Inga | Inga |
> | lagringKonton /tjänster | Inga | Inga |
> | lagringKonton / tjänster / diagnostikInställningar | Inga | Inga |
> | lagringKonton / tjänster / metricDefinitioner | Inga | Inga |
> | lagringKonton / tjänster / mått | Inga | Inga |
> | lagringKonton / tabellTjänster | Inga | Inga |
> | lagringKonton / vmImages | Inga | Inga |
> | vmImages | Inga | Inga |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | RateCard | Inga | Inga |
> | Användningsagga | Inga | Inga |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | tillgänglighetSeter | Ja | Ja |
> | diskEncryptionSets | Ja | Ja |
> | Diskar | Ja | Ja |
> | Gallerier | Ja | Ja |
> | gallerier / applikationer | Inga | Inga |
> | gallerier / applikationer / versioner | Inga | Inga |
> | gallerier / bilder | Inga | Inga |
> | gallerier / bilder / versioner | Inga | Inga |
> | hostGroups | Ja | Ja |
> | hostGroups /värdar | Ja | Ja |
> | images | Ja | Ja |
> | närhetPlatsmentGrupper | Ja | Ja |
> | restorePointCollections restorePointCollections restorePointCollections restorePoint | Ja | Ja |
> | restorePointCollections /restorePoints restorePoints restorePoints restorePoints restorePoints restorePoints | Inga | Inga |
> | sharedVMExtensions | Ja | Ja |
> | sharedVMExtensions / versioner | Inga | Inga |
> | sharedVMImages | Ja | Ja |
> | sharedVMImages / versioner | Inga | Inga |
> | snapshots | Ja | Ja |
> | sshPublicKeys (sshPublicKeys) | Ja | Ja |
> | virtuellaMaskiner | Ja | Ja |
> | virtualMachines / tillägg | Ja | Ja |
> | virtualMachines / metricDefinitions | Inga | Inga |
> | virtuellaMachineScaleSets | Ja | Ja |
> | virtualMachineScaleSets / tillägg | Inga | Inga |
> | virtualMachineScaleSets / nätverkInterfaces | Inga | Inga |
> | virtualMachineScaleSets / publicIPAddresses | Inga | Inga |
> | virtualMachineScaleSets / virtualMachines | Inga | Inga |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Inga | Inga |

## <a name="microsoftconsumption"></a>Microsoft.Förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Aggregeradcost | Inga | Inga |
> | Saldon | Inga | Inga |
> | Budgetar | Inga | Inga |
> | Avgifter | Inga | Inga |
> | Kostnadsbrickor | Inga | Inga |
> | Krediter | Inga | Inga |
> | händelser | Inga | Inga |
> | Prognoser | Inga | Inga |
> | Massor | Inga | Inga |
> | Marknadsplatser | Inga | Inga |
> | Prisdokument | Inga | Inga |
> | Produkter | Inga | Inga |
> | ReservationDetaljer | Inga | Inga |
> | ReservationRecommendations | Inga | Inga |
> | ReservationSummaries | Inga | Inga |
> | ReservationTransaktioner | Inga | Inga |
> | Taggar | Inga | Inga |
> | Hyresgäster | Inga | Inga |
> | Villkor | Inga | Inga |
> | UsageDetails | Inga | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ja | Ja |
> | serviceAssociationLänkar | Inga | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft.Container-registret

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Register | Ja | Ja |
> | register / agentPools | Ja | Ja |
> | register / bygger | Inga | Inga |
> | register / byggen / avbryt | Inga | Inga |
> | register / byggen / getLogLink | Inga | Inga |
> | register / buildTasks | Ja | Ja |
> | register / buildTasks / steg | Inga | Inga |
> | register / eventGridFilters | Inga | Inga |
> | register / generateCredentials | Inga | Inga |
> | register / getBuildSourceUploadUrl | Inga | Inga |
> | register / GetCredentials | Inga | Inga |
> | register / importBild | Inga | Inga |
> | register / privateEndpointConnectionProxies | Inga | Inga |
> | register / privateEndpointConnectionProxies / validera | Inga | Inga |
> | register / privateEndpointConnections | Inga | Inga |
> | register / privateLinkResources | Inga | Inga |
> | register / queueBuild | Inga | Inga |
> | register / regenerateCredential | Inga | Inga |
> | register / regenerateCredentials | Inga | Inga |
> | register /replikeringar | Ja | Ja |
> | register / körningar | Inga | Inga |
> | register / körningar / avbryt | Inga | Inga |
> | register / scheduleRun | Inga | Inga |
> | register / scopeMaps | Inga | Inga |
> | register / taskRuns | Ja | Ja |
> | register /uppgifter | Ja | Ja |
> | register / tokens | Inga | Inga |
> | register / uppdateringPolicies | Inga | Inga |
> | register / webhooks | Ja | Ja |
> | register / webhooks / getCallbackConfig | Inga | Inga |
> | register / webhooks / ping | Inga | Inga |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | containerServices | Ja | Ja |
> | managedClusters | Ja | Ja |
> | openShiftManagedClusters | Ja | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Aviseringar | Inga | Inga |
> | FaktureringKonto | Inga | Inga |
> | Budgetar | Inga | Inga |
> | CloudConnectors | Inga | Inga |
> | Anslutningar | Ja | Ja |
> | Avdelningar | Inga | Inga |
> | Dimensioner | Inga | Inga |
> | RegistreringKonto | Inga | Inga |
> | Export | Inga | Inga |
> | ExternafaktureringKonton | Inga | Inga |
> | ExternafaktureringKonton / Aviseringar | Inga | Inga |
> | Externafaktureringskonton / dimensioner | Inga | Inga |
> | Externafaktureringskonton / Prognos | Inga | Inga |
> | ExternafaktureringKonton / Fråga | Inga | Inga |
> | Externa prenumerationer | Inga | Inga |
> | Externa prenumerationer / Varningar | Inga | Inga |
> | Externa prenumerationer/dimensioner | Inga | Inga |
> | Externa prenumerationer / prognos | Inga | Inga |
> | Externa prenumerationer / Fråga | Inga | Inga |
> | Prognos | Inga | Inga |
> | Söka i data | Inga | Inga |
> | registrera | Inga | Inga |
> | Rapportkonfigs | Inga | Inga |
> | Rapporter | Inga | Inga |
> | Inställningar | Inga | Inga |
> | showbackRules (showbackRules) | Inga | Inga |
> | Vyer | Inga | Inga |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Begäranden | Inga | Inga |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Sammanslutningar | Inga | Inga |
> | resurserProviders | Ja | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Jobb | Ja | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeEnheter | Ja | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | arbetsytor | Ja | Inga |
> | arbetsytor / dbWorkspaces | Inga | Inga |
> | arbetsytor / lagringFörkryptering | Inga | Inga |
> | arbetsytor / virtualNetworkPeerings | Inga | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kataloger | Ja | Ja |
> | datakategorier | Ja | Ja |
> | datakataloger / datasources | Inga | Inga |
> | datakataloger / datasources / skanningar | Inga | Inga |
> | datakataloger / datakällor / skanningar / dataset | Inga | Inga |
> | datakataloger / datakällor / skanningar / triggers | Inga | Inga |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | dataFakta | Ja | Inga |
> | dataFakta / diagnostikInställningar | Inga | Inga |
> | dataFakta / metricDefinitioner | Inga | Inga |
> | dataFactorySchema | Inga | Inga |
> | Fabriker | Ja | Inga |
> | fabriker / integrationRuntimes | Inga | Inga |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton / dataLakeStoreAccounts | Inga | Inga |
> | konton / lagringKonton | Inga | Inga |
> | konton / lagringKonton / behållare | Inga | Inga |
> | konton / transferAnalyticsUnits | Inga | Inga |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton / eventGridFilters | Inga | Inga |
> | konton / firewallRules | Inga | Inga |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | services | Inga | Inga |
> | tjänster /projekt | Inga | Inga |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton / aktier | Inga | Inga |
> | konton / aktier / datauppsättningar | Inga | Inga |
> | konton / aktier / inbjudningar | Inga | Inga |
> | konton / aktier / leverantöraktieteckningar | Inga | Inga |
> | konton / aktier / synkroniseringSinställningar | Inga | Inga |
> | konton / aktieabonnemang | Inga | Inga |
> | konton / aktieabonnemang / consumerSourceDataSets | Inga | Inga |
> | konton / aktieabonnemang / datasetmappings | Inga | Inga |
> | konton / aktieabonnemang / triggers | Inga | Inga |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Servrar | Ja | Ja |
> | servrar / rådgivare | Inga | Inga |
> | servrar / nycklar | Inga | Inga |
> | servrar / privateEndpointConnectionProxies | Inga | Inga |
> | servrar / privateEndpointAnslutningar | Inga | Inga |
> | servrar / privateLinkResources | Inga | Inga |
> | servrar / queryTexter | Inga | Inga |
> | servrar / återvinningsbaraServers | Inga | Inga |
> | servrar / topQueryStatistics | Inga | Inga |
> | servrar / virtualNetworkRules | Inga | Inga |
> | servrar / waitStatistics | Inga | Inga |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Servrar | Ja | Ja |
> | servrar / rådgivare | Inga | Inga |
> | servrar / nycklar | Inga | Inga |
> | servrar / privateEndpointConnectionProxies | Inga | Inga |
> | servrar / privateEndpointAnslutningar | Inga | Inga |
> | servrar / privateLinkResources | Inga | Inga |
> | servrar / queryTexter | Inga | Inga |
> | servrar / återvinningsbaraServers | Inga | Inga |
> | servrar / topQueryStatistics | Inga | Inga |
> | servrar / virtualNetworkRules | Inga | Inga |
> | servrar / waitStatistics | Inga | Inga |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | serverGrupper | Ja | Ja |
> | Servrar | Ja | Ja |
> | servrar / rådgivare | Inga | Inga |
> | servrar / nycklar | Inga | Inga |
> | servrar / privateEndpointConnectionProxies | Inga | Inga |
> | servrar / privateEndpointAnslutningar | Inga | Inga |
> | servrar / privateLinkResources | Inga | Inga |
> | servrar / queryTexter | Inga | Inga |
> | servrar / återvinningsbaraServers | Inga | Inga |
> | servrar / topQueryStatistics | Inga | Inga |
> | servrar / virtualNetworkRules | Inga | Inga |
> | servrar / waitStatistics | Inga | Inga |
> | servrarv2 | Ja | Ja |
> | singleServers | Ja | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | artefakterKällar | Ja | Ja |
> | Utbyggnader | Ja | Ja |
> | serviceTopologies | Ja | Ja |
> | serviceTopologier / tjänster | Ja | Ja |
> | serviceTopologier / tjänster / serviceUnits | Ja | Ja |
> | steg | Ja | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | programgrupper | Ja | Ja |
> | programgrupper/program | Inga | Inga |
> | programgrupper / stationära datorer | Inga | Inga |
> | applikationsgrupper / startmenuitems | Inga | Inga |
> | värdpooler | Ja | Ja |
> | hostpools / sessionhosts | Inga | Inga |
> | hostpools / sessionhosts / usersessions hostpools / sessions | Inga | Inga |
> | hostpools / usersessions | Inga | Inga |
> | arbetsytor | Ja | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ja | Ja |
> | ElasticPools / IotHubTenants | Ja | Ja |
> | ElasticPools / IotHubTenants / securitySettings | Inga | Inga |
> | IotHubs | Ja | Ja |
> | IotHubs / eventGridFilters | Inga | Inga |
> | IotHubs / securitySettings | Inga | Inga |
> | EtableringTjänster | Ja | Ja |
> | Användningsområden | Inga | Inga |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Rörledningar | Ja | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Styrenheter | Ja | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | labcenters | Ja | Ja |
> | Labs | Ja | Ja |
> | laboratorier / miljöer | Ja | Ja |
> | labb / serviceRunners | Ja | Ja |
> | laboratorier / virtuellaMaskiner | Ja | Ja |
> | Scheman | Ja | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | databasKontoNamn | Inga | Inga |
> | databasKonton | Ja | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Domäner | Ja | Ja |
> | domäner / domänÄgarebetare | Inga | Inga |
> | genereraSsoRequest | Inga | Inga |
> | topLevelDomains | Inga | Inga |
> | valideraDomainRegistrationInformation | Inga | Inga |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | lcsprojekt | Inga | Inga |
> | lcsprojects / clouddeployments lcsprojects / clouddeployments lcsprojects / clouddeployments lc | Inga | Inga |
> | lcsprojects / kontakter | Inga | Inga |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Domäner | Ja | Ja |
> | domäner / ämnen | Inga | Inga |
> | händelseAbonnemang | Inga | Inga |
> | extensionTopics | Inga | Inga |
> | partnerNamnområden | Ja | Ja |
> | partnerNamnområden / eventKanaler | Inga | Inga |
> | partnerRegistrations | Ja | Ja |
> | partnerTopics | Ja | Ja |
> | partnerTopics / eventAbonnemang | Inga | Inga |
> | systemTopics | Ja | Ja |
> | systemTopics / eventAbonnemang | Inga | Inga |
> | Ämnen | Ja | Ja |
> | topicTypes | Inga | Inga |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kluster | Ja | Ja |
> | Namnområden | Ja | Ja |
> | namnrymd / tillståndsregler | Inga | Inga |
> | namnrymd / katastrofåterupptäcktconfigs | Inga | Inga |
> | namnområden / eventhubs | Inga | Inga |
> | namnrymd / eventhubs / authorizationrules | Inga | Inga |
> | namnområden / eventhubs / konsumentgrupper | Inga | Inga |
> | namnrymd / nätverksregler | Inga | Inga |

## <a name="microsoftfalcon"></a>Microsoft.Falcon (olika)

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Namnområden | Ja | Ja |

## <a name="microsoftfeatures"></a>Microsoft.Funktioner

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | featureProviders | Inga | Inga |
> | funktioner | Inga | Inga |
> | Leverantörer | Inga | Inga |
> | abonnemangFöreelser | Inga | Inga |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Registrera | Inga | Inga |
> | galleriiter | Inga | Inga |
> | generateartifactaccessuri | Inga | Inga |
> | myareas | Inga | Inga |
> | myareas / områden | Inga | Inga |
> | myareas / områden / områden | Inga | Inga |
> | myareas / områden / områden / galleryitems | Inga | Inga |
> | myareas / områden / galleryitems | Inga | Inga |
> | myareas / galleryitems | Inga | Inga |
> | registrera | Inga | Inga |
> | resources | Inga | Inga |
> | retrieveresourcesbyid | Inga | Inga |

## <a name="microsoftgenomics"></a>Microsoft.Genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | autoManagedRedovisningar | Ja | Ja |
> | autoManagedVmConfigurationProfiles | Ja | Ja |
> | konfigurationProfileAstilldelningar | Inga | Inga |
> | gästKonfigurationTilldelningar | Inga | Inga |
> | Programvara | Inga | Inga |
> | softwareUpdateProfile | Inga | Inga |
> | programvaraUppdater | Inga | Inga |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | hanaInstances (olikart) | Ja | Ja |
> | sapMonitors (sapMonitors) | Ja | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | dedikeradeHSM-moduler | Ja | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kluster | Ja | Ja |
> | kluster / applikationer | Inga | Inga |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | services | Ja | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Maskiner | Ja | Ja |
> | maskiner / förlängningar | Ja | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | dataHantverkare | Ja | Ja |

## <a name="microsofthydra"></a>Microsoft.Hydra (på andra)

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Komponenter | Ja | Ja |
> | nätverkScopes | Ja | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Jobb | Ja | Ja |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | actionGroups | Ja | Ja |
> | aktivitetLogAlerts | Ja | Ja |
> | varningsreglerna | Ja | Ja |
> | automatisk skalning | Ja | Ja |
> | Komponenter | Ja | Ja |
> | komponenter / linkedStorageAccounts | Inga | Inga |
> | komponenter / ProactiveDetectionConfigs | Inga | Inga |
> | diagnostikInställningar | Inga | Inga |
> | gästDiagnosticSettings | Ja | Ja |
> | gästDiagnosticSettingsAssociation | Ja | Ja |
> | logprofiler | Ja | Ja |
> | metricAlerts | Ja | Ja |
> | privateLinkScopes | Ja | Ja |
> | privateLinkScopes / privateEndpointConnections | Inga | Inga |
> | privateLinkScopes / scopedResources | Inga | Inga |
> | queryPacks | Ja | Ja |
> | queryPacks / frågor | Inga | Inga |
> | schemalagdakrytor | Ja | Ja |
> | webbtests | Ja | Ja |
> | arbetsböcker | Ja | Ja |
> | arbetsboksmallar | Ja | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | diagnostikInställningar | Inga | Inga |
> | diagnostikEtteringsKategories | Inga | Inga |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | appEmplates | Inga | Inga |
> | IoTApps | Ja | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Graph | Ja | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Inga | Inga |
> | hsmPools (hsmPools) | Ja | Ja |
> | Valv | Ja | Ja |
> | valv / accessPolicies | Inga | Inga |
> | valv / eventGridFilters | Inga | Inga |
> | valv / hemligheter | Inga | Inga |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | anslutnaKlusterer | Ja | Ja |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kluster | Ja | Ja |
> | kluster /bifogadedatabaskonfigurationer | Inga | Inga |
> | kluster / databaser | Inga | Inga |
> | kluster / databaser / dataanslutningar | Inga | Inga |
> | kluster / databaser / eventhubconnections | Inga | Inga |
> | kluster / databaser / principalassignments | Inga | Inga |
> | kluster/dataanslutningar | Inga | Inga |
> | kluster /principalastilldelningar | Inga | Inga |
> | kluster /delade enheter | Inga | Inga |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ja | Ja |
> | användare | Inga | Inga |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | hostingMiljöer | Ja | Ja |
> | integrationKonton | Ja | Ja |
> | integrationTjänstMiljöer | Ja | Ja |
> | integrationServiceMiljöer / managedApis | Ja | Ja |
> | isoleradeMiljöer | Ja | Ja |
> | Arbetsflöden | Ja | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLärning

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | åtagandePlaner | Ja | Ja |
> | Webservices | Ja | Ja |
> | Arbetsytor | Ja | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | arbetsytor | Ja | Ja |
> | arbetsytor / beräkningar | Inga | Inga |
> | arbetsytor / eventGridFilters | Inga | Inga |

## <a name="microsoftmaintenance"></a>Microsoft.Underhåll

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Inga | Inga |
> | configurationAstilldelningar | Inga | Inga |
> | underhållKonfigurationer | Ja | Ja |
> | uppdateringar | Inga | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Identiteter | Inga | Inga |
> | userAssignedId entiteter | Ja | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | marknadsplatsRegistrationDefinitioner | Inga | Inga |
> | registreringTilldelningar | Inga | Inga |
> | registreringDefinier | Inga | Inga |

## <a name="microsoftmanagement"></a>Microsoft.Management (På)Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | hämta enheter | Inga | Inga |
> | managementGroups | Inga | Inga |
> | managementGroups /inställningar | Inga | Inga |
> | resources | Inga | Inga |
> | startTenantBackfill | Inga | Inga |
> | klientBackfillStatus | Inga | Inga |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |
> | konton / eventGridFilters | Inga | Inga |
> | konton / privateAtlases | Ja | Ja |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Erbjuder | Inga | Inga |
> | offerTyper | Inga | Inga |
> | offerTyper/utgivare | Inga | Inga |
> | offerTyper/ utgivare/erbjudanden | Inga | Inga |
> | offerTyper / utgivare / erbjudanden / planer | Inga | Inga |
> | offerTyper / utgivare / erbjudanden / planer / avtal | Inga | Inga |
> | offerTyper / utgivare / erbjudanden / planer / configs | Inga | Inga |
> | offerTyper / utgivare / erbjudanden / planer / configs / importImage | Inga | Inga |
> | privategalleryitems | Inga | Inga |
> | privatButikClient | Inga | Inga |
> | privateStores | Inga | Inga |
> | privateStores / erbjudanden | Inga | Inga |
> | Produkter | Inga | Inga |
> | Förlag | Inga | Inga |
> | förlag / erbjudanden | Inga | Inga |
> | förlag / erbjudanden / ändringar | Inga | Inga |
> | registrera | Inga | Inga |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ja | Ja |
> | uppdateraKommunikationsövertolkning | Inga | Inga |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceBeställa

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Avtal | Inga | Inga |
> | offertyper | Inga | Inga |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | medietjänster | Ja | Ja |
> | mediatjänster / kontoFilter | Inga | Inga |
> | mediaservice /tillgångar | Inga | Inga |
> | mediatjänster / tillgångar / tillgångFilter | Inga | Inga |
> | mediatjänster / contentKeyPolicies | Inga | Inga |
> | mediaservices / eventGridFilters | Inga | Inga |
> | mediaservices / liveEventOperations | Inga | Inga |
> | mediaservices / liveEvents | Ja | Ja |
> | mediaservices / liveEvents / liveOutputs | Inga | Inga |
> | mediaservices / liveOutputOperations | Inga | Inga |
> | mediatjänster / mediaGrafer | Inga | Inga |
> | mediatjänster / streamingEndpointOperations | Inga | Inga |
> | mediatjänster / streamingEndpoints | Ja | Ja |
> | mediatjänster / streamingLocators | Inga | Inga |
> | mediatjänster / streamingPolicies | Inga | Inga |
> | mediaservices / transformeringar | Inga | Inga |
> | mediaservices / transformeringar / jobb | Inga | Inga |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | appKluster | Ja | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrera

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | bedömningProjekt | Ja | Ja |
> | migreraprojekt | Ja | Ja |
> | flyttaKolleringar | Ja | Ja |
> | Projekt | Ja | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | holografiSkräkenUtnämningar | Ja | Ja |
> | objektUnderstandingAccounts | Ja | Ja |
> | remoteRenderingRedovisningar | Ja | Ja |
> | spatialAnchorsKonton | Ja | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | netAppAccounts (netAppAccounts) | Ja | Inga |
> | netAppAccounts / accountBackups | Inga | Inga |
> | netAppAccounts / capacityPools | Ja | Inga |
> | netAppAccounts / capacityPools / volymer | Ja | Inga |
> | netAppAccounts / capacityPools / volymer / ögonblicksbilder | Inga | Inga |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | ansökanGateways | Ja | Ja |
> | ansökanGatewayWebApplicationFirewallPolicies | Ja | Ja |
> | applicationSecurityGroups | Ja | Ja |
> | azureFirewallFqdnTags | Inga | Inga |
> | azureFirewalls | Ja | Inga |
> | bastionHosts (bastionHosts) | Ja | Inga |
> | bgpServiceKommuner | Inga | Inga |
> | Anslutningar | Ja | Ja |
> | ddosCustomPolicies | Ja | Ja |
> | ddosProtectionPlans | Ja | Ja |
> | dnsOperationStatuses | Inga | Inga |
> | dnszones | Ja | Ja |
> | dnszones / A | Inga | Inga |
> | dnszones / AAAA | Inga | Inga |
> | dnszones / alla | Inga | Inga |
> | dnszones / CAA | Inga | Inga |
> | dnszones / CNAME | Inga | Inga |
> | dnszones / MX | Inga | Inga |
> | dnszones / NS | Inga | Inga |
> | dnszones / PTR | Inga | Inga |
> | dnszones / recordset | Inga | Inga |
> | dnszones / SOA | Inga | Inga |
> | dnszones / SRV | Inga | Inga |
> | dnszones / TXT | Inga | Inga |
> | expressRouteCirklar | Ja | Ja |
> | expressRouteCrossAnslutningar | Ja | Ja |
> | expressRouteGateways | Ja | Ja |
> | expressRoutePorts | Ja | Ja |
> | expressRouteServiceProviders | Inga | Inga |
> | brandväggPolicies | Ja | Ja |
> | frontdörrar | Ja, men begränsad (se [anmärkning nedan)](#frontdoor) | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ja, men begränsad (se [anmärkning nedan)](#frontdoor) | Inga |
> | frontdoorWebApplicationFirewallPolicies | Ja, men begränsad (se [anmärkning nedan)](#frontdoor) | Ja |
> | getDnsResourceReference | Inga | Inga |
> | interntAnmäl dig | Inga | Inga |
> | loadBalancers | Ja | Ja |
> | localNetworkGateways | Ja | Ja |
> | natGateways (natGateways) | Ja | Ja |
> | nätverkIntentPolicies | Ja | Ja |
> | nätverkGränssnitt | Ja | Ja |
> | nätverkProfiler | Ja | Ja |
> | nätverkSäkerhetsgrupper | Ja | Ja |
> | nätverkWatchers | Ja | Ja |
> | nätverkWatchers / connectionMonitors | Ja | Inga |
> | nätverkWatchers / flowLogs | Inga | Inga |
> | nätverkWatchers / linser | Ja | Inga |
> | nätverkWatchers / pingMeshes | Ja | Inga |
> | p2sVpnGateways | Ja | Ja |
> | privateDnsOperationStatuses | Inga | Inga |
> | privatDnsZones | Ja | Ja |
> | privateDnsZones / A | Inga | Inga |
> | privateDnsZones / AAAA | Inga | Inga |
> | privateDnsZones / alla | Inga | Inga |
> | privateDnsZones / CNAME | Inga | Inga |
> | privateDnsZones / MX | Inga | Inga |
> | privateDnsZones / PTR | Inga | Inga |
> | privateDnsZones / SOA | Inga | Inga |
> | privateDnsZones / SRV | Inga | Inga |
> | privateDnsZones / TXT | Inga | Inga |
> | privateDnsZones / virtualNetworkLinks | Ja | Ja |
> | privateEndpoints | Ja | Ja |
> | privateLinkServices | Ja | Ja |
> | offentliga IP-adresser | Ja | Ja |
> | offentligaIP-korrigeringar | Ja | Ja |
> | routeFilters | Ja | Ja |
> | routeTables | Ja | Ja |
> | serviceEndpointPolicies | Ja | Ja |
> | trafikManagerGeographicHierarchies | Inga | Inga |
> | trafficmanagerprofiles | Ja | Ja |
> | trafficmanagerprofiles/heatMaps | Inga | Inga |
> | trafikManagerUserMetricsKeys | Inga | Inga |
> | virtualHubs | Ja | Ja |
> | virtualNetworkGateways | Ja | Ja |
> | virtualNetworks | Ja | Ja |
> | virtualNetworkTaps | Ja | Ja |
> | virtuellaWans | Ja | Inga |
> | vpnGateways (vpnGateways) | Ja | Ja |
> | vpnSites (vpnSites) | Ja | Ja |
> | webApplicationFirewallPolicies | Ja | Ja |

<a id="frontdoor" />

> [!NOTE]
> För Azure Front Door Service kan du använda taggar när du skapar resursen, men det går inte att uppdatera eller lägga till taggar för närvarande.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Bärbara datorer | Inga | Inga |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Namnområden | Ja | Inga |
> | namnområden / anmälanHubs | Ja | Inga |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ja | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | HyperVSites (HyperVSites) | Ja | Ja |
> | ImportPlatser | Ja | Ja |
> | ServerPlatser | Ja | Ja |
> | VmwareSites (VMwareSites) | Ja | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kluster | Ja | Ja |
> | länkMål | Inga | Inga |
> | lagringInightConfigs | Inga | Inga |
> | arbetsytor | Ja | Ja |
> | arbetsytor / dataExportar | Inga | Inga |
> | arbetsytor / dataSources | Inga | Inga |
> | arbetsytor / linkedServices | Inga | Inga |
> | arbetsytor / linkedStorageAccounts | Inga | Inga |
> | arbetsytor / fråga | Inga | Inga |
> | arbetsytor / scopedPrivateLinkProxies | Inga | Inga |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | managementassociations | Inga | Inga |
> | hanteringskonfigurationer | Ja | Ja |
> | lösningar | Ja | Ja |
> | Visningar | Ja | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Inga | Inga |
> | peerAsns | Inga | Inga |
> | peerings | Ja | Ja |
> | peeringServiceLänder | Inga | Inga |
> | peeringServiceProviders | Inga | Inga |
> | peeringTjänster | Ja | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | policyEvents | Inga | Inga |
> | policyMetadata | Inga | Inga |
> | policyStates | Inga | Inga |
> | policyTrackedResources | Inga | Inga |
> | avhjälpande åtgärder | Inga | Inga |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> |  -konsoler | Inga | Inga |
> | instrumentpaneler | Ja | Ja |
> | userSettings | Inga | Inga |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | arbetsytaInsamlingar | Ja | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Kapacitet | Ja | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Ja | Ja |

## <a name="microsoftquantum"></a>Microsoft.Quantum (På andra)

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Arbetsytor | Ja | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | backupSkyddadeobjektier | Inga | Inga |
> | Valv | Ja | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Namnområden | Ja | Ja |
> | namnrymd / tillståndsregler | Inga | Inga |
> | namnrymder / hybridanslutningar | Inga | Inga |
> | namnrymder / hybridanslutningar / authorizationrules | Inga | Inga |
> | namnområden / wcfrelays | Inga | Inga |
> | namnrymd / wcfrelays / authorizationrules | Inga | Inga |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | konton | Inga | Inga |
> | Samlingar | Ja | Ja |
> | samlingar / applikationer | Inga | Inga |
> | samlingar /säkerhetsprinciper | Inga | Inga |
> | mallBilder | Inga | Inga |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Frågor | Ja | Ja |
> | resursChangeDetails | Inga | Inga |
> | resurserChanges | Inga | Inga |
> | resources | Inga | Inga |
> | resurserHistoria | Inga | Inga |
> | abonnemangStatus | Inga | Inga |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | tillgänglighetStatusar | Inga | Inga |
> | childAvailabilityStatuses | Inga | Inga |
> | childResources | Inga | Inga |
> | nyafrågor | Inga | Inga |
> | händelser | Inga | Inga |
> | påverkadeResurser | Inga | Inga |
> | metadata | Inga | Inga |
> | meddelanden | Inga | Inga |

## <a name="microsoftresources"></a>Microsoft.Resurser

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Distributioner | Ja | Inga |
> | distributioner / operationer | Inga | Inga |
> | deploymentScripts | Ja | Ja |
> | deploymentScripts / loggar | Inga | Inga |
> | Länkar | Inga | Inga |
> | notifyResourceJobs | Inga | Inga |
> | Leverantörer | Inga | Inga |
> | resursGrupper | Ja | Inga |
> | Prenumerationer | Ja | Inga |
> | Hyresgäster | Inga | Inga |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | saasresources | Inga | Inga |

## <a name="microsoftsearch"></a>Microsoft.Sök

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | resursHealthMetadata | Inga | Inga |
> | sökTjänster | Ja | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Säkerhet

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHårningar | Inga | Inga |
> | avanceratTre skyddsinställningar | Inga | Inga |
> | aviseringar | Inga | Inga |
> | tillåtnaAnslutningar | Inga | Inga |
> | programVitlistings | Inga | Inga |
> | bedömningMetadata | Inga | Inga |
> | utvärderingar | Inga | Inga |
> | autoDismissAlertsRules | Inga | Inga |
> | automatiseringar | Ja | Ja |
> | AutoProvisioningSettings | Inga | Inga |
> | Efterlevnad | Inga | Inga |
> | dataInsamlingAgenter | Inga | Inga |
> | deviceSecurityGroups | Inga | Inga |
> | upptäcktSäkerhetLösningar | Inga | Inga |
> | extern SäkerhetLösningar | Inga | Inga |
> | InformationProtectionPolicies | Inga | Inga |
> | iotSecurityLösningar | Ja | Ja |
> | iotSecurityLösningar / analyticsModels | Inga | Inga |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Inga | Inga |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Inga | Inga |
> | jitNetworkAccessPolicies | Inga | Inga |
> | nätverkData | Inga | Inga |
> | policies | Inga | Inga |
> | priser | Inga | Inga |
> | reglerande EfterlevnadStandarder | Inga | Inga |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Inga | Inga |
> | reglerande EfterlevnadStandards / regulatoriska EfterlevnadKontroller / regulatoriska Efterlevnadsassessments | Inga | Inga |
> | säkraScoreControlDefinitions | Inga | Inga |
> | säkraScoreControls | Inga | Inga |
> | säkraScores | Inga | Inga |
> | secureScores / secureScoreControls | Inga | Inga |
> | säkerhetKontakter | Inga | Inga |
> | säkerhetLösningar | Inga | Inga |
> | säkerhetLösningarReferenceData | Inga | Inga |
> | säkerhetStatusar | Inga | Inga |
> | säkerhetStatusEsSummaries | Inga | Inga |
> | serverVulnerbarhetBes här. | Inga | Inga |
> | settings | Inga | Inga |
> | understämningar | Inga | Inga |
> | uppgifter | Inga | Inga |
> | Topologier | Inga | Inga |
> | arbetsytorInställningar | Inga | Inga |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph Microsoft.SecurityGraph Microsoft.SecurityGraph Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | diagnostikInställningar | Inga | Inga |
> | diagnostikEtteringsKategories | Inga | Inga |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Aggregeringar | Inga | Inga |
> | alertRules | Inga | Inga |
> | alertRuleTemplates | Inga | Inga |
> | bokmärken | Inga | Inga |
> | Fall | Inga | Inga |
> | dataAnslutningar | Inga | Inga |
> | dataConnectorsCheckRequirements | Inga | Inga |
> | Enheter | Inga | Inga |
> | entityQueries | Inga | Inga |
> | Incidenter | Inga | Inga |
> | kontorConsents | Inga | Inga |
> | settings | Inga | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Namnområden | Ja | Ja |
> | namnrymd / tillståndsregler | Inga | Inga |
> | namnrymd / katastrofåterupptäcktconfigs | Inga | Inga |
> | namnområden / eventgridfilter | Inga | Inga |
> | namnrymd / nätverksregler | Inga | Inga |
> | namnområden / köer | Inga | Inga |
> | namnrymd / köer / authorizationrules | Inga | Inga |
> | namnområden / ämnen | Inga | Inga |
> | namnrymd / ämnen / authorizationrules | Inga | Inga |
> | namnrymd / ämnen / prenumerationer | Inga | Inga |
> | namnrymd / ämnen / abonnemang / regler | Inga | Inga |
> | premiumMessagingRegioner | Inga | Inga |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | Kluster | Ja | Ja |
> | kluster / applikationer | Inga | Inga |
> | containerGroups | Ja | Ja |
> | containerGroupSets | Ja | Ja |
> | kantklusor | Ja | Ja |
> | kantkluster / applikationer | Inga | Inga |
> | managedclusters | Ja | Ja |
> | managedclusters / nodetyper | Inga | Inga |
> | Nätverk | Ja | Ja |
> | hemliga butiker | Ja | Ja |
> | secretstores / certifikat | Inga | Inga |
> | secretstores / hemligheter | Inga | Inga |
> | volumes | Ja | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | program | Ja | Ja |
> | containerGroups | Ja | Ja |
> | Gateways | Ja | Ja |
> | Nätverk | Ja | Ja |
> | secrets | Ja | Ja |
> | volumes | Ja | Ja |

## <a name="microsoftservices"></a>Microsoft.Tjänster

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | leverantörRegistrationer | Inga | Inga |
> | providerRegistrations / resourceTypeRegistrations | Inga | Inga |
> | Utbyggnader | Ja | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | SignalR | Ja | Ja |
> | SignalR / eventGridFilters | Inga | Inga |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Ja | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | hybridAnvändNingspassningar | Inga | Inga |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | ansökanDefinitioner | Ja | Ja |
> | program | Ja | Ja |
> | jitRequests (påtängen) | Ja | Ja |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | registreradeAbonnemang | Inga | Inga |
> | Spolar | Ja | Ja |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | managedInstances | Ja | Ja |
> | managedInstances / databaser | Ja (se [anmärkning nedan](#sqlnote)) | Ja |
> | managedInstances / databaser / backupKortTermRetentionPolicies | Inga | Inga |
> | managedInstances / databaser / scheman / tabeller / kolumner / känslighetLabels | Inga | Inga |
> | managedInstances / databaser / sårbarhetAssessments | Inga | Inga |
> | managedInstances / databaser / sårbarhetAssessments / regler / baslinjer | Inga | Inga |
> | managedInstances / encryptionProtector | Inga | Inga |
> | managedInstances / nycklar | Inga | Inga |
> | managedInstances / restorableDroppedDatabases / backupKortTermRetentionPolicies | Inga | Inga |
> | managedInstances /vulnerabilityAssessments | Inga | Inga |
> | Servrar | Ja | Ja |
> | servrar /administratörer | Inga | Inga |
> | servrar / communicationLinks | Inga | Inga |
> | servrar / databaser | Ja (se [anmärkning nedan](#sqlnote)) | Ja |
> | servrar / krypteringSkydd | Inga | Inga |
> | servrar / firewallRules | Inga | Inga |
> | servrar / nycklar | Inga | Inga |
> | servrar / reparerbaraDroppedDatabases | Inga | Inga |
> | servrar /serviceobjektiv | Inga | Inga |
> | servrar / tdeCertificates | Inga | Inga |
> | virtuellaClusters | Inga | Inga |

<a id="sqlnote" />

> [!NOTE]
> Huvuddatabasen stöder inte taggar, men andra databaser, inklusive Azure SQL Data Warehouse-databaser, stöder taggar. Azure SQL Data Warehouse-databaser måste vara i aktivt tillstånd (inte pausat).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ja | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Inga | Inga |
> | SqlVirtualMachines | Ja | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | lagringKonton | Ja | Ja |
> | lagringKonton / blobServices | Inga | Inga |
> | lagringKonton / filTjänster | Inga | Inga |
> | lagringKonton /queueServices | Inga | Inga |
> | lagringKonton /tjänster | Inga | Inga |
> | lagringKonton / tjänster / metricDefinitioner | Inga | Inga |
> | lagringKonton / tabellTjänster | Inga | Inga |
> | Användningsområden | Inga | Inga |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Cachar | Ja | Ja |
> | cachar / lagringTargets | Inga | Inga |
> | användningModeller | Inga | Inga |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | replikeringsgrupper | Inga | Inga |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Inga | Inga |
> | storageSyncServices/syncGroups | Inga | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga | Inga |
> | storageSyncServices/arbetsflöden | Inga | Inga |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Inga | Inga |
> | storageSyncServices/syncGroups | Inga | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga | Inga |
> | storageSyncServices/arbetsflöden | Inga | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ja | Ja |
> | storageSyncServices / registeredServers | Inga | Inga |
> | storageSyncServices/syncGroups | Inga | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga | Inga |
> | storageSyncServices/arbetsflöden | Inga | Inga |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Chefer | Ja | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | streamingjobb | Ja (se anmärkning nedan) | Ja |

> [!NOTE]
> Du kan inte lägga till en tagg när strömmande jobb körs. Stoppa resursen för att lägga till en tagg.

## <a name="microsoftsubscription"></a>Microsoft.Prenumeration

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | avbryt | Inga | Inga |
> | Skapateckning | Inga | Inga |
> | Aktivera | Inga | Inga |
> | byt namn | Inga | Inga |
> | PrenumerationDefinitioner | Inga | Inga |
> | PrenumerationEr | Inga | Inga |
> | Prenumerationer | Inga | Inga |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Miljöer | Ja | Inga |
> | miljöer / accessPolicies | Inga | Inga |
> | miljöer/eventsources | Ja | Inga |
> | miljöer / referensDataSets | Ja | Inga |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | dedikeradCloudNodes | Ja | Ja |
> | dedikeradeCloudServices | Ja | Ja |
> | virtuellaMaskiner | Ja | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | enheter | Ja | Ja |
> | registreradeAbonnemang | Inga | Inga |
> | Leverantörer | Inga | Inga |
> | leverantörer / skus | Inga | Inga |
> | leverantörer / vnfs | Inga | Inga |
> | virtualNetworkFunctionSkus | Inga | Inga |
> | vnfs (vnfs) | Ja | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | apiManagementKonton | Inga | Inga |
> | apiManagementKonton / apiAcls | Inga | Inga |
> | apiManagementAccounts / apis | Inga | Inga |
> | apiManagementAccounts / apis / apiAcls apiManagementAccounts / apis / apiAcls apiManagementAccounts / apis / apiAcls apiMan | Inga | Inga |
> | apiManagementAccounts / apis / connectionAcls | Inga | Inga |
> | apiManagementAccounts / apis / anslutningar | Inga | Inga |
> | apiManagementAccounts / apis / anslutningar / connectionAcls | Inga | Inga |
> | apiManagementAccounts / apis / localizedDefinitions | Inga | Inga |
> | apiManagementRedovisning / anslutningAkn(2) | Inga | Inga |
> | apiManagementKonton / anslutningar | Inga | Inga |
> | billingMetrar | Inga | Inga |
> | certifikat | Ja | Ja |
> | anslutningGateways | Ja | Ja |
> | Anslutningar | Ja | Ja |
> | customApis | Ja | Ja |
> | deletedSites | Inga | Inga |
> | hostingMiljöer | Ja | Ja |
> | hostingMiljöer / eventGridFilters | Inga | Inga |
> | hostingMiljöer / multiRolePools | Inga | Inga |
> | hostingMiljöer / workerPools | Inga | Inga |
> | kubeMiljöer | Ja | Ja |
> | publishingAnvändare | Inga | Inga |
> | rekommendationer | Inga | Inga |
> | resursHealthMetadata | Inga | Inga |
> | Runtimes | Inga | Inga |
> | serverFarms | Ja | Ja |
> | serverFarms / eventGridFilters | Inga | Inga |
> | webbplatser | Ja | Ja |
> | platser / config  | Inga | Inga |
> | platser / eventGridFilters | Inga | Inga |
> | webbplatser / hostNameBindings | Inga | Inga |
> | platser / nätverkConfig | Inga | Inga |
> | platser / premieraddons | Ja | Ja |
> | platser / platser | Ja | Ja |
> | platser / platser / eventGridFilters | Inga | Inga |
> | platser / platser / hostNameBindings | Inga | Inga |
> | platser / platser / nätverkConfig | Inga | Inga |
> | sourceControls | Inga | Inga |
> | staticSites | Ja | Ja |
> | Validera | Inga | Inga |
> | verifieraHostingMiljöVnet | Inga | Inga |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | diagnostikInställningar | Inga | Inga |
> | diagnostikEtteringsKategories | Inga | Inga |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Enhetstjänster | Ja | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Stöder taggar | Tagg i kostnadsrapport |
> | ------------- | ----------- | ----------- |
> | Komponenter | Inga | Inga |
> | komponenterSumma | Inga | Inga |
> | monitorInsikter | Inga | Inga |
> | monitorInstancesSummary | Inga | Inga |
> | Bildskärmar | Inga | Inga |
> | meddelandenInställningar | Inga | Inga |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder taggar på resurser finns i [Använda taggar för att ordna dina Azure-resurser](tag-resources.md).
