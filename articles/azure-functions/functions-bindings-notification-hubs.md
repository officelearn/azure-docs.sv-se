---
title: Meddelandehubbar bindningar för Azure-funktioner
description: Förstå hur du använder Azure Notification Hub-bindning i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 211f8c8a203b81a4df6a8e9515b403f99cec572a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277289"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Utdatabindning för Meddelandehubbar för Azure-funktioner

I den här artikeln beskrivs hur du skickar push-meddelanden med hjälp av [Azure Notification Hubs-bindningar](../notification-hubs/notification-hubs-push-notification-overview.md) i Azure Functions. Azure Functions stöder utdatabindningar för meddelandehubbar.

Azure Notification Hubs måste konfigureras för den PNS (Platform Notifications Service) som du vill använda. Mer information om hur du får push-meddelanden i klientappen från meddelandehubbar finns i [Komma igång med meddelandehubbar](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) och välj målklientplattform i listrutan högst upp på sidan.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google har [föråldrat Google Cloud Messaging (GCM) till förmån för Firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). Den här utdatabindningen stöder inte FCM. Om du vill skicka meddelanden med FCM använder du [Firebase API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) direkt i din funktion eller använder [mallmeddelanden](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Paket - Funktioner 1.x

Meddelandenhubbarbindningarna finns i [paketet Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet, version 1.x. Källkoden för paketet finns i [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) GitHub-databasen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket - Funktioner 2.x och högre

Den här bindningen är inte tillgänglig i Funktioner 2.x och högre.

## <a name="example---template"></a>Exempel - mall

De meddelanden du skickar kan vara inbyggda meddelanden eller [mallmeddelanden](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Inbyggda meddelanden riktar sig till en `platform` specifik klientplattform som konfigurerats i egenskapen för utdatabindningen. Ett mallmeddelande kan användas för att rikta in sig på flera plattformar.   

Se det språkspecifika exemplet:

* [C# script - ut parameter](#c-script-template-example---out-parameter)
* [C# script - asynkron](#c-script-template-example---asynchronous)
* [C# manus - JSON](#c-script-template-example---json)
* [C# script - bibliotekstyper](#c-script-template-example---library-types)
* [F #](#f-template-example)
* [Javascript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Exempel på C#-skriptmall - ut parameter

Det här exemplet skickar ett meddelande om `message` en [mallregistrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller en platshållare i mallen.

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

### <a name="c-script-template-example---asynchronous"></a>Exempel på C#-skriptmall - asynkron

Om du använder asynkron kod tillåts inte utparametrar. I det `IAsyncCollector` här fallet används för att returnera mallmeddelandet. Följande kod är ett asynkront exempel på koden ovan. 

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

### <a name="c-script-template-example---json"></a>Exempel på C#-skriptmall - JSON

Det här exemplet skickar ett meddelande om `message` en [mallregistrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller en platshållare i mallen med en giltig JSON-sträng.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>Exempel på C#-skriptmall - bibliotekstyper

Det här exemplet visar hur du använder typer som definierats i [Microsoft Azure Notification Hubs Library](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

### <a name="f-template-example"></a>Exempel på F#-mall

Det här exemplet skickar ett meddelande `location` om `message`en [mallregistrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller och .

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Exempel på JavaScript-mall

Det här exemplet skickar ett meddelande `location` om `message`en [mallregistrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller och .

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
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

## <a name="example---apns-native"></a>Exempel - APNS native

Det här c#-skriptexempeln visar hur du skickar ett inbyggt APNS-meddelande. 

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

## <a name="example---wns-native"></a>Exempel - WNS native

Det här C#-skriptexempeln visar hur du använder typer som definierats i [Microsoft Azure Notification Hubs-biblioteket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) för att skicka ett inbyggt WNS-popup-meddelande. 

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

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [NotificationHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs)

Attributets konstruktorparametrar och [configuration](#configuration) egenskaper beskrivs i konfigurationsavsnittet.

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `NotificationHub` du anger i *filen function.json* och attributet:

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** |Saknas| Måste ställas `notificationHub`in på . |
|**riktning** |Saknas| Måste ställas `out`in på . | 
|**Namn** |Saknas| Variabelnamn som används i funktionskoden för meddelandet hubbmeddelande. |
|**tagUttryck** |**TagExpression (300)** | Med tagguttryck kan du ange att meddelanden ska levereras till en uppsättning enheter som har registrerats för att ta emot meddelanden som matchar tagguttrycket.  Mer information finns i [Routning och tagguttryck](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName (hubName)** | **HubName (HubName)** | Namn på meddelandehubbens resurs i Azure-portalen. |
|**Anslutning** | **ConnectionStringSetting** | Namnet på en appinställning som innehåller en meddelandehubbaranslutningssträng.  Anslutningssträngen måste vara inställd på värdet *DefaultFullSharedAccessSignature* för meddelandehubben. Se [Anslutningsstränginställningar](#connection-string-setup) senare i den här artikeln.|
|**Plattform** | **Plattform** | Plattformsegenskapen anger klientplattformen dina aviseringsmål. Som standard, om plattformsegenskapen utelämnas från utdatabindningen, kan mallmeddelanden användas för att rikta alla plattformar som konfigurerats på Azure Notification Hub. Mer information om hur du använder mallar i allmänhet för att skicka online-meddelanden med en Azure Notification Hub finns i [Mallar](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). När den är inställd måste **plattformen** vara något av följande värden: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Mer information om hur du konfigurerar meddelandehubben för APNS och tar emot meddelandet i en klientapp finns i [Skicka push-meddelanden till iOS med Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Mer information om hur du konfigurerar meddelandehubben för ADM och tar emot meddelandet i en Kindle-app finns i [Komma igång med meddelandehubbar för Kindle-appar](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>wns</code>&mdash;[Windows Push Notification Services](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) som riktar sig till Windows-plattformar. Windows Phone 8.1 och senare stöds också av WNS. Mer information finns i [Komma igång med meddelandehubbar för Windows Universal Platform Apps](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Microsoft Push Meddelande Service](/previous-versions/windows/apps/ff402558(v=vs.105)). Den här plattformen stöder Windows Phone 8 och tidigare Windows Phone-plattformar. Mer information finns i [Skicka push-meddelanden med Azure Notification Hubs på Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>exempel på function.json-fil

Här är ett exempel på en Meddelandehubbar som binder i en *function.json-fil.*

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
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Inställningar för anslutningssträng

Om du vill använda en meddelandehubbutdatabindning måste du konfigurera anslutningssträngen för navet. Du kan välja en befintlig meddelandehubb eller skapa en ny direkt från fliken *Integrera* i Azure-portalen. Du kan också konfigurera anslutningssträngen manuellt. 

Så här konfigurerar du anslutningssträngen till en befintlig meddelandehubb:

1. Navigera till meddelandehubben i [Azure-portalen,](https://portal.azure.com)välj **Åtkomstprinciper**och välj kopieringsknappen bredvid principen **DefaultFullSharedAccessSignature.** Då kopieras anslutningssträngen för principen *DefaultFullSharedAccessSignature* till meddelandehubben. Med den här anslutningssträngen kan din funktion skicka meddelanden till navet.
    ![Kopiera anslutningssträngen för meddelandehubben](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navigera till din funktionsapp i Azure-portalen, välj **Programinställningar,** lägg till en nyckel som **MyHubConnectionString,** klistra in den kopierade *DefaultFullSharedAccessSignature* för meddelandehubben som värde och klicka sedan på **Spara**.

Namnet på den här programinställningen är vad som gäller i inställningen för utdatabindningsanslutning i *function.json* eller .NET-attributet. Se [avsnittet Konfiguration](#configuration) tidigare i den här artikeln.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Aviseringsnav | [Verksamhetsguide](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Utlösare och bindningar för Azure-funktioner](functions-triggers-bindings.md)

