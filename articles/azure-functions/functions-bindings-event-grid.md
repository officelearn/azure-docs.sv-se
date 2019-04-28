---
title: Event Grid-utlösare för Azure Functions
description: Förstå hur du hanterar Event Grid-händelser i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: b2ab07e40ac2652d97e912f8c7bd3b8893bfc114
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438756"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Event Grid-utlösare för Azure Functions

Den här artikeln beskrivs hur du hanterar [Event Grid](../event-grid/overview.md) händelser i Azure Functions.

Event Grid är en Azure-tjänst som skickar HTTP-begäranden att meddela dig om händelser som inträffar i *utgivare*. En utgivare är tjänsten eller resursen som kommer händelsen. Till exempel ett Azure blob storage-konto är en utgivare och [en ladda upp blob eller borttagning är en händelse](../storage/blobs/storage-blob-event-overview.md). Vissa [Azure-tjänster har inbyggt stöd för att publicera händelser till Event Grid](../event-grid/overview.md#event-sources).

Händelse *hanterare* tar emot och bearbetar händelser. Azure Functions är en av flera [Azure-tjänster som har inbyggt stöd för hantering av Event Grid-händelser](../event-grid/overview.md#event-handlers). I den här artikeln får du lära dig hur du använder en Event Grid-utlösare för att anropa en funktion när en händelse tas emot från Event Grid.

Om du vill kan använda du en HTTP-utlösare för att hantera Event Grid-händelserna. Se [använder en HTTP-utlösare som en Event Grid-utlösare](#use-an-http-trigger-as-an-event-grid-trigger) senare i den här artikeln. För närvarande kan du inte använda en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i den [CloudEvents-schema](../event-grid/cloudevents-schema.md). Använd istället en HTTP-utlösare.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Event Grid-utlösare finns i den [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-paketet, version 2.x. Källkoden för paketet finns i den [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Event Grid-utlösare finns i den [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-paketet, version 1.x. Källkoden för paketet finns i den [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) GitHub-lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Exempel

Se exempel språkspecifika för en Event Grid-utlösare:

* C#
* [C#-skript (.csx)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

Ett HTTP-utlösare-exempel finns i [hur du använder HTTP-utlösare](#use-an-http-trigger-as-an-event-grid-trigger) senare i den här artikeln.

### <a name="c-2x"></a>C# (2.x)

I följande exempel visas ett fungerar 2.x [C#-funktion](functions-dotnet-class-library.md) som binder till `EventGridEvent`:

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

Mer information finns i paket, [attribut](#attributes), [Configuration](#configuration), och [användning](#usage).

### <a name="c-version-1x"></a>C#(Version 1.x)

I följande exempel visas ett fungerar 1.x [C#-funktion](functions-dotnet-class-library.md) som binder till `JObject`:

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

### <a name="c-script-example"></a>Exempel på C#-skript

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen.

Här är bindningsdata i den *function.json* fil:

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

#### <a name="c-script-version-2x"></a>C#skript (Version 2.x)

Här är funktioner 2.x C#-skriptkoden som binder till `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Mer information finns i paket, [attribut](#attributes), [Configuration](#configuration), och [användning](#usage).

#### <a name="c-script-version-1x"></a>C#skript (Version 1.x)

Här är funktioner 1.x C#-skriptkoden som binder till `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

### <a name="javascript-example"></a>JavaScript-exempel

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen.

Här är bindningsdata i den *function.json* fil:

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

### <a name="python-example"></a>Python-exempel

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [funkce Pythonu](functions-reference-python.md) som använder bindningen.

Här är bindningsdata i den *function.json* fil:

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

Här är Python-kod:

```python
import logging
import azure.functions as func

def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>Utlösare - Java-exempel

Det här avsnittet innehåller följande exempel:

* [Event Grid-utlösare, strängparameter](#event-grid-trigger-string-parameter-java)
* [Event Grid-utlösare, POJO-parameter](#event-grid-trigger-pojo-parameter-java)

I följande exempel visas utlösaren bindningen i en *function.json* fil och [Java-funktioner](functions-reference-java.md) som bindningen och skriva ut en händelse som först tar emot händelsen som ```String``` och andra som en POJO.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

#### <a name="event-grid-trigger-string-parameter-java"></a>Event Grid-utlösare, strängparameter (Java)

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: " + content);      
  }
```

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Event Grid-utlösare, POJO-parametern (Java)

Det här exemplet används följande POJO, som representerar översta egenskaperna för en Event Grid-händelse:

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

Vid ankomst, händelsens JSON-nyttolast är ta bort serialiserade till den ```EventSchema``` POJO för användning av funktionen. På så sätt kan funktionen för att komma åt den händelseegenskaper i ett objektorienterat sätt.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `EventGridTrigger` anteckning på vars värde skulle hämtas från EventGrid-parametrar. Med dessa anteckningar orsaka funktionen ska köras när en händelse tas emot.  Den här anteckningen kan användas med interna Java-typer, Pojo eller kan ha värdet null-värden med hjälp av `Optional<T>`.

## <a name="attributes"></a>Attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) attribut.

Här är en `EventGridTrigger` attribut i en metodsignatur för:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Ett komplett exempel finns i C# exempel.

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil. Det finns inga parametrar i konstruktorn eller egenskaper du anger i den `EventGridTrigger` attribut.

|Function.JSON egenskap |Beskrivning|
|---------|---------|
| **typ** | Krävs – måste vara inställd på `eventGridTrigger`. |
| **riktning** | Krävs – måste vara inställd på `in`. |
| **Namn** | Krävs – variabelnamnet som används i Funktionskoden för parametern som tar emot händelsedata. |

## <a name="usage"></a>Användning

För C# och F# funktioner i Azure Functions 1.x, kan du använda följande parametertyper av för Event Grid-utlösare:

* `JObject`
* `string`

För C# och F# funktioner i Azure Functions 2.x kan du också alternativet att använda följande parametertypen för Event Grid-utlösare:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Definierar egenskaperna för fält som är gemensamma för alla händelsetyper.

> [!NOTE]
> I funktioner v1 om du försöker binda till `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, kompilatorn ska visa ett meddelande om ”inaktuell” och Använd `Microsoft.Azure.EventGrid.Models.EventGridEvent` i stället. Om du vill använda den nya typen som refererar till den [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet paketera och fullständigt kvalificerat den `EventGridEvent` typnamn av Mining med `Microsoft.Azure.EventGrid.Models`. Information om hur du refererar till NuGet-paket i en C#-skriptfunktion finns i [med hjälp av NuGet-paket](functions-reference-csharp.md#using-nuget-packages)

För JavaScript-funktioner, parametern namnges i *function.json* `name` egenskapen har en referens till händelseobjektet.

## <a name="event-schema"></a>Händelseschema

Data för en Event Grid-händelse tas emot som ett JSON-objekt i brödtexten i en HTTP-begäran. JSON som ser ut ungefär så här:

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

Exemplet som visas är en matris med ett element. Event Grid alltid skickar en matris och kan skicka fler än en händelse i matrisen. Körningen anropar funktionen en gång för varje matriselement.

Översta egenskaperna i händelsen JSON-data är samma mellan alla händelsetyper när innehållet i den `data` egenskapen är specifika för varje händelsetyp av. Exemplet som visas är en blob storage-händelse.

Förklaringar av och delade händelsespecifika egenskaper finns i [händelseegenskaper](../event-grid/event-schema.md#event-properties) i Event Grid-dokumentationen.

Den `EventGridEvent` typen definierar bara de översta egenskaperna; `Data` egenskapen är en `JObject`.

## <a name="create-a-subscription"></a>Skapa en prenumeration

Börja ta emot Event Grid HTTP-begäranden genom att skapa en Event Grid-prenumeration som du anger slutpunkts-URL som anropar funktionen.

### <a name="azure-portal"></a>Azure Portal

Funktioner som du utvecklar i Azure-portalen med Event Grid-utlösare, Välj **Lägg till Event Grid-prenumeration**.

![Skapa prenumeration i portalen](media/functions-bindings-event-grid/portal-sub-create.png)

När du väljer den här länken öppnas den **skapa händelseprenumeration** sida med slutpunkts-URL fylls i automatiskt.

![Slutpunkts-URL fylls i automatiskt](media/functions-bindings-event-grid/endpoint-url.png)

Mer information om hur du skapar prenumerationer med hjälp av Azure portal finns i [Skapa anpassad händelse – Azure-portalen](../event-grid/custom-event-quickstart-portal.md) i Event Grid-dokumentationen.

### <a name="azure-cli"></a>Azure CLI

Skapa en prenumeration med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), använda den [az eventgrid-händelseprenumeration skapa](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) kommando.

Kommandot kräver slutpunkts-URL som anropar funktionen. I följande exempel visas versionsspecifika URL-mönstret:

#### <a name="version-2x-runtime"></a>Version 2.x-körningen

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Version 1.x-körningen

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

Systemnyckeln är en auktoriseringsnyckel för som måste tas med i slutpunkts-URL för en Event Grid-utlösare. I följande avsnitt förklaras hur du hämtar systemnyckeln.

Här är ett exempel som prenumererar på ett blob storage-konto (med en platshållare för systemnyckeln):

#### <a name="version-2x-runtime"></a>Version 2.x-körningen

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Version 1.x-körningen

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Läs mer om hur du skapar en prenumeration, [snabbstarten om blob storage](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) eller andra snabbstarter i Event Grid.

### <a name="get-the-system-key"></a>Hämta systemnyckel för

Du kan få systemnyckeln med hjälp av följande API (HTTP GET):

#### <a name="version-2x-runtime"></a>Version 2.x-körningen

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Version 1.x-körningen

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Detta är en administrations-API, så att den kräver din funktionsapp [huvudnyckeln](functions-bindings-http-webhook.md#authorization-keys). Blanda inte ihop systemnyckeln (för att anropa en funktion för Event Grid-utlösare) med huvudnyckeln (för att utföra administrativa uppgifter på funktionsappen). När du prenumererar på en Event Grid-ämne, måste du använda systemnyckeln.

Här är ett exempel på ett svar som innehåller systemnyckel:

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

Du kan hämta huvudnyckeln för funktionsappen från den **fungera appinställningar** i portalen.

> [!IMPORTANT]
> Huvudnyckeln ger administratören tillgång till din funktionsapp. Inte dela den här nyckeln med tredje part eller distribuera den i interna klientprogram.

Mer information finns i [auktoriseringsregel nycklar](functions-bindings-http-webhook.md#authorization-keys) i referensartikeln för HTTP-utlösare.

Du kan också skicka en HTTP PUT att ange nyckelvärdet själv.

## <a name="local-testing-with-viewer-web-app"></a>Lokal testning med visningsprogrammet för webbprogram

Om du vill testa en Event Grid-utlösare lokalt, måste du hämta Event Grid HTTP-begäranden som levereras från sina ursprung i molnet till den lokala datorn. Det är ett sätt att göra det genom att samla in begäranden som är online och manuellt skicka dem på den lokala datorn:

1. [Skapa en webbapp för viewer](#create-a-viewer-web-app) som samlar in händelsemeddelanden.
1. [Skapa en Event Grid-prenumeration](#create-an-event-grid-subscription) som skickar händelser till viewer-appen.
1. [Skapa en begäran](#generate-a-request) och kopiera begärandetexten från viewer-appen.
1. [Manuellt efter begäran](#manually-post-the-request) till localhost-URL: en för Event Grid-Utlösarfunktion.

När du är klar testning, du kan använda samma prenumeration för produktion genom att uppdatera slutpunkten. Använd den [az eventgrid händelseprenumeration uppdatering](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI-kommando.

### <a name="create-a-viewer-web-app"></a>Skapa en webbapp för viewer

För att förenkla in händelsemeddelanden kan du distribuera en [färdiga webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelandena som händelsen. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

Välj **Deploy to Azure** (Distribuera till Azure) för att distribuera lösningen till din prenumeration. Ange parametervärdena i Azure Portal.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Du ser webbplatsen men det har inte publicerats händelser till den än.

![Visa ny webbplats](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration

Skapa en Event Grid-prenumeration för vilken du vill testa och ge den URL: en från ditt program som slutpunkt för händelseavisering. Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`. Så här är en fullständig URL `https://<your-site-name>.azurewebsites.net/api/updates`

Information om hur du skapar prenumerationer med hjälp av Azure portal finns i [Skapa anpassad händelse – Azure-portalen](../event-grid/custom-event-quickstart-portal.md) i Event Grid-dokumentationen.

### <a name="generate-a-request"></a>Skapa en begäran

Utlös en händelse som ska generera HTTP-trafik till web app-slutpunkten.  Om du har skapat en prenumeration för blob storage, ladda upp eller ta bort en blob. När en begäran som visas i din webbapp, kopiera begärandetexten.

Kommer att tas emot i begäran om verifiering av prenumeration först. Ignorera alla begäranden om verifiering och kopiera händelse-begäran.

![Kopiera begärandetexten från webbapp](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Manuellt efter begäran

Kör din Event Grid-funktion lokalt.

Använd ett verktyg som [Postman](https://www.getpostman.com/) eller [curl](https://curl.haxx.se/docs/httpscripting.html) att skapa en HTTP POST-begäran:

* Ange en `Content-Type: application/json` rubrik.
* Ange en `aeg-event-type: Notification` rubrik.
* Klistra in den RequestBin-data i begärandetexten.
* Publicera till URL: en för din funktion för Event Grid-utlösare.
  * Använd följande mönster för 2.x:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Vid användning av 1.x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Den `functionName` parametern måste vara namnet i den `FunctionName` attribut.

Följande skärmbilder visar rubrikerna och begärandetext i Postman:

![Rubriker i Postman](media/functions-bindings-event-grid/postman2.png)

![Begärandetext i Postman](media/functions-bindings-event-grid/postman.png)

Funktionen Event Grid-utlösaren körs och visar loggar som liknar följande exempel:

![Funktionen exempelloggar Event Grid-utlösare](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Lokal testning med ngrok

Ett annat sätt att testa en Event Grid-utlösare lokalt är att automatisera HTTP-anslutning mellan Internet och din utvecklingsdator. Du kan göra det med ett verktyg för öppen källkod som heter [ngrok](https://ngrok.com/):

1. [Skapa en slutpunkt för ngrok](#create-an-ngrok-endpoint).
1. [Kör funktionen Event Grid-utlösare](#run-the-event-grid-trigger-function).
1. [Skapa en Event Grid-prenumeration](#create-a-subscription) som skickar händelser till ngrok-slutpunkten.
1. [Utlös en händelse](#trigger-an-event).

När du är klar testning, du kan använda samma prenumeration för produktion genom att uppdatera slutpunkten. Använd den [az eventgrid händelseprenumeration uppdatering](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI-kommando.

### <a name="create-an-ngrok-endpoint"></a>Skapa en ngrok-slutpunkt

Ladda ned *ngrok.exe* från [ngrok](https://ngrok.com/), och kör med följande kommando:

```
ngrok http -host-header=localhost 7071
```

-Värd-huvud-parametern är nödvändigt eftersom förfrågningar från localhost förväntar sig att funktionskörningen när den körs på localhost. 7071 är standardporten när körningen körs lokalt.

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

Du kommer att använda den `https://{subdomain}.ngrok.io` URL: en för Event Grid-prenumeration.

### <a name="run-the-event-grid-trigger-function"></a>Kör funktionen Event Grid-utlösare

Ngrok URL: en få inte särskild hantering av Event Grid, så att din funktion måste köras lokalt när prenumerationen har skapats. Om den inte är verifieringssvaret skickas inte och går inte att skapa för prenumerationen.

### <a name="create-a-subscription"></a>Skapa en prenumeration

Skapa en Event Grid-prenumeration för vilken du vill testa och ge den ngrok slutpunkten.

Använd det här mönstret för slutpunkten för Functions 2.x:

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Använd det här mönstret för slutpunkten för Functions 1.x:

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

Den `{FUNCTION_NAME}` parametern måste vara namnet i den `FunctionName` attribut.

Här är ett exempel som använder Azure CLI:

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

Information om hur du skapar en prenumeration finns i [skapar du en prenumeration](#create-a-subscription) tidigare i den här artikeln.

### <a name="trigger-an-event"></a>Utlösa en händelse

Utlös en händelse som ska generera HTTP-trafik till ngrok-slutpunkten.  Om du har skapat en prenumeration för blob storage, ladda upp eller ta bort en blob.

Funktionen Event Grid-utlösaren körs och visar loggar som liknar följande exempel:

![Funktionen exempelloggar Event Grid-utlösare](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Använda en HTTP-utlösare som en Event Grid-utlösare

Event Grid-händelser tas emot som HTTP-begäranden, så att du kan hantera händelser med hjälp av en HTTP-utlösare i stället för en Event Grid-utlösare. En möjlig orsak för att göra som är att få mer kontroll över slutpunkts-URL som anropar funktionen. En annan orsak är när du vill ta emot händelser i den [CloudEvents-schema](../event-grid/cloudevents-schema.md). Event Grid-utlösaren stöder för närvarande inte CloudEvents-schema. Exemplen i det här avsnittet visar lösningar för både Event Grid-schema- och CloudEvents-schema.

Om du använder en HTTP-utlösare, som du behöver skriva kod för vad Event Grid-utlösare gör automatiskt:

* Skickar ett verifieringssvar till en [begäran om verifiering av prenumeration](../event-grid/security-authentication.md#webhook-event-delivery).
* Anropar funktionen en gång per element i matrisen händelse i begärandetexten.

Information om URL som ska användas för att anropa funktionen lokalt eller när den körs i Azure finns i den [referensdokumentation för HTTP-utlösaren bindning](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Event Grid-schema

Följande exempel C#-koden för en HTTP-utlösare simulerar beteende för Event Grid-utlösare. Använd det här exemplet för händelser som levereras i rutnätet schemat.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.LogInformation("Validate request received");
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
        log.LogInformation($"Subject: {eventGridEvent.Subject}");
        log.LogInformation($"Time: {eventGridEvent.EventTime}");
        log.LogInformation($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Följande exempel JavaScript-kod för en HTTP-utlösare simulerar beteende för Event Grid-utlösare. Använd det här exemplet för händelser som levereras i rutnätet schemat.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = messages[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        // Event Grid schema delivers events in an array.
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

Händelsehantering koden hamnar i den här slingan via den `messages` matris.

### <a name="cloudevents-schema"></a>CloudEvents-schema

Följande exempel C#-koden för en HTTP-utlösare simulerar beteende för Event Grid-utlösare.  Använd det här exemplet för händelser levereras i CloudEvents-schema.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Följande exempel JavaScript-kod för en HTTP-utlösare simulerar beteende för Event Grid-utlösare. Använd det här exemplet för händelser levereras i CloudEvents-schema.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Läs mer om Event Grid](../event-grid/overview.md)
