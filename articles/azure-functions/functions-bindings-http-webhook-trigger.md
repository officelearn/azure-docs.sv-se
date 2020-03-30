---
title: HTTP-utlösare för Azure Functions
description: Lär dig hur du anropar en Azure-funktion via HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 045f3ccdc8dc09bf657ab39ce15a0d0524c73fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277601"
---
# <a name="azure-functions-http-trigger"></a>HTTP-utlösare för Azure Functions

Med HTTP-utlösaren kan du anropa en funktion med en HTTP-begäran. Du kan använda en HTTP-utlösare för att skapa serverlösa API:er och svara på webhooks.

Standardreturvärdet för en HTTP-utlöst funktion är:

- `HTTP 204 No Content`med en tom kropp i Funktioner 2.x och högre
- `HTTP 200 OK`med en tom kropp i Funktioner 1.x

Om du vill ändra HTTP-svaret konfigurerar du en [utdatabindning](./functions-bindings-http-webhook-output.md).

Mer information om HTTP-bindningar finns i [översikts-](./functions-bindings-http-webhook.md) och [utdatabindningsreferensen](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som söker efter en `name` parameter antingen i frågesträngen eller brödtexten i HTTP-begäran. Observera att returvärdet används för utdatabindningen, men ett returvärdeattribut krävs inte.

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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en utlösarbindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen söker efter `name` en parameter antingen i frågesträngen eller brödtexten i HTTP-begäran.

Här är *filen function.json:*

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är C # skriptkod `HttpRequest`som binder till:

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

Du kan binda till ett `HttpRequest`anpassat objekt i stället för . Det här objektet skapas från brödtexten för begäran och tolkas som JSON. På samma sätt kan en typ skickas till HTTP-svarsutdatabindningen `200` och returneras som svarstext, tillsammans med en statuskod.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en utlösarbindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen söker efter `name` en parameter antingen i frågesträngen eller brödtexten i HTTP-begäran.

Här är *filen function.json:*

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

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

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en utlösarbindning i en *function.json-fil* och en [Python-funktion](functions-reference-python.md) som använder bindningen. Funktionen söker efter `name` en parameter antingen i frågesträngen eller brödtexten i HTTP-begäran.

Här är *filen function.json:*

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är Python-koden:

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

# <a name="java"></a>[Java](#tab/java)

* [Läs parametern från frågesträngen](#read-parameter-from-the-query-string)
* [Läsa brödtext från en POST-begäran](#read-body-from-a-post-request)
* [Läs parametern från en väg](#read-parameter-from-a-route)
* [Läs POJO-brödtexten från en POST-begäran](#read-pojo-body-from-a-post-request)

I följande exempel visas HTTP-utlösarbindningen.

#### <a name="read-parameter-from-the-query-string"></a>Läs parametern från frågesträngen

I det här exemplet `id`visas en parameter med namnet , från frågesträngen, och den `application/json`används för att skapa ett JSON-dokument som returneras till klienten, med innehållstyp .

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

#### <a name="read-body-from-a-post-request"></a>Läsa brödtext från en POST-begäran

I det här exemplet visas brödtexten för en POST-begäran, som en `String`och använder `application/json`den för att skapa ett JSON-dokument som returneras till klienten, med innehållstyp .

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

#### <a name="read-parameter-from-a-route"></a>Läs parametern från en väg

I det här exemplet visas `id`en obligatorisk `name` parameter med namnet , och en valfri parameter från vägsökvägen, `application/json`och de används för att skapa ett JSON-dokument som returneras till klienten, med innehållstyp . T

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

#### <a name="read-pojo-body-from-a-post-request"></a>Läs POJO-brödtexten från en POST-begäran

Här är koden `ToDoItem` för klassen, som refereras i det här exemplet:

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

I det här exemplet läses brödtexten för en POST-begäran. Den begärande brödtexten av serialiseras automatiskt till ett `ToDoItem` objekt och `application/json`returneras till klienten, med innehållstyp . Parametern `ToDoItem` serialiseras av funktionskörningen när den `body` tilldelas `HttpMessageResponse.Builder` egenskapen för klassen.

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

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

I [C#-klassbibliotek](functions-dotnet-class-library.md) och `HttpTrigger` Java är attributet tillgängligt för att konfigurera funktionen.

Du kan ange auktoriseringsnivå och tillåtna HTTP-metoder i attributkonstruktorparametrar, webhook-typ och en vägmall. Mer information om dessa inställningar finns i [konfigurationen](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Det här exemplet visar hur du använder [attributet HttpTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs)

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Ett fullständigt exempel finns i [utlösarexempeln](#example).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Det här exemplet visar hur du använder [attributet HttpTrigger.](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java)

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Ett fullständigt exempel finns i [utlösarexempeln](#example).

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `HttpTrigger` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
| **Typ** | Saknas| Obligatoriskt - måste `httpTrigger`ställas in på . |
| **riktning** | Saknas| Obligatoriskt - måste `in`ställas in på . |
| **Namn** | Saknas| Obligatoriskt - variabelnamnet som används i funktionskoden för förfråga eller begära brödtext. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Bestämmer vilka nycklar, om några, som måste finnas på begäran för att kunna anropa funktionen. Auktoriseringsnivån kan vara ett av följande värden: <ul><li><code>anonymous</code>&mdash;Ingen API-nyckel krävs.</li><li><code>function</code>&mdash;En funktionsspecifik API-nyckel krävs. Detta är standardvärdet om ingen anges.</li><li><code>admin</code>&mdash;Huvudnyckeln krävs.</li></ul> Mer information finns i avsnittet om [auktoriseringsnycklar](#authorization-keys). |
| **Metoder** |**Metoder** | En matris med HTTP-metoder som funktionen svarar på. Om inget anges svarar funktionen på alla HTTP-metoder. Se [anpassa HTTP-slutpunkten](#customize-the-http-endpoint). |
| **Rutt** | **Routa** | Definierar flödesmallen och styr vilken begäran webbadresser som din funktion svarar på. Standardvärdet om inget anges `<functionname>`är . Mer information finns [i Anpassa HTTP-slutpunkten](#customize-the-http-endpoint). |
| **webHookType (olikartade)** | **WebHookType (olikartade)** | _Stöds endast för körningen version 1.x._<br/><br/>Konfigurerar HTTP-utlösaren så att den fungerar som [en webhook-mottagare](https://en.wikipedia.org/wiki/Webhook) för den angivna providern. Ange inte egenskapen om du anger den `methods` här egenskapen. Webhook-typen kan vara något av följande värden:<ul><li><code>genericJson</code>&mdash;En webhook-slutpunkt för allmänt ändamål utan logik för en viss provider. Den här inställningen begränsar begäranden till endast `application/json` de som använder HTTP POST och med innehållstypen.</li><li><code>github</code>&mdash;Funktionen svarar på [GitHub webhooks](https://developer.github.com/webhooks/). Använd inte egenskapen _authLevel_ med GitHub webhooks. Mer information finns i avsnittet GitHub webhooks senare i den här artikeln.</li><li><code>slack</code>&mdash;Funktionen svarar på [Slack webhooks](https://api.slack.com/outgoing-webhooks). Använd inte egenskapen _authLevel_ med Slack webhooks. Mer information finns i avsnittet Slack webhooks senare i den här artikeln.</li></ul>|

## <a name="payload"></a>Nyttolast

Indatatypen utlösare `HttpRequest` deklareras som antingen eller en anpassad typ. Om du `HttpRequest`väljer får du fullständig åtkomst till begärandeobjektet. För en anpassad typ försöker körningen tolka JSON-begärandetexten för att ange objektegenskaperna.

## <a name="customize-the-http-endpoint"></a>Anpassa HTTP-slutpunkten

Som standard när du skapar en funktion för en HTTP-utlösare är funktionen adresserbar med en väg i formuläret:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Du kan anpassa den `route` här vägen med hjälp av den valfria egenskapen på HTTP-utlösarens indatabindning. Som ett exempel definierar följande *function.json-fil* en `route` egenskap för en HTTP-utlösare:

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

Med den här konfigurationen är funktionen nu adresserbar med följande väg i stället för den ursprungliga vägen.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Den här konfigurationen gör att funktionskoden kan stödja två parametrar i adressen, _kategorin_ och _id._

# <a name="c"></a>[C#](#tab/csharp)

Du kan använda valfri [begränsning av webb-API-flöde](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) med dina parametrar. Följande C#-funktionskod använder sig av båda parametrarna.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Du kan använda valfri [begränsning av webb-API-flöde](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) med dina parametrar. Följande C#-funktionskod använder sig av båda parametrarna.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I Nod tillhandahåller körtiden Funktioner för `context` begäran från objektet. Mer information finns i exemplet med [JavaScript-utlösare](#example).

I följande exempel visas hur `context.bindingData`du läser flödesparametrar från .

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

Funktionskörningskontexten exponeras `func.HttpRequest`via en parameter som deklarerats som . Med den här instansen kan en funktion komma åt dataruttparametrar, frågesträngvärden och metoder som gör att du kan returnera HTTP-svar.

När de har definierats är flödesparametrarna tillgängliga för funktionen genom att anropa `route_params` metoden.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

Funktionskörningskontexten är `HttpTrigger` egenskaper som deklarerats i attributet. Med attributet kan du definiera flödesparametrar, auktoriseringsnivåer, HTTP-verb och inkommande begärandeinstans.

Flödesparametrar `HttpTrigger` definieras via attributet.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Som standard föregås alla funktionsvägar med *api*. Du kan också anpassa eller ta `http.routePrefix` bort prefixet med egenskapen i [filen host.json.](functions-host-json.md) I följande exempel *api* tas api-vägprefixet bort med hjälp av en tom sträng för prefixet i *filen host.json.*

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

## <a name="using-route-parameters"></a>Använda ruttparametrar

Flödesparametrar som `route` definierade en funktions mönster är tillgängliga för varje bindning. Om du till exempel har `"route": "products/{id}"` en väg definierad som kan `{id}` en tabelllagringsbindning använda parameterns värde i bindningskonfigurationen.

Följande konfiguration visar `{id}` hur parametern skickas till `rowKey`bindningens .

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>Arbeta med klientidentiteter

Om din funktionsapp använder [App Service Authentication /Auktorisering](../app-service/overview-authentication-authorization.md)kan du visa information om autentiserade klienter från din kod. Den här informationen är tillgänglig som [begäran rubriker injiceras av plattformen](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Du kan också läsa den här informationen från bindningsdata. Den här funktionen är endast tillgänglig för funktionskörningen i 2.x och högre. Den är för närvarande endast tillgänglig för .NET-språk.

# <a name="c"></a>[C#](#tab/csharp)

Information om autentiserade klienter finns som [en ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal är tillgänglig som en del av begärandekontexten som visas i följande exempel:

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

Alternativt kan ClaimsPrincipal helt enkelt inkluderas som en ytterligare parameter i funktionssignaturen:

```csharp
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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Information om autentiserade klienter finns som [en ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal är tillgänglig som en del av begärandekontexten som visas i följande exempel:

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

Alternativt kan ClaimsPrincipal helt enkelt inkluderas som en ytterligare parameter i funktionssignaturen:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Den autentiserade användaren är tillgänglig via [HTTP-huvuden](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

Den autentiserade användaren är tillgänglig via [HTTP-huvuden](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="java"></a>[Java](#tab/java)

Den autentiserade användaren är tillgänglig via [HTTP-huvuden](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

## <a name="authorization-keys"></a>Auktoriseringsnycklar

Med funktioner kan du använda tangenter för att göra det svårare att komma åt http-funktionens slutpunkter under utvecklingen.  Om inte HTTP-auktoriseringsnivån för `anonymous`en HTTP-utlöst funktion är inställd på måste begäranden innehålla en API-nyckel i begäran. 

> [!IMPORTANT]
> Även om nycklar kan hjälpa till att fördunkla HTTP-slutpunkterna under utvecklingen, är de inte avsedda som ett sätt att skydda en HTTP-utlösare i produktionen. Mer information finns [i Skydda en HTTP-slutpunkt i produktionen](#secure-an-http-endpoint-in-production).

> [!NOTE]
> I funktionsväxlingen 1.x kan webhook-leverantörer använda nycklar för att auktorisera begäranden på olika sätt, beroende på vad leverantören stöder. Detta är täckt av [Webhooks och nycklar](#webhooks-and-keys). Funktioner runtime i version 2.x och högre innehåller inte inbyggt stöd för webhook-leverantörer.

#### <a name="authorization-scopes-function-level"></a>Auktoriseringsscope (funktionsnivå)

Det finns två auktoriseringsscope för nycklar på funktionsnivå:

* **Funktion**: Dessa nycklar gäller endast för de specifika funktioner under vilka de definieras. När den används som en API-nyckel tillåter dessa endast åtkomst till den funktionen.

* **Värd:** Nycklar med värdomfattning kan användas för att komma åt alla funktioner i funktionsappen. När de används som EN API-nyckel, dessa tillåta tillträde till någon funktion inom funktionsappen. 

Varje nyckel har namnet som referens och det finns en standardnyckel (med namnet "standard") på funktions- och värdnivå. Funktionstangenterna har företräde framför värdnycklar. När två nycklar definieras med samma namn används alltid funktionsnyckeln.

#### <a name="master-key-admin-level"></a>Huvudnyckel (admin-nivå) 

Varje funktionsapp har också en `_master`värdnyckel på administratörsnivå med namnet . Förutom att ge åtkomst på värdnivå till alla funktioner i appen ger huvudnyckeln också administrativ åtkomst till tids-REST-API:erna. Den här nyckeln kan inte återkallas. När du anger en `admin`auktoriseringsnivå för måste begäranden använda huvudnyckeln. andra viktiga resulterar i auktoriseringsfel.

> [!CAUTION]  
> På grund av de förhöjda behörigheterna i funktionsappen som beviljas av huvudnyckeln bör du inte dela den här nyckeln med tredje part eller distribuera den i inbyggda klientprogram. Var försiktig när du väljer auktoriseringsnivå för administratörer.

## <a name="obtaining-keys"></a>Hämta nycklar

Nycklar lagras som en del av din funktionsapp i Azure och krypteras i vila. Om du vill visa dina nycklar, skapa nya eller rulla nycklar till nya värden navigerar du till en av dina HTTP-utlösta funktioner i [Azure-portalen](https://portal.azure.com) och väljer **Hantera**.

![Hantera funktionstangenter i portalen.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Du kan hämta funktionsnycklar programmässigt med hjälp av [API:er för nyckelhantering](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

## <a name="api-key-authorization"></a>API-nyckel auktorisering

De flesta HTTP-utlösarmallar kräver en API-nyckel i begäran. Så din HTTP-begäran ser normalt ut som följande webbadress:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Nyckeln kan inkluderas i en `code`frågesträngvariabel med namnet , enligt ovan. Det kan också inkluderas i ett `x-functions-key` HTTP-huvud. Värdet på nyckeln kan vara valfri funktionsnyckel som definierats för funktionen, eller någon värdnyckel.

Du kan tillåta anonyma förfrågningar som inte kräver nycklar. Du kan också kräva att huvudnyckeln används. Du ändrar standardauktoriseringsnivån genom att använda egenskapen `authLevel` i bindningen JSON. Mer information finns i [Utlösare - konfiguration](#configuration).

> [!NOTE]
> När du kör funktioner lokalt inaktiveras auktoriseringen oavsett den angivna auktoriseringsnivåinställningen. När du har `authLevel` publicerat till Azure tillämpas inställningen i utlösaren. Nycklar krävs fortfarande när du kör [lokalt i en behållare](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Skydda en HTTP-slutpunkt i produktionen

Om du vill skydda dina funktionsslutpunkter helt i produktionen bör du överväga att implementera något av följande säkerhetsalternativ på funktionsappnivå:

* Aktivera App Service-autentisering/auktorisering för din funktionsapp. Med App Service-plattformen kan du använda Azure Active Directory (AAD) och flera identitetsleverantörer från tredje part för att autentisera klienter. Du kan använda den här strategin för att implementera anpassade auktoriseringsregler för dina funktioner och du kan arbeta med användarinformation från funktionskoden. Mer information finns [i Autentisering och auktorisering i Azure App Service](../app-service/overview-authentication-authorization.md) och Arbeta med [klientidentiteter](#working-with-client-identities).

* Använd Azure API Management (APIM) för att autentisera begäranden. APIM innehåller en mängd olika API-säkerhetsalternativ för inkommande begäranden. Mer information finns i [API Management-principer](../api-management/api-management-authentication-policies.md)för autentisering . Med APIM på plats kan du konfigurera din funktionsapp så att den bara accepterar begäranden från IP-adressen för APIM-instansen. Mer information finns i [BEGRÄNSNINGAR FÖR IP-adresser](ip-addresses.md#ip-address-restrictions).

* Distribuera din funktionsapp till en Azure App Service Environment (ASE). ASE erbjuder en dedikerad värdmiljö där du kan köra dina funktioner. MED ASE kan du konfigurera en enda frontend-gateway som du kan använda för att autentisera alla inkommande begäranden. Mer information finns i [Konfigurera en brandvägg för webbprogram (WAF) för App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

När du använder någon av dessa säkerhetsmetoder på funktionsappnivå bör `anonymous`du ställa in den HTTP-utlösta funktionsauktoriseringsnivån till .

## <a name="webhooks"></a>Webhooks

> [!NOTE]
> Webhook-läget är endast tillgängligt för version 1.x av funktionskörningen. Den här ändringen gjordes för att förbättra prestanda för HTTP-utlösare i version 2.x och högre.

I version 1.x ger webhook-mallar ytterligare validering för webhook-nyttolaster. I version 2.x och högre fungerar bas-HTTP-utlösaren fortfarande och är den rekommenderade metoden för webhooks. 

### <a name="github-webhooks"></a>GitHub webhooks

Om du vill svara på GitHub-webbkrokar skapar du först funktionen med `github`en HTTP-utlösare och anger egenskapen **webHookType** till . Kopiera sedan url-och API-nyckeln till sidan **Lägg till webhook** i GitHub-databasen. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Slack webhooks

Slack webhook genererar en token åt dig i stället för att låta dig ange den, så du måste konfigurera en funktionsspecifik nyckel med token från Slack. Se [auktoriseringsnycklar](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhooks och nycklar

Webhook-auktorisering hanteras av webhook-mottagaren, en del av HTTP-utlösaren, och mekanismen varierar beroende på webhook-typ. Varje mekanism är beroende av en nyckel. Som standard används funktionsnyckeln "standard". Om du vill använda en annan nyckel konfigurerar du webhook-providern så att nyckelnamnet skickas med begäran på något av följande sätt:

* **Frågesträng:** Providern skickar nyckelnamnet `clientid` i frågesträngparametern, till exempel `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Begärandehuvud:** Providern skickar nyckelnamnet i `x-functions-clientid` huvudet.

## <a name="limits"></a>Begränsningar

HTTP-begärandens längd är begränsad till 100 MB (104 857 600 byte) och URL-längden är begränsad till 4 kB (4 096 byte). Dessa gränser anges av `httpRuntime` elementet i körningens [web.config-fil](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config).

Om en funktion som använder HTTP-utlösaren inte slutförs inom 230 sekunder kommer [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) att ta timeout och returnera ett HTTP 502-fel. Funktionen fortsätter att köras men kan inte returnera ett HTTP-svar. För tidskrävande funktioner rekommenderar vi att du följer asynkronmönster och returnerar en plats där du kan pinga statusen för begäran. Information om hur länge en funktion kan köras finns i [Skala och vara värd - Förbrukningsplan](functions-scale.md#timeout).


## <a name="next-steps"></a>Nästa steg

- [Returnera ett HTTP-svar från en funktion](./functions-bindings-http-webhook-output.md)
