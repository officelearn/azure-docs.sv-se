---
title: Varaktiga fungerar publicering till Azure händelse rutnätet (förhandsgranskning)
description: Lär dig hur du konfigurerar automatisk Azure händelse rutnätet publicering för beständig funktioner.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/20/2018
ms.author: tdykstra
ms.openlocfilehash: 50e517e5719fb102fd91072abe59d3908176278e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762470"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Varaktiga fungerar publicering till Azure händelse rutnätet (förhandsgranskning)

Den här artikeln visar hur du ställer in Azure varaktiga funktioner att publicera orchestration Livscykelhändelser (som skapade, slutförda och misslyckade) till en anpassad [Azure händelse rutnätet avsnittet](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

Följande är några scenarier där den här funktionen är användbar:

* **DevOps-scenarier som blå/grön distributioner**: du kanske vill veta om alla uppgifter som körs innan du implementerar den [sida-vid-sida distributionsstrategi](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-versioning#side-by-side-deployments).

* **Avancerat stöd för övervakning och diagnostik**: du kan hålla reda på orchestration statusinformation i en extern butik som optimerats för frågor, till exempel SQL-databas eller CosmosDB.

* **Långvariga bakgrundsaktivitet**: Om du använder beständiga funktioner för en tidskrävande bakgrundsaktivitet funktionen hjälper dig att du behöver veta för aktuell status.

## <a name="prerequisites"></a>Förutsättningar

* Installera [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc eller senare i projektet varaktiga funktioner.
* Installera [Azure Storage-emulatorn](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator).
* Installera [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) eller använda [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Skapa en anpassad händelse rutnätet avsnittet

Skapa en händelse rutnätet avsnittet för att skicka händelser från varaktiga funktioner. I följande anvisningar visar hur du skapar ett ämne med hjälp av Azure CLI. Information om hur du gör med hjälp av PowerShell eller Azure-portalen finns i följande artiklar:

* [EventGrid Quickstarts: Skapa en anpassad händelse - PowerShell](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid Quickstarts: Skapa en anpassad händelse - Azure-portalen](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med det `az group create` kommando. Händelsen rutnätet stöder för närvarande inte alla regioner. Information om vilka regioner som stöds finns i [händelse rutnätet översikt](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

En händelse rutnätet avsnittet innehåller en användardefinierad slutpunkt som du publicerar en händelse. Ersätt `<topic_name>` med ett unikt namn för ditt ämne. Avsnittsnamnet måste vara unikt eftersom blir den en DNS-post.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup 
```

## <a name="get-the-endpoint-and-key"></a>Hämta slutpunkt och nyckel

Hämta slutpunkten för avsnittet. Ersätt `<topic_name>` med namnet du har valt.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Hämta nyckel för artikeln. Ersätt `<topic_name>` med namnet du har valt.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Nu kan du skicka händelser till ämnet.

## <a name="configure-azure-event-grid-publishing"></a>Konfigurera Azure händelse rutnätet publicering

I projektet varaktiga funktioner att hitta den `host.json` fil.

Lägg till `EventGridTopicEndpoint` och `EventGridKeySettingName` i en `durableTask` egenskap.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

* **EventGridTopicEndpoint** -slutpunkten för avsnittet händelse rutnätet.
* **EventGridKeySettingName** -nyckeln för inställningen på Azure-funktion. Beständiga funktioner får händelse rutnätet avsnittet nyckeln från värdet.

När du har konfigurerat den `host.json` -fil, din varaktiga funktioner projektet börjar skicka Livscykelhändelser till avsnittet händelse rutnätet. Det här fungerar när du kör i appen funktion och när du kör lokalt.

Ange appinställningen för avsnittet nyckeln i appen funktionen och `local.setting.json`. Följande JSON är ett exempel på den `local.settings.json` för lokala felsökning. Ersätt `<topic_key>` med avsnittet nyckel.  

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

Se till att [Lagringsemulatorn](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) fungerar. Det är en bra idé att köra den `AzureStorageEmulator.exe clear all` kommandot innan du kör.

## <a name="create-functions-that-listen-for-events"></a>Skapa funktioner som lyssnar efter händelser

Skapa en Funktionsapp. Det är bäst att placera den i samma region som händelsen rutnätet ämnet.

### <a name="create-an-event-grid-trigger-function"></a>Skapa en händelse rutnätet Utlösarfunktion

Skapa en funktion för att ta emot Livscykelhändelser. Välj **egen funktion**. 

![Välj Skapa en egen funktion.](media/durable-functions-event-publishing/functions-portal.png)

Välj rutnätet händelseutlösare och `C#`.

![Välj rutnätet händelseutlösare.](media/durable-functions-event-publishing/eventgrid-trigger.png)

Ange namnet på funktionen och välj sedan `Create`.

![Skapa en utlösare för rutnätet.](media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

En funktion med följande kod skapas: 

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

Välj `Add Event Grid Subscription`. Den här åtgärden lägger till en händelse rutnätet prenumeration för avsnittet händelse rutnät som du skapade. Mer information finns i [begrepp i rutnätet för Azure-händelse](https://docs.microsoft.com/en-us/azure/event-grid/concepts)

![Välj länken händelseutlösare rutnätet.](media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Välj `Event Grid Topics` för **ämnestyp**. Välj den resursgrupp som du skapade för avsnittet händelse rutnätet. Välj sedan instansen av avsnittet händelse rutnätet. Tryck på `Create`.

![Skapa en Event Grid-prenumeration.](media/durable-functions-event-publishing/eventsubscription.png)

Nu är du redo att ta emot Livscykelhändelser. 

## <a name="create-durable-functions-to-send-the-events"></a>Skapa beständiga funktioner för att skicka händelser.

Starta felsökning på den lokala datorn i projektet varaktiga funktioner.  Följande kod är samma som mall-koden för beständig funktioner. Du har redan konfigurerat `host.json` och `local.settings.json` på den lokala datorn. 

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
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
        public static string SayHello([ActivityTrigger] string name, TraceWriter log)
        {
            log.Info($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            TraceWriter log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.Info($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Om du anropar den `Sample_HttpStart` med Postman eller webbläsaren varaktiga funktionen börjar skicka Livscykelhändelser. Slutpunkten är vanligtvis `http://localhost:7071/api/Sample_HttpStart` för lokala felsökning.

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

I följande lista beskriver livscykel händelser schemat:

* **ID**: Unik identifierare för händelsen händelse rutnätet.
* **ämne**: sökvägen till ämnet för händelsen. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` kommer att `Running`, `Completed`, `Failed`, och `Terminated`.  
* **data**: varaktiga funktioner specifika parametrar.
    * **hubName**: [TaskHub](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-task-hubs) namn.
    * **functionName**: Orchestrator funktionsnamn.
    * **instanceId**: instanceId varaktiga funktioner.
    * **Orsak**: ytterligare data som är associerade med spårning av händelsen. Mer information finns i [diagnostik i varaktiga funktioner (Azure-funktioner)](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-diagnostics)
    * **runtimeStatus**: Orchestration Körningsstatus. Kör, slutfört, misslyckades, avbröts. 
* **eventType**: ”orchestratorEvent”
* **eventTime**: tidpunkt (UTC).
* **dataVersion**: Version av Händelseschema livscykel.
* **metadataVersion**: Version av metadata.
* **avsnittet**: EventGrid avsnittet resurs.

## <a name="how-to-test-locally"></a>Så här testar du lokalt

Använd för att testa lokalt [ngrok](functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs instanshantering i varaktiga funktioner](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Läs versionshantering i varaktiga funktioner](durable-functions-versioning.md)
