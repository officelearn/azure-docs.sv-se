---
title: Hur du hanterar stora mängder information i Azure Event Grid och publicering av händelser till dem med hjälp av händelse domäner
description: Lär dig skapa och hantera avsnitt i Azure Event Grid och publicering av händelser till dem med hjälp av händelse domäner.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 48a5356b03e38e864ba76f048febdb0b040893f5
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634311"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Hantera ämnen och publicera händelser med händelse-domäner

Den här artikeln visar hur du:

* Skapa en Event Grid-domän
* Prenumerera på ämnen
* Lista över nycklar
* Publicering av händelser till en domän

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Skapa en händelsedomän

Skapa en händelsedomän kan ske via den `eventgrid` -tillägg för [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). När du har skapat en domän kan använda du det för att hantera stora mängder information.

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

Har skapats returnerar följande:

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

Obs den `endpoint` och `id` som de kommer att behöva hantera domänen och publicera händelser.

## <a name="create-topics-and-subscriptions"></a>Skapa ämnen och prenumerationer

Event Grid-tjänsten automatiskt skapar och hanterar avsnittet i en domän som baserat på samtalet för att skapa en händelseprenumeration för ett ämne i domänen. Det finns ingen separat steg att skapa ett ämne i en domän. På samma sätt när den senaste händelseprenumerationen för ett ämne tas bort är ämnet också bort.

Prenumerera på ett ämne i en domän är samma som prenumererar på andra Azure-resurs:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

Resurs-ID som angetts är samma ID som returneras när du skapar domänen tidigare. Avsnittet om du vill prenumerera på lägger du till `/topics/<my-topic>` i slutet av resurs-ID.

Om du vill skapa en händelseprenumeration för domän-omfång som tar emot alla händelser i domänen, ge domänen som den `resource-id` utan att ange något ämne som till exempel `/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>`.

Om du behöver en test-slutpunkt för att prenumerera på dina händelser kan du alltid distribuera en [färdiga webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar inkommande händelser. Du kan skicka händelser till din testwebbplats på `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Behörigheter som anges för ett ämne som lagras i Azure Active Directory och måste uttryckligen tas bort. Tar bort en händelseprenumeration återkalla inte en användares åtkomst för att skapa prenumerationer på händelser om de har skrivbehörighet på ett ämne.

## <a name="manage-access-to-topics"></a>Hantera åtkomst till avsnitt

Hantera åtkomst till avsnitt sker via [rolltilldelning](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-cli). Rolltilldelning använder roll baserad åtkomstkontroll för att begränsa åtgärder på Azure-resurser till behöriga användare för ett visst omfång.

Event Grid har två inbyggda roller som du kan använda för att tilldela specifika användare åtkomst om olika ämnen inom en domän. Dessa roller är `EventGrid EventSubscription Contributor (Preview)`, vilket möjliggör skapandet och borttagningen av prenumerationer och `EventGrid EventSubscription Reader (Preview)`, vilket gör att endast lista över prenumerationer på händelser.

Följande kommando skulle begränsa `alice@contoso.com` att skapa och ta bort prenumerationer på händelser endast på avsnittet `foo`:

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

Se [Event Grid säkerhet och autentisering](./security-authentication.md) mer information om:

* Hantering av åtkomstkontroll
* Åtgärdstyper
* Skapa anpassade rolldefinitioner

## <a name="publish-events-to-an-event-grid-domain"></a>Publicering av händelser till en Event Grid-domän

Publicering av händelser till en domän är samma som [publicering till ett anpassat ämne](./post-to-custom-topic.md). Den enda skillnaden är att du behöver ange avsnittet om du vill att varje händelse att gå till. Följande matris med händelser leder händelse med `"id": "1111"` till ämnet `foo` vid händelse med `"id": "2222"` skulle skickas till ämnet `bar`:

```json
[{
  "topic": "foo",
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
  "topic": "bar",
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

Hämta nycklarna för en domän:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Och Använd din favoritmetod för att göra en HTTP POST du publicerar dina händelser till Event Grid-domänen.

## <a name="next-steps"></a>Nästa steg

* Mer information om avancerade begrepp på Event domäner och varför de är användbara finns i den [konceptuell översikt över Event domäner](./event-domains.md).