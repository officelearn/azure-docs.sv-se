---
title: Azure Event Grid-utlösare för Azure-funktioner
description: Lär dig att köra kod när Event Grid-händelser i Azure Functions skickas.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 2027629e1e9e297c97cbf40485ebe7dc2e3e6c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277731"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Event Grid-utlösare för Azure-funktioner

Använd funktionsutlösaren för att svara på en händelse som skickas till ett eventrutnätsämne.

Information om inställnings- och konfigurationsinformation finns i [översikten](./functions-bindings-event-grid.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

Ett HTTP-utlösarexempel finns [i Ta emot händelser till en HTTP-slutpunkt](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C# (2.x och högre)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som binder till: `EventGridEvent`

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

Mer information finns i Paket, [Attribut](#attributes-and-annotations), [Konfiguration](#configuration)och [Användning](#usage).

### <a name="version-1x"></a>Version 1.x

I följande exempel visas en funktion för Funktioner 1.x [C#](functions-dotnet-class-library.md) som binder till: `JObject`

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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en utlösarbindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen.

Här är bindningsdata i *filen function.json:*

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

### <a name="version-2x-and-higher"></a>Version 2.x och högre

Här är ett exempel som `EventGridEvent`binder till:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Mer information finns i Paket, [Attribut](#attributes-and-annotations), [Konfiguration](#configuration)och [Användning](#usage).

### <a name="version-1x"></a>Version 1.x

Här är Funktioner 1.x C # skriptkod som binder till: `JObject`

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en utlösarbindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen.

Här är bindningsdata i *filen function.json:*

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

Här är JavaScript-koden:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en utlösarbindning i en *function.json-fil* och en [Python-funktion](functions-reference-python.md) som använder bindningen.

Här är bindningsdata i *filen function.json:*

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

Här är Python-koden:

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

# <a name="java"></a>[Java](#tab/java)

Det här avsnittet innehåller följande exempel:

* [Utlösare av händelserutnät, strängparameter](#event-grid-trigger-string-parameter)
* [Utlösare av händelserutnät, POJO-parameter](#event-grid-trigger-pojo-parameter)

Följande exempel visar utlösarbindning i [Java](functions-reference-java.md) som använder bindningen `String` och skriver ut en händelse, först tar emot händelsen och andra som en POJO.

### <a name="event-grid-trigger-string-parameter"></a>Utlösare av händelserutnät, strängparameter

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

### <a name="event-grid-trigger-pojo-parameter"></a>Utlösare av händelserutnät, POJO-parameter

I det här exemplet används följande POJO som representerar egenskaperna på den översta nivån för en händelserutnätshändelse:

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

Vid ankomsten är evenemangets JSON nyttolast av ```EventSchema``` serialiseras till POJO för användning av funktionen. Med den här processen kan funktionen komma åt händelsens egenskaper på ett objektorienterat sätt.

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

I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `EventGridTrigger` på parametrar vars värde skulle komma från EventGrid. Parametrar med dessa anteckningar gör att funktionen körs när en händelse anländer.  Den här anteckningen kan användas med inbyggda Java-typer, `Optional<T>`POJOs eller nullable värden med .

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [EventGridTrigger.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

Här är `EventGridTrigger` ett attribut i en metodsignatur:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i C#-exempel.

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

[Med EventGridTrigger-anteckningen](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) kan du deklarativt konfigurera en Event Grid-bindning genom att ange konfigurationsvärden. Mer information finns i [exempel-](#example) och konfigurationsavsnitten. [configuration](#configuration)

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som du anger i *filen function.json.* Det finns inga konstruktorparametrar `EventGridTrigger` eller egenskaper som ska anges i attributet.

|egenskapen function.json |Beskrivning|
|---------|---------|
| **Typ** | Obligatoriskt - måste `eventGridTrigger`ställas in på . |
| **riktning** | Obligatoriskt - måste `in`ställas in på . |
| **Namn** | Obligatoriskt - variabelnamnet som används i funktionskoden för parametern som tar emot händelsedata. |

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

I Azure Functions 1.x kan du använda följande parametertyper för utlösaren för händelserutnätet:

* `JObject`
* `string`

I Azure Functions 2.x och högre har du också möjlighet att använda följande parametertyp för utlösaren för händelserutnätet:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Definierar egenskaper för de fält som är gemensamma för alla händelsetyper.

> [!NOTE]
> I Functions v1 om du `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`försöker binda till visas ett "inaktuellt" meddelande `Microsoft.Azure.EventGrid.Models.EventGridEvent` och råda dig att använda i stället. Om du vill använda den nyare typen refererar du till [Paketet Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet och kvalificerar `EventGridEvent` typnamnet helt genom att föregå med `Microsoft.Azure.EventGrid.Models`.

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I Azure Functions 1.x kan du använda följande parametertyper för utlösaren för händelserutnätet:

* `JObject`
* `string`

I Azure Functions 2.x och högre har du också möjlighet att använda följande parametertyp för utlösaren för händelserutnätet:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Definierar egenskaper för de fält som är gemensamma för alla händelsetyper.

> [!NOTE]
> I Functions v1 om du `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`försöker binda till visas ett "inaktuellt" meddelande `Microsoft.Azure.EventGrid.Models.EventGridEvent` och råda dig att använda i stället. Om du vill använda den nyare typen refererar du till [Paketet Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet och kvalificerar `EventGridEvent` typnamnet helt genom att föregå med `Microsoft.Azure.EventGrid.Models`. Information om hur du refererar till NuGet-paket i en C#-skriptfunktion finns i [Använda NuGet-paket](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascript"></a>[Javascript](#tab/javascript)

Händelserutnätsinstansen är tillgänglig via parametern som `name` konfigurerats i egenskapen *function.json.*

# <a name="python"></a>[Python](#tab/python)

Händelserutnätsinstansen är tillgänglig via parametern som `name` konfigurerats i `func.EventGridEvent`egenskapen *function.json,* som skrivs som .

# <a name="java"></a>[Java](#tab/java)

Händelserutnätshändelseinstansen är `EventGridTrigger` tillgänglig via parametern `EventSchema`som är associerad med attributet, som skrivs som en . Se [exemplet](#example) för mer information.

---

## <a name="event-schema"></a>Händelseschema

Data för en eventrutnätshändelse tas emot som ett JSON-objekt i brödtexten för en HTTP-begäran. JSON liknar följande exempel:

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

Exemplet som visas är en matris med ett element. Event Grid skickar alltid en matris och kan skicka mer än en händelse i matrisen. Körningen anropar din funktion en gång för varje matriselement.

Egenskaperna på den översta nivån i händelsen JSON-data är desamma `data` bland alla händelsetyper, medan innehållet i egenskapen är specifikt för varje händelsetyp. Exemplet som visas är för en bloblagringshändelse.

Förklaringar av de vanliga och händelsespecifika egenskaperna finns i [Händelseegenskaper](../event-grid/event-schema.md#event-properties) i dokumentationen för händelserutnät.

Typen `EventGridEvent` definierar endast de översta egenskaperna. fastigheten `Data` är `JObject`en .

## <a name="create-a-subscription"></a>Skapa en prenumeration

Om du vill börja ta emot HTTP-begäranden för händelserutnät skapar du en event grid-prenumeration som anger slutpunkts-URL:en som anropar funktionen.

### <a name="azure-portal"></a>Azure Portal

För funktioner som du utvecklar i Azure-portalen med utlösaren Event Grid väljer du **Lägg till eventrutnätsprenumeration**.

![Skapa prenumeration i portalen](media/functions-bindings-event-grid/portal-sub-create.png)

När du väljer den här länken öppnas sidan **Skapa händelseprenumeration** med slutpunkts-URL:en förfylld.

![Url för slutpunkt förifylld](media/functions-bindings-event-grid/endpoint-url.png)

Mer information om hur du skapar prenumerationer med hjälp av Azure-portalen finns i [Skapa anpassad händelse - Azure-portal](../event-grid/custom-event-quickstart-portal.md) i dokumentationen till Event Grid.

### <a name="azure-cli"></a>Azure CLI

Om du vill skapa en prenumeration med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)använder du kommandot [az eventgrid event-subscription create.](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create)

Kommandot kräver slutpunkts-URL:en som anropar funktionen. I följande exempel visas det versionsspecifika URL-mönstret:

#### <a name="version-2x-and-higher-runtime"></a>Version 2.x (och högre) körning

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Version 1.x runtime

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Systemnyckeln är en auktoriseringsnyckel som måste inkluderas i slutpunkts-URL:en för en händelserutnätsutlösare. I följande avsnitt beskrivs hur du hämtar systemnyckeln.

Här är ett exempel som prenumererar på ett blob-lagringskonto (med en platshållare för systemnyckeln):

#### <a name="version-2x-and-higher-runtime"></a>Version 2.x (och högre) körning

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Version 1.x runtime

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Mer information om hur du skapar en prenumeration finns [i snabbstarten för bloblagring](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) eller andra snabbstarter för händelserutnät.

### <a name="get-the-system-key"></a>Hämta systemnyckeln

Du kan hämta systemnyckeln med hjälp av följande API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Version 2.x (och högre) körning

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Version 1.x runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Detta är ett administratörs-API, så det kräver din [funktionsapphuvudnyckel](functions-bindings-http-webhook-trigger.md#authorization-keys). Blanda inte ihop systemnyckeln (för att anropa en utlösarfunktion för händelserutnät) med huvudnyckeln (för att utföra administrativa uppgifter i funktionsappen). När du prenumererar på ett eventrutnätsämne måste du använda systemnyckeln.

Här är ett exempel på svaret som ger systemnyckeln:

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

Du kan hämta huvudnyckeln för din funktionsapp från fliken **Funktionsappinställningar** i portalen.

> [!IMPORTANT]
> Huvudnyckeln ger administratörsåtkomst till din funktionsapp. Dela inte den här nyckeln med tredje part eller distribuera den i inbyggda klientprogram.

Mer information finns i [Auktoriseringsnycklar](functions-bindings-http-webhook-trigger.md#authorization-keys) i referensartikeln för HTTP-utlösare.

Du kan också skicka en HTTP PUT för att ange nyckelvärdet själv.

## <a name="local-testing-with-viewer-web-app"></a>Lokal testning med visningswebbapp

Om du vill testa en Event Grid-utlösare lokalt måste du få HTTP-begäranden för Event Grid som levereras från deras ursprung i molnet till den lokala datorn. Ett sätt att göra det är att fånga förfrågningar online och manuellt skicka dem på din lokala dator:

1. [Skapa en visningswebbapp](#create-a-viewer-web-app) som samlar in händelsemeddelanden.
1. [Skapa en Event Grid-prenumeration](#create-an-event-grid-subscription) som skickar händelser till visningsprogrammet.
1. [Generera en begäran](#generate-a-request) och kopiera förfråspråkstexten från visningsappen.
1. [Publicera begäran manuellt](#manually-post-the-request) i den lokala värd-URL:en för utlösarfunktionen för händelserutnät.

När du är klar med testningen kan du använda samma prenumeration för produktion genom att uppdatera slutpunkten. Använd kommandot [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI.

### <a name="create-a-viewer-web-app"></a>Skapa en visningswebbapp

Om du vill förenkla insamlingen av händelsemeddelanden kan du distribuera en [färdig webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar händelsemeddelandena. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

Välj **Deploy to Azure** (Distribuera till Azure) för att distribuera lösningen till din prenumeration. Ange parametervärdena i Azure Portal.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Du ser webbplatsen men det har inte publicerats händelser till den än.

![Visa ny webbplats](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration

Skapa en Event Grid-prenumeration av den typ du vill testa och ge den URL:en från webbappen som slutpunkt för händelseavisering. Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`. Så är den fullständiga webbadressen`https://<your-site-name>.azurewebsites.net/api/updates`

Information om hur du skapar prenumerationer med hjälp av Azure-portalen finns i [Skapa anpassad händelse - Azure-portal](../event-grid/custom-event-quickstart-portal.md) i dokumentationen till Event Grid.

### <a name="generate-a-request"></a>Generera en begäran

Utlösa en händelse som genererar HTTP-trafik till slutpunkten för webbappen.  Om du till exempel har skapat en blob-lagringsprenumeration laddar du upp eller tar bort en blob. När en begäran visas i webbappen kopierar du förfråex.

Prenumerationsvalideringsbegäran tas emot först. ignorera alla valideringsbegäranden och kopiera händelsebegäran.

![Kopiera brödtext för begäran från webbapp](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Bokför begäran manuellt

Kör funktionen Event Grid lokalt.

Använd ett verktyg som [Brevbärare](https://www.getpostman.com/) eller [curl](https://curl.haxx.se/docs/httpscripting.html) för att skapa en HTTP POST-begäran:

* Ange `Content-Type: application/json` en rubrik.
* Ange `aeg-event-type: Notification` en rubrik.
* Klistra in RequestBin-data i begärandetexten.
* Publicera i url:en för utlösarfunktionen för händelserutnät.
  * För 2.x och högre använd följande mönster:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * För 1.x användning:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Parametern `functionName` måste vara det namn `FunctionName` som anges i attributet.

Följande skärmbilder visar rubrikerna och begärandetexten i Postman:

![Rubriker i Brevbäraren](media/functions-bindings-event-grid/postman2.png)

![Begäran kropp i Brevbäraren](media/functions-bindings-event-grid/postman.png)

Utlösarfunktionen Händelserutnät körs och visar loggar som liknar följande exempel:

![Exempel på utlösarfunktionsloggar för händelserutnät](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Nästa steg

* [Skicka en händelse rutnätshändelse](./functions-bindings-event-grid-trigger.md)
