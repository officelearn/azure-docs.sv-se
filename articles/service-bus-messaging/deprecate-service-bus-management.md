---
title: Azure messaging services - Service Manager to Resource Manager
description: Den här artikeln innehåller mappning av inaktuella AZURE Service Manager REST API & PowerShell-cmdletar till RESOURCE Manager REST API & PowerShell-cmdlets.
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589914"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Infasning av Azure Service Manager-stöd för Azure Service Bus, Relay och Event Hubs

Resource Manager, vår nästa generations molninfrastrukturstack, ersätter helt den "klassiska" Azure Service Management-modellen (klassisk distributionsmodell). Därför dras klassiska REST-API:er för distributionsmodellen och stöd för servicebuss-, relä- och eventhubbar tillbaka den 1 november 2021. Denna utfasning tillkännagavs först på en [Microsoft Tech gemenskapen tillkännagivande](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909), men vi har nyligen beslutat att förlänga utfasningsperioden ytterligare två år från tidpunkten för det ursprungliga tillkännagivandet. För enkel identifiering har `management.core.windows.net` dessa API:er i sin URI. Se följande tabell för en lista över de inaktuella API:erna och deras Azure Resource Manager API-version som du nu bör använda.

Om du vill fortsätta använda Service Bus,Relay och Event Hubs flyttar du till Resource Manager senast den 31 oktober 2021. Vi uppmuntrar alla kunder som fortfarande använder gamla API:er att göra övergången snart att dra nytta av de ytterligare fördelarna med Resource Manager, som inkluderar resursgruppering, taggar, en strömlinjeformad distributions- och hanteringsprocess och finkornig åtkomst kontroll med hjälp av rollbaserad åtkomstkontroll (RBAC).

Mer information om Azure Resource Manager vs Azure Service Manager finns i [TechNet-bloggen](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/).

Mer information om Service Manager- och Resource Manager-API:er för Azure Service Bus, Relay and Event Hubs finns i vår REST API-dokumentation:

- [Azure Service Bus](/rest/api/servicebus/)
- [Azure Event Hubs](/rest/api/eventhub/)
- [Azure Relay](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>REST-API för Service Manager - REST-API FÖR Resurshanteraren

| Api:er för Service Manager (inaktuella) | Resurshanteraren – SERVICE Bus API | Resurshanteraren – API för händelsehubb | Resurshanteraren - Relay API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Namnområden-GetNamespaceAsync** <br/>[Namnområde för servicebuss får namn](/rest/api/servicebus/get-namespace)<br/>[Namnområde hämta händelsehubb](/rest/api/eventhub/get-event-hub)<br/>[Relä Hämta namnområde](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>Service Buss/Event Hub/Relay GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listnycklar](/rest/api/servicebus/namespaces/listkeys) | [listnycklar](/rest/api/eventhub/namespaces/listkeys) | [listnycklar](/rest/api/relay/namespaces/listkeys) |
| **Ämnen-GetTopicsAsync**<br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Köer-GetQueueAsync** <br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **Reläer-GetRelaysAsync**<br/>[Få reläer](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamnområdeAuktoriseringRules-GetNamespaceAuthorizationRuleAsync**<br/>Service buss/händelsehubb/relä GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/namespaces/getauthorizationrule) |[getauthorizationrule](/rest/api/eventhub/namespaces/getauthorizationrule) | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **Namnområden-DeleteNamespaceAsync**<br/>[Namnområde för borttagning av servicebuss](/rest/api/servicebus/delete-namespace)<br/>[Namnområde för händelsehubbar tar bort](/rest/api/eventhub/delete-event-hub)<br/>[Ta bort namnområde för reläer](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [delete](/rest/api/servicebus/namespaces/delete) | [delete](/rest/api/eventhub/namespaces/delete) | [delete](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-GetPlanasync**<br/>Service bus/event hub/relay hämta namnområde<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>Service bus/event hub/relay hämta namnområde<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamnområdeAuktoriseringRules-UpdateNamespaceAuthorizationRuleAsync**<br/>Service bus/event hub/relay hämta namnområde<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamnområdeauktoriseringRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
Servicebuss/Event Hub/Relay<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamnspaceEgenskaper-GetNamespacePropertiesAsync**<br/>[Namnområde för servicebuss får namn](/rest/api/servicebus/get-namespace)<br/>[Event hubbar hämta namnområde](/rest/api/eventhub/get-event-hub)<br/>[Relä Hämta namnområde](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **RegionKoder-Hämta RegionCodesAsync**<br/>Service bus/eventhub/re få namnområde<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku (listbysku)](/rest/api/servicebus/regions/listbysku) | [listbysku (listbysku)](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **NamnspaceEgenskaper-UpdateNamespacePropertyAsync**<br/>Servicebuss/EventHub/Relay<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[Lista händelsehubbar](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Hämta eventhubbar](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **NamnområdeAuktoriseringRules-DeleteNamespaceAuthorizationRuleAsync**<br/>Servicebuss/Event Hub/Relay<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>Servicebuss/EventHub/Relay<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauktoriseringsregler](/rest/api/servicebus/namespaces/listauthorizationrules) | [listauktoriseringsregler](/rest/api/eventhub/namespaces/listauthorizationrules) | [listauktoriseringsregler](/rest/api/relay/namespaces/listauthorizationrules) |
| **Namnspacetillgänglighet-isNamespacetillgängbar**<br/>[Tillgänglighet för servicebussnamnområde](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [kontrollera namntillgänglighet](/rest/api/servicebus/namespaces/checknameavailability) | [kontrollera namntillgänglighet](/rest/api/eventhub/namespaces/checknameavailability) | [kontrollera namntillgänglighet](/rest/api/relay/namespaces/checknameavailability) |
| **Namnområden-CreateOrUpdateNamespaceAsync**<br/>Servicebuss/Event Hub/Relay<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Ämnen-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Service Manager PowerShell - PowerShell för Resurshanteraren
| PowerShell-kommando för Servicehanteraren (inaktuellt) | Kommandon för nya resurshanteraren | Kommandot Nyare resurshanteraren |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Konfigurera Get-AzureRmServiceBusGeoDRKonfigurering](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Get-AzServiceBusGeoDRKonfigurering](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [Hämta-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Ny-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [Nytt-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [Ta bort AzureRmRelayAuthorizationRule](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [Ta bort-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [Ta bort-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>Nästa steg
Se följande dokumentation: 

- Senaste REST API-dokumentation
    - [Azure Service Bus](/rest/api/servicebus/)
    - [Azure Event Hubs](/rest/api/eventhub/)
    - [Azure Relay](/rest/api/relay/)
- Senaste PowerShell-dokumentation
    - [Azure Service Bus](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Azure Event Hubs](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Azure Event Grid](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
