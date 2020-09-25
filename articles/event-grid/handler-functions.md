---
title: Azure Function som händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda Azure Functions som händelse hanterare för Event Grid händelser.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 87aeb78729dcc7bec9f193fab389e5c0952e63d5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270342"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Azure Function som händelse hanterare för Event Grid händelser

En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar en åtgärd för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser och **Azure Functions** är en av dem. 

Använd **Azure Functions** i en server lös arkitektur för att svara på händelser från Event Grid. När du använder en Azure-funktion som hanterare använder du Event Grid-utlösaren i stället för den allmänna HTTP-utlösaren. Event Grid validerar automatiskt Event Grid-utlösare. Med allmänna HTTP-utlösare måste du implementera [verifierings svaret](webhook-event-delivery.md) själv.

Mer information finns i [Event Grid utlösare för Azure Functions](../azure-functions/functions-bindings-event-grid.md) för en översikt över hur du använder Event Grid-utlösaren i functions.

## <a name="tutorials"></a>Självstudier

|Rubrik  |Beskrivning  |
|---------|---------|
| [Snabb start: hantera händelser med funktion](custom-event-to-function.md) | Skickar en anpassad händelse till en funktion för bearbetning. |
| [Självstudie: automatisera storleks ändring av överförda bilder med hjälp av Event Grid](resize-images-on-storage-blob-upload-event.md) | Användare laddar upp bilder via webbapp till lagrings kontot. När en lagrings-BLOB skapas skickar Event Grid en händelse till Function-appen, som ändrar storlek på den överförda avbildningen. |
| [Självstudie: strömma Big data till ett informations lager](event-grid-event-hubs-integration.md) | När Event Hubs skapar en Infångnings fil, Event Grid skickar en händelse till en Function-app. Appen hämtar insamlings filen och migrerar data till ett data lager. |
| [Självstudie: Azure Service Bus till Azure Event Grid integrations exempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus ämne till en Function-app och en Logic-app. |

## <a name="rest-example-for-put"></a>REST-exempel (för placering)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Aktivera batchbearbetning
För ett högre genomflöde aktiverar du batching av prenumerationen. Om du använder Azure Portal kan du ange högsta antal händelser per batch och önskad batchstorlek i kilobyte byte vid tidpunkten för att skapa en prenumeration eller när den har skapats. 

Du kan konfigurera batch-inställningar med hjälp av Azure Portal, PowerShell, CLI eller Resource Manager-mallen. 

### <a name="azure-portal"></a>Azure Portal
När du skapar en prenumeration i användar gränssnittet går du till sidan **Skapa händelse prenumeration** , växlar till fliken **avancerade funktioner** och anger värden för **Max antal händelser per batch** och **önskad batchstorlek i kilobyte**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Aktivera batchbearbetning vid tidpunkten för att skapa en prenumeration":::

Du kan uppdatera de här värdena för en befintlig prenumeration på fliken **funktioner** på ämnes sidan för **Event Grid** . 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Aktivera batchbearbetning efter skapande":::

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Du kan ange **maxEventsPerBatch** och **preferredBatchSizeInKilobytes** i en Azure Resource Manager-mall. Mer information finns i [referens för Microsoft. EventGrid eventSubscriptions Template](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>Azure CLI
Du kan använda [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) eller [AZ eventgrid Event-Subscription Update-](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) kommando för att konfigurera batch-relaterade inställningar med följande parametrar: `--max-events-per-batch` eller `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
Du kan använda cmdleten [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) eller [Update-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/update-azeventgridsubscription) för att konfigurera batch-relaterade inställningar med följande parametrar: `-MaxEventsPerBatch` eller `-PreferredBatchSizeInKiloBytes` .

> [!NOTE]
> Det finns inte stöd för att leverera händelser till en Azure-funktion i **en annan klient** . 

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) . 
