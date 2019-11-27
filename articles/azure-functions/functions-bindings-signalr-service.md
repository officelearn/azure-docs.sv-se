---
title: Bindningar för Azure Functions SignalR-tjänst
description: Förstå hur du använder signalerar tjänst bindningar med Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 4c7d5d4d8777fee445585b43b58ceb261176b7f4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231026"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR Service-bindningar för Azure Functions

Den här artikeln förklarar hur du autentiserar och skickar meddelanden i real tid till klienter som är anslutna till [Azure SignalR service](https://azure.microsoft.com/services/signalr-service/) med hjälp av SignalR tjänst bindningar i Azure Functions. Azure Functions stöder indata och utgående bindningar för SignalR-tjänsten.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Signalerar tjänst bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet-paketet, version 1. *. Käll koden för paketet finns i GitHub-lagringsplatsen [Azure-Functions-signalrservice-Extensions](https://github.com/Azure/azure-functions-signalrservice-extension) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java-anteckningar

Om du vill använda Signalerare för signalering i Java-funktioner måste du lägga till ett beroende till artefakten *Azure-Functions Java-Library-signaler* (version 1,0 eller senare) till Pom. xml.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Kontrol lera att du använder version 2.4.419 eller högre av Azure Functions Core Tools (värd version 2.0.12332) om du vill använda bindningar för SignalR tjänsten i Java.

## <a name="using-signalr-service-with-azure-functions"></a>Använda SignalR-tjänsten med Azure Functions

Mer information om hur du konfigurerar och använder SignalR-tjänsten och Azure Functions tillsammans finns i [Azure Functions utveckling och konfiguration med Azure SignalR-tjänsten](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>Signals anslutnings information ingångs bindning

Innan en klient kan ansluta till Azure SignalR service måste den Hämta tjänstens slut punkts-URL och en giltig åtkomsttoken. *SignalRConnectionInfo* -databindningen genererar URL: en för SignalR-tjänstens slut punkt och en giltig token som används för att ansluta till tjänsten. Eftersom token är tidsbegränsad och kan användas för att autentisera en viss användare för en anslutning, ska du inte cachelagra token eller dela den mellan klienter. En HTTP-utlösare som använder denna bindning kan användas av klienter för att hämta anslutnings informationen.

Se exempel språkspecifika:

* [2xC#](#2x-c-input-examples)
* [2. x Java Script](#2x-javascript-input-examples)
* [2. x Java](#2x-java-input-examples)

Mer information om hur den här bindningen används för att skapa en "Negotiate"-funktion som kan användas av en Signals-klient-SDK finns i [artikeln Azure Functions utveckling och konfiguration](../azure-signalr/signalr-concept-serverless-development-config.md) i dokumentationen för SignalR-tjänstens begrepp.

### <a name="2x-c-input-examples"></a>2. x C# -ingångs exempel

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som hämtar anslutnings information för signaler med hjälp av den inkommande bindningen och returnerar den över http.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Autentiserade tokens

Om funktionen utlöses av en autentiserad klient kan du lägga till ett användar-ID-anspråk till den genererade token. Du kan enkelt lägga till autentisering i en Function-app med hjälp av [App Service autentisering](../app-service/overview-authentication-authorization.md).

App Service autentisering anger HTTP-huvuden med namnet `x-ms-client-principal-id` och `x-ms-client-principal-name` som innehåller den autentiserade användarens huvud-ID respektive namn. Du kan ange egenskapen `UserId` för bindningen till värdet från endera sidhuvudet med ett [bindnings uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}`. 

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

### <a name="2x-javascript-input-examples"></a>2. x JavaScript-inmatade exempel

I följande exempel visas en signal information om en Signals-anslutnings bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen för att returnera anslutnings informationen.

Här är bindnings data i *Function. JSON* -filen:

Exempel funktion. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Här är JavaScript-kod:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>Autentiserade tokens

Om funktionen utlöses av en autentiserad klient kan du lägga till ett användar-ID-anspråk till den genererade token. Du kan enkelt lägga till autentisering i en Function-app med hjälp av [App Service autentisering](../app-service/overview-authentication-authorization.md).

App Service autentisering anger HTTP-huvuden med namnet `x-ms-client-principal-id` och `x-ms-client-principal-name` som innehåller den autentiserade användarens huvud-ID respektive namn. Du kan ange egenskapen `userId` för bindningen till värdet från endera sidhuvudet med ett [bindnings uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}`. 

Exempel funktion. JSON:

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

Här är JavaScript-kod:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>2. x Java-inmatade exempel

I följande exempel visas en [Java-funktion](functions-reference-java.md) som hämtar anslutnings information för signaler med hjälp av den inkommande bindningen och returnerar den över http.

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

#### <a name="authenticated-tokens"></a>Autentiserade tokens

Om funktionen utlöses av en autentiserad klient kan du lägga till ett användar-ID-anspråk till den genererade token. Du kan enkelt lägga till autentisering i en Function-app med hjälp av [App Service autentisering](../app-service/overview-authentication-authorization.md).

App Service autentisering anger HTTP-huvuden med namnet `x-ms-client-principal-id` och `x-ms-client-principal-name` som innehåller den autentiserade användarens huvud-ID respektive namn. Du kan ange egenskapen `UserId` för bindningen till värdet från endera sidhuvudet med ett [bindnings uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}`.

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

## <a name="signalr-output-binding"></a>Utgående bindning för SignalR

Använd *signalens* utgående bindning för att skicka ett eller flera meddelanden med Azure SignalR-tjänsten. Du kan sända ett meddelande till alla anslutna klienter, eller så kan du bara sända det till anslutna klienter som har autentiserats för en specifik användare.

Du kan också använda den för att hantera de grupper som en användare tillhör.

Se exempel språkspecifika:

* [2xC#](#2x-c-send-message-output-examples)
* [2. x Java Script](#2x-javascript-send-message-output-examples)
* [2. x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2. x C# skicka meddelanden utdata exempel

#### <a name="broadcast-to-all-clients"></a>Sänd till alla klienter

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som skickar ett meddelande med hjälp av utgående bindning till alla anslutna klienter. `Target` är namnet på den metod som ska anropas på varje klient. Egenskapen `Arguments` är en matris med noll eller flera objekt som ska skickas till klient metoden.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>Skicka till en användare

Du kan bara skicka ett meddelande till anslutningar som har autentiserats för en användare genom att ange egenskapen `UserId` i signal meddelandet.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>Skicka till en grupp

Du kan bara skicka ett meddelande till anslutningar som har lagts till i en grupp genom att ange egenskapen `GroupName` i signal meddelandet.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>2. x C# grupp hantering utdata exempel

Med SignalR-tjänsten kan användare läggas till i grupper. Meddelanden kan sedan skickas till en grupp. Du kan använda `SignalRGroupAction`-klassen med `SignalR` utgående bindning för att hantera en användares grupp medlemskap.

#### <a name="add-user-to-a-group"></a>Lägg till användare i en grupp

I följande exempel läggs en användare till i en grupp.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Ta bort användare från en grupp

I följande exempel tas en användare bort från en grupp.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Du måste ha konfigurerat autentiseringsinställningarna i Azure Functions för att få `ClaimsPrincipal` korrekt kopplad.

### <a name="2x-javascript-send-message-output-examples"></a>2. x Java Script skicka meddelande utdata exempel

#### <a name="broadcast-to-all-clients"></a>Sänd till alla klienter

I följande exempel visas en utgående bindning för signalering i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen för att skicka ett meddelande med Azure SignalR-tjänsten. Ange utgående bindning till en matris med ett eller flera signaler meddelande. Ett signal meddelande består av en `target`-egenskap som anger namnet på metoden som ska anropas på varje klient och en `arguments` egenskap som är en matris med objekt som ska skickas till klient metoden som argument.

Här är bindnings data i *Function. JSON* -filen:

Exempel funktion. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Här är JavaScript-kod:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>Skicka till en användare

Du kan bara skicka ett meddelande till anslutningar som har autentiserats för en användare genom att ange egenskapen `userId` i signal meddelandet.

*Function. JSON* är oförändrad. Här är JavaScript-kod:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>Skicka till en grupp

Du kan bara skicka ett meddelande till anslutningar som har lagts till i en grupp genom att ange egenskapen `groupName` i signal meddelandet.

*Function. JSON* är oförändrad. Här är JavaScript-kod:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>2. x JavaScript-utdata exempel för Java Script Group Management

Med SignalR-tjänsten kan användare läggas till i grupper. Meddelanden kan sedan skickas till en grupp. Du kan använda `SignalR` utgående bindning för att hantera en användares grupp medlemskap.

#### <a name="add-user-to-a-group"></a>Lägg till användare i en grupp

I följande exempel läggs en användare till i en grupp.

*function. JSON*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index. js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Ta bort användare från en grupp

I följande exempel tas en användare bort från en grupp.

*function. JSON*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index. js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2. x Java-exempel på sändnings meddelande

#### <a name="broadcast-to-all-clients"></a>Sänd till alla klienter

I följande exempel visas en [Java-funktion](functions-reference-java.md) som skickar ett meddelande med hjälp av utgående bindning till alla anslutna klienter. `target` är namnet på den metod som ska anropas på varje klient. Egenskapen `arguments` är en matris med noll eller flera objekt som ska skickas till klient metoden.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>Skicka till en användare

Du kan bara skicka ett meddelande till anslutningar som har autentiserats för en användare genom att ange egenskapen `userId` i signal meddelandet.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>Skicka till en grupp

Du kan bara skicka ett meddelande till anslutningar som har lagts till i en grupp genom att ange egenskapen `groupName` i signal meddelandet.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>2. x Java-exempel på utdata i grupp hantering

Med SignalR-tjänsten kan användare läggas till i grupper. Meddelanden kan sedan skickas till en grupp. Du kan använda `SignalRGroupAction`-klassen med `SignalROutput` utgående bindning för att hantera en användares grupp medlemskap.

#### <a name="add-user-to-a-group"></a>Lägg till användare i en grupp

I följande exempel läggs en användare till i en grupp.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Ta bort användare från en grupp

I följande exempel tas en användare bort från en grupp.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

## <a name="configuration"></a>Konfiguration

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `SignalRConnectionInfo`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**|| Måste anges till `signalRConnectionInfo`.|
|**riktning**|| Måste anges till `in`.|
|**Namn**|| Variabel namn som används i funktions kod för objektet anslutnings information. |
|**hubName**|**HubName**| Värdet måste anges till namnet på den Signals Hub som anslutnings informationen genereras för.|
|**userId**|**UserId**| Valfritt: värdet på det användar-ID-anspråk som ska anges i åtkomst nyckelns token. |
|**connectionStringSetting**|**ConnectionStringSetting**| Namnet på den app-inställning som innehåller signal tjänstens anslutnings sträng (Standardvärdet är "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `SignalR`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**|| Måste anges till `signalR`.|
|**riktning**|| Måste anges till `out`.|
|**Namn**|| Variabel namn som används i funktions kod för objektet anslutnings information. |
|**hubName**|**HubName**| Värdet måste anges till namnet på den Signals Hub som anslutnings informationen genereras för.|
|**connectionStringSetting**|**ConnectionStringSetting**| Namnet på den app-inställning som innehåller signal tjänstens anslutnings sträng (Standardvärdet är "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Functions-utveckling och -konfiguration med Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
