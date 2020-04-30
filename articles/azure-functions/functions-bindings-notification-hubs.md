---
title: Notification Hubs bindningar för Azure Functions
description: Lär dig hur du använder Azure Notification Hub-bindning i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 211f8c8a203b81a4df6a8e9515b403f99cec572a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277289"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Notification Hubs utgående bindning för Azure Functions

Den här artikeln beskriver hur du skickar push-meddelanden med hjälp av [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) -bindningar i Azure Functions. Azure Functions stöder utgående bindningar för Notification Hubs.

Azure Notification Hubs måste konfigureras för den plattforms meddelande tjänst (PNS) som du vill använda. Information om hur du hämtar push-meddelanden i din klient app från Notification Hubs finns i [komma igång med Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) och välj mål klient plattform i list rutan längst upp på sidan.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google har [föråldrade Google Cloud Messaging (GCM) i stället för Firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). Den här utgående bindningen stöder inte FCM. Om du vill skicka meddelanden med hjälp av FCM kan du använda [Firebase-API: et](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) direkt i din funktion eller använda [mall meddelanden](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Paket-funktioner 1. x

Notification Hubs-bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet-paketet, version 1. x. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

Den här bindningen är inte tillgänglig i functions 2. x och högre.

## <a name="example---template"></a>Exempel-mall

De meddelanden som du skickar kan vara interna meddelanden eller [mal meddelanden](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Interna meddelanden riktar sig till en specifik klient plattform enligt `platform` konfigurationen i egenskapen för utgående bindning. Ett mal Lav besked kan användas för att rikta in flera plattformar.   

Se språkspecifika exempel:

* [C#-skript parameter](#c-script-template-example---out-parameter)
* [C#-skript – asynkron](#c-script-template-example---asynchronous)
* [C#-skript – JSON](#c-script-template-example---json)
* [C#-skript – biblioteks typer](#c-script-template-example---library-types)
* [B #](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Exempel på skript mal len C#-parameter

Det här exemplet skickar ett meddelande för en [mall registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller `message` en plats hållare i mallen.

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

### <a name="c-script-template-example---asynchronous"></a>Exempel på C#-skript mal len – asynkron

Om du använder asynkron kod tillåts inte out-parametrar. I det här fallet `IAsyncCollector` använder du för att returnera meddelandet från en mall. Följande kod är ett asynkront exempel på koden ovan. 

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

### <a name="c-script-template-example---json"></a>C#-skript mal len exempel – JSON

Det här exemplet skickar ett meddelande för en [mall registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som innehåller `message` en plats hållare i mallen med en giltig JSON-sträng.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C#-skript mal len exempel – biblioteks typer

Det här exemplet visar hur du använder typer som definieras i [Microsoft Azure Notification Hubs-biblioteket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

### <a name="f-template-example"></a>Exempel på F #-mall

Det här exemplet skickar ett meddelande för en [mall registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som `location` innehåller `message`och.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Exempel på JavaScript-mall

Det här exemplet skickar ett meddelande för en [mall registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) som `location` innehåller `message`och.

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

## <a name="example---apns-native"></a>Exempel – APN Native

Det här C#-skript exemplet visar hur du skickar ett internt APN-meddelande. 

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

## <a name="example---wns-native"></a>Exempel – WNS Native

Det här C#-skript exemplet visar hur du använder typer som definierats i [Microsoft Azure Notification Hubs-biblioteket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) för att skicka en inbyggd WNS popup-avisering. 

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

Använd attributet [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor parametrar och egenskaper beskrivs i [konfigurations](#configuration) avsnittet.

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `NotificationHub` attributet:

|function. JSON-egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** |saknas| Måste anges till `notificationHub`. |
|**riktning** |saknas| Måste anges till `out`. | 
|**Namn** |saknas| Variabel namn som används i funktions koden för Notification Hub-meddelandet. |
|**tagExpression** |**TagExpression** | Med tagg uttryck kan du ange att meddelanden ska skickas till en uppsättning enheter som har registrerats för att ta emot meddelanden som matchar etikett uttrycket.  Mer information finns i avsnittet om [Routning och tagg uttryck](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Namnet på resursen för Notification Hub i Azure Portal. |
|**anslutningen** | **ConnectionStringSetting** | Namnet på en app-inställning som innehåller en Notification Hubs anslutnings sträng.  Anslutnings strängen måste anges till *DefaultFullSharedAccessSignature* -värdet för Notification Hub. Se [installationen av anslutnings strängen](#connection-string-setup) senare i den här artikeln.|
|**systemet** | **Plattform** | Egenskapen Platform anger klient plattformen som meddelande mål. Som standard, om egenskapen Platform utelämnas från utgående bindning, kan mal meddelanden användas för att rikta in alla plattformar som kon figurer ATS i Azure Notification Hub. Mer information om hur du använder mallar i allmänhet för att skicka plattforms oberoende meddelanden med en Azure Notification Hub finns i [mallar](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). När den har angetts måste **plattformen** vara något av följande värden: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Mer information om hur du konfigurerar Notification Hub för APN och tar emot meddelandet i en klient app finns i [skicka push-meddelanden till iOS med Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Mer information om hur du konfigurerar Notification Hub för ADM och tar emot meddelandet i en Kindle-app finns i [komma igång med Notification Hubs för Kindle-appar](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>wns</code>&mdash;Windows- [Push Notification Services](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) Windows-plattformar. Windows Phone 8,1 och senare stöds också av WNS. Mer information finns i [komma igång med Notification Hubs för Windows Universal Platform-appar](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Microsoft Push Notification Service](/previous-versions/windows/apps/ff402558(v=vs.105)). Den här plattformen stöder Windows Phone 8-och tidigare Windows Phone-plattformar. Mer information finns i [skicka push-meddelanden med Azure Notification Hubs på Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>exempel på function. JSON-fil

Här är ett exempel på en Notification Hubs-bindning i en *Function. JSON* -fil.

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

### <a name="connection-string-setup"></a>Konfiguration av anslutnings sträng

Om du vill använda en utgående bindning för Notification Hub måste du konfigurera anslutnings strängen för hubben. Du kan välja en befintlig Notification Hub eller skapa en ny rättighet från fliken *integrera* i Azure Portal. Du kan också konfigurera anslutnings strängen manuellt. 

Så här konfigurerar du anslutnings strängen till en befintlig Notification Hub:

1. Navigera till Notification Hub i [Azure Portal](https://portal.azure.com), Välj **åtkomst principer**och välj sedan knappen Kopiera bredvid **DefaultFullSharedAccessSignature** -principen. Detta kopierar anslutnings strängen för *DefaultFullSharedAccessSignature* -principen till Notification Hub. Med den här anslutnings strängen kan funktionen skicka meddelanden till hubben.
    ![Kopiera anslutnings strängen för Notification Hub](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navigera till din Function-app i Azure Portal, Välj **program inställningar**, Lägg till en nyckel, till exempel **MyHubConnectionString**, klistra in den kopierade *DefaultFullSharedAccessSignature* för Notification Hub som värde och klicka sedan på **Spara**.

Namnet på den här program inställningen är vad som händer i anslutnings inställningen för utgående bindning i *Function. JSON* eller .NET-attributet. Se [konfigurations avsnittet](#configuration) tidigare i den här artikeln.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Bindning | Referens |
|---|---|
| Notification Hub | [Drift guide](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

