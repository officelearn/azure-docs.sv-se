---
title: Hur du använder WebJobs-SDK - Azure
description: Läs mer om hur du skriver kod för WebJobs SDK. Skapa en händelsedriven bakgrund bearbetar jobb som har åtkomst till data i Azure-tjänster och tjänster från tredje part.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: f6d343d42bf9d918bf23c9f5f442d977a5caca96
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343725"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Så här använder du Azure WebJobs SDK för händelsedrivna Bakgrundsbearbetning

Den här artikeln innehåller råd om hur du skriver kod för [Azure WebJobs SDK](webjobs-sdk-get-started.md). Dokumentationen gäller 2.x och 3.x utom om inget annat anges. Den huvudsakliga ändringen som introducerades av 3.x är användningen av .NET Core i stället för .NET Framework.

>[!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) bygger på WebJobs SDK och den här artikellänkar till Azure Functions-dokumentationen för vissa ämnen. Observera följande skillnader mellan Functions och WebJobs-SDK:
> * Azure Functions-version 1.x motsvarar WebJobs-SDK-version 2.x och Azure Functions 2.x motsvarar WebJobs SDK 3.x. Källa för kodförråd Följ WebJobs SDK numrering, och många har v2.x grenar med huvudgrenen för närvarande med 3.x kod.
> * Exempelkod för Azure Functions C#-klassbibliotek fungerar som WebJobs SDK code förutom du inte behöver en `FunctionName` attribut i ett projekt med WebJobs SDK.
> * Vissa bindningstyper av stöds bara i funktioner, t.ex HTTP, webhook och Event Grid (som baseras på HTTP). 
> 
> Mer information finns i [jämföra Azure Functions och WebJobs SDK](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har läst [Kom igång med WebJobs SDK](webjobs-sdk-get-started.md).

## <a name="jobhost"></a>JobHost

Den `JobHost` objektet är körningsbehållare för functions: den lyssnar efter utlösare och-anrop. Du skapar den `JobHost` i din kod och skriva kod för att anpassa sitt beteende.

Det här är en viktig skillnad mellan med hjälp av WebJobs SDK direkt och använda den indirekt med hjälp av Azure Functions. I Azure Functions, service-kontroller i `JobHost`, och du kan anpassa den genom att skriva kod. Azure Functions kan du anpassa värden beteende via inställningarna i den *host.json* fil. Dessa inställningar är strängar, inte koden, vilket begränsar typerna av anpassningar som du kan göra.

### <a name="jobhost-connection-strings"></a>JobHost anslutningssträngar

WebJobs SDK ser ut för lagring och Service Bus anslutningssträngar i *local.settings.json* när du kör lokalt eller i den WebJob miljö när du kör i Azure. Om du vill använda egna namn för dessa anslutningssträngar och lagra dem på en annan plats, kan du ange dem i kod, som visas här:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>Inställningar för utveckling av JobHost

Den `JobHostConfiguration` klassen har en `UseDevelopmentSettings` metod som du kan anropa för att effektivisera lokal utveckling. Här är några av de inställningar som den här metoden ändras:

| Egenskap  | Inställningen för utveckling |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` Maximera loggutdata. |
| `Queues.MaxPollingInterval`  | Ett lågt värde så kö metoder utlöses omedelbart.  |
| `Singleton.ListenerLockPeriod` | 15 sekunder att underlätta snabb iterativ utveckling. |

I följande exempel visar hur du använder inställningar för webbprogramutveckling. Att göra `config.IsDevelopment` returnera `true` när körs lokalt kan du ställa in en lokal miljövariabeln `AzureWebJobsEnv` med värdet `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>JobHost ServicePointManager inställningar

.NET Framework innehåller ett API som kallas [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) som styr antalet samtidiga anslutningar till en värd. Vi rekommenderar att du ökar det här värdet från standardvärdet 2 innan du börjar din WebJobs-värd.

Alla utgående HTTP-begäranden som du gör en funktion med hjälp av `HttpClient` flödar genom den `ServicePointManager`. När du når den `DefaultConnectionLimit`, `ServicePointManager` startar jobbköer begäranden innan de skickas. Anta att dina `DefaultConnectionLimit` anges till 2 och din kod gör 1 000 HTTP-begäranden. Endast 2 begäranden tillåts ursprungligen, faktiskt via till Operativsystemet. De andra 998 köas tills det finns plats för dessa. Det innebär att din `HttpClient` får timeout, eftersom den *tror* begäran har gjorts, men förfrågan skickades aldrig av Operativsystemet till målservern. Så att du kan se beteende som inte verkar vara meningsfullt: lokalt `HttpClient` tar 10 sekunder att slutföra en begäran, men tjänsten returnerar varje begäran på 200 ms. 

Standardvärdet för ASP.NET-program är `Int32.MaxValue`, och som sannolikt kommer att fungera bra WebJobs körs i en grundläggande eller högre App Service-plan. WebJobs vanligtvis måste alltid på inställningen och som stöds endast av grundläggande och högre App Service-planer. 

Om ditt WebJob körs i en kostnadsfri eller delad App Service-Plan, ditt program är begränsad av App Service-sandboxen som för närvarande har en [anslutning på högst 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Med en anslutningsgräns som har delats upp i `ServicePointManager`, det är troligare att tröskelvärdet för sandbox-anslutningen kommer att nå och Stäng av webbplatsen. I så fall kan ställa in `DefaultConnectionLimit` till något lägre som 50 eller 100, kan förhindra att detta inträffar och fortfarande tillåter tillräckligt dataflöde.

Inställningen måste konfigureras innan alla HTTP-begäranden som görs. Därför WebJobs-värden får inte försöka justera inställningen automatiskt. Det kan finnas HTTP-begäranden som görs innan värden startas och detta kan leda till oväntade resultat. Det bästa sättet är att ange ett värde direkt i din `Main` sättet innan initierar den `JobHost`, enligt följande exempel

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Utlösare

Funktioner måste vara offentliga metoderna och måste ha ett utlösarattribut eller [NoAutomaticTrigger](#manual-trigger) attribut.

### <a name="automatic-trigger"></a>Automatisk utlösare

Automatisk utlösare anropar en funktion som svar på en händelse. Ett exempel finns i kö-utlösare i den [Get artikeln](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Manuell utlösare

Utlös en funktion manuellt genom att använda den `NoAutomaticTrigger` attributet som visas i följande exempel:

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>Indata och utdata bindningar

Indatabindningar tillhandahåller en deklarativ metod för att göra data från Azure eller tjänster från tredje part tillgängliga för din kod. Utdatabindningar är ett sätt att uppdatera data. Den [Get artikeln](webjobs-sdk-get-started.md) visar ett exempel på var och en.

Du kan använda metoden returvärdet för en utdatabindning genom att använda attributet returvärdet för metoden. Se exemplet i Azure Functions [utlösare och bindningar](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) artikeln.

## <a name="binding-types"></a>Bindningstyper

Följande typer av utlösare och bindning som ingår i den `Microsoft.Azure.WebJobs` paketet:

* Blob Storage
* Queue Storage
* Table Storage

Om du vill använda andra utlösare och bindningstyper, installera NuGet-paketet som innehåller dem och anropa en `Use<binding>` metoden på den `JobHostConfiguration` objekt. Till exempel om du vill använda en Timer som utlösare installera `Microsoft.Azure.WebJobs.Extensions` och anropa `UseTimers` i den `Main` metod som i följande exempel:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Du kan hitta att uppdateringen ska installeras för en viss bindningstypen i den **paket** avsnitt av den bindningstypen [referensartikeln](#binding-reference-information) för Azure Functions. Ett undantag är utlösaren filer och bindningen (för det lokala filsystemet), vilket inte stöds av Azure Functions. att använda filerna bindning, installera `Microsoft.Azure.WebJobs.Extensions` och anropa `UseFiles`.

### <a name="usecore"></a>UseCore

Den `Microsoft.Azure.WebJobs.Extensions` paket som tidigare nämnts innehåller också en särskild bindningstyp som du kan registrera genom att anropa den `UseCore` metoden. Den här bindningen kan du definiera en [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) parametern i funktionssignaturen. Context-objektet får du tillgång till anrops-ID som du kan använda för att korrelera alla loggar som producerats av en viss funktionsanrop. Här är ett exempel:

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

## <a name="binding-configuration"></a>Bindningen konfiguration

Många utlösa och bindningstyper kan du konfigurera deras beteende genom att ange egenskaper i ett konfigurationsobjekt som du anger för att den `JobHost`.

### <a name="queue-trigger-configuration"></a>Kön utlösarkonfiguration

Vilka inställningar du kan konfigurera för Storage-kö-utlösare beskrivs i Azure Functions [referens för host.json](../azure-functions/functions-host-json.md#queues). I följande exempel visas hur du ställer in dem i ett projekt med WebJobs SDK:

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

### <a name="configuration-for-other-bindings"></a>Konfiguration för andar bindningar

Vissa typer av utlösare och bindningen definiera sina egna anpassade konfigurationstyp. Till exempel kan utlösaren fil du ange rotsökvägen att övervaka:

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

Läs mer om bindning uttryck [bindning uttryck och mönster](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) i Azure Functions-dokumentationen.

### <a name="custom-binding-expressions"></a>Anpassade bindningen uttryck

Vill ibland du ange en kö, ett blobnamn eller behållare eller en tabell i kod i stället för hårdkoda ge den namnet. Du kanske exempelvis vill ange namnet på kön för den `QueueTrigger` attributet i en konfiguration av fil- eller miljö variabel.

Du kan göra det genom att skicka in en `NameResolver` objekt till den `JobHostConfiguration` objekt. Du inkluderar platshållare i utlösaren eller bindande attributet konstruktor parametrar och din `NameResolver` kod innehåller de faktiska värdena som ska användas i stället för platshållarna. Platshållarna identifieras om de med procenttecken (%), som visas i följande exempel:
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Den här koden kan du använda en kö med namnet logqueuetest i testmiljön och en namngiven logqueueprod i produktion. I stället för ett hårdkodat könamn du anger namnet på en post i den `appSettings` samling. 

Det finns en standard NameResolver som påverkas om du inte anger en anpassad. Standard hämtar värden från appen inställningar eller miljövariabler.

Din `NameResolver` klass hämtar könamn från `appSettings` som visas i följande exempel:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Skicka din `NameResolver` klassen i till den `JobHost` objekt som visas i följande exempel:

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
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

Mer information finns i [bindning vid körning](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) i Azure Functions-dokumentationen.

## <a name="binding-reference-information"></a>Referensinformation för bindning

Information om varje bindningstyp av har angetts i Azure Functions-dokumentationen. Med Storage-kö som exempel, hittar du följande information i alla bindning referens:

* [Paket](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) – vilket paket för att installera för att inkludera stöd för bindningen i ett projekt med WebJobs SDK.
* [Exempel](../azure-functions/functions-bindings-storage-queue.md#trigger---example) -i C# class library-exempel som gäller för WebJobs SDK; bara utelämna den `FunctionName` attribut.
* [Attribut](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) -attribut ska användas för bindningstypen.
* [Konfigurationen](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) -förklaringar av de attributegenskaper och konstruktor parametrar.
* [Användning](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) – vad skriver du kan bindas till och information om hur bindningen fungerar. Till exempel: avsökning algoritmen, poison kö bearbetning.
  
En lista över bindning referensartiklar, se **stöds bindningar** i den [utlösare och bindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings) artikel för Azure Functions. I listan är HTTP, webhook och Event Grid-Bindningar stöds endast av Azure Functions, inte av WebJobs SDK.

## <a name="disable-attribute"></a>Inaktivera attribut 

Den [inaktivera](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attributet låter dig kontrollera om en funktion som kan utlösas. 

I följande exempel, om appinställningen `Disable_TestJob` har värdet ”1” eller ”True” (skiftlägesokänsligt), funktionen inte körs. I så fall körningen skapar ett loggmeddelande *funktionen ”Functions.TestJob” är inaktiverad*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

När du ändrar app inställningsvärden i Azure-portalen gör Webbjobbet startas, plocka upp den nya inställningen.

Attributet kan deklareras på parametern, metoden eller klassnivå. Inställningens namn kan också innehålla bindning uttryck.

## <a name="timeout-attribute"></a>Timeout-attributet

Den [Timeout](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) -attributet skapar en funktion som ska avbrytas om den inte slutförs inom en angiven tidsperiod. I följande exempel körs funktionen under en dag utan timeout-värdet. Med tidsgränsen avbryts funktionen efter 15 sekunder.

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

Du kan tillämpa Timeout-attributet på klass eller metod nivå och du kan ange en global timeout med hjälp av `JobHostConfiguration.FunctionTimeout`. Klass eller metod på tidsgränser åsidosätter den globala tidsgränsen.

## <a name="singleton-attribute"></a>Singleton-attribut

Använd den [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attribut för att se till att endast en instans av en funktion körs även när det finns flera instanser av webbappen värden. Detta sker genom att implementera [distribuerade låsning](#viewing-lease-blobs).

I följande exempel visas endast en instans av den `ProcessImage` funktionen körs vid en given tidpunkt:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Vissa utlösare har inbyggt stöd för hantering av samtidighet:

* **QueueTrigger** – Ställ in `JobHostConfiguration.Queues.BatchSize` till 1.
* **ServiceBusTrigger** – Ställ in `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` till 1.
* **FileTrigger** – Ställ in `FileProcessor.MaxDegreeOfParallelism` till 1.

Du kan använda de här inställningarna för att säkerställa att din funktion körs som en singleton på en enda instans. För att säkerställa att endast en instans av funktionen körs när appen skalas ut till flera instanser, lägga till en lyssnare på Singleton-lås på funktionen (`[Singleton(Mode = SingletonMode.Listener)]`). Lyssnaren Lås förvärvas vid start av JobHost. Om tre utskalade instanser alla startar på samma gång, endast en av instanserna hämtar Lås och endast en lyssnare börjar.

### <a name="scope-values"></a>Områdesvärden

Du kan ange en **omfånguttrycksvärdet/** på Singleton som säkerställer att alla körningar av funktionen på detta omfång ska serialiseras. Implementera mer detaljerade låsning på så vis kan tillåta för viss grad av parallellitet för funktionen vid serialisering av andra anrop enligt dina krav. Till exempel i följande exempel scope-uttrycket Binder till den `Region` värdet för det inkommande meddelandet. Om kön innehåller 3 meddelanden i regioner ”östra”, ”östra” och ”västra” respektive sedan meddelanden som har region ”, östra” körs seriellt när meddelandet med region ”västra” körs parallellt med de.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item
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

Standardomfattning för ett lås är `SingletonScope.Function` vilket innebär att låsomfattningen (blob lease sökväg) är knuten till fullständigt kvalificerade funktionsnamnet. Om du vill låsa över funktioner, ange `SingletonScope.Host` och använda ett scope-ID-namn som är samma för alla funktioner som du inte vill ska köras samtidigt. I följande exempel visas endast en instans av `AddItem` eller `RemoveItem` körs vid ett tillfälle:

```charp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>Visa lånet blobar

WebJobs-SDK använder [Azure blob-lån](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) under försättsbladen att implementera distribuerade låsning. Lån-blobar som används av Singleton finns i den `azure-webjobs-host` behållaren i den `AzureWebJobsStorage` storage-konto under sökvägen ”Lås”. Till exempel den lån blob sökvägen för först `ProcessImage` exempel som visades tidigare kan vara `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Alla sökvägar med JobHost-ID i det här fallet 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Async-funktioner

Information om hur du kod async funktioner finns i Azure Functions-dokumentationen på [Async funktioner](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Annulleringstoken

Information om hur du hanterar annulleringstoken finns i Azure Functions-dokumentationen på [annulleringstoken och avslutning](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Flera instanser

Om din webbapp körs på flera instanser, ett kontinuerligt Webbjobb som körs på varje instans kan lyssna efter utlösare och anropa funktioner. De olika utlösare-bindningarna är utformade för att dela effektivt arbets samarbeta över instanser, kan så att skala ut till fler instanser du hantera mer belastning.

Kö- och blob-utlösare automatiskt förhindra att en funktion som bearbetar ett meddelande i kö eller blob mer än en gång. Functions behöver inte vara idempotenta.

Timer som utlösare säkerställer att endast en instans av timer-körningar automatiskt så att du inte får mer än en funktion-instans som körs vid en given schemalagda tidpunkt.

Om du vill se till att endast en instans av en funktion körs även om det finns flera instanser av webbappen värd kan du använda den [Singleton-attributet](#singleton-attribute).
    
## <a name="filters"></a>Filter 

Funktionen filter (förhandsversion) är ett sätt att anpassa WebJobs körningspipeline med egen logik. Filter liknar [ASP.NET Core filter](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). De kan implementeras som deklarativ attribut som tillämpas på dina funktioner eller klasser. Mer information finns i [funktionen filter](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Vi rekommenderar vilket loggningsramverk som har utvecklats specifikt för ASP.NET, och [börjar](webjobs-sdk-get-started.md) artikeln visar hur du använder den. 

### <a name="log-filtering"></a>Log filtrering

Varje log som skapats av en `ILogger` -instansen har en associerad `Category` och `Level`. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) är en uppräkning och heltal koden visar relativa prioriteten:

|LogLevel    |Kod|
|------------|---|
|Spårning       | 0 |
|Felsökning       | 1 |
|Information | 2 |
|Varning     | 3 |
|Fel       | 4 |
|Kritisk    | 5 |
|Ingen        | 6 |

Varje kategori kan filtreras oberoende till en viss [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel). Exempel: du kan se alla loggar för blob utlösare men endast bearbeta `Error` och högre för allt annat.

Om du vill göra det enklare att ange filtreringsregler, WebJobs SDK innehåller de `LogCategoryFilter` som kan skickas till många av de befintliga loggning-dataleverantörer, inklusive Application Insights och konsolen.

Den `LogCategoryFilter` har en `Default` egenskap med ett startvärde för `Information`, vilket innebär att alla meddelanden med `Information`, `Warning`, `Error`, eller `Critical` loggas, men alla meddelanden med `Debug` eller `Trace` filtreras bort.

Den `CategoryLevels` egenskapen kan du ange loggningsnivåerna för specifika kategorier så att du kan finjustera loggning utdata. Om ingen matchning hittas i den `CategoryLevels` ordboken, filtret faller tillbaka till den `Default` värdet när du bestämmer om du vill filtrera meddelandet.

I följande exempel skapas ett filter som standard filtreras alla loggar på den `Warning` nivå. Kategorier av `Function` eller `Host.Results` filtreras på den `Error` nivå. Den `LogCategoryFilter` jämför den aktuella kategorin för alla registrerade `CategoryLevels` och väljer den längsta matchningen. Detta innebär att den `Debug` nivå som har registrerats för `Host.Triggers` matchar `Host.Triggers.Queue` eller `Host.Triggers.Blob`. På så sätt kan du styra bredare kategorier utan att behöva lägga till var och en.

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

Internt i `TelemetryClient` skapade av Application Insights-providern för WebJobs-SDK använder den [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). När Application Insights-slutpunkten är inte tillgänglig eller begränsa inkommande begäranden, den här kanalen [sparar begäranden i webbappens filsystemet och skickar dem senare](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

Den `TelemetryClient` har skapats av en klass som implementerar `ITelemetryClientFactory`. Som standard är detta den [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Om du vill ändra någon del av din pipeline för Application Insights kan du ange egna `ITelemetryClientFactory`, och värden kommer att använda klassen för att konstruera ett `TelemetryClient`. Exempelvis kan den här koden åsidosätter den `DefaultTelemetryClientFactory` att ändra en egenskap för den `ServerTelemetryChannel`:

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

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Objektet SamplingPercentageEstimatorSettings konfigurerar [Adaptiv sampling](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Det innebär att i vissa scenarier med hög volym App Insights skickar en valda delmängd av dessa data till servern.

När du har skapat telemetri fabriken kan ange du i till Application Insights loggningsprovider:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a>Nästa steg

Den här guiden tillhandahåller kodsnuttar som demonstrerar hur du hanterar vanliga scenarier för att arbeta med WebJobs SDK. Komplett exempel finns [azure webjobs-sdk-exempel](https://github.com/Azure/azure-webjobs-sdk-samples).