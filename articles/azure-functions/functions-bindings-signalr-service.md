---
title: Azure Functions SignalR Service-bindningar
description: Förstå hur du använder SignalR Service-bindningar med Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: ab050a683913e62c6671bf01397e76311a08952b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006786"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR Service-bindningar för Azure Functions

Den här artikeln förklarar hur du autentiserar och skicka realtidsmeddelanden till klienter som är anslutna till [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) med SignalR Service-bindningar i Azure Functions. Azure Functions stöder indata och utdata-bindningar för SignalR Service.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Med SignalR Service-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet-paketet, version 1.*. Källkoden för paketet finns i den [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java-anteckningar

Om du vill använda SignalR Service-anteckningar i Java-funktioner du behöver lägga till ett beroende till den *azure-functions-java-bibliotek-signalr* artefakten (version 1.0 eller högre) till din pom.xml.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Använda SignalR Service-bindningar i Java, kontrollera att du använder version 2.4.419 eller senare för Azure Functions Core Tools (värdversion 2.0.12332).

## <a name="using-signalr-service-with-azure-functions"></a>Med SignalR Service med Azure Functions

Information om hur du konfigurerar och använder SignalR Service och Azure Functions i [Azure Functions-utvecklingen och konfiguration med Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>SignalR anslutning info-indatabindning

Innan en klient kan ansluta till Azure SignalR Service, måste den hämta service slutpunkts-URL och en giltig åtkomsttoken. Den *SignalRConnectionInfo* indatabindning producerar SignalR Service slutpunkts-URL och en giltig token som används för att ansluta till tjänsten. Eftersom token är begränsade och kan användas för att autentisera en viss användare till en anslutning, bör du inte cachelagra token eller dela den mellan klienter. En HTTP-utlösare som använder den här bindningen kan användas av klienter för att hämta anslutningsinformationen.

Se exempel språkspecifika:

* [2.x C#](#2x-c-input-examples)
* [2.x JavaScript](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

Mer information om hur den här bindningen används för att skapa en ”förhandla”-funktion som kan användas av en SignalR-klient SDK finns i den [artikel för utveckling och konfiguration av Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md) i SignalR Service-koncept dokumentation.

### <a name="2x-c-input-examples"></a>2.x C# indata exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skaffar SignalR anslutningsinformation med indatabindningen och återställer den via HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Autentiserad token

Om funktionen utlöses av en autentiserad klient, kan du lägga till ID användaranspråk till genererade token. Du kan enkelt lägga till autentisering i en funktionsapp med hjälp [Apptjänstautentisering](../app-service/overview-authentication-authorization.md).

App Service-autentisering anger HTTP-huvuden med namnet `x-ms-client-principal-id` och `x-ms-client-principal-name` som innehåller den autentiserade användaren huvudnamn klient-ID och namn, respektive. Du kan ange den `UserId` egenskapen om bindningen till värdet från antingen rubrik med hjälp av en [bindning uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}`. 

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

### <a name="2x-javascript-input-examples"></a>inkommande 2.x JavaScript-exempel

I följande exempel visas en SignalR anslutning info indatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen för att returnera anslutningsinformationen.

Här är bindningsdata i den *function.json* fil:

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

Här är JavaScript-kod:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>Autentiserad token

Om funktionen utlöses av en autentiserad klient, kan du lägga till ID användaranspråk till genererade token. Du kan enkelt lägga till autentisering i en funktionsapp med hjälp [Apptjänstautentisering](../app-service/overview-authentication-authorization.md).

App Service-autentisering anger HTTP-huvuden med namnet `x-ms-client-principal-id` och `x-ms-client-principal-name` som innehåller den autentiserade användaren huvudnamn klient-ID och namn, respektive. Du kan ange den `userId` egenskapen om bindningen till värdet från antingen rubrik med hjälp av en [bindning uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}`. 

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

Här är JavaScript-kod:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>inkommande 2.x Java-exempel

I följande exempel visas en [Java funktionen](functions-reference-java.md) som skaffar SignalR anslutningsinformation med indatabindningen och återställer den via HTTP.

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

#### <a name="authenticated-tokens"></a>Autentiserad token

Om funktionen utlöses av en autentiserad klient, kan du lägga till ID användaranspråk till genererade token. Du kan enkelt lägga till autentisering i en funktionsapp med hjälp [Apptjänstautentisering](../app-service/overview-authentication-authorization.md).

App Service-autentisering anger HTTP-huvuden med namnet `x-ms-client-principal-id` och `x-ms-client-principal-name` som innehåller den autentiserade användaren huvudnamn klient-ID och namn, respektive. Du kan ange den `UserId` egenskapen om bindningen till värdet från antingen rubrik med hjälp av en [bindning uttryck](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}`.

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

## <a name="signalr-output-binding"></a>SignalR-utdatabindning

Använd den *SignalR* utdatabindning att skicka en eller flera meddelanden med Azure SignalR Service. Du kan skicka ett meddelande till alla anslutna klienter, eller du kan skicka det endast till anslutna klienter som har autentiserats av en viss användare.

Du kan också använda den för att hantera de grupper som en användare tillhör.

Se exempel språkspecifika:

* [2.x C#](#2x-c-send-message-output-examples)
* [2.x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2.x C# skicka meddelande utdata-exempel

#### <a name="broadcast-to-all-clients"></a>Skicka till alla klienter

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skickar ett meddelande med hjälp av en utdatabindning till alla anslutna klienter. Den `Target` är namnet på metoden anropas på varje klient. Den `Arguments` egenskapen är en matris med noll eller flera objekt som ska skickas till klienter.

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

Du kan skicka ett meddelande endast till anslutningar som har autentiserats till en användare genom att ange den `UserId` för SignalR-meddelandet.

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

Du kan skicka ett meddelande endast till anslutningar som har lagts till i en grupp genom att ange den `GroupName` för SignalR-meddelandet.

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

### <a name="2x-c-group-management-output-examples"></a>2.x C# grupphantering utdata exempel

SignalR Service kan användare som ska läggas till grupper. Meddelanden kan sedan skickas till en grupp. Du kan använda den `SignalRGroupAction` med den `SignalR` utdatabindning för att hantera en användares gruppmedlemskap.

#### <a name="add-user-to-a-group"></a>Lägga till användare till en grupp

I följande exempel lägger till en användare till en grupp.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Ta bort användare från en grupp

I följande exempel tar bort en användare från en grupp.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

### <a name="2x-javascript-send-message-output-examples"></a>2.x JavaScript send message utdata-exempel

#### <a name="broadcast-to-all-clients"></a>Skicka till alla klienter

I följande exempel visas en SignalR-utdatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen för att skicka ett meddelande med Azure SignalR Service. Ange utdata-bindning till en matris med ett eller flera SignalR-meddelanden. Ett SignalR-meddelande består av en `target` egenskap som anger namnet på metoden som ska anropas på varje klient och en `arguments` egenskap som är en matris med objekt som ska skickas till klienter som argument.

Här är bindningsdata i den *function.json* fil:

Exempel function.json:

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

Du kan skicka ett meddelande endast till anslutningar som har autentiserats till en användare genom att ange den `userId` för SignalR-meddelandet.

*Function.JSON* förblir densamma. Här är JavaScript-kod:

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

Du kan skicka ett meddelande endast till anslutningar som har lagts till i en grupp genom att ange den `groupName` för SignalR-meddelandet.

*Function.JSON* förblir densamma. Här är JavaScript-kod:

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

### <a name="2x-javascript-group-management-output-examples"></a>2.x JavaScript grupphantering utdata exempel

SignalR Service kan användare som ska läggas till grupper. Meddelanden kan sedan skickas till en grupp. Du kan använda den `SignalR` utdatabindning för att hantera en användares gruppmedlemskap.

#### <a name="add-user-to-a-group"></a>Lägga till användare till en grupp

I följande exempel lägger till en användare till en grupp.

*function.json*

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

*index.js*

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

I följande exempel tar bort en användare från en grupp.

*function.json*

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

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2.x Java skicka meddelande utdata-exempel

#### <a name="broadcast-to-all-clients"></a>Skicka till alla klienter

I följande exempel visas en [Java funktionen](functions-reference-java.md) som skickar ett meddelande med hjälp av en utdatabindning till alla anslutna klienter. Den `target` är namnet på metoden anropas på varje klient. Den `arguments` egenskapen är en matris med noll eller flera objekt som ska skickas till klienter.

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

Du kan skicka ett meddelande endast till anslutningar som har autentiserats till en användare genom att ange den `userId` för SignalR-meddelandet.

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

Du kan skicka ett meddelande endast till anslutningar som har lagts till i en grupp genom att ange den `groupName` för SignalR-meddelandet.

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

### <a name="2x-java-group-management-output-examples"></a>2.x Java grupphantering utdata exempel

SignalR Service kan användare som ska läggas till grupper. Meddelanden kan sedan skickas till en grupp. Du kan använda den `SignalRGroupAction` med den `SignalROutput` utdatabindning för att hantera en användares gruppmedlemskap.

#### <a name="add-user-to-a-group"></a>Lägga till användare till en grupp

I följande exempel lägger till en användare till en grupp.

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

I följande exempel tar bort en användare från en grupp.

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

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `SignalRConnectionInfo` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**|| Måste anges till `signalRConnectionInfo`.|
|**riktning**|| Måste anges till `in`.|
|**Namn**|| Variabelnamnet som används i Funktionskoden för anslutningsobjektet för information. |
|**hubName**|**HubName**| Det här värdet måste vara samma som namnet på den SignalR hub för vilken informationen som genereras.|
|**Användar-ID**|**Användar-ID**| Valfritt: Värdet för användaridentifierare anspråk som ska anges i viktiga åtkomsttoken. |
|**connectionStringSetting**|**connectionStringSetting**| Namnet på den appinställning som innehåller anslutningssträngen SignalR Service (standardvärdet är ”AzureSignalRConnectionString”) |

### <a name="signalr"></a>SignalR

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `SignalR` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**|| Måste anges till `signalR`.|
|**riktning**|| Måste anges till `out`.|
|**Namn**|| Variabelnamnet som används i Funktionskoden för anslutningsobjektet för information. |
|**hubName**|**HubName**| Det här värdet måste vara samma som namnet på den SignalR hub för vilken informationen som genereras.|
|**connectionStringSetting**|**connectionStringSetting**| Namnet på den appinställning som innehåller anslutningssträngen SignalR Service (standardvärdet är ”AzureSignalRConnectionString”) |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Functions-utvecklingen och konfiguration med Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
