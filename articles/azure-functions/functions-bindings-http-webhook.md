---
title: Azure Functions HTTP och webhook bindningar | Microsoft Docs
description: "Förstå hur du använder HTTP och webhook utlösare och bindningar i Azure Functions."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelse bearbetning, webhooks, dynamiska beräknings-, serverlösa arkitektur, HTTP, API REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: mahender
ms.openlocfilehash: 3c3247592cbe2bc382d220264b0c646ee566b8a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure Functions HTTP och webhook bindningar
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Den här artikeln beskriver hur du konfigurerar och arbetar med HTTP-utlösare och bindningar i Azure Functions.
Med dessa kan använda du Azure Functions för att bygga serverlösa API: er och svara på webhooks.

Azure Functions erbjuder följande Bindningar:
- En [HTTP-utlösaren](#httptrigger) kan du anropa en funktion med en HTTP-begäran. Detta kan anpassas för att svara på [webhooks](#hooktrigger).
- En [HTTP-utdatabindning](#output) kan du svara på begäran.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>HTTP-utlösare
HTTP-utlösaren kör din funktion som svar på en HTTP-begäran. Du kan anpassa den för att svara på en viss URL eller uppsättning HTTP-metoderna. En HTTP-utlösare kan också konfigureras för att svara på webhooks. 

Om du använder Functions-portalen kan du också sätta igång direkt med en fördefinierad mall. Välj **nya funktionen** och välj ”API och Webhooks” den **scenariot** listrutan. Välj en av mallar och klicka på **skapa**.

Som standard svarar ett HTTP-utlösaren på begäran med ett 200 OK HTTP-statuskoden och en brödtext. Om du vill ändra svarstypen, konfigurera en [HTTP-utdatabindning](#output)

### <a name="configuring-an-http-trigger"></a>Konfigurera en HTTP-utlösare
En HTTP-utlösare definieras av ett JSON-objekt i den `bindings` matris med function.json som visas i följande exempel:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "get" ],
    "route": "values/{id}"
},
```
Bindningen stöder följande egenskaper:

|Egenskap  |Beskrivning  |
|---------|---------|
| **Namn** | Obligatoriskt - variabelnamnet som används i Funktionskoden för begäran eller begärandetexten. Se [arbetar med en HTTP-utlösare från kod](#httptriggerusage). |
| **typ** | Krävs – måste vara inställd på `httpTrigger`. |
| **riktning** | Krävs – måste vara inställd på `in`. |
| **authLevel** | Anger vad nycklar, eventuella måste finnas på begäran för att anropa funktionen. Värdet kan vara något av följande värden: <ul><li><code>anonymous</code>&mdash;Inga API-nyckeln är obligatorisk.</li><li><code>function</code>&mdash;Det krävs en funktionsspecifika API-nyckel. Detta är standardvärdet om ingen anges.</li><li><code>admin</code>&mdash;Huvudnyckeln krävs.</li></ul> Mer information finns i [arbeta med nycklar](#keys). |
| **metoder** | En matris med HTTP-metoderna som funktionen svarar. Om inget annat anges, svarar funktionen alla HTTP-metoderna. Se [anpassa HTTP-slutpunkten](#url). |
| **väg** | Definierar flödesmallen styra som begära webbadresserna din funktion svarar. Standardvärdet om ingen anges är `<functionname>`. Mer information finns i [anpassa HTTP-slutpunkten](#url). |
| **webHookType** | Konfigurerar HTTP-utlösaren ska fungera som en webhook mottagare för den angivna providern. Använd inte den _metoder_ egenskapen när du använder den här inställningen. Värdet kan vara något av följande värden:<ul><li><code>genericJson</code>&mdash;En generell webhook slutpunkt utan logik för en specifik provider.</li><li><code>github</code>&mdash;Funktionen svarar på GitHub webhooks. Använd inte den _authLevel_ egenskapen när du använder det här värdet.</li><li><code>slack</code>&mdash;Funktionen svarar Slack webhooks. Använd inte den _authLevel_ egenskapen när du använder det här värdet.</li></ul> Mer information finns i [svarar på webhooks](#hooktrigger). |

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Arbeta med en HTTP-utlösare från kod
För C# och F #, kan du deklarera typen av din utlösare som indata till antingen `HttpRequestMessage` eller en anpassad .NET-typ. Om du väljer `HttpRequestMessage`, du får fullständig åtkomst till request-objektet. För en anpassad .NET-typ försöker funktioner att tolka brödtexten för JSON-begäran för att ange objektets egenskaper. 

För Node.js-funktion ger Functions-runtime begärandetexten i stället för request-objektet. Mer information finns i [http-utlösaren exempel](#httptriggersample).


<a name="output"></a>
## <a name="http-response-output-binding"></a>HTTP-svar utdatabindning
Använd HTTP-utdata bindning svarar på http-begäran avsändaren. Den här bindningen kräver en HTTP-utlösare och kan du anpassa svaret som är associerade med utlösarens begäran. Om en HTTP-utdatabindning anges inte är en HTTP-utlösare returnerar HTTP 200 OK utan en brödtext. 

### <a name="configuring-an-http-output-binding"></a>Konfigurera en HTTP-utdatabindning
En HTTP-utdatabindning är definierat ett JSON-objekt i den `bindings` matris med function.json som visas i följande exempel:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
Bindningen stöder följande nödvändiga egenskaper:

|Egenskap  |Beskrivning  |
|---------|---------|
|**Namn** | Variabelnamnet som används i Funktionskoden för svaret. Se [arbetar med en HTTP-utdatabindning från kod](#outputusage). |
| **typ** |måste anges till `http`. |
| **riktning** | måste anges till `out`. |

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Arbeta med en HTTP-utdatabindning från kod
Du kan använda output-parameter svarar på http- eller webhook anroparen. Du kan också använda de språk som standard svar-mönster. Till exempel svar, se [http-utlösaren prover](#httptriggersample) och [Webhook utlösaren exempel](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Svara på webhooks
En HTTP-utlösare med den _webHookType_ egenskapen är konfigurerade för att svara [webhooks](https://en.wikipedia.org/wiki/Webhook). Den grundläggande konfigurationen använder inställningen ”genericJson”. Detta begränsar begäranden till endast de som använder HTTP POST och med den `application/json` innehållstyp.

Utlösaren dessutom skräddarsys till en specifik webhook-provider som [GitHub](https://developer.github.com/webhooks/) eller [Slack](https://api.slack.com/outgoing-webhooks). Om du har angett en provider kan Functions-runtime hantera valideringslogik provider för dig.  

### <a name="configuring-github-as-a-webhook-provider"></a>Konfigurera GitHub som en webhook-provider
För att svara på GitHub webhooks, först skapa din funktion med en HTTP-utlösare och ange den **webHookType** egenskapen `github`. Kopiera sedan dess [URL](#url) och [API-nyckel](#keys) till den **lägga till webhook** sidan i GitHub-lagringsplatsen. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Ett exempel finns [skapa en funktion som utlöses av en GitHub-webhook](functions-create-github-webhook-triggered-function.md).

### <a name="configuring-slack-as-a-webhook-provider"></a>Konfigurera Slack som en webhook-provider
Slack webhooken genererar en token för du i stället för där du kan ange den, så måste du konfigurera en funktionsspecifika nyckel med token från Slack. Se [arbeta med nycklar](#keys).

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>Anpassa HTTP-slutpunkten
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

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

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

Här är Node.js funktionskod för att använda samma vägparametrar.

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

Som standard alla funktionen vägar föregås *api*. Du kan också anpassa eller ta bort prefix med hjälp av den `http.routePrefix` egenskap i din *host.json* fil. I följande exempel tar bort den *api* väg prefix genom att använda en tom sträng för prefix i den *host.json* fil.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

Detaljerad information om hur du uppdaterar den *host.json* filen för din funktion finns [hur du uppdaterar funktionen programfilerna](functions-reference.md#fileupdate). 

Mer information om andra egenskaper som du kan konfigurera i din *host.json* fil, se [host.json referens](functions-host-json.md).


<a name="keys"></a>
## <a name="working-with-keys"></a>Arbeta med nycklar
HTTP-utlösare kan du använda nycklar för extra säkerhet. En standard HTTP-utlösare kan använda dem som en API-nyckel som kräver nyckeln måste finnas på begäran. Webhooks kan använda för att godkänna begäranden i en mängd olika sätt beroende på vad providern stöder.

Nycklar lagras som en del av din funktionsapp i Azure och krypterat i vila. Om du vill visa dina nycklar, skapa nya eller återställa nycklar till nya värden, navigera till en av dina funktioner i portalen och välj ”hantera”. 

Det finns två typer av nycklar:
- **Värdnycklar**: nycklarna delas av alla funktioner i appen funktion. När det används som en API-nyckel, Tillåt dessa åtkomst till en funktion i funktionen appen.
- **Funktionstangenter**: nycklarna gäller endast för specifika funktioner som de har definierats. När det används som en API-nyckel, tillåta dessa endast åtkomst till den funktionen.

Varje nyckel som heter referens och det finns en standard-nyckel (med namnet ”standard”) på funktionen och värden. Den **huvudnyckeln** är en standard värd-nyckel med namnet ”_master” som har definierats för varje funktionsapp. Den här nyckeln kan inte återkallas. Det ger administrativ åtkomst till runtime API: er. Med hjälp av `"authLevel": "admin"` i bindningen JSON kräver den här nyckeln som ska visas i begäran; andra nyckeln resulterar i auktoriseringsfel.

> [!IMPORTANT]  
> På grund av de utökade behörigheter som tilldelats av huvudnyckeln, bör du inte dela den här nyckeln med tredje part eller distribuera den i native client-program. Var försiktig när du väljer admin åtkomstnivå.

### <a name="api-key-authorization"></a>Auktorisering av innehållsnyckel API
Som standard kräver ett HTTP-utlösaren en API-nyckel i HTTP-begäran. HTTP-begäran ser så normalt ut ungefär så här:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Nyckeln kan ingå i en fråga string-variabel med namnet `code`, enligt ovan, eller det kan ingå i en `x-functions-key` HTTP-huvud. Värdet för nyckeln kan vara valfri Funktionstangent för som definierats för funktionen eller valfri tangent för värden.

Du kan tillåta anonyma begäranden som inte kräver nycklar. Du kan också kräva att huvudnyckeln kan användas. Du kan ändra standardnivån för auktorisering med hjälp av den `authLevel` egenskapen i bindningen JSON. Mer information finns i [HTTP-utlösaren](#httptrigger).

### <a name="keys-and-webhooks"></a>Nycklar och webhooks
Webhook-auktorisering hanteras av webhook mottagare komponent, en del av HTTP-utlösaren och mekanismen varierar beroende på vilken webhooken. Varje mekanism matchar, men förlitar sig på en nyckel. Funktionen nyckeln med namnet ”default” används som standard. Om du vill använda en annan nyckel, konfigurera webhook-providern för att skicka nyckelnamnet med förfrågan i något av följande sätt:

- **Frågesträng**: providern skickar nyckelnamnet i den `clientid` frågesträngparametern, t.ex `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Förfrågningshuvudet**: providern skickar nyckelnamnet i den `x-functions-clientid` rubrik.

> [!NOTE]
> Funktionstangenter företräde framför värdnycklar. Funktionen-nyckeln används alltid när två nycklar har definierats med samma namn.


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>HTTP-utlösaren prover
Anta att du har följande HTTP-utlösaren den `bindings` matris med function.json:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

I avsnittet språkspecifika exempel som söker efter en `name` parameter i frågesträngen eller brödtext för HTTP-begäran.

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.js](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>HTTP-utlösaren exemplet i C# #
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

Du kan också binda till en .NET-objekt i stället för **HttpRequestMessage**. Det här objektet skapas från brödtexten i begäran parsade som JSON. På samma sätt kan kan en typ som skickas till HTTP-svaret utdata bindning och returneras som brödtext för svar, tillsammans med statuskod 200.

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

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>HTTP-utlösaren exemplet i F # #
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

Du behöver en `project.json` fil som använder NuGet för att referera till den `FSharp.Interop.Dynamic` och `Dynamitey` sammansättningar som följande:

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

Detta använder NuGet för att hämta dina beroenden och refererar till dem i skriptet.

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>HTTP-utlösaren exemplet i Node.JS
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
     
<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Webhook-exempel
Anta att du har följande webhook-utlösaren den `bindings` matris med function.json:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Se exemplet språkspecifika loggar GitHub problemet kommentarer.

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.js](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>Webhook-exempel i C# #
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

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>Webhook-exempel i F # #
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

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Webhook-exempel i Node.JS
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>Nästa steg
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

