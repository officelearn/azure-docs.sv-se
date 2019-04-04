---
title: Hur du använder WebJobs-SDK - Azure
description: Läs mer om hur du skriver kod för WebJobs SDK. Skapa en händelsedriven bakgrund bearbetar jobb som har åtkomst till data i Azure-tjänster och tjänster från tredje part.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 38d8bdfcba48d2080b434ebec192b41f3663ae6a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895215"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Så här använder du Azure WebJobs SDK för händelsedrivna Bakgrundsbearbetning

Den här artikeln innehåller råd om hur du arbetar med Azure WebJobs SDK. Kom igång med WebJobs direkt, se [Kom igång med Azure WebJobs-SDK för händelsedrivna Bakgrundsbearbetning](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-versioner

Dessa är de viktigaste skillnaderna mellan version 3. *x* och version 2. *x* av WebJobs SDK:

* Version 3. *x* lägger till stöd för .NET Core.
* I version 3. *x*, måste du uttryckligen installera Storage bindningstillägget krävs av WebJobs SDK. I version 2. *x*, Storage-bindningar ingår i SDK.
* Visual Studio-verktyg för .NET Core (3. *x*) projekt skiljer sig från Verktyg för .NET Framework (2. *x*) projekt. Mer information finns i [utveckla och distribuera WebJobs med hjälp av Visual Studio – Azure App Service](webjobs-dotnet-deploy-vs.md).

Om det är möjligt finns exempel för både version 3. *x* och version 2. *x*.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) bygger på WebJobs-SDK och den här artikeln innehåller länkar till Azure Functions-dokumentationen för vissa ämnen. Observera skillnaderna mellan Functions och WebJobs-SDK:
> * Azure Functions-version 2. *x* motsvarar WebJobs SDK version 3. *x*, och Azure Functions 1. *x* motsvarar WebJobs SDK 2. *x*. Källa för kodförråd använda WebJobs-SDK numrering.
> * Exempelkoden för Azure Functions C# klassbibliotek liknar WebJobs SDK-kod, förutom att du inte behöver en `FunctionName` attribut i ett projekt med WebJobs SDK.
> * Vissa bindningstyper av stöds bara i funktioner, t.ex. HTTP (Webhooks) och Event Grid (som baseras på HTTP).
>
> Mer information finns i [jämföra Azure Functions och WebJobs SDK](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>WebJobs-värd

Värden är en körningsbehållare för funktioner.  Den lyssnar efter utlösare och-anrop. I version 3. *x*, värden är en implementering av `IHost`. I version 2. *x*, du använder den `JobHost` objekt. Du skapar en värdinstans i din kod och skriva kod för att anpassa sitt beteende.

Det här är en viktig skillnad mellan med hjälp av WebJobs SDK direkt och använda den indirekt via Azure Functions. Tjänsten styr värden i Azure Functions, och du inte kan anpassa värden genom att skriva kod. Azure Functions kan du anpassa värden beteende via inställningarna i filen host.json. Dessa inställningar är strängar, inte kod, och detta begränsar typerna av anpassningar som du kan göra.

### <a name="host-connection-strings"></a>Anslutningssträngar för värd

WebJobs SDK söker efter Azure Storage och Azure Service Bus-anslutningssträngar i filen local.settings.json när du kör lokalt eller i Webbjobbet miljö när du kör i Azure. Som standard en lagringsanslutning sträng inställning med namnet `AzureWebJobsStorage` krävs.  

Version 2. *x* av SDK kan du använda ditt eget namn för dessa anslutningssträngar eller lagra dem på andra ställen. Du kan ange namnen i kod med hjälp av den [ `JobHostConfiguration` ]enligt nedan:

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

Eftersom version 3. *x* använder standardkonfigurationen för .NET Core API: er, det finns inga API för att ändra anslutningsnamnen sträng.

### <a name="host-development-settings"></a>Värdinställningar för utveckling

Du kan köra värden i utvecklingsläge att effektivisera lokal utveckling. Här är några av de inställningar som ändras när du kör i utvecklingsläge:

| Egenskap  | Inställningen för utveckling |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` Maximera loggutdata. |
| `Queues.MaxPollingInterval`  | Ett lågt värde så kö metoder utlöses omedelbart.  |
| `Singleton.ListenerLockPeriod` | 15 sekunder att underlätta snabb iterativ utveckling. |

Processen för att aktivera utvecklingsläge beror på vilken SDK-version. 

#### <a name="version-3x"></a>Version 3.*x*

Version 3. *x* använder standard ASP.NET Core API: erna. Anropa den [ `UseEnvironment` ](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metoden på den [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instans. Skicka en sträng med namnet `development`, som i det här exemplet:

```cs
static void Main()
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
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

Den `JobHostConfiguration` klassen har en `UseDevelopmentSettings` metoden som gör att utvecklingsläge.  I följande exempel visar hur du använder inställningar för webbprogramutveckling. Att göra `config.IsDevelopment` returnera `true` när den körs lokalt, ange en lokal miljövariabeln `AzureWebJobsEnv` med värdet `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Hantera samtidiga anslutningar (version 2. *x*)

I version 3. *x*, anslutningsgränsen som standard oändlig anslutningar. Om du av någon anledning som du vill ändra den här gränsen kan du använda den [ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) egenskapen för den [ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler) klass.

I version 2. *x*, du styr antalet samtidiga anslutningar till en värd med den [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. I 2. *x*, bör du öka det här värdet från standardvärdet 2 innan du börjar din WebJobs-värd.

Alla utgående HTTP-begäranden som du gör en funktion med hjälp av `HttpClient` flödar genom `ServicePointManager`. När du når värdet i `DefaultConnectionLimit`, `ServicePointManager` startar jobbköer begäranden innan de skickas. Anta att dina `DefaultConnectionLimit` anges till 2 och din kod gör 1 000 HTTP-begäranden. Först har tillåts bara två begäranden via Operativsystemet. De andra 998 köas tills det finns plats för dessa. Det innebär att din `HttpClient` kan tidsgräns eftersom den verkar ha gjorde begäran, men förfrågan skickades aldrig av Operativsystemet till målservern. Så att du kan se beteende som inte verkar vara meningsfullt: lokalt `HttpClient` tar 10 sekunder att slutföra en begäran, men tjänsten returnerar varje begäran på 200 ms. 

Standardvärdet för ASP.NET-program är `Int32.MaxValue`, och som sannolikt kommer att fungera bra WebJobs körs i en grundläggande eller högre App Service-Plan. WebJobs vanligtvis måste alltid på inställningen och som stöds endast av grundläggande och högre App Service-planer.

Om ditt WebJob körs i en kostnadsfri eller delad App Service-Plan, ditt program är begränsad av App Service-sandboxen som för närvarande har en [anslutning på högst 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Med en anslutningsgräns som har delats upp i `ServicePointManager`, det är troligare att tröskelvärdet för sandbox-anslutningen ska nås och platsen stängs. I så fall kan ställa in `DefaultConnectionLimit` till något lägre som 50 eller 100, kan förhindra att detta inträffar och fortfarande tillåter tillräckligt dataflöde.

Inställningen måste konfigureras innan alla HTTP-begäranden som görs. Därför bör inte WebJobs värden justera inställningen automatiskt. Det kan vara HTTP-begäranden som görs innan startar värd, vilket kan leda till oväntade resultat. Det bästa sättet är att ange ett värde direkt i din `Main` sättet innan initiering av `JobHost`enligt nedan:

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

Funktioner måste vara offentliga metoderna och måste ha ett utlösarattribut eller [ `NoAutomaticTrigger` ](#manual-triggers) attribut.

### <a name="automatic-triggers"></a>Automatisk utlösare

Automatisk utlösare anropar en funktion som svar på en händelse. Överväg det här exemplet på en funktion som utlöses av ett meddelande som har lagts till i Azure Queue storage. Den svarar genom att läsa en blob från Azure Blob storage:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Den `QueueTrigger` attributet anger körning för att anropa funktionen när ett kömeddelande som visas i den `myqueue-items` kö. Den `Blob` attributet anger att körningen kan använda kömeddelandet för att läsa en blob i den *exempel-workitems* behållare. Innehållet i kö-meddelandet skickades till funktionen i den `myQueueItem` parametern är namnet på blobben.


### <a name="manual-triggers"></a>Manuella utlösare

Utlös en funktion manuellt genom att använda den `NoAutomaticTrigger` attributet som visas här:

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

Processen för att manuellt utlösa funktionen är beroende av SDK-version.

#### <a name="version-3x"></a>Version 3.*x*

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

#### <a name="version-2x"></a>Version 2.*x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Indata och utdata bindningar

Indatabindningar tillhandahåller en deklarativ metod för att göra data från Azure eller tjänster från tredje part tillgängliga för din kod. Utdatabindningar är ett sätt att uppdatera data. Den [börjar](webjobs-sdk-get-started.md) artikeln visar ett exempel på var och en.

Du kan använda metoden returvärdet för en utdatabindning genom att använda attributet returvärdet för metoden. Se exemplet i [med hjälp av Azure Function returvärde](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Bindningstyper

Processen för att installera och hantera bindningstyper beror på om du använder version 3. *x* eller version 2. *x* av SDK. Du kan hitta att uppdateringen ska installeras för en viss bindningstypen i avsnittet ”Packages” i den bindningstypen Azure Functions [referensartikeln](#binding-reference-information). Ett undantag är utlösaren filer och bindningen (för det lokala filsystemet), som inte stöds av Azure Functions.

#### <a name="version-3x"></a>Version 3.*x*

I version 3. *x*, storage-bindningar som ingår i den `Microsoft.Azure.WebJobs.Extensions.Storage` paketet. Anropa den `AddAzureStorage` tilläggsmetod i den `ConfigureWebJobs` metod, som visas här:

```cs
static void Main()
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
        host.Run();
    }
}
```

Om du vill använda andra utlösare och bindningstyper, installera NuGet-paketet som innehåller dem och anropa den `Add<binding>` tilläggsmetod som implementerats i tillägget. Om du vill använda en Azure Cosmos DB-bindning till exempel installera `Microsoft.Azure.WebJobs.Extensions.CosmosDB` och anropa `AddCosmosDB`, så här:

```cs
static void Main()
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
        host.Run();
    }
}
```

Att använda den Timer som utlösaren eller filerna bindning, som är en del av grundläggande tjänster, anropa den `AddTimers` eller `AddFiles` tilläggsmetoder, respektive.

#### <a name="version-2x"></a>Version 2.*x*

Dessa typer av utlösare och bindning som ingår i version 2. *x* av den `Microsoft.Azure.WebJobs` paketet:

* Blob Storage
* Queue Storage
* Table Storage

Om du vill använda andra utlösare och bindningstyper, installera NuGet-paketet som innehåller dem och anropa en `Use<binding>` metoden på den `JobHostConfiguration` objekt. Till exempel om du vill använda en Timer som utlösare installera `Microsoft.Azure.WebJobs.Extensions` och anropa `UseTimers` i den `Main` metod, som visas här:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

att använda filerna bindning, installera `Microsoft.Azure.WebJobs.Extensions` och anropa `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

WebJobs kan du binda till en [ `ExecutionContext` ]. Med den här bindningen kan du komma åt den [ `ExecutionContext` ] som en parameter i funktionssignaturen. Följande kod använder till exempel context-objektet för att komma åt anrops-ID som du kan använda för att korrelera alla loggar som producerats av en viss funktionsanrop.  

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

Processen för bindningen till den [ `ExecutionContext` ] beror på din SDK-version.

#### <a name="version-3x"></a>Version 3.*x*

Anropa den `AddExecutionContextBinding` tilläggsmetod i den `ConfigureWebJobs` metod, som visas här:

```cs
static void Main()
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
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

Den `Microsoft.Azure.WebJobs.Extensions` paket som tidigare nämnts innehåller också en särskild bindningstyp som du kan registrera genom att anropa den `UseCore` metoden. Den här bindningen kan du definiera en [ `ExecutionContext` ] parametern i funktionen signaturen, som är aktiverad så här:

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

## <a name="binding-configuration"></a>Bindningen konfiguration

Du kan konfigurera beteendet för vissa utlösare och bindningar. Processen för att konfigurera dem beror på vilken SDK-version.

* **Version 3.*x*:** Ange konfiguration när den `Add<Binding>` metoden anropas `ConfigureWebJobs`.
* **Version 2.*x*:** Ange konfiguration genom att ange egenskaper i ett konfigurationsobjekt som du anger för att `JobHost`.

Dessa bindning-specifika inställningar som är likvärdiga med inställningarna i den [host.json projektfilen](../azure-functions/functions-host-json.md) i Azure Functions.

Du kan konfigurera bindningarna som följande:

* [Azure CosmosDB trigger](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs-utlösare](#event-hubs-trigger-configuration-version-3x)
* Queue storage-utlösare
* [SendGrid-bindning](#sendgrid-binding-configuration-version-3x)
* [Service Bus-utlösare](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Konfiguration av Azure cosmos DB-utlösare (version 3. *x*)

Det här exemplet visar hur du konfigurerar Azure Cosmos DB-utlösare:

```cs
static void Main()
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

        host.Run();
    }
}
```

Mer information finns i den [Azure CosmosDB bindning](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) artikeln.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Händelsehubbar utlösa konfiguration (version 3. *x*)

Det här exemplet visar hur du konfigurerar Event Hubs-utlösare:

```cs
static void Main()
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

        host.Run();
    }
}
```

Mer information finns i den [Händelsehubbar bindning](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) artikeln.

### <a name="queue-storage-trigger-configuration"></a>Queue storage utlösarkonfiguration

De här exemplen visar hur du konfigurerar Queue storage-utlösare:

#### <a name="version-3x"></a>Version 3.*x*

```cs
static void Main()
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

        host.Run();
    }
}
```

Mer information finns i den [kö lagringsbindningen](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) artikeln.

#### <a name="version-2x"></a>Version 2.*x*

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

Mer information finns i den [referens för host.json v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid-bindning konfiguration (version 3. *x*)

Det här exemplet visar hur du konfigurerar SendGrid-utdatabindning:

```cs
static void Main()
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

        host.Run();
    }
}
```

Mer information finns i den [SendGrid bindning](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) artikeln.

### <a name="service-bus-trigger-configuration-version-3x"></a>Konfiguration av Service Bus-utlösare (version 3. *x*)

Det här exemplet visar hur du konfigurerar Service Bus-utlösare:

```cs
static void Main()
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

        host.Run();
    }
}
```

Mer information finns i den [Service Bus-bindning](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) artikeln.

### <a name="configuration-for-other-bindings"></a>Konfiguration för andar bindningar

Vissa typer av utlösare och bindningen definiera sina egna anpassade konfigurationerna. Till exempel kan utlösaren fil du ange rotsökvägen ska övervakas, som i följande exempel:

#### <a name="version-3x"></a>Version 3.*x*

```cs
static void Main()
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

        host.Run();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

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

## <a name="binding-expressions"></a>Uttryck för bindning

Du kan använda uttryck som matchas till värden från olika källor i attributet konstruktor parametrar. Till exempel i följande kod, sökvägen för den `BlobTrigger` attributet skapar ett uttryck med namnet `filename`. När den används för utdata-bindning `filename` matchar namnet på den utlösande blobben.

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

Läs mer om bindning uttryck [bindning uttryck och mönster](../azure-functions/functions-bindings-expressions-patterns.md) i Azure Functions-dokumentationen.

### <a name="custom-binding-expressions"></a>Anpassade bindningen uttryck

Ibland vill du ange en kö, ett blobbnamn behållare eller ett tabellnamn i kod i stället för att hårdkoda den. Du kanske exempelvis vill ange namnet på kön för den `QueueTrigger` attributet i en konfiguration av fil- eller miljö variabel.

Du kan göra det genom att skicka en `NameResolver` objekt i att den `JobHostConfiguration` objekt. Du inkluderar platshållare i utlösaren eller bindande attributet konstruktor parametrar och din `NameResolver` kod innehåller de faktiska värdena som ska användas i stället för platshållarna. Du kan identifiera platshållare om de med procent (%) loggar som visas här:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Den här koden kan du använda en kö med namnet `logqueuetest` i testmiljön och en med namnet `logqueueprod` i produktion. I stället för ett hårdkodat könamn du anger namnet på en post i den `appSettings` samling.

Det finns en standard `NameResolver` som träder i kraft om du inte anger en anpassad. Standard hämtar värden från appen inställningar eller miljövariabler.

Din `NameResolver` klass hämtar könamn från `appSettings`enligt nedan:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Version 3.*x*

Du kan konfigurera matcharen med hjälp av beroendeinmatning. De här exemplen kräver följande `using` instruktionen:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Du lägger till matcharen genom att anropa den [ `ConfigureServices` ] tilläggsmetod på [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder), som i det här exemplet:

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

#### <a name="version-2x"></a>Version 2.*x*

Skicka din `NameResolver` klassen i till den `JobHost` objekt, som visas här:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions implementerar `INameResolver` att hämta värden från appen inställningar, som visas i exemplet. När du använder WebJobs SDK direkt, kan du skriva en anpassad implementering platshållaren ersättningsvärden hämtas från den källa som du föredrar.

## <a name="binding-at-runtime"></a>Bindningen vid körning

Om du vill göra en del arbete i din funktion innan du använder attributet bindning som `Queue`, `Blob`, eller `Table`, du kan använda den `IBinder` gränssnitt.

I följande exempel tar ett inkommande kö-meddelande och skapar ett nytt meddelande med samma innehåll i en utgående kö. Könamn utdata har angetts av kod i brödtexten till funktionen.

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

Mer information finns i [bindning vid körning](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) i Azure Functions-dokumentationen.

## <a name="binding-reference-information"></a>Referensinformation för bindning

Azure Functions-dokumentationen innehåller information om varje bindningstyp av. Du hittar följande information i varje referensartikeln för bindningen. (Det här exemplet är baserad på Storage-kö.)

* [Paket](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). Det paket som du måste installera för att inkludera stöd för bindningen i ett projekt med WebJobs SDK.
* [Exempel](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Kodexempel. Den C# class library exempel gäller för WebJobs SDK. Utelämna bara den `FunctionName` attribut.
* [Attribut](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Attribut för bindningstypen.
* [Konfiguration av](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Förklaringar av de attributegenskaper och konstruktor parametrar.
* [Användning](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Du kan binda till typer och information om hur bindningen fungerar. Till exempel: avsökning algoritmen, poison kö bearbetning.
  
En lista över bindning referensartiklar, finns i ”stöds bindningar” i den [utlösare och bindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings) artikel för Azure Functions. I listan stöds HTTP och Webhooks Event Grid-bindningar endast av Azure Functions, inte av WebJobs SDK.

## <a name="disable-attribute"></a>Inaktivera attribut 

Den [ `Disable` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attributet låter dig kontrollera om en funktion som kan utlösas. 

I följande exempel, om appinställningen `Disable_TestJob` har värdet `1` eller `True` (skiftlägesokänsligt), funktionen inte körs. I så fall körningen skapar ett loggmeddelande *funktionen ”Functions.TestJob” är inaktiverad*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

När du ändrar app inställningsvärden i Azure-portalen, Webbjobbet startas om för att hämta upp den nya inställningen.

Attributet kan deklareras på parametern, metoden eller klassnivå. Inställningens namn kan också innehålla bindning uttryck.

## <a name="timeout-attribute"></a>Timeout-attributet

Den [ `Timeout` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) -attributet skapar en funktion som ska avbrytas om den inte slutförs inom en angiven tidsperiod. I följande exempel körs funktionen under en dag utan Timeout-attributet. Timeout gör att funktionen som ska avbrytas efter 15 sekunder.

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

Du kan tillämpa Timeout-attributet på nivån klass eller metod och du kan ange en global timeout med hjälp av `JobHostConfiguration.FunctionTimeout`. Klass- eller metoden nivå tidsgränser åsidosätta globala tidsgränser.

## <a name="singleton-attribute"></a>Singleton-attribut

Den [ `Singleton` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attributet säkerställer att endast en instans av en funktion körs, även om det finns flera instanser av webbappen värden. Detta sker med hjälp av [distribuerade låsning](#viewing-lease-blobs).

I det här exemplet, endast en instans av den `ProcessImage` funktionen körs vid en given tidpunkt:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Vissa utlösare har inbyggt stöd för hantering av samtidighet:

* **QueueTrigger**. Ange `JobHostConfiguration.Queues.BatchSize` till `1`.
* **ServiceBusTrigger**. Ange `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` till `1`.
* **FileTrigger**. Ange `FileProcessor.MaxDegreeOfParallelism` till `1`.

Du kan använda de här inställningarna för att säkerställa att din funktion körs som en singleton på en enda instans. För att säkerställa att endast en instans av funktionen körs när appen skalas ut till flera instanser, lägga till en lyssnare på servernivå singleton-lås på funktionen (`[Singleton(Mode = SingletonMode.Listener)]`). Lyssnaren Lås är upptagna när JobHost startar. Om tre utskalade instanser alla startar på samma gång, endast en av instanserna hämtar Lås och endast en lyssnare börjar.

### <a name="scope-values"></a>Områdesvärden

Du kan ange en *omfånguttrycksvärdet/* på en singleton. Uttrycksvärdet/säkerställer att alla körningar för funktionen i ett visst omfång ska serialiseras. Implementera mer detaljerade låsning på så vis kan tillåta för viss grad av parallellitet för funktionen vid serialisering av andra anrop enligt dina krav. Till exempel i följande kod, scope-uttrycket Binder till den `Region` värdet för det inkommande meddelandet. När kön innehåller tre meddelanden i regionerna östra, östra och västra respektive, medan de meddelanden som har region Öst körs seriellt meddelandet med region Väst körs parallellt med de i östra.

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

Standardomfattning för ett lås är `SingletonScope.Function`, vilket innebär att låsomfattningen (blob lease sökväg) är knuten till fullständigt kvalificerade funktionsnamnet. Om du vill låsa över funktioner, ange `SingletonScope.Host` och använda ett scope-ID-namn som är samma för alla funktioner som du inte vill ska köras samtidigt. I följande exempel visas endast en instans av `AddItem` eller `RemoveItem` körs vid ett tillfälle:

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

### <a name="viewing-lease-blobs"></a>Visa lånet blobar

WebJobs-SDK använder [Azure blob-lån](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) under försättsbladen att implementera distribuerade låsning. Lån-blobar som används av Singleton finns i den `azure-webjobs-host` behållaren i den `AzureWebJobsStorage` storage-konto under sökvägen ”Lås”. Till exempel den lån blob sökvägen för först `ProcessImage` exempel som visades tidigare kan vara `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Alla sökvägar med JobHost-ID i det här fallet 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Async-funktioner

Information om hur du kod async funktioner finns i den [Azure Functions-dokumentationen](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Annulleringstoken

Information om hur du hanterar annulleringstoken finns i Azure Functions-dokumentationen på [annulleringstoken och avslutning](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Flera instanser

Om din webbapp körs på flera instanser, ett kontinuerligt Webbjobb som körs på varje instans kan lyssna efter utlösare och anropa funktioner. De olika utlösare-bindningarna är utformade för att dela effektivt arbets samarbeta över instanser, kan så att skala ut till fler instanser du hantera mer belastning.

Kö- och blob-utlösare automatiskt förhindra att en funktion som bearbetar ett meddelande i kö eller blob mer än en gång. Functions behöver vara idempotenta.

Timer som utlösare säkerställer att endast en instans av timer-körningar automatiskt så att du inte får mer än en funktion-instans som körs vid en given schemalagda tidpunkt.

Om du vill se till att endast en instans av en funktion körs även om det finns flera instanser av webbappen värd kan du använda den [ `Singleton` ](#singleton-attribute) attribut.

## <a name="filters"></a>Filter

Funktionen filter (förhandsversion) är ett sätt att anpassa WebJobs körningspipeline med egen logik. Filter liknar [ASP.NET Core filtrerar](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Du kan implementera dem som deklarativ attribut som tillämpas på dina funktioner eller klasser. Mer information finns i [funktionen filter](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Vi rekommenderar vilket loggningsramverk som har utvecklats specifikt för ASP.NET. Den [börjar](webjobs-sdk-get-started.md) artikeln visar hur du använder den. 

### <a name="log-filtering"></a>Log filtrering

Varje log som skapats av en `ILogger` -instansen har en associerad `Category` och `Level`. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) en uppräkning och koden för heltal anger relativa prioriteten:

|LogLevel    |Kod|
|------------|---|
|Spårning       | 0 |
|Felsökning       | 1 |
|Information | 2 |
|Varning     | 3 |
|Fel       | 4 |
|Kritisk    | 5 |
|Ingen        | 6 |

Du kan filtrera varje kategori till en viss oberoende [ `LogLevel` ](/dotnet/api/microsoft.extensions.logging.loglevel). Exempel: du kan se alla loggar för blob utlösare men endast bearbeta `Error` och högre för allt annat.

#### <a name="version-3x"></a>Version 3.*x*

Version 3. *x* av SDK är beroende av filtrering inbyggt i .NET Core. Den `LogCategories` klassen kan du definiera enhetskategorier för specifika funktioner, utlösare eller användare. Den definierar även filter för specifika värden tillstånd som `Startup` och `Results`. På så sätt kan du finjustera loggning utdata. Om ingen matchning hittas inom de angivna kategorierna filtret faller tillbaka till den `Default` värdet när du bestämmer om du vill filtrera meddelandet.

`LogCategories` kräver följande med instruktionen:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

I följande exempel skapas ett filter som filtrerar alla loggar på som standard den `Warning` nivå. Den `Function` och `results` kategorier (motsvarar `Host.Results` i version 2. *x*) filtreras på den `Error` nivå. Filtret jämför den aktuella kategorin till alla registrerade nivåer i den `LogCategories` instans och väljer den längsta matchningen. Detta innebär att den `Debug` nivå som har registrerats för `Host.Triggers` matchar `Host.Triggers.Queue` eller `Host.Triggers.Blob`. På så sätt kan du styra bredare kategorier utan att behöva lägga till var och en.

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

#### <a name="version-2x"></a>Version 2.*x*

I version 2. *x* av SDK som, du använder den `LogCategoryFilter` klassen för att styra filtrering. Den `LogCategoryFilter` har en `Default` egenskap med ett startvärde för `Information`, vilket innebär att alla meddelanden i den `Information`, `Warning`, `Error`, eller `Critical` nivåer är inloggad, men meddelanden på den `Debug` eller `Trace` nivåer har filtrerats bort.

Precis som med `LogCategories` i version 3. *x*, `CategoryLevels` egenskap kan du ange loggningsnivåerna för specifika kategorier så att du kan finjustera loggning utdata. Om ingen matchning hittas i den `CategoryLevels` ordboken, filtret faller tillbaka till den `Default` värdet när du bestämmer om du vill filtrera meddelandet.

I följande exempel skapas ett filter som standard filtreras alla loggar på den `Warning` nivå. Den `Function` och `Host.Results` kategorier filtreras på den `Error` nivå. Den `LogCategoryFilter` jämför den aktuella kategorin för alla registrerade `CategoryLevels` och väljer den längsta matchningen. Så `Debug` nivå som har registrerats för `Host.Triggers` matchar `Host.Triggers.Queue` eller `Host.Triggers.Blob`. På så sätt kan du styra bredare kategorier utan att behöva lägga till var och en.

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

### <a name="custom-telemetry-for-application-insights"></a>Anpassad telemetri för Application Insights

Processen för att implementera anpassad telemetri för [Application Insights](../azure-monitor/app/app-insights-overview.md) beror på vilken SDK-version. Läs hur du konfigurerar Application Insights i [Lägg till Application Insights-loggning](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Version 3.*x*

Eftersom version 3. *x* av WebJobs SDK är beroende av .NET Core allmän värd, en anpassad telemetri-fabrik finns inte längre. Men du kan lägga till anpassad telemetri till pipelinen genom att använda beroendeinmatning. Exemplen i det här avsnittet kräver följande `using` instruktioner:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Följande anpassade implementeringen av [ `ITelemetryInitializer` ] låter dig lägga till egna [ `ITelemetry` ](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) standardvärdet [ `TelemetryConfiguration` ].

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Anropa [ `ConfigureServices` ] i builder att lägga till dina anpassade [ `ITelemetryInitializer` ] till pipelinen.

```cs
static void Main()
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

        host.Run();
    }
}
```

När den [ `TelemetryConfiguration` ] skapas, alla registrerade typer av [ `ITelemetryInitializer` ] ingår. Mer information finns i [Application Insights API för anpassade händelser och mått](../azure-monitor/app/api-custom-events-metrics.md).

I version 3. *x*, du behöver inte längre att tömma den [ `TelemetryClient` ] när värden avslutades. .NET Core beroende inmatning systemet tar automatiskt bort av det registrerade `ApplicationInsightsLoggerProvider`, vilka rensar den [ `TelemetryClient` ].

#### <a name="version-2x"></a>Version 2.*x*

I version 2. *x*, [ `TelemetryClient` ] skapats internt av Application Insights-providern för WebJobs-SDK använder [ `ServerTelemetryChannel` ](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). När Application Insights-slutpunkten är inte tillgänglig eller begränsa inkommande begäranden, den här kanalen [sparar begäranden i webbappens filsystemet och skickar dem senare](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

Den [ `TelemetryClient` ] har skapats av en klass som implementerar `ITelemetryClientFactory`. Som standard är detta den [ `DefaultTelemetryClientFactory` ](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Om du vill ändra någon del av din pipeline för Application Insights kan du ange egna `ITelemetryClientFactory`, och värden kommer att använda klassen för att konstruera ett [ `TelemetryClient` ]. Exempelvis kan den här koden åsidosätter `DefaultTelemetryClientFactory` att ändra en egenskap för `ServerTelemetryChannel`:

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

Den `SamplingPercentageEstimatorSettings` objekt konfigurerar [Adaptiv sampling](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Det innebär att i vissa scenarier med hög volym program Insights skickar en valda delmängd av dessa data till servern.

När du har skapat telemetri fabriken kan ange du i till Application Insights loggningsprovider:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a>Nästa steg

Den här artikeln har tillhandahållit kodavsnitt som visar hur du hanterar vanliga scenarier för att arbeta med WebJobs SDK. Komplett exempel finns [azure webjobs-sdk-exempel](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
