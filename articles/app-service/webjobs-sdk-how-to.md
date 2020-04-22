---
title: Så här använder du WebJobs SDK
description: Läs mer om hur du skriver kod för WebJobs SDK. Skapa händelsedrivna bakgrundsbearbetningsjobb som får åtkomst till data i Azure- och tredjepartstjänster.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 5a8d5f96449cfecd4628c38fa2788a1e06e96b07
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758903"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Så använder du Azure WebJobs SDK för händelsedriven bakgrundsbearbetning

Den här artikeln innehåller vägledning om hur du arbetar med Azure WebJobs SDK. Information om hur du kommer igång med WebJobs direkt finns i [Komma igång med Azure WebJobs SDK för händelsedriven bakgrundsbearbetning](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-versioner

Dessa är de viktigaste skillnaderna mellan version 3. *x* och version 2. *x* av WebJobs SDK:

* Version 3. *x* lägger till stöd för .NET Core.
* I version 3. *x*måste du uttryckligen installera det lagringsbindningstillägg som krävs av WebJobs SDK. I version 2. *x*inkluderades lagringsbindningarna i SDK.
* Visual Studio-verktyg för .NET Core (3.* x*) projekt skiljer sig från verktyg för .NET Framework (2.* x*) projekt. Mer information finns i [Utveckla och distribuera WebJobs med Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md).

När det är möjligt finns exempel för båda version 3. *x* och version 2. *x*.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) bygger på WebJobs SDK och den här artikeln innehåller länkar till Azure Functions-dokumentation för vissa ämnen. Observera dessa skillnader mellan Funktioner och WebJobs SDK:
> * Version av Azure Functions version 2. *x* motsvarar WebJobs SDK version 3. *x*och Azure-funktioner 1. *x* motsvarar WebJobs SDK 2. *x*. Källkodsdatabaser använder WebJobs SDK-numrering.
> * Exempelkod för Azure Functions C#-klassbibliotek är som WebJobs SDK-kod, förutom att du inte behöver ett `FunctionName` attribut i ett WebJobs SDK-projekt.
> * Vissa bindningstyper stöds endast i Funktioner, till exempel HTTP (Webhooks) och Event Grid (som baseras på HTTP).
>
> Mer information finns i [Jämför WebJobs SDK och Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>WebJobs värd

Värden är en körningsbehållare för funktioner.  Den lyssnar efter triggers och samtalsfunktioner. I version 3. *x*är värden en `IHost`implementering av . I version 2. *x*använder du `JobHost` objektet. Du skapar en värdinstans i koden och skriver kod för att anpassa dess beteende.

Detta är en viktig skillnad mellan att använda WebJobs SDK direkt och att använda den indirekt via Azure Functions. I Azure Functions styr tjänsten värden och du kan inte anpassa värden genom att skriva kod. Med Azure Functions kan du anpassa värdbeteendet via inställningarna i filen host.json. Dessa inställningar är strängar, inte kod, och detta begränsar vilka typer av anpassningar du kan göra.

### <a name="host-connection-strings"></a>Anslutningssträngar för värd

WebJobs SDK söker efter Azure Storage- och Azure Service Bus-anslutningssträngar i filen local.settings.json när du körs lokalt eller i miljön i WebJob när du kör i Azure. Som standard krävs en lagringsanslutningsstränginställning med namnet. `AzureWebJobsStorage`  

Version 2. *x* i SDK kan du använda dina egna namn för dessa anslutningssträngar eller lagra dem någon annanstans. Du kan ange namn [`JobHostConfiguration`]i kod med hjälp av , som visas här:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Eftersom version 3. *x* använder standard-NET Core-konfigurations-API:erna, det finns inget API för att ändra anslutningssträngnamn.

### <a name="host-development-settings"></a>Inställningar för värdutveckling

Du kan köra värden i utvecklingsläge för att göra den lokala utvecklingen mer effektiv. Här är några av inställningarna som ändras när du kör i utvecklingsläge:

| Egenskap | Utvecklingsinställning |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`för att maximera loggutgången. |
| `Queues.MaxPollingInterval`  | Ett lågt värde för att säkerställa att kömetoder utlöses omedelbart.  |
| `Singleton.ListenerLockPeriod` | 15 sekunder för att underlätta en snabb iterativ utveckling. |

Processen för att aktivera utvecklingsläge beror på SDK-versionen. 

#### <a name="version-3x"></a>Version 3. *x*

Version 3. *x* använder standard-ASP.NET Core API:er. Anropa [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metoden på [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instansen. Skicka en `development`sträng med namnet , som i det här exemplet:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

Klassen `JobHostConfiguration` har `UseDevelopmentSettings` en metod som aktiverar utvecklingsläge.  I följande exempel visas hur du använder utvecklingsinställningar. Om `config.IsDevelopment` du `true` vill göra avkastning när den körs `AzureWebJobsEnv` lokalt `Development`anger du en lokal miljövariabel med namnet .

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Hantera samtidiga anslutningar (version 2.* x*)

I version 3. *x,* anslutningsgränsen som standard till oändliga anslutningar. Om du av någon anledning behöver ändra den [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) här [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) gränsen kan du använda egenskapen för klassen.

I version 2. *x*styr du antalet samtidiga anslutningar till en värd med hjälp av Api:et [För ServicePointManager.DefaultConnectionLimit.](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) I 2. *x*bör du öka det här värdet från standardvärdet 2 innan du startar webjobs-värden.

Alla utgående HTTP-begäranden som `HttpClient` du `ServicePointManager`gör från en funktion med hjälp av flödet genom . När du har nått `DefaultConnectionLimit` `ServicePointManager` det värde som angetts i börjar köförfrågningar innan du skickar dem. Anta `DefaultConnectionLimit` att du är inställd på 2 och koden gör 1 000 HTTP-begäranden. Inledningsvis tillåts endast två begäranden till operativsystemet. De andra 998 står i kö tills det finns plats för dem. Det innebär `HttpClient` att du kan time out eftersom det verkar ha gjort begäran, men begäran skickades aldrig av operativsystemet till målservern. Så du kan se beteende som inte verkar `HttpClient` vettigt: din lokala tar 10 sekunder att slutföra en begäran, men din tjänst returnerar varje begäran i 200 ms. 

Standardvärdet för ASP.NET program är `Int32.MaxValue`, och det kommer sannolikt att fungera bra för WebJobs som körs i en grundläggande eller högre App Service Plan. WebJobs behöver vanligtvis inställningen Alltid på, och det stöds endast av grundläggande och högre App Service Plans.

Om webjob körs i en kostnadsfri eller delad apptjänstplan begränsas programmet av sandlådan För App-tjänsten, som för närvarande har en [anslutningsgräns på 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Med en obunden `ServicePointManager`anslutningsgräns i är det mer troligt att tröskelvärdet för sandlådeanslutningen nås och att platsen stängs av. I så fall `DefaultConnectionLimit` kan inställningen till något lägre, som 50 eller 100, förhindra att detta händer och ändå möjliggöra tillräckligt med dataflöde.

Inställningen måste konfigureras innan http-begäranden görs. Därför bör WebJobs-värden inte justera inställningen automatiskt. Det kan finnas HTTP-begäranden som inträffar innan värden startar, vilket kan leda till oväntat beteende. Det bästa sättet är att ställa `Main` in värdet `JobHost`omedelbart i din metod innan du initierar , som visas här:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Utlösare

Funktioner måste vara offentliga metoder och måste [`NoAutomaticTrigger`](#manual-triggers) ha ett utlösarattribut eller attributet.

### <a name="automatic-triggers"></a>Automatiska utlösare

Automatiska utlösare anropar en funktion som svar på en händelse. Tänk på det här exemplet på en funktion som utlöses av ett meddelande som lagts till i Azure Queue storage. Den svarar genom att läsa en blob från Azure Blob storage:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Attributet `QueueTrigger` talar om för körningen att anropa funktionen `myqueue-items` när ett kömeddelande visas i kön. Attributet `Blob` talar om för körningen att använda kömeddelandet för att läsa en blob i *exempelarbetsplatsbehållaren.* Namnet på blob-objektet `samples-workitems` i behållaren hämtas direkt från köutlösaren som ett bindningsuttryck (`{queueTrigger}`).

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Manuella utlösare

Om du vill utlösa en `NoAutomaticTrigger` funktion manuellt använder du attributet, som visas här:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

Processen för att manuellt utlösa funktionen beror på SDK-versionen.

#### <a name="version-3x"></a>Version 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>In- och utdatabindningar

Indatabindningar är ett deklarativt sätt att göra data från Azure- eller tredjepartstjänster tillgängliga för din kod. Utdatabindningar är ett sätt att uppdatera data. Artikeln [Kom igång](webjobs-sdk-get-started.md) visar ett exempel på var och en.

Du kan använda ett metodreturvärde för en utdatabindning genom att använda attributet på metodens returvärde. Se exemplet i [Använda returvärdet för Azure-funktionen](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Bindningstyper

Processen för att installera och hantera bindningstyper beror på om du använder version 3. *x* eller version 2. *x* av SDK. Du hittar paketet som ska installeras för en viss bindningstyp i avsnittet "Paket" i den bindningstypens [azure functions-referensartikel](#binding-reference-information). Ett undantag är filutlösaren och bindningen (för det lokala filsystemet), som inte stöds av Azure Functions.

#### <a name="version-3x"></a>Version 3. *x*

I version 3. *x*ingår lagringsbindningarna `Microsoft.Azure.WebJobs.Extensions.Storage` i paketet. Anropa `AddAzureStorage` förlängningsmetoden `ConfigureWebJobs` i metoden, som visas här:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Om du vill använda andra utlösar- och bindningstyper `Add<binding>` installerar du NuGet-paketet som innehåller dem och anropar tilläggsmetoden som implementeras i tillägget. Om du till exempel vill använda en Azure `Microsoft.Azure.WebJobs.Extensions.CosmosDB` Cosmos `AddCosmosDB`DB-bindning, installera och anropa , så här:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Om du vill använda timerutlösaren eller filbindningen, som ingår i kärntjänsterna, anropar du metoderna `AddTimers` eller `AddFiles` tilläggsmetoderna.

#### <a name="version-2x"></a>Version 2. *x*

Dessa utlösande och bindande typer ingår i version 2. *x* i `Microsoft.Azure.WebJobs` förpackningen:

* Blob Storage
* Queue Storage
* Table Storage

Om du vill använda andra utlösar- och bindningstyper `Use<binding>` installerar `JobHostConfiguration` du NuGet-paketet som innehåller dem och anropar en metod för objektet. Om du till exempel vill använda en `Microsoft.Azure.WebJobs.Extensions` timer-utlösare installerar och anropar du `UseTimers` metoden, `Main` som visas här:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Om du vill använda `Microsoft.Azure.WebJobs.Extensions` filbindningen installerar och anropar du `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

WebJobs kan du [`ExecutionContext`]binda till en . Med den här bindningen [`ExecutionContext`] kan du komma åt som en parameter i din funktionssignatur. Följande kod använder till exempel kontextobjektet för att komma åt anrops-ID: t. som du kan använda för att korrelera alla loggar som produceras av en viss funktionsyrop.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

Processen för bindning [`ExecutionContext`] till beror på din SDK-version.

#### <a name="version-3x"></a>Version 3. *x*

Anropa `AddExecutionContextBinding` förlängningsmetoden `ConfigureWebJobs` i metoden, som visas här:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

Paketet `Microsoft.Azure.WebJobs.Extensions` som nämndes tidigare ger också en speciell `UseCore` bindningstyp som du kan registrera genom att anropa metoden. Med den här [`ExecutionContext`] bindningen kan du definiera en parameter i funktionssignaturen, som är aktiverad så här:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Bindningskonfiguration

Du kan konfigurera beteendet för vissa utlösare och bindningar. Processen för att konfigurera dem beror på SDK-versionen.

* **Version 3. *x*:** Ställ `Add<Binding>` in konfiguration `ConfigureWebJobs`när metoden anropas i .
* **Version 2. *x*:** Ange konfiguration genom att ange egenskaper `JobHost`i ett konfigurationsobjekt som du skickar in till .

Dessa bindningsspecifika inställningar motsvarar inställningarna i [project-filen host.json](../azure-functions/functions-host-json.md) i Azure Functions.

Du kan konfigurera följande bindningar:

* [Azure CosmosDB-utlösare](#azure-cosmosdb-trigger-configuration-version-3x)
* [Utlösare av händelsehubbar](#event-hubs-trigger-configuration-version-3x)
* [Kölagringsutlösare](#queue-storage-trigger-configuration)
* [SendGrid-bindning](#sendgrid-binding-configuration-version-3x)
* [Utlösare av servicebuss](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB-utlösarkonfiguration (version 3.* x*)

Det här exemplet visar hur du konfigurerar Azure Cosmos DB-utlösaren:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Mer information finns i [bindningsartikeln för Azure CosmosDB.](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

### <a name="event-hubs-trigger-configuration-version-3x"></a>Konfiguration av utlösare av händelsehubbar (version 3.* x*)

I det här exemplet visas hur du konfigurerar utlösaren för händelsehubbar:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Mer information finns i [bindningsartikeln Event Hubs.](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json)

### <a name="queue-storage-trigger-configuration"></a>Konfiguration av kölagringsutlösare

De här exemplen visar hur du konfigurerar kölagringsutlösaren:

#### <a name="version-3x"></a>Version 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Mer information finns i artikeln [Om kölagringsbindning.](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)

#### <a name="version-2x"></a>Version 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Mer information finns i [referensen host.json v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid bindning konfiguration (version 3.* x*)

I det här exemplet visas hur du konfigurerar SendGrid-utdatabindningen:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Mer information finns i [SendGrid-bindningsartikeln.](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)

### <a name="service-bus-trigger-configuration-version-3x"></a>Konfiguration av servicebussutlösare (version 3.* x*)

I det här exemplet visas hur du konfigurerar servicebussutlösaren:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Mer information finns i artikeln [Service Bus bindning.](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings)

### <a name="configuration-for-other-bindings"></a>Konfiguration för andra bindningar

Vissa utlösar- och bindningstyper definierar sina egna anpassade konfigurationstyper. Med filutlösaren kan du till exempel ange rotsökvägen som ska övervakas, som i följande exempel:

#### <a name="version-3x"></a>Version 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Bindande uttryck

I attributkonstruktorparametrar kan du använda uttryck som matchar till värden från olika källor. I följande kod skapar till exempel `BlobTrigger` sökvägen för attributet ett uttryck med namnet `filename`. När den används för `filename` utdatabindningen, matchas till namnet på den utlösande bloben.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Mer information om bindningsuttryck finns i [Bindningsuttryck och mönster](../azure-functions/functions-bindings-expressions-patterns.md) i Azure Functions-dokumentationen.

### <a name="custom-binding-expressions"></a>Anpassade bindningsuttryck

Ibland vill du ange ett könamn, ett blobnamn eller en behållare eller ett tabellnamn i kod i stället för att koda den. Du kanske till exempel vill ange könamnet för `QueueTrigger` attributet i en konfigurationsfil eller miljövariabel.

Du kan göra det `NameResolver` genom att `JobHostConfiguration` skicka ett objekt till objektet. Du inkluderar platshållare i parametrar för utlösare `NameResolver` eller bindningsattributkonstruktorer, och koden tillhandahåller de faktiska värden som ska användas i stället för dessa platshållare. Du identifierar platshållare genom att omge dem med procent (%) tecken, som visas här:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Med den här koden `logqueuetest` kan du använda en `logqueueprod` kö som namnges i testmiljön och en som namnges i produktionen. I stället för ett hårdkodat könamn anger du `appSettings` namnet på en post i samlingen.

Det finns en `NameResolver` standard som börjar gälla om du inte tillhandahåller en anpassad. Standard hämtar värden från appinställningar eller miljövariabler.

Klassen `NameResolver` hämtar könamnet `appSettings`från , som visas här:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Version 3. *x*

Du konfigurerar matcharen med hjälp av beroendeinjektion. Dessa exempel kräver `using` följande uppgift:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Du lägger till matcharen genom att anropa tilläggsmetoden [`ConfigureServices`] på [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder), som i det här exemplet:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

Skicka `NameResolver` din klass `JobHost` till objektet, som visas här:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions `INameResolver` implementerar för att hämta värden från appinställningar, som visas i exemplet. När du använder WebJobs SDK direkt kan du skriva en anpassad implementering som hämtar platshållarersättningsvärden från vilken källa du vill.

## <a name="binding-at-runtime"></a>Bindning vid körning

Om du behöver göra en del arbete i din `Queue` `Blob`funktion `Table`innan du använder `IBinder` ett bindningsattribut som , eller kan du använda gränssnittet.

I följande exempel visas ett indatakömeddelande och ett nytt meddelande skapas med samma innehåll i en utdatakö. Utdatakönamnet anges efter kod i funktionens brödtext.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Mer information finns i [Bindning vid körning](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) i Azure Functions-dokumentationen.

## <a name="binding-reference-information"></a>Bindande referensinformation

Azure Functions-dokumentationen innehåller referensinformation om varje bindningstyp. Du hittar följande information i varje bindningsreferensartikel. (Det här exemplet baseras på lagringskö.)

* [Paket](../azure-functions/functions-bindings-storage-queue.md). Paketet som du behöver installera för att inkludera stöd för bindningen i ett WebJobs SDK-projekt.
* [Exempel](../azure-functions/functions-bindings-storage-queue-trigger.md). Kodexempel. Exemplet med klassbiblioteket C# gäller för WebJobs SDK. Utelämna bara `FunctionName` attributet.
* [Attribut](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). De attribut som ska användas för bindningstypen.
* [Konfiguration](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Förklaringar av attributegenskaperna och konstruktorparametrarna.
* [Användning](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). De typer du kan binda till och information om hur bindningen fungerar. Till exempel: avsökningsalgoritm, bearbetning av giftkö.
  
En lista över bindande referensartiklar finns i "Bindningar som stöds" i artikeln [Utlösare och bindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings) för Azure Functions. I den listan stöds BINDningarna HTTP, Webhooks och Event Grid endast av Azure Functions, inte av WebJobs SDK.

## <a name="disable-attribute"></a>Inaktivera attribut 

Med [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attributet kan du styra om en funktion kan utlösas. 

I följande exempel, om `Disable_TestJob` appinställningen `1` har `True` ett värde av eller (skiftlägesokänsligt) körs inte funktionen. I så fall skapar körningen en *loggmeddelandefunktion "Functions.TestJob" är inaktiverad*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

När du ändrar värden för appinställning i Azure-portalen startas WebJob om för att hämta den nya inställningen.

Attributet kan deklareras på parameter-, metod- eller klassnivå. Inställningsnamnet kan också innehålla bindningsuttryck.

## <a name="timeout-attribute"></a>Timeout-attribut

Attributet [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) gör att en funktion avbryts om den inte avslutas inom en viss tidsperiod. I följande exempel skulle funktionen köras en dag utan timeout-attributet. Timeout gör att funktionen avbryts efter 15 sekunder.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Du kan använda timeout-attributet på klass- eller metodnivå och `JobHostConfiguration.FunctionTimeout`du kan ange en global timeout med hjälp av . Tidsutgångar på klassnivå eller metodnivå åsidosätter globala timeout.

## <a name="singleton-attribute"></a>Singleton-attribut

Attributet [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) säkerställer att endast en instans av en funktion körs, även när det finns flera instanser av värdwebbappen. Detta gör det genom att använda [distribuerad låsning](#viewing-lease-blobs).

I det här exemplet körs `ProcessImage` endast en enskild instans av funktionen vid en given tidpunkt:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Vissa utlösare har inbyggt stöd för samtidighetshantering:

* **QueueTrigger**. Ställ `JobHostConfiguration.Queues.BatchSize` `1`in på .
* **ServiceBusTrigger**. Ställ `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` `1`in på .
* **FileTrigger**. Ställ `FileProcessor.MaxDegreeOfParallelism` `1`in på .

Du kan använda dessa inställningar för att säkerställa att din funktion körs som en singleton på en enda instans. Om du vill vara säkra på att endast en enskild instans av funktionen körs när webbappen skalas`[Singleton(Mode = SingletonMode.Listener)]`ut till flera instanser använder du ett singleton-låsning på lyssnarnivå på funktionen ( ). Lyssnarlås hämtas när JobHost startar. Om tre utskalade instanser startar samtidigt får bara en av instanserna låset och endast en lyssnare startar.

### <a name="scope-values"></a>Scopevärden

Du kan ange ett *scopeuttryck/värde* på en singleton. Uttrycket/värdet säkerställer att alla körningar av funktionen i ett visst scope serialiseras. Genomföra mer detaljerad låsning på detta sätt kan möjliggöra en viss nivå av parallellism för din funktion medan serialisera andra anrop som dikteras av dina krav. I följande kod binder scopeuttrycket till exempel `Region` värdet för det inkommande meddelandet. När kön innehåller tre meddelanden i regionerna Öst, Öst respektive Väst körs de meddelanden som har region Öst seriellt medan meddelandet med region Väst körs parallellt med meddelandena i öst.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

Standardomfånget för `SingletonScope.Function`ett lås är , vilket innebär att låsomfånget (blob-lånesökvägen) är kopplat till det fullständigt kvalificerade funktionsnamnet. Om du vill `SingletonScope.Host` låsa över funktioner anger och använder du ett scope-ID-namn som är detsamma för alla funktioner som du inte vill köra samtidigt. I följande exempel bara en `AddItem` `RemoveItem` instans av eller körs åt gången:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Visa leasingblobar

WebJobs SDK använder [Azure blob-lån](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) under skalen för att implementera distribuerade låsning. De låneblobar som används av Singleton `azure-webjobs-host` finns `AzureWebJobsStorage` i behållaren i lagringskontot under sökvägen "lås". Till exempel kan låneblobbsökvägen `ProcessImage` för det `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`första exemplet som visas tidigare vara . Alla sökvägar inkluderar JobHost-ID, i det här fallet 061851c758f04938a4426a9ab3869c0.

## <a name="async-functions"></a>Asynkronfunktioner

Information om hur du kodar async-funktioner finns i [Azure Functions-dokumentationen](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Token för annullering

Information om hur du hanterar avbokningstoken finns i Azure Functions-dokumentationen om [avbokningstoken och graciös avstängning](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Flera instanser

Om webbappen körs på flera instanser körs ett kontinuerligt WebJob på varje instans, lyssnar efter utlösare och anropar funktioner. De olika utlösarbindningarna är utformade för att effektivt dela arbete tillsammans mellan instanser, så att skala ut till fler instanser gör att du kan hantera mer belastning.

Vissa utlösare kan resultera i dubbelbearbetning, men kö- och blob-lagringsutlösare förhindrar automatiskt en funktion från att bearbeta ett kömeddelande eller blob mer än en gång. Mer information finns i [Designa för identiska indata](../azure-functions/functions-idempotent.md) i Azure Functions-dokumentationen.

Timerutlösaren säkerställer automatiskt att endast en instans av timern körs, så att du inte får mer än en funktionsinstans som körs vid en viss schemalagd tid.

Om du vill vara säkra på att endast en instans av en funktion körs även [`Singleton`](#singleton-attribute) när det finns flera instanser av värdwebbappen kan du använda attributet.

## <a name="filters"></a>Filter

Funktionsfilter (förhandsgranskning) är ett sätt att anpassa WebJobs-körningspipelinen med din egen logik. Filter liknar [ASP.NET Core-filter](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Du kan implementera dem som deklarativa attribut som tillämpas på dina funktioner eller klasser. Mer information finns i [Funktionsfilter](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Vi rekommenderar loggningsramverket som har utvecklats för ASP.NET. Artikeln [Kom igång](webjobs-sdk-get-started.md) visar hur du använder den. 

### <a name="log-filtering"></a>Loggfiltrering

Varje logg som `ILogger` skapas `Category` av `Level`en instans har en associerad och . [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)är en uppräkning och heltalskoden anger relativ betydelse:

|Loggnivå    |Kod|
|------------|---|
|Spårning       | 0 |
|Felsökning       | 1 |
|Information | 2 |
|Varning     | 3 |
|Fel       | 4 |
|Kritisk    | 5 |
|Ingen        | 6 |

Du kan filtrera varje kategori [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)oberoende av varandra till en viss . Du kanske till exempel vill se alla loggar för `Error` blob-utlösare bearbetning men bara och högre för allt annat.

#### <a name="version-3x"></a>Version 3. *x*

Version 3. *x* av SDK förlitar sig på filtrering inbyggd i .NET Core. Med `LogCategories` klassen kan du definiera kategorier för specifika funktioner, utlösare eller användare. Det definierar också filter för `Startup` specifika `Results`värdtillstånd, som och . På så sätt kan du finjustera loggningsutdata. Om ingen matchning hittas inom de definierade `Default` kategorierna återgår filtret till värdet när du bestämmer dig för om meddelandet ska filtreras.

`LogCategories`kräver följande med hjälp av uttalande:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

I följande exempel skapas ett filter som som standard `Warning` filtrerar alla loggar på nivån. `Function` Kategorierna `results` och (motsvarande `Host.Results` i version 2.* x*) filtreras `Error` på nivån. Filtret jämför den aktuella kategorin med `LogCategories` alla registrerade nivåer i instansen och väljer den längsta matchningen. Det innebär `Debug` att nivån `Host.Triggers` `Host.Triggers.Queue` som `Host.Triggers.Blob`registrerats för matchningar eller . På så sätt kan du styra bredare kategorier utan att behöva lägga till var och en.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

I version 2. *x* av SDK använder `LogCategoryFilter` du klassen för att styra filtrrering. `LogCategoryFilter` Egenskapen `Default` har ett ursprungligt `Information`värde i , vilket `Information` `Warning`innebär `Error`att `Critical` alla meddelanden på nivåerna , `Debug` eller `Trace` loggas, men alla meddelanden på nivåerna eller filtreras bort.

Som `LogCategories` med i version 3. *x*kan `CategoryLevels` du med egenskapen ange loggnivåer för specifika kategorier så att du kan finjustera loggningsutdata. Om ingen matchning `CategoryLevels` hittas i ordlistan `Default` återgår filtret till värdet när du bestämmer dig för om meddelandet ska filtreras.

I följande exempel skapas ett filter som som `Warning` standard filtrerar alla loggar på nivån. `Function` Kategorierna `Host.Results` och filtreras `Error` på nivån. Jämför `LogCategoryFilter` den aktuella kategorin `CategoryLevels` med alla registrerade och väljer den längsta matchningen. Så `Debug` den nivå `Host.Triggers` som `Host.Triggers.Queue` `Host.Triggers.Blob`registrerats för kommer att matcha eller . På så sätt kan du styra bredare kategorier utan att behöva lägga till var och en.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Anpassad telemetri för application insights

Processen för att implementera anpassad telemetri för [Application Insights](../azure-monitor/app/app-insights-overview.md) beror på SDK-versionen. Mer information om hur du konfigurerar Application Insights finns i [Lägga till programstatistikloggning](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Version 3. *x*

Eftersom version 3. *x* av WebJobs SDK förlitar sig på .NET Core generiska värd, en anpassad telemetri fabrik inte längre tillhandahålls. Men du kan lägga till anpassad telemetri i pipelinen med hjälp av beroendeinjektion. Exemplen i det här `using` avsnittet kräver följande påståenden:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Med följande anpassade [`ITelemetryInitializer`] implementering av [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) kan du [`TelemetryConfiguration`]lägga till egna i standardvärdet .

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Ring [`ConfigureServices`] in byggaren för [`ITelemetryInitializer`] att lägga till din anpassade till pipelinen.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

När [`TelemetryConfiguration`] den är konstruerad inkluderas [`ITelemetryInitializer`] alla registrerade typer av. Mer information finns i [API för Application Insights för anpassade händelser och mått](../azure-monitor/app/api-custom-events-metrics.md).

I version 3. *x*behöver du inte längre [`TelemetryClient`] spola när värden stannar. Beroendeinsprutningssystemet .NET Core bortfogar `ApplicationInsightsLoggerProvider`automatiskt från [`TelemetryClient`]det registrerade , som tömmer .

#### <a name="version-2x"></a>Version 2. *x*

I version 2. *x*använder [`TelemetryClient`] [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)den som skapats internt av Application Insights-providern för WebJobs SDK . När slutpunkten Application Insights inte är tillgänglig eller begränsning av inkommande begäranden sparar den här kanalen [begäranden i webbappens filsystem och skickar dem igen senare](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

Skapas [`TelemetryClient`] av en klass `ITelemetryClientFactory`som implementerar . Som standard är [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)detta .

Om du vill ändra någon del av application insights-pipelinen kan du ange din `ITelemetryClientFactory` [`TelemetryClient`]egen och värden använder klassen för att skapa en . Den här koden åsidosätter `DefaultTelemetryClientFactory` till exempel `ServerTelemetryChannel`om du vill ändra en egenskap för:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Objektet `SamplingPercentageEstimatorSettings` konfigurerar [adaptiv sampling](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Det innebär att i vissa scenarier med stora volymer skickar Applications Insights en vald delmängd telemetridata till servern.

När du har skapat telemetrifabriken skickar du den till loggningsleverantören Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a>Nästa steg

Den här artikeln innehåller kodavsnitt som visar hur du hanterar vanliga scenarier för att arbeta med WebJobs SDK. Fullständiga exempel finns i [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

["ExecutionContext"]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
["TelemetriClient"]: /dotnet/api/microsoft.applicationinsights.telemetryclient
['ConfigureServices']: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
["ITelemetryInitializer"]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
["Telemetrikonfigurering"]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
["JobHostConfiguration"]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
