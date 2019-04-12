---
title: Azure Functions HTTP-utlösare och bindningar
description: Förstå hur du använder HTTP-utlösare och bindningar i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funktioner, händelse bearbetning, webhooks, dynamisk beräkning, serverlös arkitektur, HTTP, API, REST
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: a1d66cf4506e3b8f58572576db908812f4e2be07
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490418"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Azure Functions HTTP-utlösare och bindningar

Den här artikeln förklarar hur du arbetar med HTTP-utlösare och utdatabindningar i Azure Functions.

En HTTP-utlösare kan anpassas för att svara på [webhooks](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

Koden i den här artikeln som standard Functions 2.x syntax som använder .NET Core. Information om 1.x-syntax finns i den [1.x fungerar mallar](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

HTTP-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet-paketet, version 1.x. Källkoden för paketet finns i den [azure-webjobs-sdk-tilläggen](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

HTTP-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet-paketet, version 3.x. Källkoden för paketet finns i den [azure-webjobs-sdk-tilläggen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Utlösare

HTTP-utlösare kan du anropa en funktion med en HTTP-begäran. Du kan använda en HTTP-utlösare för att skapa serverfria API: er och svara på webhookar.

Som standard returnerar en HTTP-utlösare HTTP 200 OK utan en brödtext i Functions 1.x eller HTTP 204 inget innehåll utan en brödtext i Functions 2.x. Om du vill ändra svarstypen, konfigurera en [HTTP-utdatabindning](#output).

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Utlösare – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som söker efter en `name` parameter i frågesträngen eller brödtexten i HTTP-begäran. Observera att det returnera värdet används för utdata-bindning, men krävs inte ett returvärde attribut.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

### <a name="trigger---c-script-example"></a>Utlösare – exempel på C#-skript

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen söker efter en `name` parameter i frågesträngen eller brödtexten i HTTP-begäran.

Här är den *function.json* fil:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden som binder till `HttpRequest`:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Du kan bindas till ett anpassat objekt i stället för `HttpRequest`. Det här objektet skapas från brödtexten i begäran och parsade som JSON. På samma sätt kan kan en typ skickas till HTTP-svaret utdata bindning och returneras som svarstexten, tillsammans med statuskoden 200.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

### <a name="trigger---f-example"></a>Utlösare – F# exempel

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [ F# funktionen](functions-reference-fsharp.md) som använder bindningen. Funktionen söker efter en `name` parameter i frågesträngen eller brödtexten i HTTP-begäran.

Här är den *function.json* fil:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är den F# kod:

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

Du behöver en `project.json` -fil som använder NuGet för att referera till den `FSharp.Interop.Dynamic` och `Dynamitey` sammansättningar, som visas i följande exempel:

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

### <a name="trigger---javascript-example"></a>Utlösare – JavaScript-exempel

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen söker efter en `name` parameter i frågesträngen eller brödtexten i HTTP-begäran.

Här är den *function.json* fil:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
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

### <a name="trigger---python-example"></a>Utlösare – Python-exempel

I följande exempel visas en utlösare-bindning i en *function.json* fil och en [funkce Pythonu](functions-reference-python.md) som använder bindningen. Funktionen söker efter en `name` parameter i frågesträngen eller brödtexten i HTTP-begäran.

Här är den *function.json* fil:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är Python-kod:

```python
import logging
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

### <a name="trigger---java-examples"></a>Utlösare - Java-exempel

* [Läsa parametern från frågesträngen](#read-parameter-from-the-query-string-java)
* [Läsa innehållet från en POST-begäran](#read-body-from-a-post-request-java)
* [Läsa parameter från en väg](#read-parameter-from-a-route-java)
* [Läs POJO text från en POST-begäran](#read-pojo-body-from-a-post-request-java)

I följande exempel visas HTTP-utlösaren bindning i en *function.json* filen och motsvarande [Java-funktioner](functions-reference-java.md) som använder bindningen. 

Här är den *function.json* fil:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

#### <a name="read-parameter-from-the-query-string-java"></a>Läsa parametern från frågesträngen (Java)  

Det här exemplet läser en parameter med namnet ```id```, från frågesträngen och använder den för att skapa en JSON-dokument som returneras till klienten, med innehåll av typen ```application/json```. 

```java
    @FunctionName("TriggerStringGet")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("GET parameters are: " + request.getQueryParameters());

        // Get named parameter
        String id = request.getQueryParameters().getOrDefault("id", "");

        // Convert and display
        if (id.isEmpty()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String name = "fake_name";
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-body-from-a-post-request-java"></a>Läsa innehållet från en POST-begäran (Java)  

Det här exemplet läser brödtexten i en POST-begäran som en ```String```, och används för att skapa en JSON-dokument som returneras till klienten med innehållstyp ```application/json```.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route-java"></a>Läsa parameter från en väg (Java)  

Det här exemplet läser en obligatorisk parameter som heter ```id```, och en valfri parameter ```name``` från väg sökväg och använder dem för att skapa en JSON-dokument returneras till klienten, med innehåll av typen ```application/json```. T

```java
    @FunctionName("TriggerStringRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
            HttpRequestMessage<Optional<String>> request,
            @BindingName("id") String id,
            @BindingName("name") String name,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Route parameters are: " + id);

        // Convert and display
        if (id == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-pojo-body-from-a-post-request-java"></a>Läs POJO text från en POST-begäran (Java)  

Här är koden för den ```ToDoItem``` klass, som refereras till i det här exemplet:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Det här exemplet läser brödtexten i en POST-begäran. Begärandetexten hämtar automatiskt ta bort serialiserade i en ```ToDoItem``` objekt och returneras till klienten med innehållstyp ```application/json```. Den ```ToDoItem``` parametern serialiseras av funktionskörningen när den är tilldelad till den ```body``` egenskapen för den ```HttpMessageResponse.Builder``` klass.

```java
    @FunctionName("TriggerPojoPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<ToDoItem>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(null));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final ToDoItem body = request.getBody().get();
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(body)
                          .build();
        }
    }
```

## <a name="trigger---attributes"></a>Utlösare - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) attribut.

Du kan ange tillståndet och det tillåtna HTTP-metoder i attributet konstruktor parametrar och det finns webhook typ och dirigera mall. Mer information om dessa inställningar finns i [utlösare - konfigurationen](#trigger---configuration). Här är en `HttpTrigger` attribut i en metodsignatur för:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Ett komplett exempel finns i [utlösare – C#-exempel](#trigger---c-example).

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `HttpTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
| **typ** | Saknas| Krävs – måste vara inställd på `httpTrigger`. |
| **riktning** | Saknas| Krävs – måste vara inställd på `in`. |
| **namn** | Saknas| Krävs – variabelnamnet som används i Funktionskoden för begäran och begärandetexten. |
| <a name="http-auth"></a>**authLevel** |  **authLevel** |Anger vad nycklar, om sådana finns, måste finnas på begäran för att anropa funktionen. Åtkomstnivån kan vara något av följande värden: <ul><li><code>anonymous</code>&mdash;Inga API-nyckeln är obligatorisk.</li><li><code>function</code>&mdash;Det krävs en funktionsspecifika API-nyckel. Detta är standardvärdet om ingen har angetts.</li><li><code>admin</code>&mdash;Huvudnyckeln krävs.</li></ul> Mer information finns i avsnittet [auktoriseringsregel nycklar](#authorization-keys). |
| **Metoder** |**Metoder** | En matris med HTTP-metoder som funktionen svarar. Om inte anges svarar funktionen på alla HTTP-metoder. Se [anpassa http-slutpunkt](#customize-the-http-endpoint). |
| **route** | **Routa** | Definierar flödesmallen, kontrollera som begär URL: er som din funktion svarar. Standardvärdet om inget är `<functionname>`. Mer information finns i [anpassa http-slutpunkt](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Stöds endast för version 1.x-körningen._<br/><br/>Konfigurerar HTTP-utlösare ska fungera som en [webhook](https://en.wikipedia.org/wiki/Webhook) mottagare för den angivna providern. Konfigurerar inte den `methods` egenskapen om du ställer in den här egenskapen. Webhook-typen kan vara något av följande värden:<ul><li><code>genericJson</code>&mdash;Ett allmänt webhook-slutpunkt utan logik för en viss leverantör. Den här inställningen begränsar begäranden till endast de som använder HTTP POST och med den `application/json` innehållstyp.</li><li><code>github</code>&mdash;Funktionen svarar på [GitHub webhooks](https://developer.github.com/webhooks/). Använd inte den _authLevel_ egenskap med GitHub webhooks. Mer information finns i avsnittet GitHub webhooks senare i den här artikeln.</li><li><code>slack</code>&mdash;Funktionen svarar på [Slack webhooks](https://api.slack.com/outgoing-webhooks). Använd inte den _authLevel_ egenskap med Slack webhooks. Mer information finns i avsnittet Slack webhooks senare i den här artikeln.</li></ul>|

## <a name="trigger---usage"></a>Utlösare - användning

För C# och F# funktion, kan du deklarera vilken typ av utlösaren indata ska vara antingen `HttpRequest` eller en anpassad typ. Om du väljer `HttpRequest`, får du fullständig åtkomst till Begäranobjektet. För en anpassad typ försöker körningen parsa JSON-begärandetexten för att ange objektets egenskaper.

Functions-körning ger begärandetexten i stället för Begäranobjektet för JavaScript-funktioner. Mer information finns i den [JavaScript utlösaren exempel](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>Anpassa HTTP-slutpunkt

Som standard när du skapar en funktion för en HTTP-utlösare är funktionen adresserbara med en väg i formatet:

    http://<yourapp>.azurewebsites.net/api/<funcname>

Du kan anpassa den här vägen med det valfria `route` indatabindning för egenskapen för HTTP-utlösaren. Till exempel följande *function.json* fil definierar en `route` för en HTTP-utlösare:

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

Med den här konfigurationen kan är funktionen nu adresserbara med följande väg i stället för det ursprungliga flödet.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

På så sätt kan function-koden att stödja två parametrar i adressen _kategori_ och _id_. Du kan använda någon [webb-API: et Route begränsningen](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) med parametrarna. Den följande C#-Funktionskoden använder båda parametrarna.

```csharp
public static Task<IActionResult> Run(HttpRequest req, string category, int? id, ILogger log)
{
    if (id == null)
    {
        return (ActionResult)new OkObjectResult($"All {category} items were requested.");
    }
    else
    {
        return (ActionResult)new OkObjectResult($"{category} item with id = {id} has been requested.");
    }
    
    // -----
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
}
```

Här är Node.js-Funktionskoden som använder samma vägparametrar.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
}
```

Som standard alla funktionen vägar har prefixet *api*. Du kan också anpassa eller ta bort prefix med hjälp av den `http.routePrefix` -egenskapen i din [host.json](functions-host-json.md) fil. I följande exempel tar bort den *api* väg prefix genom att använda en tom sträng för prefix i den *host.json* fil.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Arbeta med klient-identiteter

Om din funktionsapp använder [App Service-autentisering / auktorisering](../app-service/overview-authentication-authorization.md), du kan visa information om autentiserade klienter från din kod. Den här informationen är tillgänglig som [begärandehuvuden matas in av plattformen](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Du kan också läsa den här informationen från binda data. Den här funktionen är endast tillgängligt för funktioner 2.x-körningen. Det är också för närvarande endast tillgängliga för .NET-språk.

I .NET-språk, den här informationen är tillgänglig som en [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal?view=netstandard-2.0). ClaimsPrincipal är tillgänglig som en del av begärandets kontext som visas i följande exempel:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Du kan också kan ClaimsPrincipal bara ingå som en extra parameter i i funktionssignaturen:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}

```

### <a name="authorization-keys"></a>Auktoriseringsregel för nycklar

Functions kan du använda för att göra det svårare att komma åt din HTTP-slutpunkter för funktionen under utveckling.  En standard HTTP-utlösare kan kräva sådana en API-nyckel finnas i begäran. 

> [!IMPORTANT]
> När nycklar kan hjälpa Förvräng HTTP-slutpunkter under utvecklingen, är men inte avsedda som ett sätt att skydda en HTTP-utlösare i produktion. Mer information finns i [skydda en HTTP-slutpunkt i produktion](#secure-an-http-endpoint-in-production).

> [!NOTE]
> Webhook-leverantörer kan använda nycklar för att godkänna begäranden på flera olika sätt beroende på vad providern har stöd i Functions 1.x-körningen. Detta beskrivs i [Webhooks och nycklar](#webhooks-and-keys). Version 2.x-körningen innehåller inte inbyggt stöd för webhook-leverantörer.

Det finns två typer av nycklar:

* **Värdnycklar**: De här nycklarna är gemensamma för alla funktioner i funktionsappen. När det används som en API-nyckel, de här alternativen kan åtkomst till en funktion i funktionsappen.
* **Funktionstangenter**: De här nycklarna gäller endast för specifika funktioner som de definieras. När det används som en API-nyckel, Tillåt dessa endast åtkomst till funktionen.

Varje nyckel heter referens och det är en standardnyckel (med namnet ”standard”) på funktionen och värden. Funktionstangenter företräde framför värdnycklar. När två nycklar har definierats med samma namn, används alltid funktionsnyckel.

Varje funktionsapp har även en särskild **huvudnyckeln**. Den här nyckeln är en värdnyckel med namnet `_master`, som tillhandahåller administrativ åtkomst till körningsmiljön API: er. Den här nyckeln kan inte återkallas. När du ställer in en auktorisering för `admin`, begäranden måste använda huvudnyckeln; andra nyckeln resulterar i Auktoriseringen misslyckades.

> [!CAUTION]  
> På grund av de utökade behörigheterna i din funktionsapp som beviljats av huvudnyckeln bör du inte dela den här nyckeln med tredje part eller distribuera den i interna klientprogram. Var försiktig när du väljer administratören åtkomstnivå.

### <a name="obtaining-keys"></a>Erhålla nycklar

Nycklar lagras som en del av din funktionsapp i Azure och krypteras på plats. Om du vill visa dina nycklar, skapa nya, eller distribuera nycklarna till nya värden, navigerar du till något av dina HTTP-utlösta funktioner i den [Azure-portalen](https://portal.azure.com) och välj **hantera**.

![Hantera funktionstangenterna i portalen.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Det finns inga stöds API för att programmässigt erhålla funktionstangenter.

### <a name="api-key-authorization"></a>API: et auktoriseringsprincipen

De flesta http-utlösaren mallar kräver en API-nyckel i begäran. HTTP-begäran ser så normalt ut som följande URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Nyckeln kan ingå i en fråga variabel med namnet `code`, precis som ovan. Det kan också ingå i en `x-functions-key` HTTP-huvud. Värdet för nyckeln kan vara valfri funktionsnyckel som definierats för funktionen eller valfri tangent för värden.

Du kan tillåta anonyma begäranden som inte kräver nycklar. Du kan också kräva att huvudnyckeln kan användas. Du kan ändra standardnivån för auktorisering med hjälp av den `authLevel` -egenskapen i bindningen JSON. Mer information finns i [utlösare - konfigurationen](#trigger---configuration).

> [!NOTE]
> När du kör funktioner lokalt kan inaktiveras auktorisering, oavsett den angivna autentiseringstypen inställningen. Efter publicering till Azure, den `authLevel` inställningen i utlösaren tillämpas.



### <a name="secure-an-http-endpoint-in-production"></a>Skydda en HTTP-slutpunkt i produktion

För att fullständigt skydda dina slutpunkter för funktionen i produktion, bör du implementering av en av följande alternativ för funktionen säkerhet på radnivå app:

* Aktivera App Service-autentisering / auktorisering för din funktionsapp. App Service-plattformen kan använda Azure Active Directory (AAD) och flera Identitetsproviders från tredje part för att autentisera klienter. Du kan använda detta för att implementera anpassade regler för dina funktioner och du kan arbeta med användarinformation från funktionskoden. Mer information finns i [autentisering och auktorisering i Azure App Service](../app-service/overview-authentication-authorization.md) och [arbeta med klient-identiteter](#working-with-client-identities).

* Använd Azure API Management (APIM) för att autentisera begäranden. APIM erbjuder en mängd olika API säkerhetsalternativ för inkommande begäranden. Mer information finns i [API Management autentiseringsprinciper](../api-management/api-management-authentication-policies.md). Med APIM på plats kan du konfigurera funktionsappen för att godkänna begäranden endast från IP-adressen för din APIM-instansen. Mer information finns i [IP-adressbegränsningar](ip-addresses.md#ip-address-restrictions).

* Distribuera appen till en Azure App Service Environment (ASE). ASE ger en dedikerad värdmiljö där du kan köra dina funktioner. ASE kan du konfigurera en enda klientdelsgateway som du kan använda för att autentisera alla inkommande begäranden. Mer information finns i [konfigurera Web Application Firewall (WAF) för App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

När du använder någon av följande metoder för säkerhet på radnivå app av funktionen, bör du ange HTTP-utlöst funktion-autentisering till `anonymous`.

### <a name="webhooks"></a>Webhooks

> [!NOTE]
> Webhook-läge är endast tillgänglig för version 1.x av Functions-körning. Den här ändringen gjordes att förbättra prestanda för HTTP-utlösare i version 2.x.

I version 1.x, webhook mallar tillhandahåller ytterligare verifiering för webhook-nyttolaster. I version 2.x, grundläggande HTTP-utlösaren fortfarande fungerar och är den rekommenderade metoden för webhooks. 

#### <a name="github-webhooks"></a>GitHub webhooks

För att svara på GitHub webhooks, först skapa din funktion med en HTTP-utlösare och ange den **webHookType** egenskap `github`. Kopiera sedan dess URL och API-nyckeln i den **Lägg till webhook** sidan i din GitHub-lagringsplats. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Slack webhooks

Slack webhook genererar en token för dig i stället för där du kan ange den, så måste du konfigurera en funktionsspecifika nyckel med token från Slack. Se [auktoriseringsregel nycklar](#authorization-keys).

### <a name="webhooks-and-keys"></a>Webhooks och nycklar

Webhook-auktorisering hanteras av webhook mottagare komponent, en del av HTTP-utlösare och mekanismen varierar beroende på typ av webhook. Varje metod förlitar sig på en nyckel. Som standard används funktionsnyckel med namnet ”standard”. Konfigurera webhook-providern för att skicka nyckelnamnet med förfrågan i något av följande sätt om du vill använda en annan nyckel:

* **Frågesträng**: Providern skickar nyckelnamnet i den `clientid` frågesträngparametern, till exempel `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Begärandehuvud**: Providern skickar nyckelnamnet i den `x-functions-clientid` rubrik.

## <a name="trigger---limits"></a>Utlösare - gränser

HTTP-begäran längden är begränsad till 100 MB (104,857,600 byte) och URL-längd är begränsad till 4 KB (4 096 byte). Dessa gränser anges av den `httpRuntime` element i en runtime [Web.config-filen](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config).

Om en funktion som använder HTTP-utlösaren inte slutförs inom cirka 2,5 minuter, gateway att gälla och returnera ett HTTP 502-fel. Funktionen fortsätter att köras, men kommer inte att returnera ett HTTP-svar. För långvariga funktioner rekommenderar vi att du följer async mönster och returnera en plats där du kan pinga status för begäran. Information om hur lång tid en funktion kan köra finns i [skalning och värdtjänster - standardförbrukningsplanen](functions-scale.md#consumption-plan).

## <a name="trigger---hostjson-properties"></a>Utlösare - host.json egenskaper

Den [host.json](functions-host-json.md) filen innehåller inställningar som styr beteendet för HTTP-utlösare.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Resultat

Använd HTTP-utdatabindning svarar på http-begäran avsändaren. Den här bindningen kräver en HTTP-utlösare och kan du anpassa svaret som är associerade med utlösarens begäran. Om en HTTP-utdatabindning har inte angetts en HTTP-utlösare returnerar HTTP 200 OK utan en brödtext i Functions 1.x eller HTTP 204 inget innehåll utan en brödtext i Functions 2.x.

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil. Det finns inga attributegenskaper som motsvarar dessa för C#-klassbibliotek, *function.json* egenskaper.

|Egenskap  |Beskrivning  |
|---------|---------|
| **typ** |Måste anges till `http`. |
| **riktning** | Måste anges till `out`. |
|**namn** | Variabelnamnet som används i Funktionskoden för svaret, eller `$return` att använda det returnera värdet. |

## <a name="output---usage"></a>Utdata - användning

Använda språk-standard svar mönster för att skicka en HTTP-svar. I C# eller C#-skript, se funktionen returtyp `IActionResult` eller `Task<IActionResult>`. I C#, krävs inte ett returvärde attribut.

Till exempel svar, se den [utlösaren exempel](#trigger---example).

## <a name="next-steps"></a>Nästa steg

[Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
