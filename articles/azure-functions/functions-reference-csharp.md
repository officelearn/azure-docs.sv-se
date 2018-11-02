---
title: Azure Functions C# skript för utvecklare
description: Förstå hur du utvecklar Azure Functions med hjälp av C#-skript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 6c9172140691f7107d3907ab86938d879989a6c0
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748246"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions C#-skript (.csx) för utvecklare

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Den här artikeln ger en introduktion till utvecklar Azure Functions med hjälp av C#-skript (*.csx*).

Azure Functions har stöd för C# och C#-skript som programmeringsspråk. Om du letar efter information [med C# i en Visual Studio-klassbiblioteksprojektet](functions-develop-vs.md), se [C#-utvecklarreferens](functions-dotnet-class-library.md).

Den här artikeln förutsätter att du redan har läst den [utvecklarguide för Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Hur .csx fungerar

C#-skript upplevelsen för Azure Functions är baserad på den [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Data flödar till C#-funktionen via metodargument. Argumentnamn anges i en `function.json` fil och det är fördefinierade namn för att komma åt saker som funktionen loggare och uppsägning token.

Den *.csx* format kan du skriva mindre ”formaterad” och fokusera på att bara en C#-funktion. I stället för omsluter allt i ett namnområde och en klass kan bara definiera en `Run` metod. Inkludera alla referenser till sammansättningar och namnområden i början av filen som vanligt.

En funktionsapp *.csx* filer kompileras när en instans har initierats. Den här kompileringssteg innebär exempelvis kallstart kan ta längre tid för C#-skriptfunktioner jämfört med C#-klassbibliotek. Den här kompileringssteg är också varför C#-skript-funktioner kan redigeras i Azure-portalen, C#-klassbibliotek finns inte.

## <a name="folder-structure"></a>mappstruktur

Mappstrukturen för ett C#-skript projekt ser ut som följande:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Det finns en delad [host.json](functions-host-json.md) -fil som kan användas för att konfigurera funktionsappen. Varje funktion har sina egna kodfilen (.csx) och bindningen konfigurationsfil (function.json).

Bindningen-tillägg som krävs i [version 2.x](functions-versions.md) funktioner runtime definieras i den `extensions.csproj` -fil med faktiska library-filer i den `bin` mapp. När du utvecklar lokalt, måste du [registrera tillägg av bindning](functions-triggers-bindings.md#local-development-azure-functions-core-tools). När du utvecklar funktioner i Azure-portalen görs denna registrering för dig.

## <a name="binding-to-arguments"></a>Bindning till argument

Inkommande eller utgående data är bunden till en C#-skript funktionsparameter via den `name` -egenskapen i den *function.json* konfigurationsfilen. I följande exempel visas en *function.json* fil och *run.csx* -filen för en funktion som utlöses av kön. Den parameter som tar emot data från kömeddelandet heter `myQueueItem` eftersom det är värdet för den `name` egenskapen.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

Den `#r` instruktionen förklaras [senare i den här artikeln](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Typer som stöds för bindningar

Varje bindning har sin egen typer som stöds. Exempelvis kan en blob-utlösare kan användas med en strängparameter, en POCO-parameter, en `CloudBlockBlob` parametern eller någon av flera andra typer som stöds. Den [bindning referensartikeln för blobbindningar](functions-bindings-storage-blob.md#trigger---usage) visar alla stöds parametertyper för blobutlösare. Mer information finns i [utlösare och bindningar](functions-triggers-bindings.md) och [bindning referensdokument för varje bindningstyp](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Refererar till egna klasser

Om du vill använda en anpassad klass för vanlig gamla CLR objekt (POCO) kan du inkludera klassdefinitionen i samma fil eller placerar den i en separat fil.

I följande exempel visas en *run.csx* exempel som innehåller en POCO-klassdefinition.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

En POCO-klass måste ha en get- och set som definierats för varje egenskap.

## <a name="reusing-csx-code"></a>Återanvända .csx kod

Du kan använda klasser och metoder som definierats i andra *.csx* filer i din *run.csx* fil. Gör detta genom att använda `#load` direktiv i din *run.csx* fil. I följande exempel visas en rutin för loggning med namnet `MyLogger` delas i *myLogger.csx* och läsas in i *run.csx* med hjälp av den `#load` direktiv:

Exempel *run.csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exempel *mylogger.csx*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Med hjälp av en delad *.csx* filen är ett vanligt mönster när du vill skriva starkt de data som skickas mellan funktioner genom att använda ett POCO-objekt. I följande förenklat exempel, en HTTP-utlösare och köutlösare delar en POCO-objekt med namnet `Order` starkt skriver ordningsdata:

Exempel *run.csx* för HTTP-utlösare:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Exempel *run.csx* för kö-utlösare:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Exempel *order.csx*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

Du kan använda en relativ sökväg med den `#load` direktiv:

* `#load "mylogger.csx"` läser in en fil i mappen funktion.
* `#load "loadedfiles\mylogger.csx"` läser in en fil som finns i en mapp i mappen funktion.
* `#load "..\shared\mylogger.csx"` läser in en fil i en mapp på samma nivå som funktionen mappen, det vill säga direkt under *wwwroot*.

Den `#load` direktiv fungerar bara med *.csx* filer, inte med *.cs* filer.

## <a name="binding-to-method-return-value"></a>Bindning till returvärdet för metoden

Du kan använda metoden returvärdet för en utdatabindning genom att använda namnet `$return` i *function.json*. Exempel finns i [utlösare och bindningar](functions-triggers-bindings.md#using-the-function-return-value).

Använd det returnera värdet bara om lyckad körning av en funktion alltid resulterar i ett returvärde ska skickas till utdata-bindning. Annars kan du använda `ICollector` eller `IAsyncCollector`, vilket visas i följande avsnitt.

## <a name="writing-multiple-output-values"></a>Skriva flera utdatavärden

Skriva flera värden till en utdatabindning eller om en lyckad funktionsanrop inte kan resultera i att skicka till utdata-bindning kan du använda den [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) eller [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typer. Dessa typer är lässkyddad samlingar som har skrivits till utdata-bindning när metoden har slutförts.

Det här exemplet skriver flera Kömeddelanden i samma kö med `ICollector`:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Loggning

Logga utdata på din direktuppspelningsloggarna i C#, innehålla ett argument av typen [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Vi rekommenderar att du namnger den `log`. Undvik att använda `Console.Write` i Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Information om ett nyare loggningsramverk som du kan använda i stället för `TraceWriter`, se [skriva loggar i C#-funktioner](functions-monitoring.md#write-logs-in-c-functions) i den **övervaka Azure Functions** artikeln.

## <a name="async"></a>Async

Att göra en funktion [asynkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), använda den `async` nyckelord och returnera en `Task` objekt.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Du kan inte använda `out` parametrar i async-funktioner. Utdatabindningar, använda den [fungera returvärdet](#binding-to-method-return-value) eller en [insamlingsobjektet](#writing-multiple-output-values) i stället.

## <a name="cancellation-tokens"></a>Annulleringstoken

En funktion kan acceptera en [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) parametern, som gör det möjligt för operativsystemet för att meddela din kod när funktionen ska avslutas. Du kan använda det här meddelandet för att kontrollera att funktionen inte avslutas oväntat på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du kontrollerar om nära förestående uppsägning av funktionen.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>Importera namnområden

Om du vill importera namnområden kan du göra så som vanligt, med den `using` satsen.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Följande namnrymder importeras automatiskt och därför är valfria:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Refererar till externa sammansättningar

Lägg till referenser för framework-sammansättningar, med hjälp av den `#r "AssemblyName"` direktiv.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Följande sammansättningar läggs automatiskt till i Azure-funktioner som är värd för miljön:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Följande sammansättningar kan refereras till av enkel-namn (till exempel `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Referera till anpassade sammansättningar

Om du vill referera till en anpassad sammansättning, kan du använda antingen en *delade* sammansättningen eller en *privata* sammansättningen:

* Delade sammansättningar är gemensamma för alla funktioner i en funktionsapp. Om du vill referera till en anpassad sammansättning, att ladda upp sammansättningen till en mapp med namnet `bin` i din [funktionen app-rotmappen](functions-reference.md#folder-structure) (wwwroot).

* Privata sammansättningar är en del av en viss funktion kontext och stöd för separat inläsning med olika versioner. Privata sammansättningar ska överföras i en `bin` mapp i katalogen funktion. Referera till sammansättningar med filnamnet, till exempel `#r "MyAssembly.dll"`.

Information om hur du överför filer till mappen funktionen finns i avsnittet på [paket på](#using-nuget-packages).

### <a name="watched-directories"></a>Bevakad kataloger

Katalogen som innehåller funktionen skriptfilen bevakas automatiskt för ändringar i paket. Titta på sammansättningen ändringar i andra kataloger, lägga till dem i den `watchDirectories` lista i [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Med hjälp av NuGet-paket

Om du vill använda NuGet-paket i en C#-funktion, ladda upp en *project.json* filen till funktionens mapp på den funktionsapp filsystem. Här är ett exempel *project.json* -fil som läggs en referens till Microsoft.ProjectOxford.Face version 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

I Azure Functions 1.x, endast .NET Framework 4.6 stöds, så se till att din *project.json* filen anger `net46` som visas här.

När du laddar upp en *project.json* körnings-fil hämtar paketen och lägger automatiskt till referenser till sammansättningar för paketet. Du behöver inte lägga till `#r "AssemblyName"` direktiv. Att använda typer som definieras i NuGet-paket. Lägg bara till de nödvändiga `using` -uttryck för att din *run.csx* fil. 

I Functions-körning NuGet återställningen fungerar genom att jämföra `project.json` och `project.lock.json`. Om datum och tid stämplar filernas **inte** matchar, en återställning av NuGet körs och NuGet nedladdningar uppdateras paket. Men om datum och tid stämplar filernas **gör** matchar, NuGet inte utför en återställning. Därför `project.lock.json` bör inte distribuerats som orsakar NuGet för att hoppa över paketåterställning. Undvik att distribuera låsfilen genom att lägga till den `project.lock.json` till den `.gitignore` filen.

Ange om du vill använda en anpassad NuGet feed på flödet på en *Nuget.Config* filen i roten för Funktionsappen. Mer information finns i [konfigurera NuGet beteende](/nuget/consume-packages/configuring-nuget-behavior).

### <a name="using-a-projectjson-file"></a>Med hjälp av en project.json-fil

1. Öppna funktionen i Azure-portalen. Fliken loggar visar resultatet för paket-installationen.
2. Om du vill överföra en project.json-fil, använder du någon av metoderna som beskrivs i den [så här uppdaterar du funktionen appfiler](functions-reference.md#fileupdate) i referensavsnittet för Azure Functions-utvecklare.
3. Efter den *project.json* fil laddas upp kan du se utdata som liknar följande exempel i din funktion strömning loggen:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Miljövariabler

Hämta en miljövariabel eller en app som inställningsvärde `System.Environment.GetEnvironmentVariable`, vilket visas i följande kodexempel:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>Bindningen vid körning

I C# och andra .NET-språk, kan du använda en [tvingande](https://en.wikipedia.org/wiki/Imperative_programming) bindning mönster, inte den [ *deklarativa* ](https://en.wikipedia.org/wiki/Declarative_programming) bindningar i *function.json*. Tvingande bindning är användbar när bindande parametrar behöver beräknas vid körning i stället för design tidpunkt. Med det här mönstret kan du binda till stöds indata och utdata bindningar på direkt i funktionskoden.

Definiera konkurrensavseende bindning på följande sätt:

- **Inte** Lägg till en post i *function.json* för din önskade tvingande bindningar.
- Pass i en indataparameter [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) eller [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Använd följande C#-mönster för att utföra databindningen.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` är .NET-attribut som definierar din bindningen och `T` är en inkommande eller utgående typ som stöds av den bindningstypen. `T` får inte vara en `out` parametertypen (till exempel `out JObject`). Till exempel Mobile Apps-tabell utdata har stöd för bindning [sex utdata typer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), men du kan bara använda [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) eller [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)för `T`.

### <a name="single-attribute-example"></a>Attribut-exempel

Följande exempelkod skapar en [blob Storage-utdatabindning](functions-bindings-storage-blob.md#output) med blob sökväg som anges vid körningstillfället, sedan skriver en sträng till bloben.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definierar den [lagringsblob](functions-bindings-storage-blob.md) indata eller utdata bindning, och [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) är av typen stöds utdata-bindning.

### <a name="multiple-attribute-example"></a>Flera attribut-exempel

I föregående exempel hämtar appinställningen för function-appens main anslutningssträng för Lagringskonto (vilket är `AzureWebJobsStorage`). Du kan ange en anpassad app-inställning som ska användas för lagringskontot genom att lägga till den [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) och för att skicka attributet matrisen i `BindAsync<T>()`. Använd en `Binder` parametern inte `IBinder`.  Exempel:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

I följande tabell visas .NET-attribut för varje bindningstypen och de paket som de definieras.

> [!div class="mx-codeBreakAll"]
| Bindning | Attribut | Lägg till referens |
|------|------|------|
| Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Lagringskö | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Lagringsblob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Storage-tabell | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mer information om utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Läs mer om bästa praxis för Azure Functions](functions-best-practices.md)
