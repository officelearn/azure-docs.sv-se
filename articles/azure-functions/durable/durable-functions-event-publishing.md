---
title: Durable Functions publicering till Azure Event Grid (förhands granskning)
description: Lär dig hur du konfigurerar automatisk Azure Event Grid publicering för Durable Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: f3fd59c0d17bd9094f6887aa5ec088f9fdcdd979
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734442"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publicering till Azure Event Grid (förhands granskning)

Den här artikeln visar hur du konfigurerar Durable Functions att publicera livs cykel händelser för dirigering (till exempel skapade, slutförda och misslyckade) till ett anpassat [Azure Event Grid-ämne](https://docs.microsoft.com/azure/event-grid/overview).

Här följer några scenarier där den här funktionen är användbar:

* **DevOps-scenarier som blå/gröna distributioner**: Du kanske vill veta om några aktiviteter körs innan du implementerar [distributions strategin sida vid sida](durable-functions-versioning.md#side-by-side-deployments).

* **Avancerad övervakning och diagnostisk support**: Du kan hålla koll på dirigerings status information i ett externt arkiv som är optimerat för frågor, till exempel SQL Database eller CosmosDB.

* **Tids krävande bakgrunds aktivitet**: Om du använder Durable Functions för en tids krävande bakgrunds aktivitet hjälper den här funktionen dig att känna till aktuell status.

## <a name="prerequisites"></a>Förutsättningar

* Installera [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-RC eller senare i Durable Functions-projektet.
* Installera [Azure Storage emulator](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Installera [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) eller Använd [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Skapa ett anpassat Event Grid-ämne

Skapa ett event Grid-ämne för att skicka händelser från Durable Functions. Följande instruktioner visar hur du skapar ett ämne med hjälp av Azure CLI. Information om hur du gör det med hjälp av PowerShell eller Azure Portal finns i följande artiklar:

* [EventGrid snabb starter: Skapa anpassad händelse – PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid snabb starter: Skapa anpassad händelse – Azure Portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot `az group create`. Azure Event Grid stöder för närvarande inte alla regioner. Information om vilka regioner som stöds finns i [Översikt över Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett event Grid-ämne innehåller en användardefinierad slut punkt som du kan använda för att publicera din händelse. Ersätt `<topic_name>` med ett unikt namn för ditt ämne. Ämnes namnet måste vara unikt eftersom det blir en DNS-post.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Hämta slut punkten och nyckeln

Hämta slut punkten för ämnet. Ersätt `<topic_name>` med det namn som du har valt.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Hämta ämnes nyckeln. Ersätt `<topic_name>` med det namn som du har valt.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Nu kan du skicka händelser till ämnet.

## <a name="configure-azure-event-grid-publishing"></a>Konfigurera Azure Event Grid publicering

Leta upp `host.json` filen i Durable Functions-projektet.

Lägg `eventGridTopicEndpoint` till `eventGridKeySettingName` och i `durableTask` en egenskap.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

De möjliga Azure Event Grid konfigurations egenskaperna finns i [Host. JSON-dokumentationen](../functions-host-json.md#durabletask). När du har konfigurerat `host.json` filen skickar din Function-app livs cykel händelser till avsnittet Event Grid. Detta fungerar när du kör din funktions app både lokalt och i Azure.

Ange appens inställning för ämnes nyckeln i Funktionsapp och `local.setting.json`. Följande JSON är ett exempel på `local.settings.json` för lokal fel sökning. Ersätt `<topic_key>` med ämnes nyckeln.  

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

Kontrol lera att [Storage-emulatorn](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) fungerar. Det är en bra idé att köra kommandot `AzureStorageEmulator.exe clear all` innan du kör.

## <a name="create-functions-that-listen-for-events"></a>Skapa funktioner som lyssnar efter händelser

Skapa en Funktionsapp. Det är bäst att hitta det i samma region som avsnittet Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Skapa en Event Grid-utlösnings funktion

Skapa en funktion för att ta emot livs cykel händelser. Välj **anpassad funktion**.

![Välj Skapa en anpassad funktion.](./media/durable-functions-event-publishing/functions-portal.png)

Välj Event Grid utlösare och `C#`Välj.

![Välj den Event Grid utlösaren.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Ange namnet på funktionen och välj `Create`sedan.

![Skapa Event Grid-utlösaren.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

En funktion med följande kod skapas:

#### <a name="precompiled-c"></a>FörkompileradeC#
```csharp
public static void Run([HttpTrigger] JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script"></a>C#Över

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

Välj `Add Event Grid Subscription`. Den här åtgärden lägger till en Event Grid-prenumeration för det event Grid-ämne som du skapade. Mer information finns [i begrepp i Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts)

![Välj länken Event Grid utlösare.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Välj `Event Grid Topics` för **ämnes typ**. Välj den resurs grupp som du skapade för Event Grid-ämnet. Välj sedan instansen för Event Grid-ämnet. Tryck `Create`på.

![Skapa en Event Grid-prenumeration.](./media/durable-functions-event-publishing/eventsubscription.png)

Nu är du redo att ta emot livs cykel händelser.

## <a name="create-durable-functions-to-send-the-events"></a>Skapa Durable Functions för att skicka händelser

Starta fel sökning på den lokala datorn i Durable Functions-projektet.  Följande kod är samma som mall koden för Durable Functions. Du har redan `host.json` konfigurerat `local.settings.json` och på den lokala datorn.

### <a name="precompiled-c"></a>FörkompileradeC#

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
            [OrchestrationClient] DurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Om du anropar `Sample_HttpStart` med Postman eller din webbläsare börjar den varaktiga funktionen Skicka livs cykel händelser. Slut punkten är vanligt `http://localhost:7071/api/Sample_HttpStart` vis för lokal fel sökning.

Se loggarna från funktionen som du skapade i Azure Portal.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
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
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
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
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Händelseschema

I följande lista beskrivs schemat för livs cykel händelser:

* **`id`** : Unik identifierare för Event Grid-händelsen.
* **`subject`** : Sökväg till händelsens ämne. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}``Running`är ,`Completed`, och`Terminated`. `Failed`  
* **`data`** : Durable Functions vissa parametrar.
  * **`hubName`** : [TaskHub](durable-functions-task-hubs.md) namn.
  * **`functionName`** : Orchestrator-funktions namn.
  * **`instanceId`** : Durable Functions instanceId.
  * **`reason`** : Ytterligare data som är associerade med spårnings händelsen. Mer information finns i [diagnostik i Durable functions (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : Dirigerings körnings status. Körs, slutförd, misslyckades, avbröts.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Tid för händelsen (UTC).
* **`dataVersion`** : Version av livs cykelns händelse schema.
* **`metadataVersion`** :  Version av metadata.
* **`topic`** : Ämnes resurs för Event Grid.

## <a name="how-to-test-locally"></a>Testa lokalt

Använd [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok)om du vill testa lokalt.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om instans hantering i Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Lär dig mer om versions hantering i Durable Functions](durable-functions-versioning.md)