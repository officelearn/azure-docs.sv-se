---
title: Functions-Durable publicering till Azure Event Grid (förhandsversion)
description: Lär dig hur du konfigurerar automatisk Azure Event Grid-publicering för varaktiga funktioner.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: c07a42349fbd81a46b1b7cd9bcad1978f891a6b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136369"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Functions-Durable publicering till Azure Event Grid (förhandsversion)

Den här artikeln visar hur du ställer in varaktiga funktioner för att publicera orchestration Livscykelhändelser (till exempel skapade, slutförda och misslyckade) till en anpassad [Azure Event Grid-ämne](https://docs.microsoft.com/azure/event-grid/overview).

Nedan följer några scenarier där den här funktionen är användbar:

* **DevOps-scenarier som blå/grön distributioner**: Du kanske vill veta om alla uppgifter körs innan du implementerar den [sida-vid-sida-distributionsstrategi](durable-functions-versioning.md#side-by-side-deployments).

* **Avancerad support för övervakning och diagnostik**: Du kan hålla reda på orchestration statusinformation i en extern lagring som är optimerat för frågor, till exempel SQL-databas eller CosmosDB.

* **Långvariga bakgrundsaktiviteten**: Om du använder varaktiga funktioner för en tidskrävande bakgrundsaktiviteten kan du enkelt se aktuell status i den här funktionen.

## <a name="prerequisites"></a>Förutsättningar

* Installera [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc eller senare i projektet varaktiga funktioner.
* Installera [Azure Storage-emulatorn](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Installera [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) eller Använd [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Skapa ett anpassat event grid-ämne

Skapa en event grid-ämne för att skicka händelser från varaktiga funktioner. Följande instruktioner visar hur du skapar ett ämne med Azure CLI. Information om hur du gör det med hjälp av PowerShell eller Azure-portalen finns i följande artiklar:

* [EventGrid Snabbstarter: Skapa anpassad händelse – PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid Snabbstarter: Skapa anpassad händelse – Azure-portalen](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot `az group create`. Azure Event Grid stöder för närvarande inte alla regioner. Information om vilka regioner som stöds finns i den [översikt över Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett event grid-ämne tillhandahåller en användardefinierad slutpunkt där du publicerar din händelse till. Ersätt `<topic_name>` med ett unikt namn för ditt ämne. Ämnesnamnet måste vara unikt eftersom det blir en DNS-post.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Hämta slutpunkten och nyckeln

Hämta slutpunkten av avsnittet. Ersätt `<topic_name>` med det namn du valde.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Hämta nyckel för ämnet. Ersätt `<topic_name>` med det namn du valde.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Nu kan du skicka händelser till ämnet.

## <a name="configure-azure-event-grid-publishing"></a>Konfigurera Azure Event Grid-publicering

I projektet varaktiga funktioner hitta den `host.json` fil.

Lägg till `eventGridTopicEndpoint` och `eventGridKeySettingName` i en `durableTask` egenskapen.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Möjliga konfigurationsegenskaper för Azure Event Grid kan hittas i den [host.json dokumentation](../functions-host-json.md#durabletask). När du har konfigurerat den `host.json` filen funktionsappen skickar Livscykelhändelser till event grid-ämne. Det här fungerar när du kör din funktionsapp, både lokalt och i Azure ”.”

Ange app-inställning för avsnittet nyckeln i Funktionsappen och `local.setting.json`. Följande JSON är ett exempel på den `local.settings.json` för lokal felsökning. Ersätt `<topic_key>` med avsnittet nyckel.  

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

Se till att [Lagringsemulatorn](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) fungerar. Det är en bra idé att köra den `AzureStorageEmulator.exe clear all` kommandot innan du kör.

## <a name="create-functions-that-listen-for-events"></a>Skapa funktioner som lyssna efter händelser

Skapa en Funktionsapp. Det är bäst att hitta den i samma region som event grid-ämne.

### <a name="create-an-event-grid-trigger-function"></a>Skapa en Utlösarfunktion för event grid

Skapa en funktion för att ta emot Livscykelhändelser. Välj **anpassad funktion**.

![Välj Skapa en anpassad funktion.](./media/durable-functions-event-publishing/functions-portal.png)

Välj Event Grid-utlösare och `C#`.

![Välj Event Grid-utlösare.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Ange namnet på funktionen och välj sedan `Create`.

![Skapa Event Grid-utlösare.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

En funktion med följande kod skapas:

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

Välj `Add Event Grid Subscription`. Den här åtgärden lägger till en event grid-prenumeration för event grid-ämne som du skapade. Mer information finns i [begrepp i Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts)

![Välj länken Event Grid-utlösare.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Välj `Event Grid Topics` för **ämnestyp**. Välj den resursgrupp som du skapade för event grid-ämne. Välj sedan instansen av event grid-ämne. Tryck på `Create`.

![Skapa en Event Grid-prenumeration.](./media/durable-functions-event-publishing/eventsubscription.png)

Nu är du redo att ta emot Livscykelhändelser.

## <a name="create-durable-functions-to-send-the-events"></a>Skapa varaktiga funktioner för att skicka händelser

Starta felsökning på den lokala datorn i projektet varaktiga funktioner.  Följande kod är samma som mallkod för varaktiga funktioner. Du har redan konfigurerat `host.json` och `local.settings.json` på den lokala datorn.

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
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
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

Om du anropar den `Sample_HttpStart` med Postman eller webbläsaren varaktiga funktion börjar skicka Livscykelhändelser. Slutpunkten är vanligtvis `http://localhost:7071/api/Sample_HttpStart` för lokal felsökning.

Se loggar från den funktion som du skapade i Azure-portalen.

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

I följande lista beskriver händelseschemat livscykel:

* **`id`**: Unik identifierare för event grid-händelse.
* **`subject`**: Sökväg till ämne för händelsen. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` kommer att `Running`, `Completed`, `Failed`, och `Terminated`.  
* **`data`**: Varaktiga funktioner specifika parametrar.
  * **`hubName`**: [TaskHub](durable-functions-task-hubs.md) namn.
  * **`functionName`**: Orchestrator-funktionsnamn.
  * **`instanceId`**: Varaktiga funktioner instanceId.
  * **`reason`**: Ytterligare data som är associerade med spårningshändelsen. Mer information finns i [diagnostik i varaktiga funktioner (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Körningsstatus för orkestrering. Körs, slutfört, misslyckades, har avbrutits.
* **`eventType`**: ”orchestratorEvent”
* **`eventTime`**: Händelsetid (UTC).
* **`dataVersion`**: Version av händelseschemat livscykel.
* **`metadataVersion`**:  Version av metadata.
* **`topic`**: Event grid avsnittet resursen.

## <a name="how-to-test-locally"></a>Hur du testar lokalt

Använd för att testa lokalt [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig instanshantering i varaktiga funktioner](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Lär dig versionshantering i varaktiga funktioner](durable-functions-versioning.md)
