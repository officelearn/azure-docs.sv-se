---
title: Hämta åtkomst nyckel för en Event Grid resurs
description: Den här artikeln beskriver hur du får åtkomst nyckeln för ett Event Grid ämne eller en domän
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 06/25/2020
ms.author: spelluru
ms.openlocfilehash: 75ab09de40a4305ebd215985f2f9f181bfd69a24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85414894"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Få åtkomst nycklar för Event Grid resurser (ämnen eller domäner)
Åtkomst nycklar används för att autentisera ett program publicerings händelser för att Azure Event Grid resurser (ämnen och domäner). Vi rekommenderar att du återskapar dina nycklar regelbundet och sparar dem på ett säkert sätt. Du får två åtkomst nycklar så att du kan underhålla anslutningar med en nyckel när du återskapar den.

Den här artikeln beskriver hur du får åtkomst nycklar för en Event Grid resurs (ämne eller domän) med hjälp av Azure Portal, PowerShell eller CLI. 

## <a name="azure-portal"></a>Azure Portal
I Azure Portal växlar du till fliken **åtkomst nycklar** i **avsnittet Event Grid** eller **Event Grid domän** för ämnet eller domänen.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Sidan åtkomst nycklar":::

## <a name="azure-powershell"></a>Azure PowerShell
Använd kommandot [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey?view=azps-4.3.0) för att få åtkomst nycklar för ämnen. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Använd [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey?view=azps-4.3.0) -kommandot för att få åtkomst nycklar för domäner. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
Använd [AZ eventgrid ämnes nyckel lista](/cli/azure/eventgrid/topic/key?view=azure-cli-latest#az-eventgrid-topic-key-list) för att få åtkomst nycklar för ämnen. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Använd [AZ eventgrid-domän nyckel lista](/cli/azure/eventgrid/domain/key?view=azure-cli-latest#az-eventgrid-domain-key-list) för att få åtkomst nycklar för domäner. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Nästa steg
Se följande artikel: [autentisera publicerings klienter](security-authenticate-publishing-clients.md). 
