---
title: Twilio-bindning för Azure Functions
description: Förstå hur du använder Twilio-bindningar med Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1426d6e770cca566c4b77ca4742e2f8a0fbb5465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715060"
---
# <a name="twilio-binding-for-azure-functions"></a>Twilio-bindning för Azure-funktioner

I den här artikeln beskrivs hur du skickar textmeddelanden med hjälp av [Twilio-bindningar](https://www.twilio.com/) i Azure Functions. Azure Functions stöder utdatabindningar för Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - Funktioner 1.x

Twilio-bindningarna finns i [paketet Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet, version 1.x. Källkoden för paketet finns i [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub-databasen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket - Funktioner 2.x och högre

Twilio-bindningarna finns i [paketet Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet, version 3.x. Källkoden för paketet finns i [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub-databasen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>Exempel - Funktioner 2.x och högre

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skickar ett textmeddelande när det utlöses av ett kömeddelande.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

I det `TwilioSms` här exemplet används attributet med metodens returvärde. Ett alternativ är att använda `out CreateMessageOptions` attributet `ICollector<CreateMessageOptions>` `IAsyncCollector<CreateMessageOptions>` med en parameter eller en eller en parameter.

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en Twilio-utdatabindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder `out` en parameter för att skicka ett textmeddelande.

Här är bindningsdata i *filen function.json:*

Exempel function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Här är C # skriptkod:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

Du kan inte använda parametrar i asynkron kod. Här är ett asynkront C#-skriptkodsexempel:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en Twilio-utdatabindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen.

Här är bindningsdata i *filen function.json:*

Exempel function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Här är JavaScript-koden:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas hur du skickar ett SMS-meddelande med hjälp av utdatabindningen enligt definitionen i följande *function.js*.

```json
    {
      "type": "twilioSms",
      "name": "twilioMessage",
      "accountSidSetting": "TwilioAccountSID",
      "authTokenSetting": "TwilioAuthToken",
      "from": "+1XXXXXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }
```

Du kan skicka ett serialiserat `func.Out` JSON-objekt till parametern för att skicka SMS-meddelandet.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, twilioMessage: func.Out[str]) -> func.HttpResponse:

    message = req.params.get('message')
    to = req.params.get('to')

    value = {
      "body": message,
      "to": to
    }

    twilioMessage.set(json.dumps(value))

    return func.HttpResponse(f"Message sent")
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas hur du använder [TwilioSmsOutput-anteckningen](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) för att skicka ett SMS-meddelande. Värden `to`för `from`, `body` och krävs i attributdefinitionen även om du åsidosätter dem programmässigt.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class TwilioOutput {

    @FunctionName("TwilioOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST },
                authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @TwilioSmsOutput(
                name = "twilioMessage",
                accountSid = "AzureWebJobsTwilioAccountSID",
                authToken = "AzureWebJobsTwilioAuthToken",
                to = "+1XXXXXXXXXX",
                body = "From Azure Functions",
                from = "+1XXXXXXXXXX") OutputBinding<String> twilioMessage,
            final ExecutionContext context) {

        String message = request.getQueryParameters().get("message");
        String to = request.getQueryParameters().get("to");

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"body\": \"%s\",")
            .append("\"to\": \"%s\"")
            .append("}");

        final String body = String.format(builder.toString(), message, to);

        twilioMessage.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Message sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du attributet [TwilioSms.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs)

Information om attributegenskaper som du kan konfigurera finns i [Konfiguration](#configuration). Här är `TwilioSms` ett attributexempel i en metodsignatur:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Ett fullständigt exempel finns i [C#-exempel](#example).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Placera [TwilioSmsOutput-anteckning](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) på [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.outputbinding) en `T` parameter där det kan `int` `String`finnas `byte[]`någon inbyggd Java-typ, till exempel , , eller en POJO-typ.

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `TwilioSms` du anger i *filen function.json* och attributet.

| v1 function.json, egenskapen | v2 function.json, egenskapen | Egenskapen Attribute |Beskrivning|
|---------|---------|---------|----------------------|
|**Typ**|**Typ**| måste ställas `twilioSms`in på .|
|**riktning**|**riktning**| måste ställas `out`in på .|
|**Namn**|**Namn**| Variabelnamn som används i funktionskoden för Twilio SMS-sms: n. |
|**kontoSid**|**kontoSidSetting**| **AccountSidSetting**| Det här värdet måste anges till namnet på en appinställning`TwilioAccountSid`som innehåller din Twilio-konto sid ( ). Om inte anges är standardappinställningsnamnet "AzureWebJobsTwilioAccountSid". |
|**authToken (svenska)**|**authTokenSetting**|**AuthTokenSetting**| Det här värdet måste anges till namnet på en appinställning`TwilioAccountAuthToken`som innehåller din Twilio-autentiseringstoken ( ). Om inte anges är standardappinställningsnamnet "AzureWebJobsTwilioAuthToken". |
|**till**| Ej till - ange i kod | **Att**| Det här värdet är inställt på det telefonnummer som SMS-texten skickas till.|
|**Från**|**Från** | **Från**| Det här värdet är inställt på det telefonnummer som SMS-texten skickas från.|
|**Kroppen**|**Kroppen** | **Brödtext**| Det här värdet kan användas för att hårdkoda SMS:et om du inte behöver ställa in det dynamiskt i koden för din funktion. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Utlösare och bindningar för Azure-funktioner](functions-triggers-bindings.md)
