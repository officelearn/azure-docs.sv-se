---
title: "Med hjälp av .NET-klassbibliotek med Azure Functions | Microsoft Docs"
description: "Lär dig hur du skapar .NET-klassbibliotek för användning med Azure Functions"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: 9f5db0c2-a88e-4fa8-9b59-37a7096fc828
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/10/2017
ms.author: glenga
ms.openlocfilehash: 6f6f89d62f1442198f80247cc5c433aa0c54030b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="using-net-class-libraries-with-azure-functions"></a>Med hjälp av .NET-klassbibliotek med Azure Functions

Azure Functions stöder publicerar en klassbiblioteket som implementeringen för en eller flera funktioner utöver skriptfiler. Vi rekommenderar att du använder den [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/).

## <a name="prerequisites"></a>Krav 

Den här artikeln har följande krav:

- [Visual Studio 2017 version 15,3](https://www.visualstudio.com/vs/), eller en senare version.
- Installera den **Azure-utveckling** arbetsbelastning.

## <a name="functions-class-library-project"></a>Funktioner för klassbiblioteksprojektet

Skapa ett nytt Azure Functions-projekt från Visual Studio. Den nya projektmallen för skapar filerna *host.json* och *local.settings.json*. Du kan [Anpassa inställningar för Azure Functions-runtime i host.json](functions-host-json.md). 

Filen *local.settings.json* lagrar app-inställningar, anslutningssträngar och inställningar för Azure Functions Core verktyg. Läs mer om strukturen i [koden och testa Azure functions lokalt](functions-run-local.md#local-settings-file).

### <a name="functionname-attribute"></a>FunctionName attribut

Attributet [ `FunctionNameAttribute` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/FunctionNameAttribute.cs) markerar en metod som en startpunkt för funktionen. Den måste användas med en utlösare och 0 eller fler indata och utdata bindningar.

### <a name="conversion-to-functionjson"></a>Konvertering till function.json

När du skapar ett Azure Functions-projekt, en *function.json* filen skapas i katalogen för den funktionen. Katalognamnet är samma som funktionen som den `[FunctionName]` attributet anger. Den *function.json* filen innehåller utlösare och bindningar och pekar på sammansättningen projektfilen.

Den här konverteringen utförs av NuGet-paketet [Microsoft\.NET\.Sdk\.funktioner](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). Källan är tillgänglig i GitHub-repo [azure\-funktioner\-vs\-skapa\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="triggers-and-bindings"></a>Utlösare och bindningar 

I följande tabell visas utlösare och bindningar som är tillgängliga i en Azure Functions klassbiblioteksprojektet. Alla attribut som finns i namnområdet `Microsoft.Azure.WebJobs`.

| Bindning | Attribut | NuGet-paketet |
|------   | ------    | ------        |
| [BLOB storage-utlösare, indata, utdata](#blob-storage) | [BlobAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | [Blobblagring] |
| [Cosmos DB utlösare](#cosmos-db) | [CosmosDBTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] | 
| [Cosmos DB inkommande och utgående](#cosmos-db) | [DocumentDBAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] |
| [Händelseutlösare NAV- och utdata](#event-hub) | [EventHubTriggerAttribute], [EventHubAttribute] | [Microsoft.Azure.WebJobs.ServiceBus] |
| [Den externa filen inkommande och utgående](#api-hub) | [ApiHubFileAttribute] | [Microsoft.Azure.WebJobs.Extensions.ApiHub] |
| [HTTP- och webhook-utlösare](#http) | [HttpTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.Http] |
| [Mobile Apps inkommande och utgående](#mobile-apps) | [MobileTableAttribute] | [Microsoft.Azure.WebJobs.Extensions.MobileApps] | 
| [Notification Hubs utdata](#nh) | [NotificationHubAttribute] | [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] | 
| [Queue storage utlösare och utdata](#queue) | [QueueAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [SendGrid-utdata](#sendgrid) | [SendGridAttribute] | [Microsoft.Azure.WebJobs.Extensions.SendGrid] | 
| [Service Bus-utlösare och utdata](#service-bus) | [ServiceBusAttribute], [ServiceBusAccountAttribute] | [Microsoft.Azure.WebJobs.ServiceBus]
| [Table storage inkommande och utgående](#table) | [TableAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Timerutlösare](#timer) | [TimerTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions] | 
| [Twilio-utdata](#twilio) | [TwilioSmsAttribute] | [Microsoft.Azure.WebJobs.Extensions.Twilio] | 

<a name="blob-storage"></a>

### <a name="blob-storage-trigger-input-bindings-and-output-bindings"></a>BLOB storage-utlösare, ange bindningar och utdata bindningar

Azure Functions stöder utlösaren indata och utdata bindningar för Azure Blob storage. Mer information om bindande uttryck och metadata finns [Azure Functions Blob storage bindningar](functions-bindings-storage-blob.md).

En blob-utlösare har definierats med den `[BlobTrigger]` attribut. Du kan använda attributet `[StorageAccount]` definiera app Inställningens namn som innehåller anslutningssträngen till storage-konto som används av ett hela funktion eller en klass.

```csharp
[StorageAccount("AzureWebJobsStorage")]
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

BLOB som indata och utdata har definierats med hjälp av den `[Blob]` attributet tillsammans med en `FileAccess` parameter som anger att läsa eller skriva. I följande exempel används en blob utlösare och blob-utdatabindning.

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

<a name="cosmos-db"></a>

### <a name="cosmos-db-trigger-input-bindings-and-output-bindings"></a>Cosmos DB utlösning, ange bindningar och utdata bindningar

Azure Functions stöder utlösare och indata och utdata bindningar för Cosmos DB. Mer information om funktionerna i Cosmos-DB-bindning finns [Azure Functions Cosmos DB bindningar](functions-bindings-documentdb.md).

Utlös från ett Cosmos-DB-dokument genom att använda attributet `[CosmosDBTrigger]` i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. Följande exempel utlösare från en specifik `database` och `collection`. Inställningen `myCosmosDB` innehåller anslutningen till Cosmos-DB-instans. 

```csharp
[FunctionName("DocumentUpdates")]
public static void Run(
    [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
IReadOnlyList<Document> documents, TraceWriter log)
{
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
}
```

Om du vill binda till en Cosmos-DB-dokumentet använder attributet `[DocumentDB]` i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. I följande exempel har en kö-utlösare och ett DocumentDB-API-utdatabindning.

```csharp
[FunctionName("QueueToDocDB")]        
public static void Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, 
    [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
{
    document = new { Text = myQueueItem, id = Guid.NewGuid() };
}

```

<a name="event-hub"></a>

### <a name="event-hubs-trigger-and-output"></a>Händelseutlösare NAV- och utdata

Azure Functions stöder utlösa och utgående bindningar för Händelsehubbar. Mer information finns i [Azure Functions Event Hub bindningar](functions-bindings-event-hubs.md).

Typerna `[Microsoft.Azure.WebJobs.ServiceBus.EventHubTriggerAttribute]` och `[Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute]` definieras i NuGet-paketet [Microsoft.Azure.WebJobs.ServiceBus]. 

I följande exempel används en Event Hub-utlösare:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

I följande exempel har en Händelsehubb utdata, med hjälp av metoden returvärdet som utdata:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

<a name="api-hub"></a>

### <a name="external-file-input-and-output"></a>Den externa filen inkommande och utgående

Azure Functions stöder utlösaren indata och utdata bindningar för externa filer, till exempel Google Drive, Dropbox och OneDrive. Läs mer i [Azure Functions extern fil bindningar](functions-bindings-external-file.md). Attributen `[ExternalFileTrigger]` och `[ExternalFile]` definieras i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.ApiHub].

C# exemplet nedan visar en extern fil indata och utdata bindning. Koden kopieras filen till utdatafilen.

```csharp
[StorageAccount("MyStorageConnection")]
[FunctionName("ExternalFile")]
[return: ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}-Copy", FileAccess.Write)]
public static string Run([QueueTrigger("myqueue-items")] string myQueueItem, 
    [ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}", FileAccess.Read)] string myInputFile, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    return myInputFile;
}
```

<a name="http"></a>

### <a name="http-and-webhooks"></a>HTTP och webhooks

Använd den `HttpTrigger` attribut för att ange en HTTP-utlösare eller webhooken. Det här attributet definieras i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.Http]. Du kan anpassa åtkomstnivå, webhook-typ, väg och metoder. I följande exempel definieras en HTTP-utlösare med anonym autentisering och _genericJson_ webhook-typen.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a name="mobile-apps"></a>

### <a name="mobile-apps-input-and-output"></a>Mobile Apps inkommande och utgående

Azure Functions stöder indata och utdata bindningar för Mobile Apps. Läs mer i [Azure Functions Mobile Apps bindningar](functions-bindings-mobile-apps.md). Attributet `[MobileTable]` har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.MobileApps].

I följande exempel visas en Mobile Apps-utdatabindning:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

<a name="nh"></a>

### <a name="notification-hubs-output"></a>Notification Hubs utdata

Azure Functions stöder en output-bindning för Notification Hubs. Läs mer i [Azure Functions Notification Hub-utdatabindning](functions-bindings-notification-hubs.md). Attributet `[NotificationHub]` har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs].

<a name="queue"></a>

### <a name="queue-storage-trigger-and-output"></a>Queue storage utlösare och utdata

Azure Functions stöder utlösa och utgående bindningar för Azure köer. Mer information finns i [Azure Functions Queue Storage bindningar](functions-bindings-storage-queue.md).

I följande exempel visas hur du använder funktionen returtypen med en kö utdata bindning, med hjälp av `[Queue]` attribut. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // HTTP trigger with queue output binding
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}

```

Om du vill definiera en utlösare för kön, använder den `[QueueTrigger]` attribut.
```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // Queue trigger
    [FunctionName("QueueTrigger")]
    [StorageAccount("AzureWebJobsStorage")]
    public static void QueueTrigger([QueueTrigger("myqueue-items")] string myQueueItem, TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}

```


<a name="sendgrid"></a>

### <a name="sendgrid-output"></a>SendGrid-utdata

Azure Functions stöder en SendGrid utdata bindning för att skicka e-post via programmering. Läs mer i [Azure Functions SendGrid bindningar](functions-bindings-sendgrid.md).

Attributet `[SendGrid]` har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.Extensions.SendGrid]. En SendGrid-bindning kräver ett program inställning med namnet `AzureWebJobsSendGridApiKey`, som innehåller din SendGrid API-nyckel. Det här är inställningen för standardnamnet för din SendGrid API-nyckel. Om du behöver ha fler än en SendGrid nyckel eller välj ett annat namn, kan du ange det här namnet med den `ApiKey` -egenskapen för den `SendGrid` bindning attribut, som i följande exempel:

    [SendGrid(ApiKey = "MyCustomSendGridKeyName")]

Följande är ett exempel på med en utlösare för Service Bus-kö och en SendGrid utdata bindning med `SendGridMessage`:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```
Observera att det här exemplet kräver SendGrid API-nyckeln ska vara i ett program som inställning med namnet `AzureWebJobsSendGridApiKey`.

<a name="service-bus"></a>

### <a name="service-bus-trigger-and-output"></a>Service Bus-utlösare och utdata

Azure Functions stöder utlösa och utgående bindningar för Service Bus-köer och ämnen. Mer information om hur du konfigurerar bindningar finns [Azure Functions Service Bus-bindningar](functions-bindings-service-bus.md).

Attributen `[ServiceBusTrigger]` och `[ServiceBus]` definieras i NuGet-paketet [Microsoft.Azure.WebJobs.ServiceBus]. 

Följande är ett exempel på en utlösare för Service Bus-kö:

```csharp
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run([ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

Följande är ett exempel på en Service Bus-utdata bindning, med Returtypen för metoden som utdata:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```        

<a name="table"></a>

### <a name="table-storage-input-and-output"></a>Table storage inkommande och utgående

Azure Functions stöder indata och utdata bindningar för Azure Table storage. Läs mer i [Azure Functions Table storage bindningar](functions-bindings-storage-table.md).

I följande exempel är en klass med två funktioner som visar tabellen lagring utgående och inkommande bindningar. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }

    // use the metadata parameter "queueTrigger" to bind the queue payload
    [FunctionName("TableInput")]
    public static void TableInput([QueueTrigger("table-items")] string input, [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, TraceWriter log)
    {
        log.Info($"C# function processed: {poco.Text}");
    }
}

```

<a name="timer"></a>

### <a name="timer-trigger"></a>Timerutlösare

Azure Functions har en timer utlösaren bindning som låter dig köra din funktionskod baserat på ett definierat schema. Läs mer om funktionerna i bindningen i [schemalägga kodkörning med Azure Functions](functions-bindings-timer.md).

På planen förbrukning, kan du definiera scheman med en [CRON-uttryck](http://en.wikipedia.org/wiki/Cron#CRON_expression). Om du använder en App Service-Plan kan använda du också en TimeSpan-sträng. 

I följande exempel definieras en timer som utlösare som körs var femte minut:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

<a name="twilio"></a>

### <a name="twilio-output"></a>Twilio-utdata

Azure Functions stöder Twilio utdata bindningar om du vill aktivera dina funktioner att skicka SMS textmeddelanden. Läs mer i [skicka SMS-meddelanden från Azure-funktioner med hjälp av Twilio-utdatabindning](functions-bindings-twilio.md). 

Attributet `[TwilioSms]` har definierats i paketet [Microsoft.Azure.WebJobs.Extensions.Twilio].

I följande C#-exempel används en kö utlösare och en Twilio-utdatabindning:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

<!-- NuGet packages --> 
[Microsoft.Azure.WebJobs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.DocumentDB]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB/1.1.0-beta4
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.MobileApps]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.SendGrid]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.Http]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http/1.0.0-beta1
[Microsoft.Azure.WebJobs.Extensions.BotFramework]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.BotFramework/1.0.15-beta
[Microsoft.Azure.WebJobs.Extensions.ApiHub]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApiHub/1.0.0-beta4
[Microsoft.Azure.WebJobs.Extensions.Twilio]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/2.1.0-beta1


<!-- Links to source --> 
[DocumentDBAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs
[CosmosDBTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/Trigger/CosmosDBTriggerAttribute.cs
[EventHubAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs
[EventHubTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs
[MobileTableAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs
[NotificationHubAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs 
[ServiceBusAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs
[ServiceBusAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs
[QueueAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs
[StorageAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs
[BlobAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs
[TableAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs
[TwilioSmsAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs
[SendGridAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs
[HttpTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs
[ApiHubFileAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.ApiHub/ApiHubFileAttribute.cs
[TimerTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs
