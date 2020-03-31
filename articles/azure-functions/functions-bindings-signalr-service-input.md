---
title: Indatabindning av Azure Functions SignalR-tjänst
description: Lär dig att returnera en Url för SignalR-tjänstslutpunkt och åtkomsttoken i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530268"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Indatabindning av SignalR-tjänst för Azure-funktioner

Innan en klient kan ansluta till Azure SignalR-tjänsten måste den hämta tjänstslutpunkts-URL:en och en giltig åtkomsttoken. Indatabindningen *SignalRConnectionInfo* producerar slutpunkts-URL:en för SignalR-tjänsten och en giltig token som används för att ansluta till tjänsten. Eftersom token är tidsbegränsad och kan användas för att autentisera en viss användare till en anslutning, bör du inte cachelagra token eller dela den mellan klienter. En HTTP-utlösare som använder den här bindningen kan användas av klienter för att hämta anslutningsinformationen.

Mer information om hur den här bindningen används för att skapa en "förhandla"-funktion som kan förbrukas av en SignalR-klient SDK finns i [utvecklings- och konfigurationsartikeln](../azure-signalr/signalr-concept-serverless-development-config.md) för Azure Functions i dokumentationen för SignalR-tjänstbegrepp.

Information om inställnings- och konfigurationsinformation finns i [översikten](functions-bindings-signalr-service.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar SignalR-anslutningsinformation med hjälp av indatabindningen och returnerar den via HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en Indatabindning av SignalR-anslutningsinformation i en *function.json-fil* och en [C#Script-funktion](functions-reference-csharp.md) som använder bindningen för att returnera anslutningsinformationen.

Här är bindningsdata i *filen function.json:*

Exempel function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är C # Script kod:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en Indatabindning av SignalR-anslutningsinformation i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen för att returnera anslutningsinformationen.

Här är bindningsdata i *filen function.json:*

Exempel function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är JavaScript-koden:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en SignalR-anslutningsinformationsinmatningsbindning i en *function.json-fil* och en [Python-funktion](functions-reference-python.md) som använder bindningen för att returnera anslutningsinformationen.

Här är bindningsdata i *filen function.json:*

Exempel function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är Python-koden:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en [Java-funktion](functions-reference-java.md) som hämtar SignalR-anslutningsinformation med hjälp av indatabindningen och returnerar den via HTTP.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>Autentiserade token

Om funktionen utlöses av en autentrad klient kan du lägga till ett användar-ID-anspråk i den genererade token. Du kan enkelt lägga till autentisering i en funktionsapp med [apptjänstautentisering](../app-service/overview-authentication-authorization.md).

App Service Authentication anger `x-ms-client-principal-id` HTTP-huvuden namngivna och `x-ms-client-principal-name` som innehåller den autentiserade användarens klienthuvudnamn respektive namn.

# <a name="c"></a>[C#](#tab/csharp)

Du kan `UserId` ange egenskapen för bindningen till värdet från `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`antingen huvudet med hjälp av ett [bindningsuttryck](./functions-bindings-expressions-patterns.md): eller .

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Du kan `userId` ange egenskapen för bindningen till värdet från `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`antingen huvudet med hjälp av ett [bindningsuttryck](./functions-bindings-expressions-patterns.md): eller .

Exempel function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är C # Script kod:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Du kan `userId` ange egenskapen för bindningen till värdet från `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`antingen huvudet med hjälp av ett [bindningsuttryck](./functions-bindings-expressions-patterns.md): eller .

Exempel function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är JavaScript-koden:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Du kan `userId` ange egenskapen för bindningen till värdet från `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`antingen huvudet med hjälp av ett [bindningsuttryck](./functions-bindings-expressions-patterns.md): eller .

Exempel function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är Python-koden:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Du kan `userId` ange egenskapen för bindningen till värdet från `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`antingen huvudet med hjälp av ett [bindningsuttryck](./functions-bindings-expressions-patterns.md): eller .

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>Nästa steg

- [Skicka SignalR-tjänstmeddelanden (utdatabindning)](./functions-bindings-signalr-service-output.md) 
