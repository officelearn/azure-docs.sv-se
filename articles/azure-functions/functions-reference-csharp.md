---
title: Azure Functions C# skriptutvecklare referens
description: Förstå hur du utvecklar Azure-funktioner med C#-skriptet.
author: craigshoemaker
ms.topic: reference
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 76af1f51c83e9554a51e6c17266fac739e6bd6b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276821"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Utvecklarreferens för Azure Functions C#-skript (.csx)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Den här artikeln är en introduktion till utvecklingen av Azure Functions med C#-skript (*.csx*).

Azure Functions stöder programmeringsspråk för C# och C#. Om du letar efter vägledning om [hur du använder C# i ett visual studio-klassbiblioteksprojekt](functions-develop-vs.md)läser du [referens för C#-utvecklare](functions-dotnet-class-library.md).

Den här artikeln förutsätter att du redan har läst [Azure Functions-utvecklarguiden](functions-reference.md).

## <a name="how-csx-works"></a>Så här fungerar CSX

C#-skriptupplevelsen för Azure Functions baseras på [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Data flödar in i din C#-funktion via metodargument. Argumentnamn anges i `function.json` en fil och det finns fördefinierade namn för åtkomst till saker som funktionslogger och annulleringstoken.

*Den .csx* format kan du skriva mindre "standard" och fokusera på att skriva bara en C # funktion. I stället för att radbryta allt i `Run` ett namnområde och en klass definierar du bara en metod. Inkludera eventuella sammansättningsreferenser och namnområden i början av filen som vanligt.

*Csx-filerna* i funktionsappen kompileras när en instans initieras. Det här kompileringssteget innebär att saker som kallstart kan ta längre tid för C#-skriptfunktioner jämfört med C#-klassbibliotek. Det här kompileringssteget är också anledningen till att C#-skriptfunktioner kan redigeras i Azure-portalen, medan C#-klassbibliotek inte är det.

## <a name="folder-structure"></a>Mappstrukturen

Mappstrukturen för ett C#-skriptprojekt ser ut så här:

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

Det finns en delad [host.json-fil](functions-host-json.md) som kan användas för att konfigurera funktionsappen. Varje funktion har sin egen kodfil (.csx) och bindning konfigurationsfil (function.json).

De bindningstillägg som krävs i [version 2.x och senare versioner](functions-versions.md) av funktionskörningen `bin` `extensions.csproj` definieras i filen, med de faktiska biblioteksfilerna i mappen. När du utvecklar lokalt måste du [registrera bindningstillägg.](./functions-bindings-register.md#extension-bundles) När du utvecklar funktioner i Azure-portalen görs den här registreringen åt dig.

## <a name="binding-to-arguments"></a>Bindning till argument

Indata eller utdata är bundna till en `name` C#-skriptfunktionsparameter via egenskapen i *konfigurationsfilen function.json.* I följande exempel visas en *function.json-fil* och *run.csx-fil* för en köutlöst funktion. Parametern som tar emot data `myQueueItem` från kömeddelandet namnges `name` eftersom det är egenskapens värde.

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

Uttalandet `#r` förklaras [senare i den här artikeln](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Typer som stöds för bindningar

Varje bindning har sina egna typer som stöds. En blob-utlösare kan till exempel användas med en strängparameter, en POCO-parameter, en `CloudBlockBlob` parameter eller någon av flera andra typer som stöds. [Bindningsreferensartikeln för blob-bindningar listar](functions-bindings-storage-blob-trigger.md#usage) alla parametertyper som stöds för blob-utlösare. Mer information finns i [Utlösare och bindningar](functions-triggers-bindings.md) och [bindningsreferensdokument för varje bindningstyp](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Referera till anpassade klasser

Om du behöver använda en anpassad POCO-klass (Plain Old CLR Object) kan du inkludera klassdefinitionen i samma fil eller placera den i en separat fil.

I följande exempel visas ett *run.csx-exempel* som innehåller en POCO-klassdefinition.

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

En POCO-klass måste ha en getter och en setter definierad för varje egenskap.

## <a name="reusing-csx-code"></a>Återanvända CSX-kod

Du kan använda klasser och metoder som definierats i andra *CSX-filer* i filen *run.csx.* Det gör du `#load` genom att använda direktiv i filen *run.csx.* I följande exempel delas en `MyLogger` loggningsrutin som heter i *myLogger.csx* och laddas i *run.csx* med `#load` hjälp av direktivet:

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

Exempel *mylogger.csx:*

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Att använda en delad *CSX-fil* är ett vanligt mönster när du vill skriva in data som skickas mellan funktioner med hjälp av ett POCO-objekt. I följande förenklade exempel delar en HTTP-utlösare och `Order` köutlösare ett POCO-objekt med namnet för att starkt skriva orderdata:

Exempel *på run.csx* för HTTP-utlösare:

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

Exempel *på run.csx* för köutlösare:

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

Exempel *order.csx:*

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

Du kan använda en `#load` relativ sökväg med direktivet:

* `#load "mylogger.csx"`läser in en fil som finns i funktionsmappen.
* `#load "loadedfiles\mylogger.csx"`läser in en fil som finns i en mapp i funktionsmappen.
* `#load "..\shared\mylogger.csx"`läser in en fil som finns i en mapp på samma nivå som funktionsmappen, det vill de vill ha, direkt under *wwwroot*.

Direktivet `#load` fungerar bara med *CSX-filer,* inte med *CS-filer.*

## <a name="binding-to-method-return-value"></a>Bindning till metodreturvärde

Du kan använda ett metodreturvärde för en `$return` utdatabindning genom att använda namnet i *function.json*. Exempel finns i [Utlösare och bindningar](./functions-bindings-return-value.md).

Använd returvärdet endast om ett lyckat funktionskörning alltid resulterar i ett returvärde för att gå vidare till utdatabindningen. Annars använder `ICollector` `IAsyncCollector`eller , som visas i följande avsnitt.

## <a name="writing-multiple-output-values"></a>Skriva flera utdatavärden

Om du vill skriva flera värden till en utdatabindning, eller om en lyckad [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) funktionsyrkcation kanske inte resulterar i något som går över till utdatabindningen, använder du eller typerna. Dessa typer är skrivskyddade samlingar som skrivs till utdatabindningen när metoden är klar.

I det här exemplet skrivs flera `ICollector`kömeddelanden till samma kö med:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Loggning

Om du vill logga utdata till dina streamingloggar i C#, inkluderar du ett argument av typen [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Vi rekommenderar att `log`du namnger den . Undvik `Console.Write` att använda i Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Information om ett nyare loggningsramverk `TraceWriter`som du kan använda i stället för finns [i Skriv loggar i C#-funktioner](functions-monitoring.md#write-logs-in-c-functions) i artikeln Övervaka Azure **Functions.**

## <a name="async"></a>Asynkrona

Om du vill göra en funktion `async` [asynkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)använder du nyckelordet och returnerar ett `Task` objekt.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Du kan inte `out` använda parametrar i asynkronfunktioner. För utdatabindningar använder du [funktionens returvärde](#binding-to-method-return-value) eller ett [insamlarobjekt](#writing-multiple-output-values) i stället.

## <a name="cancellation-tokens"></a>Token för annullering

En funktion kan acceptera en [CancellationToken-parameter,](/dotnet/api/system.threading.cancellationtoken) som gör det möjligt för operativsystemet att meddela din kod när funktionen är på väg att avslutas. Du kan använda det här meddelandet för att se till att funktionen inte avslutas oväntat på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du söker efter förestående funktionsuppslutning.

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

Om du behöver importera namnområden kan du göra det `using` som vanligt med satsen.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Följande namnområden importeras automatiskt och är därför valfria:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Referera till externa sammansättningar

Lägg till referenser med hjälp av `#r "AssemblyName"` direktivet för ramförsamlingar.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Följande sammansättningar läggs automatiskt till av Azure Functions-värdmiljön:

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

Följande sammansättningar kan refereras med ett enkelt namn `#r "AssemblyName"`(till exempel):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Referera till anpassade sammansättningar

Om du vill referera till en anpassad sammansättning kan du använda antingen en *delad* sammansättning eller en *privat* sammansättning:

* Delade sammansättningar delas mellan alla funktioner i en funktionsapp. Om du vill referera till en anpassad `bin` sammansättning laddar du upp sammansättningen till en mapp som heter i [funktionsapprotmappen](functions-reference.md#folder-structure) (wwwroot).

* Privata sammansättningar är en del av en viss funktions kontext och stöder sidoinläsning av olika versioner. Privata sammansättningar ska överföras i `bin` en mapp i funktionskatalogen. Referera till sammansättningarna med filnamnet, `#r "MyAssembly.dll"`till exempel .

Information om hur du laddar upp filer till funktionsmappen finns i avsnittet om [pakethantering](#using-nuget-packages).

### <a name="watched-directories"></a>Bevakade kataloger

Katalogen som innehåller funktionsskriptfilen visas automatiskt för ändringar i sammansättningar. Om du vill titta efter monteringsändringar `watchDirectories` i andra kataloger lägger du till dem i listan i [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Använda NuGet-paket
Om du vill använda NuGet-paket i en 2.x- och senare C#-funktion laddar du upp en *function.proj-fil* till funktionens mapp i funktionsappens filsystem. Här är ett exempel *på filen Function.proj* som lägger till en referens till *Microsoft.ProjectOxford.Face* version *1.1.0:*

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Om du vill använda en anpassad NuGet-feed anger du feeden i en *Nuget.Config-fil* i funktionsapproten. Mer information finns i [Konfigurera NuGet-beteende](/nuget/consume-packages/configuring-nuget-behavior).

> [!NOTE]
> I 1.x C#-funktioner refereras NuGet-paket med en *project.json-fil* i stället för en *function.proj-fil.*

För 1.x-funktioner använder du i stället en *project.json-fil.* Här är ett exempel *project.json* fil:

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

### <a name="using-a-functionproj-file"></a>Använda en function.proj-fil

1. Öppna funktionen i Azure-portalen. På fliken Loggar visas utskriften för paketinstallationen.
2. Om du vill ladda upp en *function.proj-fil* använder du någon av metoderna som beskrivs i referensavsnittet [Hur du uppdaterar funktionsappfiler](functions-reference.md#fileupdate) i referensavsnittet för Utvecklare av Azure Functions.
3. När *filen function.proj* har laddats upp visas utdata som följande exempel i funktionens direktuppspelningslogg:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Miljövariabler

Om du vill hämta en miljövariabel eller ett appinställningsvärde använder du `System.Environment.GetEnvironmentVariable`, som visas i följande kodexempel:

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

## <a name="binding-at-runtime"></a>Bindning vid körning

I C# och andra .NET-språk kan du använda ett [tvingande](https://en.wikipedia.org/wiki/Imperative_programming) bindningsmönster, i motsats till [*deklarativa*](https://en.wikipedia.org/wiki/Declarative_programming) bindningarna i *function.json*. Tvingande bindning är användbart när bindningsparametrar måste beräknas vid körning i stället för designtid. Med det här mönstret kan du binda till ingångs- och utdatabindningar som stöds i funktionskoden.

Definiera en tvingande bindning enligt följande:

- **Inkludera inte** en post i *function.json* för dina önskade tvingande bindningar.
- Skicka in en [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)indataparameter eller .
- Använd följande C#-mönster för att utföra databindningen.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute`är attributet .NET som definierar bindningen och `T` är en indata- eller utdatatyp som stöds av den bindningstypen. `T`kan inte `out` vara en `out JObject`parametertyp (till exempel ). Tabellutdatabindningen för mobilappar stöder till exempel [sex utdatatyper,](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)men du [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) kan `T`bara använda [\<ICollector T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) eller för .

### <a name="single-attribute-example"></a>Exempel på ett enda attribut

I följande exempelkod skapas en [Storage blob-utdatabindning](functions-bindings-storage-blob-output.md) med blob-sökväg som har definierats vid körning och sedan en sträng till bloben.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definierar [storage blob-indata](functions-bindings-storage-blob.md) eller utdatabindning och [TextWriter](/dotnet/api/system.io.textwriter) är en utdatabindningstyp som stöds.

### <a name="multiple-attribute-example"></a>Exempel på flera attribut

Det föregående exemplet hämtar appinställningen för funktionsappens huvudsakliga `AzureWebJobsStorage`anslutningssträng för lagringskonto (som är ). Du kan ange en anpassad appinställning som ska användas för lagringskontot genom `BindAsync<T>()`att lägga till [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) och skicka attributmatrisen till . Använd `Binder` en parameter, inte `IBinder`.  Ett exempel:

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

I följande tabell visas .NET-attributen för varje bindningstyp och de paket som de definieras i.

> [!div class="mx-codeBreakAll"]
> | Bindning | Attribut | Lägga till en referens |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Händelsehubbar | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Lagringskö | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Lagringsblob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tabell för lagring | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Läs mer om metodtips för Azure Functions](functions-best-practices.md)
