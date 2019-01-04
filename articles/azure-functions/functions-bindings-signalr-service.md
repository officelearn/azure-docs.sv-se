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
ms.date: 09/23/2018
ms.author: cshoe
ms.openlocfilehash: 7a7063b9177774c5207746283dc7cd25e3dd5793
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721894"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR Service-bindningar för Azure Functions

Den här artikeln förklarar hur du autentiserar och skicka realtidsmeddelanden till klienter som är anslutna till [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) med SignalR Service-bindningar i Azure Functions. Azure Functions stöder indata och utdata-bindningar för SignalR Service.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Med SignalR Service-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet-paketet, version 1.0.0-preview1-*. Källkoden för paketet finns i den [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub-lagringsplatsen.

> [!NOTE]
> Azure SignalR Service är allmänt tillgänglig. SignalR Service-bindningar för Azure Functions är dock för närvarande i förhandsversion.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>SignalR anslutning info-indatabindning

Innan en klient kan ansluta till Azure SignalR Service, måste den hämta service slutpunkts-URL och en giltig åtkomsttoken. Den *SignalRConnectionInfo* indatabindning producerar SignalR Service slutpunkts-URL och en giltig token som används för att ansluta till tjänsten. Eftersom token är begränsade och kan användas för att autentisera en viss användare till en anslutning, bör du inte cachelagra token eller dela den mellan klienter. En HTTP-utlösare som använder den här bindningen kan användas av klienter för att hämta anslutningsinformationen.

Se exempel språkspecifika:

* [2.x C#](#2x-c-input-example)
* [2.x JavaScript](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>inkommande 2.x C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skaffar SignalR anslutningsinformation med indatabindningen och återställer den via HTTP.

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Autentiserad token

Om funktionen utlöses av en autentiserad klient, kan du lägga till ID användaranspråk till genererade token. Du kan enkelt lägga till autentisering till en funktionsapp med hjälp av [App Service-autentisering] (.. /App-Service/Overview-Authentication-Authorization.MD).

App Service-autentisering anger HTTP-huvuden med namnet `x-ms-client-principal-id` och `x-ms-client-principal-name` som innehåller den autentiserade användaren huvudnamn klient-ID och namn, respektive. Du kan ange den `UserId` egenskapen om bindningen till värdet från antingen rubrik med hjälp av en [bindning uttryck](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>inkommande 2.x JavaScript-exempel

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
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
};
```

#### <a name="authenticated-tokens"></a>Autentiserad token

Om funktionen utlöses av en autentiserad klient, kan du lägga till ID användaranspråk till genererade token. Du kan enkelt lägga till autentisering till en funktionsapp med hjälp av [App Service-autentisering] (.. /App-Service/Overview-Authentication-Authorization.MD).

App Service-autentisering anger HTTP-huvuden med namnet `x-ms-client-principal-id` och `x-ms-client-principal-name` som innehåller den autentiserade användaren huvudnamn klient-ID och namn, respektive. Du kan ange den `userId` egenskapen om bindningen till värdet från antingen rubrik med hjälp av en [bindning uttryck](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` eller `{headers.x-ms-client-principal-name}`. 

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
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>SignalR-utdatabindning

Använd den *SignalR* utdatabindning att skicka en eller flera meddelanden med Azure SignalR Service. Du kan skicka ett meddelande till alla anslutna klienter, eller du kan skicka det endast till anslutna klienter som har autentiserats av en viss användare.

Se exempel språkspecifika:

* [2.x C#](#2x-c-output-example)
* [2.x JavaScript](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>exempel på utdata 2.x C#

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
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>2.x utmatningsexempel för JavaScript

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
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>Skicka till en användare

Du kan skicka ett meddelande endast till anslutningar som har autentiserats till en användare genom att ange den `userId` för SignalR-meddelandet.

*Function.JSON* förblir densamma. Här är JavaScript-kod:

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

## <a name="configuration"></a>Konfiguration

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `SignalRConnectionInfo` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**|| Måste anges till `signalRConnectionInfo`.|
|**riktning**|| Måste anges till `in`.|
|**Namn**|| Variabelnamnet som används i Funktionskoden för anslutningsobjektet för information. |
|**HubName**|**HubName**| Det här värdet måste vara samma som namnet på den SignalR hub för vilken informationen som genereras.|
|**Användar-ID**|**Användar-ID**| Valfritt: Värdet för användaridentifierare anspråk som ska anges i viktiga åtkomsttoken. |
|**connectionStringSetting**|**connectionStringSetting**| Namnet på den appinställning som innehåller anslutningssträngen SignalR Service (standardvärdet är ”AzureSignalRConnectionString”) |

### <a name="signalr"></a>SignalR

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `SignalR` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**|| Måste anges till `signalR`.|
|**riktning**|| Måste anges till `out`.|
|**Namn**|| Variabelnamnet som används i Funktionskoden för anslutningsobjektet för information. |
|**HubName**|**HubName**| Det här värdet måste vara samma som namnet på den SignalR hub för vilken informationen som genereras.|
|**connectionStringSetting**|**connectionStringSetting**| Namnet på den appinställning som innehåller anslutningssträngen SignalR Service (standardvärdet är ”AzureSignalRConnectionString”) |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

