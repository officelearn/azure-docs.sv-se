---
title: "Händelseutlösare rutnät för Azure Functions"
description: "Förstå hur du hanterar händelsen rutnätet händelser i Azure Functions."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: 2a6fe85c2c3d6d4f44dc197db6c28ebbc2b1d431
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="event-grid-trigger-for-azure-functions"></a>Händelseutlösare rutnät för Azure Functions

Den här artikeln beskrivs hur du hanterar [händelse rutnätet](../event-grid/overview.md) händelser i Azure Functions.

Händelsen rutnätet är en Azure-tjänst som skickar HTTP-begäranden att meddela dig om händelser som sker i *utgivare*. En utgivare är tjänsten eller resursen kommer händelsen. Till exempel ett Azure blob storage-konto är en utgivare och en blob överför eller tas bort är en händelse. Vissa [Azure-tjänster har inbyggt stöd för att publicera händelser på händelsen rutnätet](../event-grid/overview.md#event-publishers). 

Händelsen *hanterare* ta emot och bearbeta händelser. Azure Functions är ett av flera [Azure-tjänster som har inbyggt stöd för att hantera händelsen rutnätet händelser](../event-grid/overview.md#event-handlers). Lär dig hur du använder en händelse rutnätet utlösare för att anropa en funktion när en händelse tas emot från Event rutnät i den här artikeln.

Om du vill kan kan du använda en HTTP-utlösare för att hantera händelsen rutnätet händelser; Se [använda en HTTP-utlösare som en händelse rutnätet utlösare](#use-an-http-trigger-as-an-event-grid-trigger) senare i den här artikeln.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Exempel

Se språkspecifika exemplet för en händelse rutnätet utlösare:

* [C#](#c-example)
* [C#-skript (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

Ett HTTP-utlösaren exempel finns [hur du använder HTTP-utlösaren](#use-an-http-trigger-as-an-event-grid-trigger) senare i den här artikeln.

### <a name="c-example"></a>C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som loggar vissa fält som är gemensamma för alla händelser och alla händelsespecifika data.

```cs
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```

Den `EventGridTrigger` attribut har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid).

### <a name="c-script-example"></a>Exempel på C#-skript

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen loggar vissa fält som är gemensamma för alla händelser och alla händelsespecifika data.

Här är de bindande data den *function.json* fil:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Här är skriptkod C#:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.Azure.WebJobs.Extensions.EventGrid"
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```

### <a name="javascript-example"></a>JavaScript-exempel

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen loggar vissa fält som är gemensamma för alla händelser och alla händelsespecifika data.

Här är de bindande data den *function.json* fil:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Här är JavaScript-kod:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>Attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) attribut som definierats i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid).

Här är ett `EventGridTrigger` attribut i en signatur:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] EventGridEvent eventGridEvent, TraceWriter log)
{
    ...
}
 ```

En komplett exempel finns [C#-exempel](#c-example).

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil. Inga parametrar som konstruktorn eller ange den `EventGridTrigger` attribut.

|Egenskapen Function.JSON |Beskrivning|
|---------|---------|----------------------|
| **Typ** | Krävs – måste vara inställd på `eventGridTrigger`. |
| **Riktning** | Krävs – måste vara inställd på `in`. |
| **Namn** | Obligatoriskt - variabelnamnet som används i Funktionskoden för den parameter som tar emot informationen om händelsen. |

## <a name="usage"></a>Användning

För C# och F #, ange utlösaren indata ska vara `EventGridEvent` eller en anpassad typ. För en anpassad typ försöker Functions-runtime parsa händelsen JSON att ange objektets egenskaper.

För JavaScript-funktioner med namnet parametern med det *function.json* `name` egenskapen har en referens till event-objektet.

## <a name="event-schema"></a>Händelseschema

Data för en händelse med händelse rutnät som tas emot som en JSON-objekt i brödtexten för en HTTP-begäran. JSON ser ut ungefär så här:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

I exemplet är en matris av ett element. Händelsen rutnätet alltid skickar en matris och kan skicka fler än en händelse i matrisen. Körningen anropar din funktion en gång för varje matriselement i.

Egenskaperna på den översta nivån i händelsen JSON-data är samma mellan alla händelsetyper när innehållet i den `data` egenskapen är specifika för varje händelsetyp av. I exemplet är en blob storage-händelse.

Beskrivningar av och delade händelsespecifika egenskaper finns i [händelseegenskaper](../event-grid/event-schema.md#event-properties) i händelsen rutnätet-dokumentationen.

Den `EventGridEvent` typen definierar endast de översta egenskaperna; `Data` -egenskapen är en `JObject`. 

## <a name="create-a-subscription"></a>Skapa en prenumeration

Om du vill börja ta emot händelsen rutnätet HTTP-begäranden, skapa en händelse rutnätet-prenumeration som anger slutpunkts-URL som anropar funktionen.

### <a name="azure-portal"></a>Azure Portal

Funktioner som du utvecklar i Azure-portalen med händelsen rutnätet utlösaren, Välj **Lägg till händelse rutnätet prenumeration**.

![Skapa prenumeration i portalen](media/functions-bindings-event-grid/portal-sub-create.png)

När du väljer den här länken öppnar portalen den **skapa händelseprenumerationen** fylls automatiskt i med slutpunkts-URL.

![Slutpunkts-URL som fylls i automatiskt](media/functions-bindings-event-grid/endpoint-url.png)

Mer information om hur du skapar prenumerationer med hjälp av Azure portal finns [skapa anpassade händelsen - Azure-portalen](../event-grid/custom-event-quickstart-portal.md) i händelsen rutnätet-dokumentationen.

### <a name="azure-cli"></a>Azure CLI

Skapa en prenumeration med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), använda den [az eventgrid händelseprenumerationen skapa](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) kommando.

Kommandot kräver slutpunkts-URL som anropar funktionen. I följande exempel visar URL-mönster:

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

Systemnyckeln är auktoriseringsnyckel som ska ingå i slutpunkts-URL för en händelse rutnätet utlösare. I följande avsnitt förklaras hur du hämtar systemnyckeln.

Här är ett exempel som prenumererar på ett blob storage-konto (med en platshållare för systemnyckeln):

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

Mer information om hur du skapar en prenumeration finns [blob storage quickstart](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) eller andra händelsen rutnätet-Snabbstart.

### <a name="get-the-system-key"></a>Hämta systemnyckel för

Du kan få systemnyckeln med hjälp av följande API (HTTP GET):

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

Det här är en administrations-API, så det krävs en [administrationsnyckeln](functions-bindings-http-webhook.md#authorization-keys). Blanda inte ihop systemnyckeln (för att anropa en funktion för händelsen rutnätet utlösare) med admin-nyckel (för att utföra administrativa uppgifter i funktionen appen). När du prenumererar på en händelse rutnätet avsnittet måste du använda systemnyckeln.

Här är ett exempel på svaret som innehåller systemnyckeln:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Mer information finns i [auktorisering nycklar](functions-bindings-http-webhook.md#authorization-keys) i HTTP-utlösaren referensartikeln. 

Du kan också skicka en HTTP PUT för att ange värdet för nyckeln själv.

## <a name="local-testing-with-requestbin"></a>Lokal testning med RequestBin

Om du vill testa en händelse rutnätet utlösare lokalt, måste du hämta händelsen rutnätet HTTP-begäranden som levereras från sina ursprung i molnet till den lokala datorn. Ett sätt att göra det är genom att samla in begäranden online och manuellt skicka dem på den lokala datorn:

2. [Skapa en slutpunkt för RequestBin](#create-a-RequestBin-endpoint).
3. [Skapa en händelse rutnätet prenumeration](#create-an-event-grid-subscription) som skickar händelser till RequestBin slutpunkten.
4. [Skapa en begäran](#generate-a-request) och kopiera begärandetexten från webbplatsen RequestBin.
5. [Manuellt efter begäran](#manually-post-the-request) till localhost-URL: en händelse rutnätets utlösa funktionen.

När du är klar testning, du kan använda samma prenumeration för produktion genom att uppdatera slutpunkten. Använd den [az eventgrid händelseprenumerationen uppdatering](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) Azure CLI-kommando.

### <a name="create-a-requestbin-endpoint"></a>Skapa en RequestBin slutpunkt

RequestBin är ett verktyg med öppen källkod som accepterar HTTP-begäranden och visar begärandetexten. URL: en http://requestb.in hämtar särskild behandling av Azure händelse rutnätet. För att underlätta testningen skickar händelse rutnätet händelser för RequestBin URL: en utan att kräva en rätt svar på begäranden för verifiering av prenumerationen. Två andra testningsverktyg behandlas på samma: http://webhookinbox.com och http://hookbin.com.

RequestBin är inte avsedd för användning med hög genomströmning. Om du push-överför fler än en händelse i taget kanske du inte ser alla händelser i verktyget.

Skapa en slutpunkt.

![Skapa RequestBin slutpunkt](media/functions-bindings-event-grid/create-requestbin.png)

Kopiera slutpunkts-URL.

![Kopiera RequestBin slutpunkt](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Skapa en händelse rutnätet-prenumeration

Skapa en händelse rutnätet prenumeration av den typ som du vill testa och ge den RequestBin slutpunkten. Information om hur du skapar en prenumeration finns [skapa en prenumeration](#create-a-subscription) tidigare i den här artikeln.

### <a name="generate-a-request"></a>Skapa en begäran

Aktivera en händelse som ska generera HTTP-trafik till RequestBin-slutpunkten.  Om du har skapat en blob storage-prenumeration, ladda upp eller ta bort en blob. När en begäran visas på sidan RequestBin, kopiera begärandetexten.

Prenumerationen validering begäran tas emot först. Ignorera alla begäranden om verifiering och kopiera händelse-begäran.

![Kopiera begärandetexten från RequestBin](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>Manuellt efter begäran

Köra funktionen händelse rutnätet lokalt.

Använd ett verktyg som [Postman](https://www.getpostman.com/) eller [curl](https://curl.haxx.se/docs/httpscripting.html) att skapa en HTTP POST-begäran:

* Ange en `Content-Type: application/json` huvud.
* Ange en `aeg-event-type: Notification` huvud.
* Klistra in den RequestBin i begärandetexten. 
* Efter att URL-Adressen för händelsen rutnätet utlösaren-funktionen med följande mönster:

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={methodname}
``` 

Den `functionName` parametern måste vara namnet som angetts i den `FunctionName` attribut.

Följande skärmbilderna visa radrubriker och begäran i Postman:

![Huvuden i Postman](media/functions-bindings-event-grid/postman2.png)

![Begärandetexten i Postman](media/functions-bindings-event-grid/postman.png)

Funktionen händelse rutnätet utlösaren kör och visar loggar liknar följande exempel:

![Exempel händelse rutnätet utlösaren funktionsloggar](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Lokal testning med ngrok

Ett annat sätt att testa en händelse rutnätet utlösare lokalt är att automatisera den HTTP-anslutningen mellan Internet och utvecklingsdatorn. Du kan göra det med ett verktyg med öppen källkod med namnet [ngrok](https://ngrok.com/):

3. [Skapa en slutpunkt för ngrok](#create-an-ngrok-endpoint).
4. [Kör funktionen händelse rutnätet utlösaren](#run-the-event-grid-trigger-function).
5. [Skapa en händelse rutnätet prenumeration](#create-a-subscription) som skickar händelser till ngrok slutpunkten.
6. [Aktivera en händelse](#trigger-an-event).

När du är klar testning, du kan använda samma prenumeration för produktion genom att uppdatera slutpunkten. Använd den [az eventgrid händelseprenumerationen uppdatering](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) Azure CLI-kommando.

### <a name="create-an-ngrok-endpoint"></a>Skapa en ngrok slutpunkt

Hämta *ngrok.exe* från [ngrok](https://ngrok.com/), och kör med följande kommando:

```
ngrok http -host-header=localhost 7071
```

-Värd-huvudet parametern behövs eftersom functions-runtime förväntar begäranden från localhost när den körs på localhost. 7071 är standardportnumret när körningen körs lokalt.

Kommandot skapar utdata som liknar följande:

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Du ska använda https://{subdomain}.ngrok.io URL: en för prenumerationen händelse rutnätet.

### <a name="run-the-event-grid-trigger-function"></a>Kör funktionen händelse rutnätet utlösare

Ngrok URL: en få inte särskild hantering av händelse rutnätet så att din funktion måste köras lokalt när prenumerationen har skapats. Om den inte skickas inte verifieringen svaret och prenumeration misslyckas.

### <a name="create-a-subscription"></a>Skapa en prenumeration

Skapa en händelse rutnätet prenumeration av den typ som du vill testa och ge den ngrok slutpunkten, med hjälp av följande mönster:

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={methodname}
``` 

Den `functionName` parametern måste vara namnet som angetts i den `FunctionName` attribut.

Här är ett exempel som använder Azure CLI:

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Information om hur du skapar en prenumeration finns [skapa en prenumeration](#create-a-subscription) tidigare i den här artikeln.

### <a name="trigger-an-event"></a>Utlösa en händelse

Aktivera en händelse som ska generera HTTP-trafik till ngrok-slutpunkten.  Om du har skapat en blob storage-prenumeration, ladda upp eller ta bort en blob.

Funktionen händelse rutnätet utlösaren kör och visar loggar liknar följande exempel:

![Exempel händelse rutnätet utlösaren funktionsloggar](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Använda en HTTP-utlösare som en händelse rutnätet utlösare

Händelsen tas rutnätet emot som HTTP-begäranden, så du kan hantera händelser med hjälp av en HTTP-utlösare i stället för en händelse rutnätet utlösare. En möjlig orsak till att göra som ska få mer kontroll över slutpunkts-URL som anropar funktionen. 

Om du använder en HTTP-utlösare som du behöver skriva kod för vad händelse rutnätet-utlösare gör automatiskt:

* Skickar en verifiering svar till en [begäran om prenumerationen](../event-grid/security-authentication.md#webhook-event-delivery).
* Anropar funktionen en gång per element i matrisen händelse i begärandetexten.

Följande C# exempelkod för en HTTP-utlösare simulerar händelse rutnät utlösare:

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Följande exempel JavaScript-kod för en HTTP-utlösare simulerar händelse rutnät utlösare:

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: messages[0].data.validationCode }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

Händelsehantering koden går i slinga genom den `messages` matris.

Information om URL som ska användas för att anropa funktionen lokalt eller när den körs i Azure finns i [referensdokumentationen för HTTP-utlösaren bindning](functions-bindings-http-webhook.md) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Mer information om händelsen rutnätet](../event-grid/overview.md)
