---
title: Event Grid utlösare för Azure Functions
description: Förstå hur du hanterar Event Grid händelser i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: 812875be47cabdd23e6307403bb95d8d6ff174ec
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167511"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Event Grid utlösare för Azure Functions

Den här artikeln beskriver hur du hanterar [Event Grid](../event-grid/overview.md) händelser i Azure Functions. Mer information om hur du hanterar Event Grid meddelanden i en HTTP-slutpunkt får [du genom att läsa ta emot händelser till en HTTP-slutpunkt](../event-grid/receive-events.md).

Event Grid är en Azure-tjänst som skickar HTTP-förfrågningar för att meddela dig om händelser som inträffar i *utgivare*. En utgivare är den tjänst eller resurs som har sitt ursprung i händelsen. Till exempel är ett Azure Blob Storage-konto en utgivare och [en BLOB-överföring eller borttagning är en händelse](../storage/blobs/storage-blob-event-overview.md). Vissa [Azure-tjänster har ett inbyggt stöd för att publicera händelser till Event Grid](../event-grid/overview.md#event-sources).

Händelse *hanterare* tar emot och bearbetar händelser. Azure Functions är en av flera [Azure-tjänster som har inbyggt stöd för att hantera Event Grid händelser](../event-grid/overview.md#event-handlers). I den här artikeln får du lära dig hur du använder en Event Grid-utlösare för att anropa en funktion när en händelse tas emot från Event Grid.

Om du vill kan du använda en HTTP-utlösare för att hantera Event Grid händelser. Se [ta emot händelser till en HTTP-slutpunkt](../event-grid/receive-events.md). För närvarande kan du inte använda en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i [CloudEvents-schemat](../event-grid/cloudevents-schema.md#azure-functions). Använd i stället en HTTP-utlösare.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

Event Grid-utlösaren finns i [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-paketet, version 2. x. Käll koden för paketet finns i GitHub-lagringsplatsen [Azure-Functions-eventgrid-Extensions](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Event Grid-utlösaren finns i [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-paketet, version 1. x. Käll koden för paketet finns i GitHub-lagringsplatsen [Azure-Functions-eventgrid-Extensions](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Exempel

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ett exempel på en HTTP-utlösare finns i [ta emot händelser till en HTTP-slutpunkt](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C#(2. x och högre)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som binder till `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Mer information finns i paket, [attribut](#attributes), [konfiguration](#configuration)och [användning](#usage).

### <a name="version-1x"></a>Version 1.x

I följande exempel visas en funktion 1. x [ C# -funktion](functions-dotnet-class-library.md) som binder till `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en trigger-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen.

Här är bindnings data i *Function. JSON* -filen:

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

#### <a name="c-script-version-2x-and-higher"></a>C#skript (version 2. x och högre)

Här är ett exempel som binder till `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Mer information finns i paket, [attribut](#attributes), [konfiguration](#configuration)och [användning](#usage).

### <a name="version-1x"></a>Version 1.x

Här är funktioner 1. x C# -skript kod som binder till `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen.

Här är bindnings data i *Function. JSON* -filen:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

I följande exempel visas en trigger-bindning i en *Function. JSON* -fil och en [python-funktion](functions-reference-python.md) som använder bindningen.

Här är bindnings data i *Function. JSON* -filen:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Här är python-koden:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Det här avsnittet innehåller följande exempel:

* [Event Grid utlösare, sträng parameter](#event-grid-trigger-string-parameter)
* [Event Grid trigger, POJO-parameter](#event-grid-trigger-pojo-parameter)

I följande exempel visas utlösare bindning i [Java](functions-reference-java.md) som använder bindningen och utskrift av en händelse, första mottagande händelsen som `String` och sekund som POJO.

### <a name="event-grid-trigger-string-parameter"></a>Event Grid utlösare, sträng parameter

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Event Grid trigger, POJO-parameter

I det här exemplet används följande POJO som motsvarar de högsta nivå egenskaperna för en Event Grid händelse:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Vid ankomsten avserialiseras händelsens JSON-nyttolast i ```EventSchema```-POJO för användning av funktionen. Detta gör att funktionen kan komma åt händelsens egenskaper på ett objektorienterat sätt.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `EventGridTrigger` kommentar för parametrar vars värde kommer från EventGrid. Parametrar med dessa anteckningar gör att funktionen körs när en händelse tas emot.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>`.

---

## <a name="attributes"></a>Attribut

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Använd attributet [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) i [ C# klass bibliotek](functions-dotnet-class-library.md).

Här är ett `EventGridTrigger`-attribut i en metodsignatur:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i C# exemplet.

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

Med [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) -anteckningen kan du konfigurera en Event Grid bindning genom att ange konfigurations värden. Se avsnittet [exempel](#example) och [konfiguration](#configuration) för mer information.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfigurationen som du anger i filen *Function. JSON* . Det finns inga parametrar eller egenskaper för konstruktorn att ange i `EventGridTrigger`-attributet.

|Function.JSON egenskap |Beskrivning|
|---------|---------|
| **typ** | Required-måste anges till `eventGridTrigger`. |
| **riktning** | Required-måste anges till `in`. |
| **Namn** | Obligatoriskt – variabel namnet som används i funktions koden för den parameter som tar emot händelse data. |

## <a name="usage"></a>Användning

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I Azure Functions 1. x kan du använda följande parameter typer för utlösaren Event Grid:

* `JObject`
* `string`

I Azure Functions 2. x och högre kan du också välja att använda följande parameter typ för Event Grid-utlösaren:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-definierar egenskaper för fälten som är gemensamma för alla händelse typer.

> [!NOTE]
> I functions v1 om du försöker binda till `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`visas ett "föråldrat" meddelande som gör att du kan använda `Microsoft.Azure.EventGrid.Models.EventGridEvent` i stället. Om du vill använda den nyare typen refererar du till [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-paketet och fullständigt kvalificerar `EventGridEvent` typ namnet genom att prefixet med `Microsoft.Azure.EventGrid.Models`.

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I Azure Functions 1. x kan du använda följande parameter typer för utlösaren Event Grid:

* `JObject`
* `string`

I Azure Functions 2. x och högre kan du också välja att använda följande parameter typ för Event Grid-utlösaren:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-definierar egenskaper för fälten som är gemensamma för alla händelse typer.

> [!NOTE]
> I functions v1 om du försöker binda till `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`visas ett "föråldrat" meddelande som gör att du kan använda `Microsoft.Azure.EventGrid.Models.EventGridEvent` i stället. Om du vill använda den nyare typen refererar du till [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-paketet och fullständigt kvalificerar `EventGridEvent` typ namnet genom att prefixet med `Microsoft.Azure.EventGrid.Models`. Information om hur du refererar till NuGet-paket i C# en-skript funktion finns i [använda NuGet-paket](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Event Grid-instansen är tillgänglig via den parameter som kon figurer ATS i egenskapen `name` i *Function. JSON* -filen.

# <a name="pythontabpython"></a>[Python](#tab/python)

Event Grid-instansen är tillgänglig via den parameter som kon figurer ATS i egenskapen *. JSON* -filens `name`, som `func.EventGridEvent`.

# <a name="javatabjava"></a>[Java](#tab/java)

Händelse instansen Event Grid är tillgänglig via den parameter som är kopplad till attributet `EventGridTrigger`, skriven som en `EventSchema`. Se [exemplet](#example) för mer information.

---

## <a name="event-schema"></a>Händelseschema

Data för en Event Grid-händelse tas emot som ett JSON-objekt i bröd texten i en HTTP-begäran. JSON ser ut ungefär som i följande exempel:

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

Exemplet som visas är en matris med ett-element. Event Grid skickar alltid en matris och kan skicka fler än en händelse i matrisen. Körningen anropar funktionen en gång för varje mat ris element.

Egenskaperna på den högsta nivån i Event JSON-data är desamma bland alla händelse typer, medan innehållet i egenskapen `data` är särskilt för varje händelse typ. Exemplet som visas är en Blob Storage-händelse.

Förklaringar av vanliga och event-/regionsspecifika egenskaper finns i [händelse egenskaper](../event-grid/event-schema.md#event-properties) i Event Grid-dokumentationen.

`EventGridEvent` typen definierar bara egenskaperna på den översta nivån. Egenskapen `Data` är en `JObject`.

## <a name="create-a-subscription"></a>Skapa en prenumeration

Om du vill börja ta emot Event Grid HTTP-begäranden skapar du en Event Grid-prenumeration som anger slut punkts-URL: en som anropar funktionen.

### <a name="azure-portal"></a>Azure-portalen

För funktioner som du utvecklar i Azure Portal med Event Grid utlösare väljer du **Lägg till Event Grid prenumeration**.

![Skapa prenumeration i portalen](media/functions-bindings-event-grid/portal-sub-create.png)

När du väljer den här länken öppnar portalen sidan **Skapa händelse prenumeration** med slut PUNKTens URL förifylld.

![Förifyllt slut punkts-URL](media/functions-bindings-event-grid/endpoint-url.png)

Mer information om hur du skapar prenumerationer med hjälp av Azure Portal finns i avsnittet [Skapa anpassad händelse-Azure Portal](../event-grid/custom-event-quickstart-portal.md) i Event Grid-dokumentationen.

### <a name="azure-cli"></a>Azure CLI

Om du vill skapa en prenumeration med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)använder du kommandot [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) .

Kommandot kräver slut punkts-URL: en som anropar funktionen. I följande exempel visas det användarspecifika URL-mönstret:

#### <a name="version-2x-and-higher-runtime"></a>Version 2. x (och högre) runtime

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Version 1. x-körning

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

System nyckeln är en auktoriseringskod som måste inkluderas i slut punkts-URL: en för en Event Grid-utlösare. I följande avsnitt beskrivs hur du hämtar system nyckeln.

Här är ett exempel som prenumererar på ett Blob Storage-konto (med en plats hållare för system nyckeln):

#### <a name="version-2x-and-higher-runtime"></a>Version 2. x (och högre) runtime

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Version 1. x-körning

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Mer information om hur du skapar en prenumeration finns i [snabb starten för Blob Storage](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) eller andra event Grid snabb starter.

### <a name="get-the-system-key"></a>Hämta system nyckeln

Du kan hämta system nyckeln med hjälp av följande API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Version 2. x (och högre) runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Version 1. x-körning

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Det här är ett administrations-API, så den kräver [huvud nyckeln](functions-bindings-http-webhook.md#authorization-keys)för Function-appen. Blanda inte ihop system nyckeln (för att anropa en Event Grid trigger-funktion) med huvud nyckeln (för att utföra administrativa uppgifter i Function-appen). När du prenumererar på ett Event Grid-ämne ska du se till att använda system nyckeln.

Här är ett exempel på svaret som innehåller system nyckeln:

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

Du kan hämta huvud nyckeln för din Function-app från fliken **funktions program inställningar** i portalen.

> [!IMPORTANT]
> Huvud nyckeln ger administratörs åtkomst till din Function-app. Dela inte den här nyckeln med tredje part eller distribuera den i interna klient program.

Mer information finns i [Authorization Keys](functions-bindings-http-webhook.md#authorization-keys) i referens artikeln för http-utlösare.

Du kan också skicka ett HTTP-värde för att ange nyckel värdet själv.

## <a name="local-testing-with-viewer-web-app"></a>Lokal testning med Viewer Web App

Om du vill testa en Event Grid-utlösare lokalt måste du få Event Grid HTTP-begäranden som levereras från deras ursprung i molnet till din lokala dator. Ett sätt att göra detta är genom att fånga förfrågningar online och skicka dem manuellt på den lokala datorn:

1. [Skapa en visnings webb program](#create-a-viewer-web-app) som fångar händelse meddelanden.
1. [Skapa en Event Grid-prenumeration](#create-an-event-grid-subscription) som skickar händelser till Viewer-appen.
1. [Generera en begäran](#generate-a-request) och kopiera begär ande texten från visnings programmet.
1. [Publicera begäran](#manually-post-the-request) till localhost-URL: en manuellt för din event Grid trigger-funktion.

När du är klar med testningen kan du använda samma prenumeration för produktion genom att uppdatera slut punkten. Använd kommandot [AZ eventgrid Event-Subscription Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI.

### <a name="create-a-viewer-web-app"></a>Skapa ett visnings program för webb program

För att förenkla insamling av händelse meddelanden kan du distribuera en [förbyggd webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar händelse meddelandena. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

Välj **Deploy to Azure** (Distribuera till Azure) för att distribuera lösningen till din prenumeration. Ange parametervärdena i Azure Portal.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Du ser webbplatsen men det har inte publicerats händelser till den än.

![Visa ny webbplats](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration

Skapa en Event Grid-prenumeration av den typ som du vill testa och ge den URL: en från din webbapp som slut punkt för händelse aviseringen. Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`. Den fullständiga URL: en är alltså `https://<your-site-name>.azurewebsites.net/api/updates`

Information om hur du skapar prenumerationer med hjälp av Azure Portal finns i [Skapa anpassad händelse-Azure Portal](../event-grid/custom-event-quickstart-portal.md) i Event Grid-dokumentationen.

### <a name="generate-a-request"></a>Generera en begäran

Utlöser en händelse som kommer att generera HTTP-trafik till din Web App-slutpunkt.  Om du till exempel har skapat en Blob Storage-prenumeration laddar du upp eller tar bort en blob. När en begäran visas i din webbapp kopierar du begär ande texten.

Begäran om prenumerations verifiering tas emot först. ignorera eventuella verifierings begär Anden och kopiera händelse förfrågningen.

![Kopiera begär ande brödtext från webbapp](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Publicera begäran manuellt

Kör din Event Grid funktion lokalt.

Använd ett verktyg som [Postman](https://www.getpostman.com/) eller [sväng](https://curl.haxx.se/docs/httpscripting.html) för att skapa en http post-begäran:

* Ange ett `Content-Type: application/json` huvud.
* Ange ett `aeg-event-type: Notification`s huvud.
* Klistra in RequestBin-data i begär ande texten.
* Publicera till URL: en för din Event Grid trigger-funktion.
  * För 2. x och högre använder du följande mönster:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * För 1. x-användning:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Parametern `functionName` måste vara det namn som anges i attributet `FunctionName`.

Följande skärm bilder visar rubrikerna och begär ande texten i Postman:

![Rubriker i Postman](media/functions-bindings-event-grid/postman2.png)

![Begär ande text i Postman](media/functions-bindings-event-grid/postman.png)

Funktionen Event Grid trigger kör och visar loggar som liknar följande exempel:

![Exempel Event Grid utlösa funktions loggar](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Läs mer om Event Grid](../event-grid/overview.md)
