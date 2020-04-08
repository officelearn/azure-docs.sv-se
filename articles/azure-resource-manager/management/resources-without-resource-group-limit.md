---
title: Resurser utan 800 räknegräns
description: Visar en lista över azure-resurstyper som kan ha fler än 800 instanser i en resursgrupp.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 8644bec1a68acebff18cf83d17acb014784dc964
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804783"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Resurser som inte är begränsade till 800 instanser per resursgrupp

Som standard kan du distribuera upp till 800 instanser av en resurstyp i varje resursgrupp. Vissa resurstyper är dock undantagna från 800-instansgränsen. I den här artikeln visas de Azure-resurstyper som kan ha fler än 800 instanser i en resursgrupp. Alla andra resurstyper är begränsade till 800 instanser.

För vissa resurstyper måste du kontakta supporten för att få 800-instansgränsen borttagen. Dessa resurstyper noteras i den här artikeln.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationKonton

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* Registreringar
* registreringar/kundAbonnemang
* registreringar/produkter

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - Som standard begränsad till 800 instanser. Den gränsen kan höjas genom att kontakta supporten.

## <a name="microsoftcompute"></a>Microsoft.Compute

* Diskar
* Gallerier
* gallerier/bilder
* gallerier/bilder/versioner
* images
* snapshots
* virtuellaMaskiner

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.Container-registret

* register/buildTasks
* register/buildTasks/listSourceRepositoryProperties
* register/buildTasks/steps
* register/buildTasks/steps/listBuildArguments
* register/eventGridFilters
* register/replikeringar
* register/uppgifter
* register/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* Servrar

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* Servrar

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGrupper
* Servrar
* servrarv2
* singleServers

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* scheman - Som standard begränsad till 800 instanser. Den gränsen kan höjas genom att kontakta supporten.

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* Kluster
* Namnområden

## <a name="microsoftexperimentation"></a>Microsoft.Experiment

* experimentArbetsytor

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* konfigurationProfileAstilldelningar
* gästKonfigurationTilldelningar
* Programvara
* softwareUpdateProfile
* programvaraUppdater

## <a name="microsoftinsights"></a>microsoft.insights microsoft.insights microsoft.insights microsoft.

* metricalerter

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationKonton
* Arbetsflöden

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts (netAppAccounts)
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volymer
* netAppAccounts/capacityPools/volymer/mountTargets
* netAppAccounts/capacityPools/volymer/ögonblicksbilder

## <a name="microsoftnetwork"></a>Microsoft.Network

* ansökanGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts (bastionHosts)
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/alla
* dnszones/recordset
* nätverkIntentPolicies
* nätverkGränssnitt
* privatDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/alla
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses - Som standard begränsad till 800 instanser. Den gränsen kan höjas genom att kontakta supporten.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections - Som standard begränsad till 800 instanser. Den gränsen kan höjas genom att kontakta supporten.

## <a name="microsoftrelay"></a>Microsoft.Relay

* Namnområden

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobbsamlingar

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* Namnområden

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* program
* containerGroups
* Gateways
* Nätverk
* secrets
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* lagringKonton

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementKonton/api:er
* webbplatser

## <a name="next-steps"></a>Nästa steg

En fullständig lista över kvoter och begränsningar finns i [Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar](azure-subscription-service-limits.md).
