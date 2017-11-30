---
title: "Notification Hubs bindningar för Azure Functions"
description: "Förstå hur du använder Azure Notification Hub-bindning i Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: fd5ff8878e6afe95bd620bd9d1910add6dd92f3f
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Notification Hubs utdatabindning för Azure Functions

Den här artikeln beskrivs hur du skickar push-meddelanden med hjälp av [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) bindningar i Azure Functions. Azure Functions stöder utdata bindningar för Notification Hubs.

Azure Notification Hub måste konfigureras för den plattform meddelanden Service (PNS) du vill använda. Information om hur du hämtar push-meddelanden i klientappen från Notification Hubs finns [komma igång med Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) och välj målplattformen klienten från listrutan längst upp på sidan.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example---template"></a>Exempel - mall

Du skickar meddelanden kan vara interna meddelanden eller [mall meddelanden](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Interna meddelanden riktade till en viss klient-plattform som konfigurerats i den `platform` egenskapen för utdata-bindning. Ett meddelande om mall kan användas för att fokusera på flera plattformar.   

Finns i det språkspecifika:

* [C# skript - out-parameter](#c-script-template-example---out-parameter)
* [C# skript - asynkron](#c-script-template-example---asynchronous)
* [C# skript - JSON](#c-script-template-example---json)
* [C# skript - typer av dokumentbibliotek](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# mallen exempel på skript - out-parameter

Det här exemplet skickar en avisering för en [mallen registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller en `message` platshållare i mallen.

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

### <a name="c-script-template-example---asynchronous"></a>C# mallen exempel på skript - asynkron

Om du använder asynkrona koden är out-parametrar inte tillåtna. I det här fallet använder `IAsyncCollector` att returnera mall-meddelande. Följande kod är ett asynkront exempel av koden ovan. 

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

### <a name="c-script-template-example---json"></a>C# mallen exempel på skript - JSON

Det här exemplet skickar en avisering för en [mallen registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller en `message` platshållare i mallen med hjälp av en giltig JSON-sträng.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# mallen exempel på skript - typer av dokumentbibliotek

Det här exemplet visar hur du använder typer som definieras i den [Microsoft Azure Notification Hubs Library](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

### <a name="f-template-example"></a>F # exempel

Det här exemplet skickar en avisering för en [mallen registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller `location` och `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Exempel för JavaScript-mall

Det här exemplet skickar en avisering för en [mallen registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller `location` och `message`.

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

## <a name="example---apns-native"></a>Exempel – APN interna

C# skript exemplet visar hur du skickar ett enhetligt APN-meddelande. 

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

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---gcm-native"></a>Exempel - GCM interna

C# skript exemplet visar hur du skickar en inbyggd GCM-meddelanden. 

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

## <a name="example---wns-native"></a>Exempel: WNS interna

C# skript exemplet visar hur du använder typer som definieras i den [Microsoft Azure Notification Hubs Library](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) att skicka ett enhetligt WNS popup-meddelande. 

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

För [förkompilerat C#](functions-dotnet-class-library.md) funktion, Använd den [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) attribut som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs).

Attributets konstruktorparametrarna och egenskaper som beskrivs i den [configuration](#configuration) avsnitt.

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `NotificationHub` attribut:

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** |Saknas| Måste anges till ”notificationHub”. |
|**riktning** |Saknas| Måste anges till ”out”. | 
|**Namn** |Saknas| Variabelnamn som används i Funktionskoden för hubben meddelandet. |
|**tagExpression** |**TagExpression** | Tagguttryck kan du ange att meddelanden ska levereras till en uppsättning enheter som har registrerats för att ta emot meddelanden som matchar etikettuttrycket.  Mer information finns i [Routning och tagg uttryck](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Namn på notification hub resurs i Azure-portalen. |
|**anslutning** | **ConnectionStringSetting** | Namnet på en appinställning som innehåller en anslutningssträng för Notification Hubs.  Anslutningssträngen måste anges till den *DefaultFullSharedAccessSignature* värde för meddelandehubben. Se [sträng anslutningsinställningar](#connection-string-setup) senare i den här artikeln.|
|**plattform** | **Plattform** | Egenskapen plattform anger klientplattformen notification-mål. Som standard om egenskapen plattform utelämnas från utdata-bindningen kan mallen meddelanden användas för alla plattformar som konfigurerats på Azure Notification Hub. Mer information om hur du använder mallar i allmänhet att skicka mellan plattform meddelanden med en Azure Notification Hub finns [mallar](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). När inställningen **plattform** måste vara något av följande värden: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Mer information om hur du konfigurerar meddelandehubben för APNS och ta emot meddelandet i ett klientprogram, se [skicka push-meddelanden till iOS med Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Mer information om hur du konfigurerar meddelandehubben för ADM och ta emot meddelandet i en Kindle-app finns [komma igång med Notification Hubs för Kindle-appar](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging, vilket är den nya versionen av GCM stöds också. Mer information finns i [skicka push-meddelanden till Android med Azure Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Windows Push Notification Services](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) riktad Windows-plattformar. Windows Phone 8.1 och senare stöds också av WNS. Mer information finns i [komma igång med Notification Hubs för Windows Universal-plattformen appar](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Microsoft Push Notification Service](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Den här plattformen stöder Windows Phone 8 och Windows Phone-plattformar som tidigare. Mer information finns i [skicka push-meddelanden med Azure Notification Hubs på Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>exempel på Function.JSON

Här är ett exempel på en Notification Hubs-bindning i en *function.json* fil.

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

Om du vill använda en notification hub utdata-bindning måste du konfigurera anslutningssträngen för hubben. Du kan välja en befintlig meddelandehubben eller skapa en ny en direkt från den *integrera* fliken i Azure-portalen. Du kan också konfigurera anslutningssträngen manuellt. 

Konfigurera anslutningssträngen till en befintlig meddelandehubben:

1. Navigera till din meddelandehubb i de [Azure-portalen](https://portal.azure.com), Välj **åtkomstprinciper**, och välj kopieringsknappen bredvid den **DefaultFullSharedAccessSignature** princip. Då kopieras anslutningssträngen för den *DefaultFullSharedAccessSignature* principen till meddelandehubben. Den här anslutningssträngen kan funktionen Skicka meddelanden till hubben.
    ![Kopiera anslutningssträngen för notification hub](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navigera till appen funktionen i Azure portal, väljer **programinställningar**, lägga till en nyckel som **MyHubConnectionString**, klistra in den kopierade *DefaultFullSharedAccessSignature* för meddelandehubben som värdet och klicka sedan på **spara**.

Namnet på den här inställningen är vad som ska ingå i inställningen utdata bindning anslutning i *function.json* eller .NET-attribut. Finns det [konfigurationsavsnittet](#configuration) tidigare i den här artikeln.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

