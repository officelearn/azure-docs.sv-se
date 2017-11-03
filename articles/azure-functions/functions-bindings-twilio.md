---
title: Azure Functions Twilio-bindning | Microsoft Docs
description: "Förstå hur du använder Twilio-bindningar med Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: a60263aa-3de9-4e1b-a2bb-0b52e70d559b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/20/2016
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e8c5e8f2dfedae26486e1c8afbe0cec3f3228e86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="send-sms-messages-from-azure-functions-using-the-twilio-output-binding"></a>Skicka SMS meddelanden från Azure-funktioner med hjälp av Twilio-utdatabindning
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Den här artikeln beskriver hur du konfigurerar och använder Twilio-bindningar med Azure Functions. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Functions stöder Twilio utdata bindningar om du vill aktivera dina funktioner att skicka meddelanden för SMS-text med några få rader med kod och en [Twilio](https://www.twilio.com/) konto. 

## <a name="functionjson-for-the-twilio-output-binding"></a>Function.JSON för Twilio-utdatabindning
Filen function.json innehåller följande egenskaper:

|Egenskap  |Beskrivning  |
|---------|---------|
|**Namn**| Variabelnamn som används i Funktionskoden för textmeddelande Twilio SMS. |
|**typ**| måste anges till `twilioSms`.|
|**accountSid**| Det här värdet måste anges till namnet på en Appinställning som innehåller dina Twilio-konto Sid.|
|**authToken**| Det här värdet måste anges till namnet på en Appinställning som innehåller dina Twilio-token för autentisering.|
|**att**| Det här värdet anges till det telefonnummer som SMS-meddelanden skickas till.|
|**från**| Det här värdet anges till det telefonnummer som SMS-meddelanden skickas från.|
|**riktning**| måste anges till `out`.|
|**brödtext**| Det här värdet kan användas för att hård code SMS textmeddelande om du inte behöver ange den dynamiskt i koden för din funktion. |

Exempel function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Exempel C# kön utlösare med Twilio-utdatabindning
#### <a name="synchronous"></a>Synkron
Denna synkron kodexempel för en utlösare för Azure Storage-kö använder en out-parameter för att skicka ett SMS till en kund som placeras en order.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

#### <a name="asynchronous"></a>Asynkron
Den här asynkrona kodexempel för en utlösare för Azure Storage-kö skickar ett SMS till en kund som placeras en order.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Exempel Node.js kön utlösare med Twilio-utdatabindning
Det här exemplet med Node.js skickar ett SMS till en kund som placeras en order.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

