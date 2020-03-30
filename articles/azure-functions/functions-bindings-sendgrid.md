---
title: SendGrid-bindningar för Azure-funktioner
description: Azure Functions SendGrid-bindningar referens.
author: craigshoemaker
ms.topic: reference
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 9ed2b81c12c698822b9542bb6903189c865b572b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277471"
---
# <a name="azure-functions-sendgrid-bindings"></a>SendGrid-bindningar för Azure-funktioner

I den här artikeln beskrivs hur du skickar e-post med Hjälp av [SendGrid-bindningar](https://sendgrid.com/docs/User_Guide/index.html) i Azure Functions. Azure Functions stöder en utdatabindning för SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - Funktioner 1.x

SendGrid-bindningarna finns i [paketet Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet, version 2.x. Källkoden för paketet finns i [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) GitHub-databasen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket - Funktioner 2.x och högre

SendGrid-bindningarna finns i [paketet Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet, version 3.x. Källkoden för paketet finns i [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) GitHub-databasen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som använder en Service Bus-köutlösare och en SendGrid-utdatabindning.

### <a name="synchronous"></a>Synkron

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

### <a name="asynchronous"></a>Asynkron

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

Du kan utelämna inställningen av `ApiKey` attributets egenskap om du har din API-nyckel i en appinställning med namnet "AzureWebJobsSendGridApiKey".

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en SendGrid-utdatabindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen.

Här är bindningsdata i *filen function.json:*

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en SendGrid-utdatabindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen.

Här är bindningsdata i *filen function.json:*

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context, input) {
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en HTTP-utlöst funktion som skickar ett e-postmeddelande med SendGrid-bindningen. Du kan ange standardvärden i bindningskonfigurationen. Från-e-postadressen är till exempel konfigurerad i *function.json*. *from* 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "sendGrid",
      "name": "sendGridMessage",
      "direction": "out",
      "apiKey": "SendGrid_API_Key",
      "from": "sender@contoso.com"
    }
  ]
}
```

Följande funktion visar hur du kan ange anpassade värden för valfria egenskaper.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, sendGridMessage: func.Out[str]) -> func.HttpResponse:

    value = "Sent from Azure Functions"

    message = {
        "personalizations": [ {
          "to": [{
            "email": "user@contoso.com"
            }]}],
        "subject": "Azure Functions email with SendGrid",
        "content": [{
            "type": "text/plain",
            "value": value }]}

    sendGridMessage.set(json.dumps(message))

    return func.HttpResponse(f"Sent")
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel `@SendGridOutput` används anteckningen från [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime) för att skicka ett e-postmeddelande med SendGrid-utdatabindningen.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerSendGrid {

    @FunctionName("HttpTriggerSendGrid")
    public HttpResponseMessage run(

        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.GET, HttpMethod.POST },
            authLevel = AuthorizationLevel.FUNCTION)
                HttpRequestMessage<Optional<String>> request,

        @SendGridOutput(
            name = "message",
            dataType = "String",
            apiKey = "SendGrid_API_Key",
            to = "user@contoso.com",
            from = "sender@contoso.com",
            subject = "Azure Functions email with SendGrid",
            text = "Sent from Azure Functions")
                OutputBinding<String> message,

        final ExecutionContext context) {

        final String toAddress = "user@contoso.com";
        final String value = "Sent from Azure Functions";

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"personalizations\": [{ \"to\": [{ \"email\": \"%s\"}]}],")
            .append("\"content\": [{\"type\": \"text/plain\", \"value\": \"%s\"}]")
            .append("}");

        final String body = String.format(builder.toString(), toAddress, value);

        message.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [attributet SendGrid.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs)

Information om attributegenskaper som du kan konfigurera finns i [Konfiguration](#configuration). Här är `SendGrid` ett attributexempel i en metodsignatur:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
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

[Med SendGridOutput-anteckningen](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) kan du deklarativt konfigurera SendGrid-bindningen genom att ange konfigurationsvärden. Mer information finns i [exempel-](#example) och konfigurationsavsnitten. [configuration](#configuration)

---

## <a name="configuration"></a>Konfiguration

I följande tabell visas de bindningskonfigurationsegenskaper som `SendGrid` är tillgängliga i *filen function.json* och attributet/anteckningen.

| *egenskapen function.json* | Egenskapen Attribut/anteckning | Beskrivning | Valfri |
|--------------------------|-------------------------------|-------------|----------|
| typ |Saknas| Måste ställas `sendGrid`in på .| Inga |
| riktning |Saknas| Måste ställas `out`in på .| Inga |
| namn |Saknas| Variabelnamnet som används i funktionskoden för begäran eller begärandetexten. Det här `$return` värdet är när det bara finns ett returvärde. | Inga |
| apiKey (apiKey) | ApiKey (ApiKey) | Namnet på en appinställning som innehåller din API-nyckel. Om inte anges är standardnamn för appinställning *AzureWebJobsSendGridApiKey*.| Inga |
| till| Till | Mottagarens e-postadress. | Ja |
| Från| Från | Avsändarens e-postadress. |  Ja |
| Ämne| Subjekt | Ämnet för e-postmeddelandet. | Ja |
| text| Text | E-postinnehållet. | Ja |

Valfria egenskaper kan ha standardvärden definierade i bindningen och antingen tillagda eller åsidosatta programmässigt.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>värd.json-inställningar

I det här avsnittet beskrivs de globala konfigurationsinställningar som är tillgängliga för den här bindningen i version 2.x och senare. Exempelvärden.json filen nedan innehåller endast inställningarna för version 2.x+ för den här bindningen. Mer information om globala konfigurationsinställningar i version 2.x och senare finns i [host.json-referens för Azure Functions](functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x finns i [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|Från|Saknas|Avsändarens e-postadress för alla funktioner.| 


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Utlösare och bindningar för Azure-funktioner](functions-triggers-bindings.md)
