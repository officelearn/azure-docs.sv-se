---
title: Functions-Durable publicering till Azure Event Grid (förhandsversion)
description: Lär dig hur du konfigurerar automatisk Azure Event Grid-publicering för varaktiga funktioner.
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
ms.openlocfilehash: 0179a48b74ef0e37d3ac2e7fd18d43e488a89823
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341390"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Functions-Durable publicering till Azure Event Grid (förhandsversion)

Den här artikeln visar hur du ställer in Azure varaktiga funktioner för att publicera orchestration Livscykelhändelser (till exempel skapade, slutförda och misslyckade) till en anpassad [Azure Event Grid-ämne](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

Nedan följer några scenarier där den här funktionen är användbar:

* **DevOps-scenarier som blå/grön distributioner**: du kanske vill veta om alla uppgifter körs innan du implementerar den [sida-vid-sida-distributionsstrategi](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-versioning#side-by-side-deployments).

* **Avancerad support för övervakning och diagnostik**: du kan hålla reda på orchestration statusinformation i en extern lagring som är optimerat för frågor, till exempel SQL-databas eller CosmosDB.

* **Långvariga bakgrundsaktiviteten**: Om du använder varaktiga funktioner för en tidskrävande bakgrundsaktiviteten kan den här funktionen kan du enkelt se aktuell status.

## <a name="prerequisites"></a>Förutsättningar

* Installera [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc eller senare i projektet varaktiga funktioner.
* Installera [Azure Storage-emulatorn](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator).
* Installera [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) eller Använd [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Skapa ett anpassat Event Grid-ämne

Skapa ett Event Grid-ämne för att skicka händelser från varaktiga funktioner. Följande instruktioner visar hur du skapar ett ämne med Azure CLI. Information om hur du gör det med hjälp av PowerShell eller Azure-portalen finns i följande artiklar:

* [EventGrid Quickstarts: Skapa anpassad händelse – PowerShell](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid Quickstarts: Skapa anpassad händelse – Azure-portalen](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med det `az group create` kommando. Event Grid stöder för närvarande inte alla regioner. Information om vilka regioner som stöds finns i den [översikt över Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Skapa en anpassat ämne

Ett Event Grid-ämne tillhandahåller en användardefinierad slutpunkt där du publicerar din händelse till. Ersätt `<topic_name>` med ett unikt namn för ditt ämne. Ämnesnamnet måste vara unikt eftersom det blir en DNS-post.

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

Lägg till `EventGridTopicEndpoint` och `EventGridKeySettingName` i en `durableTask` egenskapen.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

Möjliga konfigurationsegenskaper för Azure Event Grid är följande:

* **EventGridTopicEndpoint** -slutpunkten för Event Grid-ämne. Den *% AppSettingName %* syntax kan användas för att lösa det här värdet från programinställningar eller miljövariabler.
* **EventGridKeySettingName** -nyckeln för inställningen på din Azure-funktion. Varaktiga funktioner ska hämta nyckel för Event Grid-ämne från värdet.
* **EventGridPublishRetryCount** – [valfritt] hur många gånger att försöka igen om publicering till Event Grid-ämne misslyckas.
* **EventGridPublishRetryInterval** -[valfritt] The Event Grid publicera återförsöksintervallet i den *: mm: ss* format. Om inte anges är standardvärdet intervallet 5 minuter.

När du har konfigurerat den `host.json` fil, Your varaktiga funktioner projekt börjar skicka Livscykelhändelser till Event Grid-ämne. Detta fungerar när du kör i Funktionsappen och när du kör lokalt.

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

Se till att [Lagringsemulatorn](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) fungerar. Det är en bra idé att köra den `AzureStorageEmulator.exe clear all` kommandot innan du kör.

## <a name="create-functions-that-listen-for-events"></a>Skapa funktioner som lyssna efter händelser

Skapa en Funktionsapp. Det är bäst att hitta den i samma region som Event Grid-ämne.

### <a name="create-an-event-grid-trigger-function"></a>Skapa en funktion för Event Grid-utlösare

Skapa en funktion för att ta emot Livscykelhändelser. Välj **anpassad funktion**. 

![Välj Skapa en anpassad funktion.](media/durable-functions-event-publishing/functions-portal.png)

Välj Event Grid-utlösare och `C#`.

![Välj Event Grid-utlösare.](media/durable-functions-event-publishing/eventgrid-trigger.png)

Ange namnet på funktionen och välj sedan `Create`.

![Skapa Event Grid-utlösare.](media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

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

Välj `Add Event Grid Subscription`. Den här åtgärden lägger till en Event Grid-prenumeration för Event Grid-ämne som du skapade. Mer information finns i [begrepp i Azure Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/concepts)

![Välj länken Event Grid-utlösare.](media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Välj `Event Grid Topics` för **ämnestyp**. Välj den resursgrupp som du skapade för Event Grid-ämne. Välj sedan instansen av Event Grid-ämne. Tryck på `Create`.

![Skapa en Event Grid-prenumeration.](media/durable-functions-event-publishing/eventsubscription.png)

Nu är du redo att ta emot Livscykelhändelser. 

## <a name="create-durable-functions-to-send-the-events"></a>Skapa varaktiga funktioner för att skicka händelser.

Starta felsökning på den lokala datorn i projektet varaktiga funktioner.  Följande kod är samma som mallkod för varaktiga funktioner. Du har redan konfigurerat `host.json` och `local.settings.json` på den lokala datorn. 

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

* **ID**: Unik identifierare för Event Grid-händelse.
* **ämne**: sökvägen till ämne för händelsen. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` kommer att `Running`, `Completed`, `Failed`, och `Terminated`.  
* **data**: varaktiga funktioner specifika parametrar.
    * **hubName**: [TaskHub](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-task-hubs) namn.
    * **functionName**: funktionsnamn för Orchestrator.
    * **instanceId**: instanceId varaktiga funktioner.
    * **Orsak**: ytterligare data som är associerade med spårningshändelsen. Mer information finns i [diagnostik i varaktiga funktioner (Azure Functions)](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-diagnostics)
    * **runtimeStatus**: Körningsstatus för orkestrering. Körs, slutfört, misslyckades, har avbrutits. 
* **Händelsetyp**: ”orchestratorEvent”
* **eventTime**: händelsetid (UTC).
* **dataVersion**: Version av händelseschemat livscykel.
* **metadataVersion**: Version av metadata.
* **avsnittet**: EventGrid avsnittet resurs.

## <a name="how-to-test-locally"></a>Hur du testar lokalt

Använd för att testa lokalt [ngrok](functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig instanshantering i varaktiga funktioner](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Lär dig versionshantering i varaktiga funktioner](durable-functions-versioning.md)
