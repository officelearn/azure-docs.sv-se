---
title: Azure Functions SendGrid-bindningar
description: Referens för Azure Functions SendGrid-bindningar.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: dc113417be3df97b9ab9509c30a1f23e1eeaf35b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086286"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid-bindningar

Den här artikeln beskriver hur du skickar e-post med hjälp av [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) -bindningar i Azure Functions. Azure Functions stöder en utgående bindning för SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

SendGrid-bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-paketet, version 2. x. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

SendGrid-bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet-paketet, version 3. x. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Exempel

Se exempel språkspecifika:

* [C#](#c-example)
* [C#-skript (.csx)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>C#exempel

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som använder en Service Bus Queue-utlösare och en SendGrid utgående bindning.

#### <a name="synchronous-c-example"></a>Synkront C# exempel:

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
#### <a name="asynchronous-c-example"></a>Asynkront C# exempel:

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

Du kan utelämna inställningen för attributets `ApiKey` egenskap om du har din API-nyckel i en app-inställning med namnet "AzureWebJobsSendGridApiKey".

### <a name="c-script-example"></a>C#skript exempel

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

### <a name="java-example"></a>Java-exempel

I följande exempel används `@SendGridOutput` anteckningen från [Java Functions runtime library](/java/api/overview/azure/functions/runtime) för att skicka ett e-postmeddelande med SendGrid utgående bindning.

```java
@FunctionName("SendEmail")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @SendGridOutput(
                name = "email", dataType = "String", apiKey = "SendGridConnection", to = "test@example.com", from = "test@example.com",
                subject= "Sending with SendGrid", text = "Hello from Azure Functions"
                ) OutputBinding<String> email
            )
    {
        String name = request.getBody().orElse("World");

        final String emailBody = "{\"personalizations\":" +
                                    "[{\"to\":[{\"email\":\"test@example.com\"}]," +
                                    "\"subject\":\"Sending with SendGrid\"}]," +
                                    "\"from\":{\"email\":\"test@example.com\"}," +
                                    "\"content\":[{\"type\":\"text/plain\",\"value\": \"Hello" + name + "\"}]}";

        email.setValue(emailBody);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
```

### <a name="javascript-example"></a>JavaScript-exempel

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

## <a name="attributes"></a>Attribut

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

Ett fullständigt exempel finns i [ C# exemplet](#c-example).

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `SendGrid` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**type**|| Required-måste anges till `sendGrid`.|
|**direction**|| Required-måste anges till `out`.|
|**name**|| Obligatoriskt – variabel namnet som används i funktions koden för begäran eller begär ande texten. Det här värdet ```$return``` är när det bara finns ett retur värde. |
|**apiKey**|**ApiKey**| Namnet på en app-inställning som innehåller din API-nyckel. Om den inte anges är standardinställnings namnet för appen "AzureWebJobsSendGridApiKey".|
|**to**|**To**| mottagarens e-postadress. |
|**from**|**From**| avsändarens e-postadress. |
|**subject**|**Subject**| e-postmeddelandets ämne. |
|**text**|**Text**| e-postinnehållet. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-inställningar

Det här avsnittet beskrivs de globala konfigurationsinställningarna som är tillgängliga för den här bindningen i version 2.x. Host.json-exempelfilen nedan innehåller bara till version 2.x inställningarna för den här bindningen. Mer information om konfigurationsinställningar i version 2.x kan se [host.json-referens för Azure Functions version 2.x](functions-host-json.md).

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

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|from|Saknas|Avsändarens e-postadress för alla funktioner.| 


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
