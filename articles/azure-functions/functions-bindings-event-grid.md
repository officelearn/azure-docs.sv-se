---
title: Event Grid utlösare för Azure Functions
description: Förstå hur du hanterar Event Grid händelser i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: ff21a1325e341f119fc8615ca03b41b8068aca19
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991426"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Event Grid utlösare för Azure Functions

Den här artikeln beskriver hur du hanterar [Event Grid](../event-grid/overview.md) händelser i Azure Functions.

Event Grid är en Azure-tjänst som skickar HTTP-förfrågningar för att meddela dig om händelser som inträffar i *utgivare*. En utgivare är den tjänst eller resurs som har sitt ursprung i händelsen. Till exempel är ett Azure Blob Storage-konto en utgivare och [en BLOB-överföring eller borttagning är en händelse](../storage/blobs/storage-blob-event-overview.md). Vissa [Azure-tjänster har ett inbyggt stöd för att publicera händelser till Event Grid](../event-grid/overview.md#event-sources).

Händelse *hanterare* tar emot och bearbetar händelser. Azure Functions är en av flera [Azure-tjänster som har inbyggt stöd för att hantera Event Grid händelser](../event-grid/overview.md#event-handlers). I den här artikeln får du lära dig hur du använder en Event Grid-utlösare för att anropa en funktion när en händelse tas emot från Event Grid.

Om du vill kan du använda en HTTP-utlösare för att hantera Event Grid händelser. Se [Använd en HTTP-utlösare som en Event Grid utlösare](#use-an-http-trigger-as-an-event-grid-trigger) längre fram i den här artikeln. För närvarande kan du inte använda en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i [CloudEvents-schemat](../event-grid/cloudevents-schema.md). Använd i stället en HTTP-utlösare.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Paket-funktioner 2. x

Event Grid-utlösaren finns i [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-paketet, version 2. x. Käll koden för paketet finns i GitHub-lagringsplatsen [Azure-Functions-eventgrid-Extensions](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Paket-funktioner 1. x

Event Grid-utlösaren finns i [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-paketet, version 1. x. Käll koden för paketet finns i GitHub-lagringsplatsen [Azure-Functions-eventgrid-Extensions](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Exempel

Se språkspecifika exempel för en Event Grid-utlösare:

* C#
* [C#skript (. CSX)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

Ett exempel på en HTTP-utlösare finns i [använda HTTP-utlösare](#use-an-http-trigger-as-an-event-grid-trigger) senare i den här artikeln.

### <a name="c-2x"></a>C#(2. x)

I följande exempel visas en funktion 2. x [ C# -funktion](functions-dotnet-class-library.md) som binder till`EventGridEvent`:

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

### <a name="c-version-1x"></a>C#(Version 1. x)

I följande exempel visas en funktion 1. x [ C# -funktion](functions-dotnet-class-library.md) som binder till`JObject`:

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

### <a name="c-script-example"></a>C#skript exempel

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

#### <a name="c-script-version-2x"></a>C#skript (version 2. x)

Här är funktioner 2. x C# -skript kod som binder till`EventGridEvent`:

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

#### <a name="c-script-version-1x"></a>C#skript (version 1. x)

Här är funktioner 1. x C# -skript kod som binder till`JObject`:

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

### <a name="python-example"></a>Python-exempel

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
import logging
import azure.functions as func


def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>Utlös – Java-exempel

Det här avsnittet innehåller följande exempel:

* [Event Grid utlösare, sträng parameter](#event-grid-trigger-string-parameter-java)
* [Event Grid trigger, POJO-parameter](#event-grid-trigger-pojo-parameter-java)

I följande exempel visas utlösarens bindning i en *Function. JSON* -fil och [Java-funktioner](functions-reference-java.md) som använder bindningen och utskriften av en händelse, först mottagandet händelsen som ```String``` och sekund som en POJO.

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

#### <a name="event-grid-trigger-string-parameter-java"></a>Event Grid utlösare, String-parameter (Java)

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

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Event Grid-utlösare, POJO-parameter (Java)

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

## <a name="attributes"></a>Attribut

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

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfigurationen som du anger i filen *Function. JSON* . Det finns inga parametrar eller egenskaper för konstruktorn att ange i `EventGridTrigger`-attributet.

|function. JSON-egenskap |Beskrivning|
|---------|---------|
| **typ** | Required-måste anges till `eventGridTrigger`. |
| **riktning** | Required-måste anges till `in`. |
| **Namn** | Obligatoriskt – variabel namnet som används i funktions koden för den parameter som tar emot händelse data. |

## <a name="usage"></a>Användning

För C# och F# -funktioner i Azure Functions 1. x kan du använda följande parameter typer för utlösaren för Event Grid:

* `JObject`
* `string`

För C# - F# och-funktionerna i Azure Functions 2. x har du också möjlighet att använda följande parameter typ för Event Grid-utlösaren:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-definierar egenskaper för fälten som är gemensamma för alla händelse typer.

> [!NOTE]
> I functions v1 om du försöker binda till `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`visas ett "föråldrat" meddelande som gör att du kan använda `Microsoft.Azure.EventGrid.Models.EventGridEvent` i stället. Om du vill använda den nyare typen refererar du till [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-paketet och fullständigt kvalificerar `EventGridEvent` typ namnet genom att prefixet med `Microsoft.Azure.EventGrid.Models`. Information om hur du refererar till NuGet-paket i C# en-skript funktion finns i [använda NuGet-paket](functions-reference-csharp.md#using-nuget-packages)

För JavaScript-funktioner har den parameter som har namnet av egenskapen *Function. json* `name` en referens till Event-objektet.

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

### <a name="azure-portal"></a>Azure portal

För funktioner som du utvecklar i Azure Portal med Event Grid utlösare väljer du **Lägg till Event Grid prenumeration**.

![Skapa prenumeration i portalen](media/functions-bindings-event-grid/portal-sub-create.png)

När du väljer den här länken öppnar portalen sidan **Skapa händelse prenumeration** med slut PUNKTens URL förifylld.

![Förifyllt slut punkts-URL](media/functions-bindings-event-grid/endpoint-url.png)

Mer information om hur du skapar prenumerationer med hjälp av Azure Portal finns i avsnittet [Skapa anpassad händelse-Azure Portal](../event-grid/custom-event-quickstart-portal.md) i Event Grid-dokumentationen.

### <a name="azure-cli"></a>Azure CLI

Om du vill skapa en prenumeration med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)använder du kommandot [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) .

Kommandot kräver slut punkts-URL: en som anropar funktionen. I följande exempel visas det användarspecifika URL-mönstret:

#### <a name="version-2x-runtime"></a>Version 2. x-körning

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Version 1. x-körning

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

System nyckeln är en auktoriseringskod som måste inkluderas i slut punkts-URL: en för en Event Grid-utlösare. I följande avsnitt beskrivs hur du hämtar system nyckeln.

Här är ett exempel som prenumererar på ett Blob Storage-konto (med en plats hållare för system nyckeln):

#### <a name="version-2x-runtime"></a>Version 2. x-körning

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

#### <a name="version-2x-runtime"></a>Version 2. x-körning

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
  * Använd följande mönster för 2. x:

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

## <a name="local-testing-with-ngrok"></a>Lokal testning med ngrok

Ett annat sätt att testa en Event Grid-utlösare lokalt är att automatisera HTTP-anslutningen mellan Internet och din utvecklings dator. Du kan göra det med ett verktyg som [ngrok](https://ngrok.com/):

1. [Skapa en ngrok-slutpunkt](#create-an-ngrok-endpoint).
1. [Kör funktionen Event Grid trigger](#run-the-event-grid-trigger-function).
1. [Skapa en Event Grid-prenumeration](#create-a-subscription) som skickar händelser till ngrok-slutpunkten.
1. [Utlösa en händelse](#trigger-an-event).

När du är klar med testningen kan du använda samma prenumeration för produktion genom att uppdatera slut punkten. Använd kommandot [AZ eventgrid Event-Subscription Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI.

### <a name="create-an-ngrok-endpoint"></a>Skapa en ngrok-slutpunkt

Hämta *ngrok. exe* från [ngrok](https://ngrok.com/)och kör med följande kommando:

```
ngrok http -host-header=localhost 7071
```

Parametern-Host-Head krävs eftersom Functions runtime förväntar sig begär Anden från localhost när den körs på localhost. 7071 är standard port numret när körningen körs lokalt.

Kommandot skapar utdata som följande:

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

Du använder `https://{subdomain}.ngrok.io`-URL: en för din Event Grid-prenumeration.

### <a name="run-the-event-grid-trigger-function"></a>Kör funktionen Event Grid trigger

Ngrok-URL: en får inte särskilt hantering av Event Grid, så din funktion måste köras lokalt när prenumerationen skapas. Om det inte är det skickas inte verifierings svaret och det går inte att skapa prenumerationen.

### <a name="create-a-subscription"></a>Skapa en prenumeration

Skapa en Event Grid-prenumeration av den typ som du vill testa och ge den ngrok-slutpunkten.

Använd det här slut punkts mönstret för functions 2. x:

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Använd det här slut punkts mönstret för functions 1. x:

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

Parametern `{FUNCTION_NAME}` måste vara det namn som anges i attributet `FunctionName`.

Här är ett exempel på hur du använder Azure CLI:

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

Information om hur du skapar en prenumeration finns i [skapa en prenumeration](#create-a-subscription) tidigare i den här artikeln.

### <a name="trigger-an-event"></a>Utlösa en händelse

Utlöser en händelse som genererar HTTP-trafik till din ngrok-slutpunkt.  Om du till exempel har skapat en Blob Storage-prenumeration laddar du upp eller tar bort en blob.

Funktionen Event Grid trigger kör och visar loggar som liknar följande exempel:

![Exempel Event Grid utlösa funktions loggar](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Använd en HTTP-utlösare som en Event Grid-utlösare

Event Grid händelser tas emot som HTTP-begäranden, så att du kan hantera händelser med hjälp av en HTTP-utlösare i stället för en Event Grid utlösare. En möjlig orsak till att göra detta är att få mer kontroll över slut punkts-URL: en som anropar funktionen. En annan orsak är när du behöver ta emot händelser i [CloudEvents-schemat](../event-grid/cloudevents-schema.md). För närvarande stöder Event Grid-utlösaren inte CloudEvents-schemat. I exemplen i det här avsnittet visas lösningar för både Event Grid schema och CloudEvents-schema.

Om du använder en HTTP-utlösare måste du skriva kod för vad den Event Grid utlösaren gör automatiskt:

* Skickar ett verifierings svar till en [begäran om prenumerations verifiering](../event-grid/security-authentication.md#webhook-event-delivery).
* Anropar funktionen en gång per element i händelse mat ris som finns i begär ande texten.

Information om den URL som ska användas för att anropa funktionen lokalt eller när den körs i Azure finns i [referens dokumentationen för http-utlösare](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Event Grid-schema

Följande exempel C# kod för en http-utlösare simulerar Event Grid utlösarens beteende. Använd det här exemplet för händelser som levereras i Event Grid-schemat.

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

Följande exempel på JavaScript-kod för en HTTP-utlösare simulerar Event Grid utlösnings beteende. Använd det här exemplet för händelser som levereras i Event Grid-schemat.

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

Din händelse hanterings kod hamnar inuti loopen genom `messages` matrisen.

### <a name="cloudevents-schema"></a>CloudEvents-schema

Följande exempel C# kod för en http-utlösare simulerar Event Grid utlösarens beteende.  Använd det här exemplet för händelser som levereras i CloudEvents-schemat.

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

Följande exempel på JavaScript-kod för en HTTP-utlösare simulerar Event Grid utlösnings beteende. Använd det här exemplet för händelser som levereras i CloudEvents-schemat.

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
> [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Läs mer om Event Grid](../event-grid/overview.md)
