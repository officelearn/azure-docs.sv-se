---
title: Azure-resurser utan antal 800-gränser
description: Visar en lista över de Azure-resurs typer som kan ha fler än 800 instanser i en resurs grupp.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: tomfitz
ms.openlocfilehash: c08b80a841199ed2737f6fe8d7f2b76943c5b269
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795661"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Resurser som inte är begränsade till 800 instanser per resurs grupp

Som standard kan du distribuera upp till 800 instanser av en resurs typ i varje resurs grupp. Vissa resurs typer är dock undantagna från gränsen på 800-instanser. Den här artikeln innehåller en lista över de Azure-resurs typer som kan ha fler än 800 instanser i en resurs grupp. Alla andra resurs typer är begränsade till 800 instanser.

För vissa resurs typer måste du kontakta supporten om du vill ta bort instans gränsen på 800. Dessa resurs typer anges i den här artikeln.


## <a name="microsoftautomation"></a>Microsoft. Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

* registreringar
* registreringar/customerSubscriptions
* registreringar/produkter

## <a name="microsoftbotservice"></a>Microsoft. BotService

* botServices – som standard är begränsad till 800 instanser. Du kan öka gränsen genom att kontakta supporten.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disk
* images
* snapshots
* VirtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

* register/buildTasks
* register/buildTasks/listSourceRepositoryProperties
* register/buildTasks/steg
* register/buildTasks/steg/listBuildArguments
* register/eventGridFilters
* register/replikeringar
* register/uppgifter
* register/Webhooks

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

* servrar

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

* servrar

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

* serverGroups
* servrar
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft. EventHub

* kluster
* namn områden

## <a name="microsoftexperimentation"></a>Microsoft. experimentering

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

* configurationProfileAssignments
* guestConfigurationAssignments
* IntelliPoint
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft. Logic

* integrationAccounts
* arbetsflöden

## <a name="microsoftnetapp"></a>Microsoft. NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/Volumes
* netAppAccounts/capacityPools/Volumes/mountTargets
* netAppAccounts/capacityPools/volym/ögonblicks bilder

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
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
* dnszones/Recordset
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
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
* publicIPAddresses – som standard är begränsad till 800 instanser. Du kan öka gränsen genom att kontakta supporten.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

* workspaceCollections – som standard är begränsad till 800 instanser. Du kan öka gränsen genom att kontakta supporten.

## <a name="microsoftrelay"></a>Microsoft. Relay

* namn områden

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

* förfrågningsåtgärder

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* namn områden

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

* program
* containerGroups
* gatewayer
* nätet
* secrets
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft. Web

* apiManagementAccounts/API: er
* stationer

## <a name="next-steps"></a>Nästa steg

En fullständig lista över kvoter och begränsningar finns i [Azure-prenumerationer, tjänst gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).
