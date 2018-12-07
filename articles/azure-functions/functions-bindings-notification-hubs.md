---
title: Notification Hubs-bindningar för Azure Functions
description: Förstå hur du använder Azure Notification Hub-bindning i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 72c0db44e1f56cab190f343a87c6b453625a7f70
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996038"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Notification Hub-utdatabindning för Azure Functions

Den här artikeln beskrivs hur du skickar push-meddelanden med hjälp av [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) bindningar i Azure Functions. Azure Functions stöder utdatabindningar för Meddelandehubbar.

Azure Meddelandehubbar måste konfigureras för den plattformen meddelanden Service (PNS) du vill använda. Läs hur du hämtar push-meddelanden i klientappen från Meddelandehubbar i [komma igång med Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) och välj din mål-klientplattform från listrutan längst upp på sidan.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Notification Hubs-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet-paketet, version 1.x. Källkoden för paketet finns i den [azure-webjobs-sdk-tilläggen](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) GitHub-lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Den här bindningen är inte tillgänglig i Functions 2.x.

## <a name="example---template"></a>Exempel – mall

Du skickar meddelanden kan vara interna meddelanden eller [mallvarningar](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Interna meddelanden rikta en specifika klient-plattform som konfigurerats i den `platform` egenskapen för utdata-bindning. Ett meddelande i mallen kan användas för att fokusera på flera plattformar.   

Se exempel språkspecifika:

* [C#-skript – out-parameter](#c-script-template-example---out-parameter)
* [C#-skript – asynkrona](#c-script-template-example---asynchronous)
* [C#-skript - JSON](#c-script-template-example---json)
* [C#-skript - bibliotekstyper](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C#-skript exempelmall - out-parameter

Det här exemplet skickar ett meddelande en [registrering av mallen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller en `message` platshållare i mallen.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>C#-skript exempelmall - asynkron

Om du använder asynkron kod är out-parametrar inte tillåtna. I det här fallet `IAsyncCollector` att returnera mall-meddelande. Följande kod är en asynkron exempel på koden ovan. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>C#-skript mall exempel - JSON

Det här exemplet skickar ett meddelande en [registrering av mallen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller en `message` platshållare i mallen med hjälp av en giltig JSON-sträng.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C#-skript exempelmall - bibliotekstyper

Det här exemplet visar hur du använder typer som definieras i den [Microsoft Azure Notification Hubs-biblioteket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>F#exempelmall

Det här exemplet skickar ett meddelande en [registrering av mallen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller `location` och `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Exempelmall för JavaScript

Det här exemplet skickar ett meddelande en [registrering av mallen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller `location` och `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Exempel – APNS interna

Det här Skriptexemplet för C# visar hur du skickar en systemspecifika meddelanden med APNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---gcm-native"></a>Exempel – interna GCM

Det här Skriptexemplet för C# visar hur du skickar interna GCM-meddelanden. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Exempel – WNS interna

Det här Skriptexemplet för C# visar hur du använder typer som definieras i den [Microsoft Azure Notification Hubs-biblioteket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) att skicka en intern WNS popup-meddelande. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) attribut.

Attributets konstruktor parametrar och egenskaper finns i den [configuration](#configuration) avsnittet.

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `NotificationHub` attribut:

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** |Saknas| Måste anges till ”notificationHub”. |
|**riktning** |Saknas| Måste anges till ”ut”. | 
|**Namn** |Saknas| Variabelnamnet som används i Funktionskoden för notification hub-meddelande. |
|**tagExpression** |**tagExpression** | Tagguttryck kan du ange att meddelanden ska levereras till en uppsättning enheter som har registrerats för att ta emot meddelanden som matchar etikettuttrycket.  Mer information finns i [Routning och tagg uttryck](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**HubName** | **HubName** | Namnet på notification hub-resurs i Azure-portalen. |
|**anslutning** | **connectionStringSetting** | Namnet på en appinställning som innehåller en anslutningssträng för Meddelandehubbar.  Anslutningssträngen måste anges till den *DefaultFullSharedAccessSignature* värde för din meddelandehubb. Se [sträng anslutningsinställningar](#connection-string-setup) senare i den här artikeln.|
|**Plattform** | **Plattform** | Egenskapen plattform anger klientplattform notification-mål. Som standard om egenskapen plattform utelämnas från utdata-bindning kan mallvarningar användas för att rikta vilken plattform som konfigurerats på Azure Notification Hub. Mer information om hur du använder mallar i allmänhet att skicka plattformsoberoende meddelanden plattformar med Azure Notification Hub finns i [mallar](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). När värdet **plattform** måste vara något av följande värden: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Läs mer på att konfigurera meddelandehubben för APNS och ta emot meddelandet i en klientapp [skicka push-meddelanden till iOS med Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Läs mer på att konfigurera meddelandehubben för ADM och ta emot meddelandet i en Kindle-app, [komma igång med Notification Hubs för Kindle-appar](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging som är den nya versionen av GCM, stöds också. Mer information finns i [skicka push-meddelanden till Android med Azure Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Windows Push Notification Services](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) utvecklar Windows-plattformen. Windows Phone 8.1 och senare stöds också av WNS. Mer information finns i [komma igång med Notification Hubs för Windows Universal-Plattformsappar](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Microsoft Push Notification Service](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Den här plattformen har stöd för Windows Phone 8 och Windows Phone-plattformar som tidigare. Mer information finns i [skicka push-meddelanden med Azure Notification Hubs på Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>exempel på en Function.JSON

Här är ett exempel på en Notification Hub-bindning i en *function.json* fil.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Anslutningsinställningar för sträng

Om du vill använda en notification hub-utdatabindning måste du konfigurera anslutningssträngen för hubben. Du kan välja en befintlig notification hub eller skapa en ny en direkt från den *integrera* fliken i Azure-portalen. Du kan också konfigurera anslutningssträngen manuellt. 

Konfigurera anslutningssträngen till en befintlig notification hub:

1. Navigera till din meddelandehubb i de [Azure-portalen](https://portal.azure.com), Välj **åtkomstprinciper**, och välj på kopieringsknappen bredvid den **DefaultFullSharedAccessSignature** princip. Anslutningssträngen för kopieras den *DefaultFullSharedAccessSignature* principen till din meddelandehubb. Den här anslutningssträngen kan funktionen Skicka meddelanden till hubben.
    ![Kopiera anslutningssträngen för notification hub](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navigera till din funktionsapp i Azure portal, Välj **programinställningar**, lägga till en nyckel som **MyHubConnectionString**, klistra in den kopierade *DefaultFullSharedAccessSignature* för meddelandehubben som värdet och klicka sedan på **spara**.

Namnet på den här inställningen är vad som ingår i utdata-bindning anslutningsinställningen i *function.json* eller .NET-attributet. Se den [konfigurationsavsnittet](#configuration) tidigare i den här artikeln.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Notification Hub | [Handboken](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

