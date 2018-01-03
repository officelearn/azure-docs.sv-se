---
title: Azure Functions HTTP och webhook bindningar
description: "Förstå hur du använder HTTP och webhook utlösare och bindningar i Azure Functions."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelse bearbetning, webhooks, dynamiska beräknings-, serverlösa arkitektur, HTTP, API REST"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: mahender
ms.openlocfilehash: 080712e0a6c05348e7163f3c8e2055e6ff2806b2
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure Functions HTTP och webhook bindningar

Den här artikeln förklarar hur du arbetar med HTTP-bindningar i Azure Functions. Azure Functions stöder HTTP-utlösare och bindningar för utdata.

En HTTP-utlösare kan anpassas för att svara på [webhooks](https://en.wikipedia.org/wiki/Webhook). En webhook-utlösare accepterar endast en JSON-nyttolast och verifierar JSON. Det finns särskilda versioner av webhook-utlösare som gör det enklare att hantera webhooks från vissa leverantörer, till exempel GitHub och Slack.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="trigger"></a>Utlösare

HTTP-utlösare kan du anropa en funktion med en HTTP-begäran. Du kan använda en HTTP-utlösare för att skapa serverlösa API: er och svara på webhooks. 

Som standard svarar ett HTTP-utlösaren på begäran med ett 200 OK HTTP-statuskoden och en brödtext. Om du vill ändra svarstypen, konfigurera en [HTTP-utdatabindning](#http-output-binding).

## <a name="trigger---example"></a>Utlösaren - exempel

Finns i det språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Utlösaren - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som söker efter en `name` parameter i frågesträngen eller brödtext för HTTP-begäran.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### <a name="trigger---c-script-example"></a>Utlösaren - exempel på C#-skript

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen söker efter en `name` parameter i frågesträngen eller brödtext för HTTP-begäran.

Här är de bindande data den *function.json* fil:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är C# skriptkod som binder till `HttpRequestMessage`:

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

Du kan binda till ett anpassat objekt i stället för `HttpRequestMessage`. Det här objektet skapas från brödtexten i begäran parsade som JSON. På samma sätt kan kan en typ som skickas till HTTP-svaret utdata bindning och returneras som brödtext för svar, tillsammans med statuskod 200.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

### <a name="trigger---f-example"></a>Utlösaren - F #-exempel

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [F # funktionen](functions-reference-fsharp.md) som använder bindningen. Funktionen söker efter en `name` parameter i frågesträngen eller brödtext för HTTP-begäran.

Här är de bindande data den *function.json* fil:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är F #-kod:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Du behöver en `project.json` fil som använder NuGet för att referera till den `FSharp.Interop.Dynamic` och `Dynamitey` sammansättningar som visas i följande exempel:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>Utlösaren - JavaScript-exempel

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen söker efter en `name` parameter i frågesträngen eller brödtext för HTTP-begäran.

Här är de bindande data den *function.json* fil:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```
     
## <a name="trigger---webhook-example"></a>Utlösaren - webhook-exempel

Finns i det språkspecifika:

* [C#](#webhook---c-example)
* [C#-skript (.csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Webhook - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som skickar en HTTP-200 som svar på en allmän JSON-begäran.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Webhook - C# exempel på skript

I följande exempel visas en webhook-utlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen loggar GitHub problemet kommentarer.

Här är de bindande data den *function.json* fil:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är skriptkod C#:

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### <a name="webhook---f-example"></a>Webhook - F #-exempel

I följande exempel visas en webhook-utlösare bindning i en *function.json* fil och en [F # funktionen](functions-reference-fsharp.md) som använder bindningen. Funktionen loggar GitHub problemet kommentarer.

Här är de bindande data den *function.json* fil:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är F #-kod:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

### <a name="webhook---javascript-example"></a>Webhook - JavaScript-exempel

I följande exempel visas en webhook-utlösare bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen loggar GitHub problemet kommentarer.

Här är de bindande data den *function.json* fil:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
```

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Utlösaren - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) attribut som definierats i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http).

Du kan ange tillståndet nivå och tillåten HTTP-metoderna i attributet konstruktorparametrarna och det finns webhook typ och väg mall. Mer information om dessa inställningar finns [utlösaren - konfiguration](#trigger---configuration). Här är ett `HttpTrigger` attribut i en signatur:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

En komplett exempel finns [utlösaren - C#-exempel](#trigger---c-example).

## <a name="trigger---configuration"></a>Utlösaren - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `HttpTrigger` attribut.


|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
| **typ** | Saknas| Krävs – måste vara inställd på `httpTrigger`. |
| **riktning** | Saknas| Krävs – måste vara inställd på `in`. |
| **Namn** | Saknas| Obligatoriskt - variabelnamnet som används i Funktionskoden för begäran eller begärandetexten. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Anger vad nycklar, eventuella måste finnas på begäran för att anropa funktionen. Åtkomstnivån kan vara något av följande värden: <ul><li><code>anonymous</code>&mdash;Inga API-nyckeln är obligatorisk.</li><li><code>function</code>&mdash;Det krävs en funktionsspecifika API-nyckel. Detta är standardvärdet om ingen anges.</li><li><code>admin</code>&mdash;Huvudnyckeln krävs.</li></ul> Mer information finns i avsnittet [auktorisering nycklar](#authorization-keys). |
| **metoder** |**Metoder** | En matris med HTTP-metoderna som funktionen svarar. Om inget annat anges, svarar funktionen alla HTTP-metoderna. Se [anpassa http-slutpunkten](#trigger---customize-the-http-endpoint). |
| **väg** | **Väg** | Definierar flödesmallen styra som begära webbadresserna din funktion svarar. Standardvärdet om ingen anges är `<functionname>`. Mer information finns i [anpassa http-slutpunkten](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Konfigurerar HTTP-utlösaren ska fungera som en [webhook](https://en.wikipedia.org/wiki/Webhook) mottagare för den angivna providern. Konfigurerar inte den `methods` egenskapen om du ställer in den här egenskapen. Webhook-typen kan vara något av följande värden:<ul><li><code>genericJson</code>&mdash;En generell webhook slutpunkt utan logik för en specifik provider. Den här inställningen begränsar begäranden till endast de som använder HTTP POST och med den `application/json` innehållstyp.</li><li><code>github</code>&mdash;Funktionen besvarar [GitHub webhooks](https://developer.github.com/webhooks/). Använd inte den _authLevel_ egenskap med GitHub webhooks. Mer information finns i avsnittet GitHub webhooks senare i den här artikeln.</li><li><code>slack</code>&mdash;Funktionen besvarar [Slack webhooks](https://api.slack.com/outgoing-webhooks). Använd inte den _authLevel_ egenskap med Slack webhooks. Mer information finns i avsnittet Slack webhooks senare i den här artikeln.</li></ul>|

## <a name="trigger---usage"></a>Utlösaren - användning

För C# och F #, kan du deklarera typen av din utlösare som indata till antingen `HttpRequestMessage` eller en anpassad typ. Om du väljer `HttpRequestMessage`, du får fullständig åtkomst till request-objektet. För en anpassad typ försöker funktioner att tolka brödtexten för JSON-begäran för att ange objektets egenskaper. 

Functions-runtime ger begärandetexten i stället för request-objektet för JavaScript-funktioner. Mer information finns i [JavaScript utlösaren exempel](#trigger---javascript-example).

### <a name="github-webhooks"></a>GitHub webhooks

För att svara på GitHub webhooks, först skapa din funktion med en HTTP-utlösare och ange den **webHookType** egenskapen `github`. Kopiera den URL: en och API-nyckeln i den **lägga till webhook** sidan i GitHub-lagringsplatsen. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Ett exempel finns i [Skapa en funktion som utlöses av en GitHub-webhook](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Slack webhooks

Slack webhooken genererar en token för du i stället för där du kan ange den, så måste du konfigurera en funktionsspecifika nyckel med token från Slack. Se [auktorisering nycklar](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>Anpassa HTTP-slutpunkten

Som standard när du skapar en funktion för ett HTTP-utlösare eller WebHook, är funktionen adresserbara med en väg i formatet:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Du kan anpassa den här vägen med det valfria `route` egenskapen för HTTP-utlösaren input bindning. Följande exempel *function.json* filen definierar en `route` för en HTTP-utlösare:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Med den här konfigurationen kan är funktionen nu adresserbara med följande vägen i stället för det ursprungliga flödet.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Detta gör att Funktionskoden stöder två parametrar i adressen _kategori_ och _id_. Du kan använda någon [Web API-väg begränsningen](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) med parametrarna. Följande C# Funktionskoden använder båda parametrarna.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Här är Node.js Funktionskoden som använder samma vägparametrar.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

Som standard alla funktionen vägar föregås *api*. Du kan också anpassa eller ta bort prefix med hjälp av den `http.routePrefix` egenskap i din [host.json](functions-host-json.md) fil. I följande exempel tar bort den *api* väg prefix genom att använda en tom sträng för prefix i den *host.json* fil.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Auktorisering nycklar

HTTP-utlösare kan du använda nycklar för extra säkerhet. En standard HTTP-utlösare kan använda dem som en API-nyckel som kräver nyckeln måste finnas på begäran. Webhooks kan använda för att godkänna begäranden i en mängd olika sätt beroende på vad providern stöder.

Nycklar lagras som en del av din funktionsapp i Azure och krypterat i vila. Om du vill visa dina nycklar, skapa nya eller återställa nycklar till nya värden, navigera till en av dina funktioner i portalen och välj ”hantera”. 

Det finns två typer av nycklar:

- **Värdnycklar**: nycklarna delas av alla funktioner i appen funktion. När det används som en API-nyckel, Tillåt dessa åtkomst till en funktion i funktionen appen.
- **Funktionstangenter**: nycklarna gäller endast för specifika funktioner som de har definierats. När det används som en API-nyckel, tillåta dessa endast åtkomst till den funktionen.

Varje nyckel som heter referens och det finns en standard-nyckel (med namnet ”standard”) på funktionen och värden. Funktionstangenter företräde framför värdnycklar. Funktionen-nyckeln används alltid när två nycklar har definierats med samma namn.

Den **huvudnyckeln** är en standard värd-nyckel med namnet ”_master” som har definierats för varje funktionsapp. Den här nyckeln kan inte återkallas. Det ger administrativ åtkomst till runtime API: er. Med hjälp av `"authLevel": "admin"` i bindningen JSON kräver den här nyckeln som ska visas i begäran; andra nyckeln resulterar i auktoriseringsfel.

> [!IMPORTANT]  
> På grund av de utökade behörigheter som tilldelats av huvudnyckeln, bör du inte dela den här nyckeln med tredje part eller distribuera den i native client-program. Var försiktig när du väljer admin åtkomstnivå.

### <a name="api-key-authorization"></a>Auktorisering av innehållsnyckel API

Som standard kräver ett HTTP-utlösaren en API-nyckel i HTTP-begäran. HTTP-begäran ser så normalt ut ungefär så här:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Nyckeln kan ingå i en fråga string-variabel med namnet `code`, enligt ovan, eller det kan ingå i en `x-functions-key` HTTP-huvud. Värdet för nyckeln kan vara valfri Funktionstangent för som definierats för funktionen eller valfri tangent för värden.

Du kan tillåta anonyma begäranden som inte kräver nycklar. Du kan också kräva att huvudnyckeln kan användas. Du kan ändra standardnivån för auktorisering med hjälp av den `authLevel` egenskapen i bindningen JSON. Mer information finns i [utlösaren - konfiguration](#trigger---configuration).

### <a name="keys-and-webhooks"></a>Nycklar och webhooks

Webhook-auktorisering hanteras av webhook mottagare komponent, en del av HTTP-utlösaren och mekanismen varierar beroende på vilken webhooken. Varje mekanism matchar, men förlitar sig på en nyckel. Funktionen nyckeln med namnet ”default” används som standard. Om du vill använda en annan nyckel, konfigurera webhook-providern för att skicka nyckelnamnet med förfrågan i något av följande sätt:

- **Frågesträng**: providern skickar nyckelnamnet i den `clientid` frågesträngparametern, t.ex `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Förfrågningshuvudet**: providern skickar nyckelnamnet i den `x-functions-clientid` rubrik.

## <a name="trigger---hostjson-properties"></a>Utlösaren - host.json egenskaper

Den [host.json](functions-host-json.md) filen innehåller inställningar som styr beteendet för HTTP-utlösare.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Resultat

Använd HTTP-utdata bindning svarar på http-begäran avsändaren. Den här bindningen kräver en HTTP-utlösare och kan du anpassa svaret som är associerade med utlösarens begäran. Om en HTTP-utdatabindning anges inte är en HTTP-utlösare returnerar HTTP 200 OK utan en brödtext. 

## <a name="output---configuration"></a>Output - konfiguration

C#-klassbibliotek finns det inga egenskaper för konfiguration av utdata-specifik bindning. Om du vill skicka ett HTTP-svar, kontrollera funktionen returtyp `HttpResponseMessage` eller `Task<HttpResponseMessage>`.

För andra språk, en HTTP-utdatabindning har definierats som ett JSON-objekt i den `bindings` matris med function.json som visas i följande exempel:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil.

|Egenskap  |Beskrivning  |
|---------|---------|
| **typ** |måste anges till `http`. |
| **riktning** | måste anges till `out`. |
|**Namn** | Variabelnamnet som används i Funktionskoden för svaret. |

## <a name="output---usage"></a>Utdata - användning

Du kan använda output-parameter svarar på http- eller webhook anroparen. Du kan också använda de språk som standard svar-mönster. Till exempel svar kan se den [utlösaren exempel](#trigger---example) och [webhook exempel](#trigger---webhook-example).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
