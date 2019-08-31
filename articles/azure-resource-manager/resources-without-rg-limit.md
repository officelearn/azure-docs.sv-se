---
title: Azure-resurser utan antal 800-gränser
description: Visar en lista över de Azure-resurs typer som kan ha fler än 800 instanser i en resurs grupp.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: tomfitz
ms.openlocfilehash: eec448dcbf1c550b6b0a0ee0f17505fd6ddd3c74
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194732"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Resurser som inte är begränsade till 800 instanser per resurs grupp

Som standard kan du distribuera upp till 800 instanser av en resurs typ i varje resurs grupp. Vissa resurs typer är dock undantagna från gränsen på 800-instanser. Den här artikeln innehåller en lista över de Azure-resurs typer som kan ha fler än 800 instanser i en resurs grupp. Alla andra resurs typer är begränsade till 800 instanser.

För vissa resurs typer måste du kontakta supporten om du vill ta bort instans gränsen på 800. Dessa resurs typer anges i den här artikeln.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registreringar
* registreringar/customerSubscriptions
* registreringar/produkter

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices – kontakta supporten för att utöka gränsen.

## <a name="microsoftcompute"></a>Microsoft.Compute

* Diskar
* bilder
* ögonblicksbilder
* virtualMachines

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* register/buildTasks
* register/buildTasks/listSourceRepositoryProperties
* register/buildTasks/steg
* register/buildTasks/steg/listBuildArguments
* registries/eventGridFilters
* register/replikeringar
* register/uppgifter
* register/Webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servrar

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servrar

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* servrar
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* guestConfigurationAssignments
* IntelliPoint
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* arbetsflöden

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
* publicIPAddresses – kontakta supporten för att utöka gränsen.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections – kontakta supporten för att utöka gränsen.

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* förfrågningsåtgärder

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* webbplatser

## <a name="next-steps"></a>Nästa steg

En fullständig lista över kvoter och begränsningar finns i [Azure-prenumerationer, tjänst gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).
