---
title: Borttagning i complete-läget
description: Visar hur resurstyper hanterar fullständig borttagning av läge i Azure Resource Manager-mallar.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e0250b289ce7a228d844023c3e1d1110438b3afc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802577"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Borttagning av Azure-resurser för distribution i fullständigt läge

I den här artikeln beskrivs hur resurstyper hanterar borttagning när de inte är i en mall som distribueras i fullständigt läge.

De resurstyper som är markerade med **Ja** tas bort när typen inte finns i mallen som distribueras med fullständigt läge.

De resurstyper som är markerade med **Nej** tas inte bort automatiskt när de inte finns i mallen. De tas dock bort om den överordnade resursen tas bort. En fullständig beskrivning av beteendet finns i [distributionslägen för Azure Resource Manager](deployment-modes.md).

Om du distribuerar till [mer än en resursgrupp i en mall](cross-resource-group-deployment.md)kan resurser i resursgruppen som anges i distributionsåtgärden tas bort. Resurser i de sekundära resursgrupperna tas inte bort.

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
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DomainServices | Ja |
> | DomainServices / oucontainer | Inga |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | supportProviders | Inga |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | aadsupportcases | Inga |
> | adderartjänster | Inga |
> | Agenter | Inga |
> | anonymapiusers | Inga |
> | konfiguration | Inga |
> | loggar | Inga |
> | rapporter | Inga |
> | servicehealthmetrics | Inga |
> | services | Inga |

## <a name="microsoftadvisor"></a>Microsoft.Advisor (Av )

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Konfigurationer | Inga |
> | genereraKommendationer | Inga |
> | metadata | Inga |
> | rekommendationer | Inga |
> | undertryckanden | Inga |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | actionRules | Ja |
> | aviseringar | Inga |
> | aviseringarLista | Inga |
> | varningarMetaData | Inga |
> | varningarSumma | Inga |
> | aviseringarSummaryList | Inga |
> | smartDetectorAlertRules | Ja |
> | smarta grupper | Inga |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Servrar | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiHanagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | rapportFeedback | Inga |
> | tjänst | Ja |
> | verifieraServiceName | Inga |

## <a name="microsoftappconfiguration"></a>Microsoft.AppKonfigurering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Inga |

## <a name="microsoftappplatform"></a>Microsoft.AppPlattform

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Spring | Ja |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | attestationProviders | Inga |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | klassiskaLäggare | Inga |
> | dataAliases | Inga |
> | nekaTilldelningar | Inga |
> | höjaAccess | Inga |
> | findOrphanRoleAssignments | Inga |
> | Lås | Inga |
> | Behörigheter | Inga |
> | policyAssignments | Inga |
> | policyDefinitioner | Inga |
> | policySetDefinitions | Inga |
> | providerOperations | Inga |
> | rollTilldelningar | Inga |
> | rollTilldelningarAnvändarnametri | Inga |
> | rollDefinitioner | Inga |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | automationKonton | Ja |
> | automationKonton / konfigurationer | Ja |
> | automationKonton / jobb | Inga |
> | automationAlän / privateEndpointConnectionProxies | Inga |
> | automationKonton / privateEndpointAnslutningar | Inga |
> | automationKonton / privateLinkResources | Inga |
> | automationKonton / runbooks | Ja |
> | automationKonto/programvaraUppdämpningar | Inga |
> | automationKonton / webhooks | Inga |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Inga |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Miljöer | Inga |
> | miljöer / konton | Inga |
> | miljöer / konton / namnområden | Inga |
> | miljöer / konton / namnrymd / konfigurationer | Inga |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | b2cKataloger | Ja |
> | b2ctenants (b2ctenants) | Inga |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridDataManagers | Ja |
> | postgresInstances | Ja |
> | sqlInstances | Ja |
> | sqlServerRegistrations | Ja |
> | sqlServerRegistrations / sqlServers | Inga |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | cloudManifestFiler | Inga |
> | Registreringar | Ja |
> | registreringar / kundAbonnemang | Inga |
> | registreringar / produkter | Inga |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | batchKonton | Ja |

## <a name="microsoftbilling"></a>Microsoft.Fakturering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | faktureringKonton | Inga |
> | faktureringKonton/avtal | Inga |
> | faktureringKonton /faktureringPermissions | Inga |
> | faktureringKonto/faktureringProfiler | Inga |
> | faktureringKonton / faktureringProfiler / faktureringPermissions | Inga |
> | faktureringKonto/faktureringProfiler / faktureringRolleAstilldelningar | Inga |
> | faktureringKonto / faktureringProfiler / faktureringRoleDefinitioner | Inga |
> | faktureringKonto/faktureringProfiler / faktureringAbonnemang | Inga |
> | faktureringKonto/faktureringProfiler / skapaFaktureringRollAstilldelning | Inga |
> | faktureringKonton / faktureringProfiler / kunder | Inga |
> | faktureringKonton / faktureringProfiler / instruktioner | Inga |
> | faktureringKonton / faktureringProfiler / fakturor | Inga |
> | faktureringKonton / faktureringProfiler / fakturor / prisblad | Inga |
> | faktureringKonton / faktureringProfiler / fakturor / transaktioner | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / faktureringPermissions | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / faktureringRolEAstilldelningar | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / faktureringRoleDefinitioner | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / faktureringAbonnemang | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / skapaFakturering | Inga |
> | faktureringKonton /faktureringProfiler / fakturaAvsnitt / initieraTransfer | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / produkter | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / produkter / överföring | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / produkter / uppdateringAutoRenew | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / transaktioner | Inga |
> | faktureringKonton / faktureringProfiler / fakturaAvsnitt / överföringar | Inga |
> | billingAccounts / BillingProfiles / patchOperations | Inga |
> | faktureringKonton / faktureringProfiler / betalningMethods | Inga |
> | faktureringKonton / faktureringProfiler / policyer | Inga |
> | faktureringKonton / faktureringProfiler / prisblad | Inga |
> | faktureringKonton / faktureringProfiler / prisbladLadda nedOperationer | Inga |
> | faktureringKonton / faktureringProfiler / produkter | Inga |
> | faktureringKonton / faktureringProfiler / transaktioner | Inga |
> | faktureringKonton /faktureringRollAtilldelningar | Inga |
> | faktureringKonto / faktureringRoleDefinitioner | Inga |
> | faktureringKonto/faktureringAbonnemang | Inga |
> | faktureringKonton / faktureringAbonnemang /fakturor | Inga |
> | billingAccounts / createBillingRoleAssignment | Inga |
> | billingAccounts / createInvoiceSectionOperations | Inga |
> | faktureringKonton / kunder | Inga |
> | faktureringKonton / kunder / faktureringPermissions | Inga |
> | faktureringKonton / kunder / faktureringAbonnemang | Inga |
> | faktureringKonton / kunder / initieraTransfer | Inga |
> | faktureringKonton / kunder / policyer | Inga |
> | faktureringKonton / kunder / produkter | Inga |
> | faktureringKonton / kunder / transaktioner | Inga |
> | faktureringKonton / kunder / överföringar | Inga |
> | faktureringKonton / avdelningar | Inga |
> | faktureringKonton / registreringKonton | Inga |
> | faktureringKonton/fakturor | Inga |
> | faktureringKonton / fakturorAvsnitt | Inga |
> | faktureringKonton / fakturorAvsnitt / faktureringAbonnemangFlyttaOperationer | Inga |
> | faktureringKonton / fakturorAvsnitt / faktureringAbonnemang | Inga |
> | faktureringKonton / fakturorAvsnitt / faktureringAbonnemang /överföring | Inga |
> | faktureringKonton / fakturorAvsnitt / höj | Inga |
> | faktureringKonton / fakturaAvsnitt / initieraTransfer | Inga |
> | faktureringKonton / fakturorAvsnitt / patchOperations | Inga |
> | faktureringKonton / fakturorAvsnitt / productMoveOperations | Inga |
> | faktureringKonton / fakturorAvsnitt / produkter | Inga |
> | faktureringKonton / fakturorAvsnitt / produkter / överföring | Inga |
> | faktureringKonton / fakturorAvsnitt / produkter / uppdateringAutoRenew | Inga |
> | faktureringKonton / fakturorAvsnitt /transaktioner | Inga |
> | faktureringKonton / fakturorAvsnitt /överföringar | Inga |
> | billingAccounts / lineOfCredit | Inga |
> | billingAccounts / patchOperations | Inga |
> | faktureringKonton / betalningMethods | Inga |
> | faktureringKonton / produkter | Inga |
> | faktureringKonton /transaktioner | Inga |
> | faktureringPerioder | Inga |
> | faktureringPermissions | Inga |
> | faktureringSförsörstighet | Inga |
> | billingRoleAstilldelningar | Inga |
> | faktureringRoleDefinitioner | Inga |
> | skapaBillingRoleAssignment | Inga |
> | Avdelningar | Inga |
> | registreringKonto | Inga |
> | Fakturor | Inga |
> | Överföringar | Inga |
> | överföringar / accepteraÖversändande | Inga |
> | överföringar / avböjTransfer | Inga |
> | överföringar /operationStatus | Inga |
> | överföringar/valideraÖverförflyttning | Inga |
> | valideraAdress | Inga |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | kartaApis | Ja |
> | uppdateraKommunikationsövertolkning | Inga |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain (på andra)

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | blockchainMembers | Ja |
> | cordaMembers | Ja |
> | Watchers | Ja |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | TokenServices | Ja |
> | TokenServices / BlockchainNetworks | Inga |
> | TokenServices / Grupper | Inga |
> | TokenServices / Grupper / Konton | Inga |
> | TokenServices / TokenTemplates | Inga |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint (Microsoft.Blueprint)

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ritningarTilldelningar | Inga |
> | skissTilldelningar/tilldelningOperationer | Inga |
> | blueprintAssignments /operationer | Inga |
> | Ritningar | Inga |
> | ritningar / artefakter | Inga |
> | ritningar / versioner | Inga |
> | ritningar / versioner / artefakter | Inga |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | botServices | Ja |
> | botServices / kanaler | Inga |
> | botServices / anslutningar | Inga |
> | språk | Inga |
> | mallar | Inga |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Redis | Ja |

## <a name="microsoftcapacity"></a>Microsoft.Kapacitet

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appliedReservations | Inga |
> | autoQuotaIncrease | Inga |
> | beräknaUtnytt på | Inga |
> | beräknaPris | Inga |
> | beräknaKöpPrit | Inga |
> | Kataloger | Inga |
> | kommersiellaReservationOrder | Inga |
> | Exchange | Inga |
> | platsKöpOrder | Inga |
> | reservationOrder | Inga |
> | reservationOrder / beräknaRefund | Inga |
> | reservationOrder / sammanfogning | Inga |
> | reservationOrder / reservationer | Inga |
> | reservationOrder / reservationer / revideringar | Inga |
> | reservationOrder / retur | Inga |
> | reservationOrder / split | Inga |
> | reservationOrder / swap | Inga |
> | Reservationer | Inga |
> | resurserProviders | Inga |
> | resources | Inga |
> | valideraReservationOrder | Inga |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSet | Inga |
> | CdnWebApplicationFirewallPolicies | Ja |
> | kantnoder | Inga |
> | Profiler | Ja |
> | profiler/slutpunkter | Ja |
> | profiler/slutpunkter / customdomains | Inga |
> | profiler/slutpunkter/ursprungsgrupper | Inga |
> | profiler/slutpunkter/ursprung | Inga |
> | valideraProbe | Inga |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | certifikatOrdar | Ja |
> | certifikatOrder/certifikat | Inga |
> | valideraCertificateRegistrationInformation | Inga |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner eller behörigheter | Inga |
> | domainNames (domainNames) | Ja |
> | domainNames /capabilities | Inga |
> | domainNames / internalLoadBalancers | Inga |
> | domainNames / serviceCertificates | Inga |
> | domainNames / slots | Inga |
> | domainNames / slots / roller | Inga |
> | domainNames / slots / roller / metricDefinitions | Inga |
> | domainNames / slots / roller / mått | Inga |
> | moveSubscriptionResources | Inga |
> | driftSystemFamilier | Inga |
> | driftSystem | Inga |
> | quotas | Inga |
> | resourceTypes | Inga |
> | valideraAbonnemangFlyttaTillgänglighet | Inga |
> | virtuellaMaskiner | Ja |
> | virtualMachines / diagnosticSettings | Inga |
> | virtualMachines / metricDefinitions | Inga |
> | virtualMachines / mått | Inga |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastrukturMigera

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | klassiskaInfrastrukturResurser | Inga |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner eller behörigheter | Inga |
> | expressRouteCrossAnslutningar | Inga |
> | expressRouteCrossConnections / peerings | Inga |
> | gatewaySupportedDevices | Inga |
> | nätverkSäkerhetsgrupper | Ja |
> | quotas | Inga |
> | reservedIps | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Inga |
> | virtualNetworks / virtualNetworkPeerings | Inga |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | funktioner eller behörigheter | Inga |
> | Diskar | Inga |
> | images | Inga |
> | osImages (osImages) | Inga |
> | osPlatformImager | Inga |
> | offentligaBilder | Inga |
> | quotas | Inga |
> | lagringKonton | Ja |
> | lagringKonton / blobServices | Inga |
> | lagringKonton / filTjänster | Inga |
> | lagringKonto/ måttDefinitioner | Inga |
> | lagringKonto/mått | Inga |
> | lagringKonton /queueServices | Inga |
> | lagringKonton /tjänster | Inga |
> | lagringKonton / tjänster / diagnostikInställningar | Inga |
> | lagringKonton / tjänster / metricDefinitioner | Inga |
> | lagringKonton / tjänster / mått | Inga |
> | lagringKonton / tabellTjänster | Inga |
> | lagringKonton / vmImages | Inga |
> | vmImages | Inga |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | RateCard | Inga |
> | Användningsagga | Inga |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | tillgänglighetSeter | Ja |
> | diskEncryptionSets | Ja |
> | Diskar | Ja |
> | Gallerier | Ja |
> | gallerier / applikationer | Inga |
> | gallerier / applikationer / versioner | Inga |
> | gallerier / bilder | Inga |
> | gallerier / bilder / versioner | Inga |
> | hostGroups | Ja |
> | hostGroups /värdar | Ja |
> | images | Ja |
> | närhetPlatsmentGrupper | Ja |
> | restorePointCollections restorePointCollections restorePointCollections restorePoint | Ja |
> | restorePointCollections /restorePoints restorePoints restorePoints restorePoints restorePoints restorePoints | Inga |
> | sharedVMExtensions | Ja |
> | sharedVMExtensions / versioner | Inga |
> | sharedVMImages | Ja |
> | sharedVMImages / versioner | Inga |
> | snapshots | Ja |
> | sshPublicKeys (sshPublicKeys) | Ja |
> | virtuellaMaskiner | Ja |
> | virtualMachines / tillägg | Ja |
> | virtualMachines / metricDefinitions | Inga |
> | virtuellaMachineScaleSets | Ja |
> | virtualMachineScaleSets / tillägg | Inga |
> | virtualMachineScaleSets / nätverkInterfaces | Inga |
> | virtualMachineScaleSets / publicIPAddresses | Inga |
> | virtualMachineScaleSets / virtualMachines | Inga |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Inga |

## <a name="microsoftconsumption"></a>Microsoft.Förbrukning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aggregeradcost | Inga |
> | Saldon | Inga |
> | Budgetar | Inga |
> | Avgifter | Inga |
> | Kostnadsbrickor | Inga |
> | Krediter | Inga |
> | händelser | Inga |
> | Prognoser | Inga |
> | Massor | Inga |
> | Marknadsplatser | Inga |
> | Prisdokument | Inga |
> | Produkter | Inga |
> | ReservationDetaljer | Inga |
> | ReservationRecommendations | Inga |
> | ReservationSummaries | Inga |
> | ReservationTransaktioner | Inga |
> | Taggar | Inga |
> | Hyresgäster | Inga |
> | Villkor | Inga |
> | UsageDetails | Inga |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerGroups | Ja |
> | serviceAssociationLänkar | Inga |

## <a name="microsoftcontainerregistry"></a>Microsoft.Container-registret

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Register | Ja |
> | register / agentPools | Ja |
> | register / bygger | Inga |
> | register / byggen / avbryt | Inga |
> | register / byggen / getLogLink | Inga |
> | register / buildTasks | Ja |
> | register / buildTasks / steg | Inga |
> | register / eventGridFilters | Inga |
> | register / generateCredentials | Inga |
> | register / getBuildSourceUploadUrl | Inga |
> | register / GetCredentials | Inga |
> | register / importBild | Inga |
> | register / privateEndpointConnectionProxies | Inga |
> | register / privateEndpointConnectionProxies / validera | Inga |
> | register / privateEndpointConnections | Inga |
> | register / privateLinkResources | Inga |
> | register / queueBuild | Inga |
> | register / regenerateCredential | Inga |
> | register / regenerateCredentials | Inga |
> | register /replikeringar | Ja |
> | register / körningar | Inga |
> | register / körningar / avbryt | Inga |
> | register / scheduleRun | Inga |
> | register / scopeMaps | Inga |
> | register / taskRuns | Ja |
> | register /uppgifter | Ja |
> | register / tokens | Inga |
> | register / uppdateringPolicies | Inga |
> | register / webhooks | Ja |
> | register / webhooks / getCallbackConfig | Inga |
> | register / webhooks / ping | Inga |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | containerServices | Ja |
> | managedClusters | Ja |
> | openShiftManagedClusters | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aviseringar | Inga |
> | FaktureringKonto | Inga |
> | Budgetar | Inga |
> | CloudConnectors | Inga |
> | Anslutningar | Ja |
> | Avdelningar | Inga |
> | Dimensioner | Inga |
> | RegistreringKonto | Inga |
> | Export | Inga |
> | ExternafaktureringKonton | Inga |
> | ExternafaktureringKonton / Aviseringar | Inga |
> | Externafaktureringskonton / dimensioner | Inga |
> | Externafaktureringskonton / Prognos | Inga |
> | ExternafaktureringKonton / Fråga | Inga |
> | Externa prenumerationer | Inga |
> | Externa prenumerationer / Varningar | Inga |
> | Externa prenumerationer/dimensioner | Inga |
> | Externa prenumerationer / prognos | Inga |
> | Externa prenumerationer / Fråga | Inga |
> | Prognos | Inga |
> | Söka i data | Inga |
> | registrera | Inga |
> | Rapportkonfigs | Inga |
> | Rapporter | Inga |
> | Inställningar | Inga |
> | showbackRules (showbackRules) | Inga |
> | Vyer | Inga |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Begäranden | Inga |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Sammanslutningar | Inga |
> | resurserProviders | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Jobb | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | DataBoxEdgeEnheter | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbetsytor | Ja |
> | arbetsytor / dbWorkspaces | Inga |
> | arbetsytor / lagringFörkryptering | Inga |
> | arbetsytor / virtualNetworkPeerings | Inga |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kataloger | Ja |
> | datakategorier | Ja |
> | datakataloger / datasources | Inga |
> | datakataloger / datasources / skanningar | Inga |
> | datakataloger / datakällor / skanningar / dataset | Inga |
> | datakataloger / datakällor / skanningar / triggers | Inga |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataFakta | Ja |
> | dataFakta / diagnostikInställningar | Inga |
> | dataFakta / metricDefinitioner | Inga |
> | dataFactorySchema | Inga |
> | Fabriker | Ja |
> | fabriker / integrationRuntimes | Inga |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.DataLakeAnalytics Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton / dataLakeStoreAccounts | Inga |
> | konton / lagringKonton | Inga |
> | konton / lagringKonton / behållare | Inga |
> | konton / transferAnalyticsUnits | Inga |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton / eventGridFilters | Inga |
> | konton / firewallRules | Inga |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |
> | tjänster /projekt | Ja |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton / aktier | Inga |
> | konton / aktier / datauppsättningar | Inga |
> | konton / aktier / inbjudningar | Inga |
> | konton / aktier / leverantöraktieteckningar | Inga |
> | konton / aktier / synkroniseringSinställningar | Inga |
> | konton / aktieabonnemang | Inga |
> | konton / aktieabonnemang / consumerSourceDataSets | Inga |
> | konton / aktieabonnemang / datasetmappings | Inga |
> | konton / aktieabonnemang / triggers | Inga |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Servrar | Ja |
> | servrar / rådgivare | Inga |
> | servrar / nycklar | Inga |
> | servrar / privateEndpointConnectionProxies | Inga |
> | servrar / privateEndpointAnslutningar | Inga |
> | servrar / privateLinkResources | Inga |
> | servrar / queryTexter | Inga |
> | servrar / återvinningsbaraServers | Inga |
> | servrar / topQueryStatistics | Inga |
> | servrar / virtualNetworkRules | Inga |
> | servrar / waitStatistics | Inga |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Servrar | Ja |
> | servrar / rådgivare | Inga |
> | servrar / nycklar | Inga |
> | servrar / privateEndpointConnectionProxies | Inga |
> | servrar / privateEndpointAnslutningar | Inga |
> | servrar / privateLinkResources | Inga |
> | servrar / queryTexter | Inga |
> | servrar / återvinningsbaraServers | Inga |
> | servrar / topQueryStatistics | Inga |
> | servrar / virtualNetworkRules | Inga |
> | servrar / waitStatistics | Inga |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | serverGrupper | Ja |
> | Servrar | Ja |
> | servrar / rådgivare | Inga |
> | servrar / nycklar | Inga |
> | servrar / privateEndpointConnectionProxies | Inga |
> | servrar / privateEndpointAnslutningar | Inga |
> | servrar / privateLinkResources | Inga |
> | servrar / queryTexter | Inga |
> | servrar / återvinningsbaraServers | Inga |
> | servrar / topQueryStatistics | Inga |
> | servrar / virtualNetworkRules | Inga |
> | servrar / waitStatistics | Inga |
> | servrarv2 | Ja |
> | singleServers | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | artefakterKällar | Ja |
> | Utbyggnader | Ja |
> | serviceTopologies | Ja |
> | serviceTopologier / tjänster | Ja |
> | serviceTopologier / tjänster / serviceUnits | Ja |
> | steg | Ja |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | programgrupper | Ja |
> | programgrupper/program | Inga |
> | programgrupper / stationära datorer | Inga |
> | applikationsgrupper / startmenuitems | Inga |
> | värdpooler | Ja |
> | hostpools / sessionhosts | Inga |
> | hostpools / sessionhosts / usersessions hostpools / sessions | Inga |
> | hostpools / usersessions | Inga |
> | arbetsytor | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ElasticPools | Ja |
> | ElasticPools / IotHubTenants | Ja |
> | ElasticPools / IotHubTenants / securitySettings | Inga |
> | IotHubs | Ja |
> | IotHubs / eventGridFilters | Inga |
> | IotHubs / securitySettings | Inga |
> | EtableringTjänster | Ja |
> | Användningsområden | Inga |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Rörledningar | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Styrenheter | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labcenters | Ja |
> | Labs | Ja |
> | laboratorier / miljöer | Ja |
> | labb / serviceRunners | Ja |
> | laboratorier / virtuellaMaskiner | Ja |
> | Scheman | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | databasKontoNamn | Inga |
> | databasKonton | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Domäner | Ja |
> | domäner / domänÄgarebetare | Inga |
> | genereraSsoRequest | Inga |
> | topLevelDomains | Inga |
> | valideraDomainRegistrationInformation | Inga |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | lcsprojekt | Inga |
> | lcsprojects / clouddeployments lcsprojects / clouddeployments lcsprojects / clouddeployments lc | Inga |
> | lcsprojects / kontakter | Inga |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Domäner | Ja |
> | domäner / ämnen | Inga |
> | händelseAbonnemang | Inga |
> | extensionTopics | Inga |
> | partnerNamnområden | Ja |
> | partnerNamnområden / eventKanaler | Inga |
> | partnerRegistrations | Ja |
> | partnerTopics | Ja |
> | partnerTopics / eventAbonnemang | Inga |
> | systemTopics | Ja |
> | systemTopics / eventAbonnemang | Inga |
> | Ämnen | Ja |
> | topicTypes | Inga |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kluster | Ja |
> | Namnområden | Ja |
> | namnrymd / tillståndsregler | Inga |
> | namnrymd / katastrofåterupptäcktconfigs | Inga |
> | namnområden / eventhubs | Inga |
> | namnrymd / eventhubs / authorizationrules | Inga |
> | namnområden / eventhubs / konsumentgrupper | Inga |
> | namnrymd / nätverksregler | Inga |

## <a name="microsoftfalcon"></a>Microsoft.Falcon (olika)

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namnområden | Ja |

## <a name="microsoftfeatures"></a>Microsoft.Funktioner

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | featureProviders | Inga |
> | funktioner | Inga |
> | Leverantörer | Inga |
> | abonnemangFöreelser | Inga |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Registrera | Inga |
> | galleriiter | Inga |
> | generateartifactaccessuri | Inga |
> | myareas | Inga |
> | myareas / områden | Inga |
> | myareas / områden / områden | Inga |
> | myareas / områden / områden / galleryitems | Inga |
> | myareas / områden / galleryitems | Inga |
> | myareas / galleryitems | Inga |
> | registrera | Inga |
> | resources | Inga |
> | retrieveresourcesbyid | Inga |

## <a name="microsoftgenomics"></a>Microsoft.Genomik

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | autoManagedRedovisningar | Ja |
> | autoManagedVmConfigurationProfiles | Ja |
> | konfigurationProfileAstilldelningar | Inga |
> | gästKonfigurationTilldelningar | Inga |
> | Programvara | Inga |
> | softwareUpdateProfile | Inga |
> | programvaraUppdater | Inga |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hanaInstances (olikart) | Ja |
> | sapMonitors (sapMonitors) | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedikeradeHSM-moduler | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kluster | Ja |
> | kluster / applikationer | Inga |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Maskiner | Ja |
> | maskiner / förlängningar | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dataHantverkare | Ja |

## <a name="microsofthydra"></a>Microsoft.Hydra (på andra)

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Komponenter | Ja |
> | nätverkScopes | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Jobb | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnostikInställningar | Inga |
> | diagnostikEtteringsKategories | Inga |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appEmplates | Inga |
> | IoTApps | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Graph | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | deletedVaults | Inga |
> | hsmPools (hsmPools) | Ja |
> | Valv | Ja |
> | valv / accessPolicies | Inga |
> | valv / eventGridFilters | Inga |
> | valv / hemligheter | Inga |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | anslutnaKlusterer | Ja |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kluster | Ja |
> | kluster /bifogadedatabaskonfigurationer | Inga |
> | kluster / databaser | Inga |
> | kluster / databaser / dataanslutningar | Inga |
> | kluster / databaser / eventhubconnections | Inga |
> | kluster / databaser / principalassignments | Inga |
> | kluster/dataanslutningar | Inga |
> | kluster /principalastilldelningar | Inga |
> | kluster /delade enheter | Inga |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | labaccounts | Ja |
> | användare | Inga |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hostingMiljöer | Ja |
> | integrationKonton | Ja |
> | integrationTjänstMiljöer | Ja |
> | integrationServiceMiljöer / managedApis | Ja |
> | isoleradeMiljöer | Ja |
> | Arbetsflöden | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLärning

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | åtagandePlaner | Ja |
> | Webservices | Ja |
> | Arbetsytor | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbetsytor | Ja |
> | arbetsytor / beräkningar | Inga |
> | arbetsytor / eventGridFilters | Inga |

## <a name="microsoftmaintenance"></a>Microsoft.Underhåll

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | applyUpdates | Inga |
> | configurationAstilldelningar | Inga |
> | underhållKonfigurationer | Ja |
> | uppdateringar | Inga |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.ManagedIdentity Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Identiteter | Inga |
> | userAssignedId entiteter | Ja |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | marknadsplatsRegistrationDefinitioner | Inga |
> | registreringTilldelningar | Inga |
> | registreringDefinier | Inga |

## <a name="microsoftmanagement"></a>Microsoft.Management (På)Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hämta enheter | Inga |
> | managementGroups | Inga |
> | managementGroups /inställningar | Inga |
> | resources | Inga |
> | startTenantBackfill | Inga |
> | klientBackfillStatus | Inga |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |
> | konton / eventGridFilters | Inga |
> | konton / privateAtlases | Ja |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Erbjuder | Inga |
> | offerTyper | Inga |
> | offerTyper/utgivare | Inga |
> | offerTyper/ utgivare/erbjudanden | Inga |
> | offerTyper / utgivare / erbjudanden / planer | Inga |
> | offerTyper / utgivare / erbjudanden / planer / avtal | Inga |
> | offerTyper / utgivare / erbjudanden / planer / configs | Inga |
> | offerTyper / utgivare / erbjudanden / planer / configs / importImage | Inga |
> | privategalleryitems | Inga |
> | privatButikClient | Inga |
> | privateStores | Inga |
> | privateStores / erbjudanden | Inga |
> | Produkter | Inga |
> | Förlag | Inga |
> | förlag / erbjudanden | Inga |
> | förlag / erbjudanden / ändringar | Inga |
> | registrera | Inga |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | classicDevServices | Ja |
> | uppdateraKommunikationsövertolkning | Inga |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceBeställa

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Avtal | Inga |
> | offertyper | Inga |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | medietjänster | Ja |
> | mediatjänster / kontoFilter | Inga |
> | mediaservice /tillgångar | Inga |
> | mediatjänster / tillgångar / tillgångFilter | Inga |
> | mediatjänster / contentKeyPolicies | Inga |
> | mediaservices / eventGridFilters | Inga |
> | mediaservices / liveEventOperations | Inga |
> | mediaservices / liveEvents | Ja |
> | mediaservices / liveEvents / liveOutputs | Inga |
> | mediaservices / liveOutputOperations | Inga |
> | mediatjänster / mediaGrafer | Inga |
> | mediatjänster / streamingEndpointOperations | Inga |
> | mediatjänster / streamingEndpoints | Ja |
> | mediatjänster / streamingLocators | Inga |
> | mediatjänster / streamingPolicies | Inga |
> | mediaservices / transformeringar | Inga |
> | mediaservices / transformeringar / jobb | Inga |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | appKluster | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrera

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | bedömningProjekt | Ja |
> | migreraprojekt | Ja |
> | flyttaKolleringar | Ja |
> | Projekt | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | holografiSkräkenUtnämningar | Ja |
> | objektUnderstandingAccounts | Ja |
> | remoteRenderingRedovisningar | Ja |
> | spatialAnchorsKonton | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | netAppAccounts (netAppAccounts) | Ja |
> | netAppAccounts / accountBackups | Inga |
> | netAppAccounts / capacityPools | Ja |
> | netAppAccounts / capacityPools / volymer | Ja |
> | netAppAccounts / capacityPools / volymer / ögonblicksbilder | Ja |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ansökanGateways | Ja |
> | ansökanGatewayWebApplicationFirewallPolicies | Ja |
> | applicationSecurityGroups | Ja |
> | azureFirewallFqdnTags | Inga |
> | azureFirewalls | Ja |
> | bastionHosts (bastionHosts) | Ja |
> | bgpServiceKommuner | Inga |
> | Anslutningar | Ja |
> | ddosCustomPolicies | Ja |
> | ddosProtectionPlans | Ja |
> | dnsOperationStatuses | Inga |
> | dnszones | Ja |
> | dnszones / A | Inga |
> | dnszones / AAAA | Inga |
> | dnszones / alla | Inga |
> | dnszones / CAA | Inga |
> | dnszones / CNAME | Inga |
> | dnszones / MX | Inga |
> | dnszones / NS | Inga |
> | dnszones / PTR | Inga |
> | dnszones / recordset | Inga |
> | dnszones / SOA | Inga |
> | dnszones / SRV | Inga |
> | dnszones / TXT | Inga |
> | expressRouteCirklar | Ja |
> | expressRouteCrossAnslutningar | Ja |
> | expressRouteGateways | Ja |
> | expressRoutePorts | Ja |
> | expressRouteServiceProviders | Inga |
> | brandväggPolicies | Ja |
> | frontdörrar | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Inga |
> | frontdoorWebApplicationFirewallPolicies | Ja |
> | getDnsResourceReference | Inga |
> | interntAnmäl dig | Inga |
> | loadBalancers | Ja |
> | localNetworkGateways | Ja |
> | natGateways (natGateways) | Ja |
> | nätverkIntentPolicies | Ja |
> | nätverkGränssnitt | Ja |
> | nätverkProfiler | Ja |
> | nätverkSäkerhetsgrupper | Ja |
> | nätverkWatchers | Ja |
> | nätverkWatchers / connectionMonitors | Ja |
> | nätverkWatchers / linser | Ja |
> | nätverkWatchers / pingMeshes | Ja |
> | p2sVpnGateways | Ja |
> | privateDnsOperationStatuses | Inga |
> | privatDnsZones | Ja |
> | privateDnsZones / A | Inga |
> | privateDnsZones / AAAA | Inga |
> | privateDnsZones / alla | Inga |
> | privateDnsZones / CNAME | Inga |
> | privateDnsZones / MX | Inga |
> | privateDnsZones / PTR | Inga |
> | privateDnsZones / SOA | Inga |
> | privateDnsZones / SRV | Inga |
> | privateDnsZones / TXT | Inga |
> | privateDnsZones / virtualNetworkLinks | Ja |
> | privateEndpoints | Ja |
> | privateLinkServices | Ja |
> | offentliga IP-adresser | Ja |
> | offentligaIP-korrigeringar | Ja |
> | routeFilters | Ja |
> | routeTables | Ja |
> | serviceEndpointPolicies | Ja |
> | trafikManagerGeographicHierarchies | Inga |
> | trafficmanagerprofiles | Ja |
> | trafficmanagerprofiles / heatMaps | Inga |
> | trafikManagerUserMetricsKeys | Inga |
> | virtualHubs | Ja |
> | virtualNetworkGateways | Ja |
> | virtualNetworks | Ja |
> | virtualNetworkTaps | Ja |
> | virtuellaWans | Ja |
> | vpnGateways (vpnGateways) | Ja |
> | vpnSites (vpnSites) | Ja |
> | webApplicationFirewallPolicies | Ja |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Bärbara datorer | Inga |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namnområden | Ja |
> | namnområden / anmälanHubs | Ja |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | osNamespaces | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | HyperVSites (HyperVSites) | Ja |
> | ImportPlatser | Ja |
> | ServerPlatser | Ja |
> | VmwareSites (VMwareSites) | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kluster | Ja |
> | länkMål | Inga |
> | lagringInightConfigs | Inga |
> | arbetsytor | Ja |
> | arbetsytor / dataExportar | Inga |
> | arbetsytor / dataSources | Inga |
> | arbetsytor / linkedServices | Inga |
> | arbetsytor / linkedStorageAccounts | Inga |
> | arbetsytor / fråga | Inga |
> | arbetsytor / scopedPrivateLinkProxies | Inga |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managementassociations | Inga |
> | hanteringskonfigurationer | Ja |
> | lösningar | Ja |
> | Visningar | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | legacyPeerings | Inga |
> | peerAsns | Inga |
> | peerings | Ja |
> | peeringServiceLänder | Inga |
> | peeringServiceProviders | Inga |
> | peeringTjänster | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | policyEvents | Inga |
> | policyMetadata | Inga |
> | policyStates | Inga |
> | policyTrackedResources | Inga |
> | avhjälpande åtgärder | Inga |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> |  -konsoler | Inga |
> | instrumentpaneler | Ja |
> | userSettings | Inga |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | arbetsytaInsamlingar | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.PowerBIDedicated Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Kapacitet | Ja |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Ja |

## <a name="microsoftquantum"></a>Microsoft.Quantum (På andra)

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Arbetsytor | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | backupSkyddadeobjektier | Inga |
> | Valv | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namnområden | Ja |
> | namnrymd / tillståndsregler | Inga |
> | namnrymder / hybridanslutningar | Inga |
> | namnrymder / hybridanslutningar / authorizationrules | Inga |
> | namnområden / wcfrelays | Inga |
> | namnrymd / wcfrelays / authorizationrules | Inga |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | konton | Inga |
> | Samlingar | Ja |
> | samlingar / applikationer | Inga |
> | samlingar /säkerhetsprinciper | Inga |
> | mallBilder | Inga |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Frågor | Ja |
> | resursChangeDetails | Inga |
> | resurserChanges | Inga |
> | resources | Inga |
> | resurserHistoria | Inga |
> | abonnemangStatus | Inga |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | tillgänglighetStatusar | Inga |
> | childAvailabilityStatuses | Inga |
> | childResources | Inga |
> | nyafrågor | Inga |
> | händelser | Inga |
> | påverkadeResurser | Inga |
> | metadata | Inga |
> | meddelanden | Inga |

## <a name="microsoftresources"></a>Microsoft.Resurser

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Distributioner | Inga |
> | distributioner / operationer | Inga |
> | deploymentScripts | Ja |
> | deploymentScripts / loggar | Inga |
> | Länkar | Inga |
> | notifyResourceJobs | Inga |
> | Leverantörer | Inga |
> | resursGrupper | Inga |
> | Prenumerationer | Inga |
> | Hyresgäster | Inga |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | saasresources | Inga |

## <a name="microsoftsearch"></a>Microsoft.Sök

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | resursHealthMetadata | Inga |
> | sökTjänster | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Säkerhet

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | adaptiveNetworkHårningar | Inga |
> | avanceratTre skyddsinställningar | Inga |
> | aviseringar | Inga |
> | tillåtnaAnslutningar | Inga |
> | programVitlistings | Inga |
> | bedömningMetadata | Inga |
> | utvärderingar | Inga |
> | autoDismissAlertsRules | Inga |
> | automatiseringar | Ja |
> | AutoProvisioningSettings | Inga |
> | Efterlevnad | Inga |
> | dataInsamlingAgenter | Inga |
> | deviceSecurityGroups | Inga |
> | upptäcktSäkerhetLösningar | Inga |
> | extern SäkerhetLösningar | Inga |
> | InformationProtectionPolicies | Inga |
> | iotSecurityLösningar | Ja |
> | iotSecurityLösningar / analyticsModels | Inga |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Inga |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Inga |
> | jitNetworkAccessPolicies | Inga |
> | nätverkData | Inga |
> | policies | Inga |
> | priser | Inga |
> | reglerande EfterlevnadStandarder | Inga |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Inga |
> | reglerande EfterlevnadStandards / regulatoriska EfterlevnadKontroller / regulatoriska Efterlevnadsassessments | Inga |
> | säkraScoreControlDefinitions | Inga |
> | säkraScoreControls | Inga |
> | säkraScores | Inga |
> | secureScores / secureScoreControls | Inga |
> | säkerhetKontakter | Inga |
> | säkerhetLösningar | Inga |
> | säkerhetLösningarReferenceData | Inga |
> | säkerhetStatusar | Inga |
> | säkerhetStatusEsSummaries | Inga |
> | serverVulnerbarhetBes här. | Inga |
> | settings | Inga |
> | understämningar | Inga |
> | uppgifter | Inga |
> | Topologier | Inga |
> | arbetsytorInställningar | Inga |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph Microsoft.SecurityGraph Microsoft.SecurityGraph Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnostikInställningar | Inga |
> | diagnostikEtteringsKategories | Inga |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Aggregeringar | Inga |
> | alertRules | Inga |
> | alertRuleTemplates | Inga |
> | bokmärken | Inga |
> | Fall | Inga |
> | dataAnslutningar | Inga |
> | dataConnectorsCheckRequirements | Inga |
> | Enheter | Inga |
> | entityQueries | Inga |
> | Incidenter | Inga |
> | kontorConsents | Inga |
> | settings | Inga |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Namnområden | Ja |
> | namnrymd / tillståndsregler | Inga |
> | namnrymd / katastrofåterupptäcktconfigs | Inga |
> | namnområden / eventgridfilter | Inga |
> | namnrymd / nätverksregler | Inga |
> | namnområden / köer | Inga |
> | namnrymd / köer / authorizationrules | Inga |
> | namnområden / ämnen | Inga |
> | namnrymd / ämnen / authorizationrules | Inga |
> | namnrymd / ämnen / prenumerationer | Inga |
> | namnrymd / ämnen / abonnemang / regler | Inga |
> | premiumMessagingRegioner | Inga |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | Kluster | Ja |
> | kluster / applikationer | Inga |
> | containerGroups | Ja |
> | containerGroupSets | Ja |
> | kantklusor | Ja |
> | kantkluster / applikationer | Inga |
> | managedclusters | Ja |
> | managedclusters / nodetyper | Inga |
> | Nätverk | Ja |
> | hemliga butiker | Ja |
> | secretstores / certifikat | Inga |
> | secretstores / hemligheter | Inga |
> | volumes | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | program | Ja |
> | containerGroups | Ja |
> | Gateways | Ja |
> | Nätverk | Ja |
> | secrets | Ja |
> | volumes | Ja |

## <a name="microsoftservices"></a>Microsoft.Tjänster

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | leverantörRegistrationer | Inga |
> | providerRegistrations / resourceTypeRegistrations | Inga |
> | Utbyggnader | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SignalR | Ja |
> | SignalR / eventGridFilters | Inga |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SiteRecoveryVault | Ja |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | hybridAnvändNingspassningar | Inga |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | ansökanDefinitioner | Ja |
> | program | Ja |
> | jitRequests (påtängen) | Ja |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | registreradeAbonnemang | Inga |
> | Spolar | Ja |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | managedInstances | Ja |
> | managedInstances / databaser | Ja |
> | managedInstances / databaser / backupKortTermRetentionPolicies | Inga |
> | managedInstances / databaser / scheman / tabeller / kolumner / känslighetLabels | Inga |
> | managedInstances / databaser / sårbarhetAssessments | Inga |
> | managedInstances / databaser / sårbarhetAssessments / regler / baslinjer | Inga |
> | managedInstances / encryptionProtector | Inga |
> | managedInstances / nycklar | Inga |
> | managedInstances / restorableDroppedDatabases / backupKortTermRetentionPolicies | Inga |
> | managedInstances /vulnerabilityAssessments | Inga |
> | Servrar | Ja |
> | servrar /administratörer | Inga |
> | servrar / communicationLinks | Inga |
> | servrar / databaser | Ja |
> | servrar / krypteringSkydd | Inga |
> | servrar / firewallRules | Inga |
> | servrar / nycklar | Inga |
> | servrar / reparerbaraDroppedDatabases | Inga |
> | servrar /serviceobjektiv | Inga |
> | servrar / tdeCertificates | Inga |
> | virtuellaClusters | Inga |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Inga |
> | SqlVirtualMachines | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | lagringKonton | Ja |
> | lagringKonton / blobServices | Inga |
> | lagringKonton / filTjänster | Inga |
> | lagringKonton /queueServices | Inga |
> | lagringKonton /tjänster | Inga |
> | lagringKonton / tjänster / metricDefinitioner | Inga |
> | lagringKonton / tabellTjänster | Inga |
> | Användningsområden | Inga |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Cachar | Ja |
> | cachar / lagringTargets | Inga |
> | användningModeller | Inga |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | replikeringsgrupper | Inga |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Inga |
> | storageSyncServices/syncGroups | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga |
> | storageSyncServices/arbetsflöden | Inga |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Inga |
> | storageSyncServices/syncGroups | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga |
> | storageSyncServices/arbetsflöden | Inga |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices / registeredServers | Inga |
> | storageSyncServices/syncGroups | Inga |
> | storageSyncServices / syncGroups / cloudEndpoints | Inga |
> | storageSyncServices / syncGroups / serverEndpoints | Inga |
> | storageSyncServices/arbetsflöden | Inga |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Chefer | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | streamingjobb | Ja |

## <a name="microsoftsubscription"></a>Microsoft.Prenumeration

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | avbryt | Inga |
> | Skapateckning | Inga |
> | Aktivera | Inga |
> | byt namn | Inga |
> | PrenumerationDefinitioner | Inga |
> | PrenumerationEr | Inga |
> | Prenumerationer | Inga |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Miljöer | Ja |
> | miljöer / accessPolicies | Inga |
> | miljöer/eventsources | Ja |
> | miljöer / referensDataSets | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | dedikeradCloudNodes | Ja |
> | dedikeradeCloudServices | Ja |
> | virtuellaMaskiner | Ja |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | enheter | Ja |
> | registreradeAbonnemang | Inga |
> | Leverantörer | Inga |
> | leverantörer / skus | Inga |
> | leverantörer / vnfs | Inga |
> | virtualNetworkFunctionSkus | Inga |
> | vnfs (vnfs) | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | apiManagementKonton | Inga |
> | apiManagementKonton / apiAcls | Inga |
> | apiManagementAccounts / apis | Inga |
> | apiManagementAccounts / apis / apiAcls apiManagementAccounts / apis / apiAcls apiManagementAccounts / apis / apiAcls apiMan | Inga |
> | apiManagementAccounts / apis / connectionAcls | Inga |
> | apiManagementAccounts / apis / anslutningar | Inga |
> | apiManagementAccounts / apis / anslutningar / connectionAcls | Inga |
> | apiManagementAccounts / apis / localizedDefinitions | Inga |
> | apiManagementRedovisning / anslutningAkn(2) | Inga |
> | apiManagementKonton / anslutningar | Inga |
> | billingMetrar | Inga |
> | certifikat | Ja |
> | anslutningGateways | Ja |
> | Anslutningar | Ja |
> | customApis | Ja |
> | deletedSites | Inga |
> | hostingMiljöer | Ja |
> | hostingMiljöer / eventGridFilters | Inga |
> | hostingMiljöer / multiRolePools | Inga |
> | hostingMiljöer / workerPools | Inga |
> | kubeMiljöer | Ja |
> | publishingAnvändare | Inga |
> | rekommendationer | Inga |
> | resursHealthMetadata | Inga |
> | Runtimes | Inga |
> | serverFarms | Ja |
> | serverFarms / eventGridFilters | Inga |
> | webbplatser | Ja |
> | platser/config  | Inga |
> | platser / eventGridFilters | Inga |
> | webbplatser / hostNameBindings | Inga |
> | platser / nätverkConfig | Inga |
> | platser / premieraddons | Ja |
> | platser / platser | Ja |
> | platser / platser / eventGridFilters | Inga |
> | platser / platser / hostNameBindings | Inga |
> | platser / platser / nätverkConfig | Inga |
> | sourceControls | Inga |
> | staticSites | Ja |
> | Validera | Inga |
> | verifieraHostingMiljöVnet | Inga |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | diagnostikInställningar | Inga |
> | diagnostikEtteringsKategories | Inga |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Enhetstjänster | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resurstyp | Borttagning i complete-läget |
> | ------------- | ----------- |
> | Komponenter | Inga |
> | komponenterSumma | Inga |
> | monitorInsikter | Inga |
> | monitorInstancesSummary | Inga |
> | Bildskärmar | Inga |
> | meddelandenInställningar | Inga |

## <a name="next-steps"></a>Nästa steg

Om du vill hämta samma data som en fil med kommaavgränsade värden hämtar du [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
