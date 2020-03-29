---
title: Publicera händelser med händelsedomäner med Azure Event Grid
description: Visar hur du hanterar stora uppsättningar ämnen i Azure Event Grid och publicerar händelser till dem med hjälp av händelsedomäner.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 1d07227249806b7d54523af66817a170c19354ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72786545"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Hantera ämnen och publicera händelser med hjälp av händelsedomäner

Den här artikeln visar hur du:

* Skapa en domän för händelserutnät
* Prenumerera på avsnitt i händelserutnätet
* Lista nycklar
* Publicera händelser till en domän

Mer information om händelsedomäner finns i [Förstå händelsedomäner för hantering av avsnitt i Event Grid](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Installera förhandsversionsfunktionen

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Skapa en händelsedomän

Skapa en händelsedomän om du vill hantera stora uppsättningar ämnen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

Framgångsrikt skapande returnerar följande värden:

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

Observera `endpoint` och `id` som de krävs för att hantera domänen och publicera händelser.

## <a name="manage-access-to-topics"></a>Hantera åtkomst till ämnen

Hantering av åtkomst till ämnen sker via [rolltilldelning](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Rolltilldelning använder rollbaserad åtkomstkontroll för att begränsa åtgärder på Azure-resurser till behöriga användare i ett visst scope.

Event Grid har två inbyggda roller som du kan använda för att tilldela vissa användare åtkomst i olika ämnen inom en domän. Dessa roller `EventGrid EventSubscription Contributor (Preview)`är , vilket gör det möjligt `EventGrid EventSubscription Reader (Preview)`att skapa och ta bort prenumerationer och , vilket endast gör det möjligt att lista händelseprenumerationer.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
Följande Azure CLI-kommando begränsar `alice@contoso.com` till att skapa `demotopic1`och ta bort händelseprenumerationer endast i ämnet:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
Följande PowerShell-kommando `alice@contoso.com` begränsar till att skapa och `demotopic1`ta bort händelseprenumerationer endast i ämnet:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Mer information om hur du hanterar åtkomst för Event Grid-åtgärder finns i [Säkerhet och autentisering av Händelserutnät](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Skapa ämnen och prenumerationer

Event Grid-tjänsten skapar och hanterar automatiskt motsvarande ämne i en domän baserat på anropet för att skapa en händelseprenumeration för ett domänämne. Det finns inget separat steg för att skapa ett ämne i en domän. På samma sätt tas även ämnet bort när den senaste händelseprenumerationen för ett ämne tas bort.

Att prenumerera på ett ämne i en domän är samma sak som att prenumerera på någon annan Azure-resurs. För källresurs-ID anger du händelsedomän-ID som returnerades när domänen skapas tidigare. Om du vill ange vilket ämne `/topics/<my-topic>` du vill prenumerera på lägger du till i slutet av källresurs-ID: t. Om du vill skapa en domänomfattningshändelseprenumeration som tar emot alla händelser i domänen anger du händelsedomän-ID utan att ange några ämnen.

Vanligtvis skapar användaren som du beviljade åtkomst till i föregående avsnitt prenumerationen. För att förenkla den här artikeln skapar du prenumerationen. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Om du behöver en testslutpunkt för att prenumerera på dina händelser kan du alltid distribuera en [färdig webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar inkommande händelser. Du kan skicka dina evenemang `https://<your-site-name>.azurewebsites.net/api/updates`till din testwebbplats på .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Behörigheter som anges för ett ämne lagras i Azure Active Directory och måste tas bort explicit. Om du tar bort en händelseprenumeration återkallas inte en användares åtkomst för att skapa händelseprenumerationer om de har skrivbehörighet i ett ämne.


## <a name="publish-events-to-an-event-grid-domain"></a>Publicera händelser i en domän för händelserutnät

Att publicera händelser till en domän är detsamma som [att publicera till ett anpassat ämne](./post-to-custom-topic.md). I stället för att publicera till det anpassade ämnet publicerar du dock alla händelser till domänslutpunkten. I JSON-händelsedata anger du det ämne som du vill att händelserna ska gå till. Följande matris med händelser skulle `"id": "1111"` resultera `demotopic1` i händelse `"id": "2222"` med att `demotopic2`ämnet medan händelse med skulle skickas till ämnet:

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
Om du vill hämta domänslutpunkten med Azure CLI använder du

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Om du vill hämta nycklarna till en domän använder du:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)
Om du vill hämta domänslutpunkten med PowerShell använder du

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Om du vill hämta nycklarna till en domän använder du:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

Och använd sedan din favoritmetod för att göra ett HTTP-INLÄGG för att publicera dina händelser i domänen Event Grid.

## <a name="next-steps"></a>Nästa steg

* Mer information om begrepp på hög nivå i händelsedomäner och varför de är användbara finns i den [konceptuella översikten över händelsedomäner](event-domains.md).
