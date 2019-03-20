---
title: Hantera stora mängder ämnen i Azure Event Grid med händelsen domäner
description: Visar hur du hanterar stora mängder information i Azure Event Grid och publicering av händelser till dem med hjälp av händelse domäner.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: c49044d8bd96efb7e86cf54509c32033900be305
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180916"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Hantera ämnen och publicera händelser med händelse-domäner

Den här artikeln visar hur du:

* Skapa en Event Grid-domän
* Prenumerera på event grid-ämnen
* Lista över nycklar
* Publicering av händelser till en domän

Läs om event domäner i [förstå händelse domäner för att hantera Event Grid-ämnen](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Installera förhandsversionsfunktionen

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Skapa en händelsedomän

Skapa en händelsedomän för att hantera stora mängder information.

Om du använder Azure CLI använder du:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```

Har skapats returnerar följande värden:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Obs den `endpoint` och `id` som de är nödvändiga för att hantera domänen och publicera händelser.

## <a name="manage-access-to-topics"></a>Hantera åtkomst till avsnitt

Hantera åtkomst till avsnitt sker via [rolltilldelning](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Rolltilldelning använder rollbaserad åtkomstkontroll för att begränsa åtgärder på Azure-resurser till behöriga användare för ett visst omfång.

Event Grid har två inbyggda roller som du kan använda för att tilldela specifika användare åtkomst om olika ämnen inom en domän. Dessa roller är `EventGrid EventSubscription Contributor (Preview)`, vilket möjliggör skapandet och borttagningen av prenumerationer och `EventGrid EventSubscription Reader (Preview)`, vilket gör att endast lista över prenumerationer på händelser.

Följande begränsningar för Azure CLI-kommandot `alice@contoso.com` att skapa och ta bort prenumerationer på händelser endast på avsnittet `demotopic1`:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Följande PowerShell-kommando begränsningar `alice@contoso.com` att skapa och ta bort prenumerationer på händelser endast på avsnittet `demotopic1`:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Läs mer om hur du hanterar åtkomst för Event Grid-åtgärder, [Event Grid säkerhet och autentisering](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Skapa ämnen och prenumerationer

Event Grid-tjänsten automatiskt skapar och hanterar avsnittet i en domän som baserat på samtalet för att skapa en händelseprenumeration för ett ämne i domänen. Det finns ingen separat steg att skapa ett ämne i en domän. På samma sätt när den senaste händelseprenumerationen för ett ämne tas bort är ämnet också bort.

Prenumerera på ett ämne i en domän är samma som prenumererar på andra Azure-resurs. Ange domän efter händelse-ID returneras när du skapar domänen tidigare för resurs-ID källa. Avsnittet om du vill prenumerera på lägger du till `/topics/<my-topic>` i slutet av källans resurs-ID. Ange domän efter händelse-ID för att skapa en händelseprenumeration för domän-omfång som tar emot alla händelser i domänen, utan att ange något ämne.

Vanligtvis användaren du beviljas åtkomst till i föregående avsnitt skapar prenumerationen. För att förenkla den här artikeln kan skapa du prenumerationen. 

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

Om du behöver en test-slutpunkt för att prenumerera på dina händelser kan du alltid distribuera en [färdiga webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar inkommande händelser. Du kan skicka händelser till din testwebbplats på `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Behörigheter som anges för ett ämne som lagras i Azure Active Directory och måste uttryckligen tas bort. Tar bort en händelseprenumeration återkalla inte en användares åtkomst för att skapa prenumerationer på händelser om de har skrivbehörighet på ett ämne.


## <a name="publish-events-to-an-event-grid-domain"></a>Publicering av händelser till en Event Grid-domän

Publicering av händelser till en domän är samma som [publicering till ett anpassat ämne](./post-to-custom-topic.md). Men i stället för publicering till det anpassade ämnet publicera du alla händelser till slutpunkten för domänen. I JSON-händelsedata anger du avsnittet om du vill att händelserna för att gå till. Följande matris med händelser leder händelse med `"id": "1111"` till ämnet `demotopic1` vid händelse med `"id": "2222"` skulle skickas till ämnet `demotopic2`:

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Hämta slutpunkten domän med Azure CLI

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Om du vill hämta nycklar för en domän, använder du:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Hämta domän-slutpunkten med PowerShell

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Om du vill hämta nycklar för en domän, använder du:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Och Använd din favoritmetod för att göra en HTTP POST du publicerar dina händelser till Event Grid-domänen.

## <a name="next-steps"></a>Nästa steg

* Mer information om avancerade begrepp på Event domäner och varför de är användbara finns i den [konceptuell översikt över Event domäner](event-domains.md).
