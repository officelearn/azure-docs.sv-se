---
title: Hållbar funktionspublicering till Azure Event Grid (förhandsversion)
description: Lär dig hur du konfigurerar automatisk Azure Event Grid-publicering för varaktiga funktioner.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 52ffcd4eb81936ffcfa61580288c60bd59ffb744
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249766"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Hållbar funktionspublicering till Azure Event Grid (förhandsversion)

Den här artikeln visar hur du konfigurerar varaktiga funktioner för att publicera livscykelhändelser för orkestrering (till exempel skapade, slutförda och misslyckade) till ett anpassat [Azure Event Grid-ämne](https://docs.microsoft.com/azure/event-grid/overview).

Här följer några scenarier där den här funktionen är användbar:

* **DevOps-scenarier som blå/grön distributioner**: Du kanske vill veta om några aktiviteter körs innan du implementerar [distributionsstrategin sida vid sida](durable-functions-versioning.md#side-by-side-deployments).

* **Stöd för avancerad övervakning och diagnostik**: Du kan hålla reda på statusinformation för orkestrering i ett externt arkiv som är optimerat för frågor, till exempel Azure SQL Database eller Azure Cosmos DB.

* **Långvarig bakgrundsaktivitet**: Om du använder varaktiga funktioner för en långvarig bakgrundsaktivitet hjälper den här funktionen dig att känna till aktuell status.

## <a name="prerequisites"></a>Krav

* Installera [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) i projektet Durable Functions.
* Installera [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) (endast Windows) eller använd ett befintligt Azure Storage-konto.
* Installera [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) eller använd Azure Cloud [Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Skapa ett anpassat händelserutnätsämne

Skapa ett händelserutnätsämne för att skicka händelser från varaktiga funktioner. Följande instruktioner visar hur du skapar ett ämne med hjälp av Azure CLI. Du kan också göra detta [genom att använda PowerShell](../../event-grid/custom-event-quickstart-powershell.md) eller [genom att använda Azure-portalen](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot `az group create`. Azure Event Grid stöder för närvarande inte alla regioner. Information om vilka regioner som stöds finns i [översikten över Azure Event Grid](../../event-grid/overview.md).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett händelserutnätsavsnitt innehåller en användardefinierad slutpunkt som du publicerar händelsen på. Ersätt `<topic_name>` med ett unikt namn för ditt ämne. Ämnesnamnet måste vara unikt eftersom det blir en DNS-post.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Hämta slutpunkten och nyckeln

Hämta slutpunkten för ämnet. Ersätt `<topic_name>` med det namn du valde.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Hämta ämnesnyckeln. Ersätt `<topic_name>` med det namn du valde.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Nu kan du skicka händelser till ämnet.

## <a name="configure-event-grid-publishing"></a>Konfigurera publicering av händelserutnät

Leta reda på filen `host.json` i projektet Varaktiga funktioner.

Lägg `eventGridTopicEndpoint` `eventGridKeySettingName` till `durableTask` och i en egenskap.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

De möjliga konfigurationsegenskaperna för Azure Event Grid finns i [host.json-dokumentationen](../functions-host-json.md#durabletask). När du `host.json` har konfigurerat filen skickar funktionsappen livscykelhändelser till avsnittet Event Grid. Detta fungerar när du kör din funktionsapp både lokalt och i Azure.

Ange appinställningen för ämnesnyckeln `local.settings.json`i funktionsappen och . Följande JSON är ett `local.settings.json` exempel på för lokal felsökning. Ersätt `<topic_key>` med ämnesnyckeln.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Om du bara använder [lagringsemulatorn](../../storage/common/storage-use-emulator.md) (endast Windows) kontrollerar du att den fungerar. Det är en bra idé `AzureStorageEmulator.exe clear all` att köra kommandot innan du kör.

Om du använder ett befintligt `UseDevelopmentStorage=true` Azure `local.settings.json` Storage-konto ersätter du med anslutningssträngen.

## <a name="create-functions-that-listen-for-events"></a>Skapa funktioner som lyssnar efter händelser

Med Hjälp av Azure-portalen skapar du en annan funktionsapp för att lyssna efter händelser som publiceras av din varaktiga funktioner-app. Det är bäst att hitta den i samma region som avsnittet Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Skapa en utlösarfunktion för händelserutnät

Skapa en funktion för att ta emot livscykelhändelserna. Välj **anpassad funktion**.

![Välj en Skapa en anpassad funktion.](./media/durable-functions-event-publishing/functions-portal.png)

Välj Utlösare av händelserutnät och välj ett språk.

![Välj utlösaren för händelserutnät.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Ange namnet på funktionen och `Create`välj sedan .

![Skapa utlösaren för händelserutnätet.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

En funktion med följande kod skapas:

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

Välj `Add Event Grid Subscription`. Den här åtgärden lägger till en Event Grid-prenumeration för det eventrutnätsämne som du skapade. Mer information finns [i Begrepp i Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts)

![Välj länken Utlösare av händelserutnät.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Välj `Event Grid Topics` för **ämnestyp**. Välj den resursgrupp som du skapade för avsnittet Event Grid. Välj sedan förekomsten av avsnittet Händelserutnät. Tryck `Create`på .

![Skapa en Event Grid-prenumeration.](./media/durable-functions-event-publishing/eventsubscription.png)

Nu är du redo att ta emot livscykelhändelser.

## <a name="run-durable-functions-app-to-send-the-events"></a>Kör appen Varaktiga funktioner för att skicka händelserna

I projektet Varaktiga funktioner som du konfigurerade tidigare börjar du felsöka på den lokala datorn och starta en orkestrering. Appen publicerar livscykelhändelser för varaktiga funktioner i Event Grid. Kontrollera att Event Grid utlöser den avlyssnarfunktion som du skapade genom att kontrollera dess loggar i Azure-portalen.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Händelseschema

I följande lista beskrivs schemat för livscykelhändelser:

* **`id`**: Unik identifierare för händelsen Event Grid.
* **`subject`**: Sökväg till händelseämnet. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}`kommer `Running`att `Completed` `Failed`vara, `Terminated`, och .  
* **`data`**: Varaktiga funktioner specifika parametrar.
  * **`hubName`**: [TaskHub-namn.](durable-functions-task-hubs.md)
  * **`functionName`**: Orchestrator funktionsnamn.
  * **`instanceId`**: Exempel på varaktiga funktioner.
  * **`reason`**: Ytterligare data som är associerade med spårningshändelsen. Mer information finns [i Diagnostik i varaktiga funktioner (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Status för Orchestration Runtime. Köra, slutfört, misslyckades, avbrutits.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Händelsetid (UTC).
* **`dataVersion`**: Version av livscykelhändelseschemat.
* **`metadataVersion`**: Version av metadata.
* **`topic`**: Ämnesresurs för händelserutnät.

## <a name="how-to-test-locally"></a>Så här testar du lokalt

Om du vill testa lokalt läser du [Azure Function Event Grid Trigger Local Debugging](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig instanshantering i varaktiga funktioner](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Lär dig att göra en version i varaktiga funktioner](durable-functions-versioning.md)
