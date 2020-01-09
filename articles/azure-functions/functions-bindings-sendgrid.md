---
title: Azure Functions SendGrid-bindningar
description: Referens för Azure Functions SendGrid-bindningar.
author: craigshoemaker
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: a0731a66af32b45215145c1d4f4404eb008cf897
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75410050"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid-bindningar

Den här artikeln beskriver hur du skickar e-post med hjälp av [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) -bindningar i Azure Functions. Azure Functions stöder en utgående bindning för SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

SendGrid-bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-paketet, version 2. x. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

SendGrid-bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-paketet, version 3. x. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) lagringsplatsen.

> [!NOTE]
> Version 2. x och högre skapar inte ämnet eller prenumerationen som kon figurer ATS i `ServiceBusTrigger`-instansen. De här versionerna är baserade på [Microsoft. Azure. Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) , som inte hanterar hantering av köer.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Exempel

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som använder en Service Bus Queue-utlösare och en SendGrid utgående bindning.

### <a name="synchronous"></a>Synkron

```cs
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

Du kan utelämna inställningen för attributets egenskap `ApiKey` om du har API-nyckeln i en app-inställning med namnet "AzureWebJobsSendGridApiKey".

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en SendGrid utgående bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen.

Här är bindningsdata i den *function.json* fil:

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

Den [configuration](#configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en SendGrid utgående bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen.

Här är bindningsdata i den *function.json* fil:

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

Den [configuration](#configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

I följande exempel visas en HTTP-utlöst funktion som skickar ett e-postmeddelande med SendGrid-bindningen. Du kan ange standardvärden i bindnings konfigurationen. Till exempel konfigureras *från* -e-postadressen i *Function. JSON*. 

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

# <a name="javatabjava"></a>[Java](#tab/java)

I följande exempel används `@SendGridOutput` kommentar från [Java Functions runtime library](/java/api/overview/azure/functions/runtime) för att skicka ett e-postmeddelande med SendGrid utgående bindning.

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Använd attributet [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) i [ C# klass bibliotek](functions-dotnet-class-library.md).

Information om vilka egenskaper för attribut som du kan konfigurera finns i [konfiguration](#configuration). Här är en `SendGrid` attributet exemplet i signaturen för metoden:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Ett fullständigt exempel finns i [ C# exemplet](#example).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

Med [SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) -anteckningen kan du konfigurera SendGrid-bindningen genom att ange konfigurations värden. Se avsnittet [exempel](#example) och [konfiguration](#configuration) för mer information.

---

## <a name="configuration"></a>Konfiguration

I följande tabell visas de egenskaper för bindnings konfiguration som är tillgängliga i filen *Function. JSON* och `SendGrid` attributet/kommentaren.

| *Function. JSON* -egenskap | Attribut/antecknings egenskap | Beskrivning | Valfritt |
|--------------------------|-------------------------------|-------------|----------|
| typ || Måste anges till `sendGrid`.| Inga |
| riktning || Måste anges till `out`.| Inga |
| namn || Variabel namnet som används i funktions koden för begäran eller begär ande texten. Det här värdet är `$return` när det bara finns ett retur värde. | Inga |
| apiKey | ApiKey | Namnet på en app-inställning som innehåller din API-nyckel. Om den inte anges är standardinställnings namnet för appen *AzureWebJobsSendGridApiKey*.| Inga |
| till| Till | Mottagarens e-postadress. | Ja |
| från| Från | Avsändarens e-postadress. |  Ja |
| subject| Ämne | E-postmeddelandets ämne. | Ja |
| text| Text | E-postinnehållet. | Ja |

Valfria egenskaper kan ha standardvärden definierade i bindningen och antingen läggas till eller åsidosättas program mässigt.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exemplet Host. JSON-filen nedan innehåller bara version 2. x +-inställningarna för den här bindningen. Mer information om globala konfigurations inställningar i version 2. x och mer finns i [Host. JSON-referens för Azure Functions](functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x, se [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md).

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
|från|Ej tillämpligt|Avsändarens e-postadress för alla funktioner.| 


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
