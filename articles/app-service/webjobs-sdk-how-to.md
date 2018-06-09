---
title: Hur du använder Azure WebJobs SDK
description: Mer information om hur du skriva kod för WebJobs-SDK. Skapa händelsedriven bakgrund bearbetar jobb som har åtkomst till data i Azure-tjänster och tjänster från tredje part.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 08272ba7d828f744336723f25b482bf06b9e43dc
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234658"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Hur du använder Azure WebJobs SDK för händelsedriven Bakgrundsbearbetning

Den här artikeln innehåller information om hur du skriva kod för [Azure WebJobs SDK](webjobs-sdk-get-started.md). Dokumentationen gäller 2.x och 3.x om inget annat anges annars. Huvudsakliga ändringen introducerades av 3.x är att använda .NET Core i stället för .NET Framework.

>[!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) bygger på WebJobs-SDK och den här artikellänkar till Azure Functions dokumentationen för vissa avsnitt. Observera följande skillnader mellan funktioner och WebJobs-SDK:
> * Azure Functions version 1.x motsvarar WebJobs SDK version 2.x och Azure Functions 2.x motsvarar WebJobs SDK 3.x. Källkodslager följer WebJobs SDK numrering och många har v2.x filialer med mastergrenen för närvarande med 3.x kod.
> * Exempelkod för Azure Functions C#-klassbibliotek fungerar som WebJobs SDK code förutom inte behöver du en `FunctionName` attribut i en WebJobs-SDK-projektet.
> * Vissa bindningstyper av stöds endast i funktioner, t.ex HTTP, webhook och händelsen rutnätet (som är baserat på HTTP). 
> 
> Mer information finns i [jämför WebJobs-SDK och Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har läst [Kom igång med WebJobs SDK](webjobs-sdk-get-started.md).

## <a name="jobhost"></a>JobHost

Den `JobHost` objektet är körningsbehållaren för funktioner: den lyssnar efter utlösare och anrop funktioner. Du skapar den `JobHost` i din kod och skriva kod för att anpassa sitt beteende.

Det här är en nyckel skillnaden mellan med hjälp av WebJobs-SDK direkt och med indirekt med hjälp av Azure Functions. I Azure Functions, service-kontroller i `JobHost`, och du kan anpassa den genom att skriva kod. Azure Functions kan du anpassa värden beteende via inställningarna i den *host.json* fil. Dessa inställningar är strängar, inte koden, som begränsar vilka typer av anpassningar som du kan göra.

### <a name="jobhost-connection-strings"></a>JobHost anslutningssträngar

WebJobs SDK söker efter lagring och Service Bus-anslutningssträngar i *local.settings.json* när du kör lokalt eller i den Webbjobb miljön när du kör i Azure. Om du vill använda ditt eget namn för dessa anslutningssträngar eller lagra dem på en annan plats, kan du ange dem i koden, som visas här:

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

### <a name="jobhost-development-settings"></a>JobHost utvecklingsinställningar

Den `JobHostConfiguration` klassen har en `UseDevelopmentSettings` metod som du kan anropa för att effektivisera lokal utveckling. Här följer några av de inställningar som den här metoden ändras:

| Egenskap  | Inställningen för utveckling |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` Maximera loggutdata. |
| `Queues.MaxPollingInterval`  | Ett lågt värde så kön metoder utlöses omedelbart.  |
| `Singleton.ListenerLockPeriod` | 15 sekunder att underlätta snabb, iterativ utveckling. |

I följande exempel visas hur du använder inställningar för webbprogramutveckling. Att göra `config.IsDevelopment` returnera `true` när du kör lokalt, ange en lokal miljövariabel som heter `AzureWebJobsEnv` med värdet `Development`.

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

.NET Framework innehåller en API som kallas [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) som styr antalet samtidiga anslutningar till en värd. Vi rekommenderar att du ökar det här värdet från standardvärdet 2 innan du startar WebJobs-värden.

Alla utgående HTTP-begäranden som du gör en funktion med hjälp av `HttpClient` gå genom det `ServicePointManager`. När du har klickat på `DefaultConnectionLimit`, `ServicePointManager` startar begäranden i kö innan de skickas. Anta att din `DefaultConnectionLimit` är inställd på 2 och din kod gör 1 000 HTTP-begäranden. Endast 2 begäranden tillåts inledningsvis faktiskt via operativsystem. De andra 998 köas tills det finns utrymme för dem. Det innebär att din `HttpClient` får timeout, eftersom den *tror* begäran har gjorts, men begäran skickades aldrig av Operativsystemet till målservern. Så att du kan se beteende som inte verkar vara meningsfullt: lokalt `HttpClient` tar 10 sekunder att slutföra en begäran, men tjänsten returnerar varje begäran på 200 ms. 

Standardvärdet för ASP.NET-program är `Int32.MaxValue`, och som kan förväntas fungerar bra för Webbjobb som körs i en grundläggande eller högre programtjänstplan. WebJobs vanligtvis måste alltid på inställningen och som stöds endast av App Service-planer för grundläggande och högre. 

Om din Webbjobbet körs i en kostnadsfri eller delad App Service-Plan, ditt program är begränsat av sandbox Apptjänst har för närvarande en [anslutningsgränsen 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Med en obunden anslutningsgränsen i `ServicePointManager`, är det mer troligt att tröskelvärdet sandbox anslutningen kommer att nå och webbplatsen avslutades. Ange i så fall `DefaultConnectionLimit` till något lägre som 50 eller 100, kan förhindra att detta inträffar och fortfarande tillåter tillräckligt genomflöde.

Inställningen måste konfigureras innan alla HTTP-begäranden som görs. Därför WebJobs-värden får inte försöka justera inställningen automatiskt. Det kan finnas HTTP-begäranden som görs innan värden startar och detta kan leda till oväntade resultat. Det bästa sättet är att ange värdet direkt i din `Main` metoden innan vid initiering av den `JobHost`som visas i följande exempel

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

Funktioner måste vara offentliga metoder och måste ha ett utlösaren attribut eller [NoAutomaticTrigger](#manual-trigger) attribut.

### <a name="automatic-trigger"></a>Automatisk utlösare

Automatisk utlösare anropa en funktion som svar på en händelse. Ett exempel finns i kön utlösaren i den [få igång artikel](webjobs-sdk-get-started.md).

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

Inkommande bindningar tillhandahåller en deklarativ metod för att göra data från Azure eller tjänster från tredje part tillgänglig för din kod. Bindningar för utdata är ett sätt att uppdatera data. Den [få igång artikel](webjobs-sdk-get-started.md) visar ett exempel på varje.

Du kan använda ett returvärde för metoden för en output-bindning genom att använda attributet returvärdet för metoden. Se exemplet i Azure Functions [utlösare och bindningar](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) artikel.

## <a name="binding-types"></a>Bindningstyper

Följande typer av utlösare och bindningen ingår i den `Microsoft.Azure.WebJobs` paketet:

* Blob Storage
* Queue Storage
* Table Storage

Om du vill använda andra utlösare och bindningstyper installera NuGet-paketet som innehåller dem och anropa en `Use<binding>` metod på den `JobHostConfiguration` objekt. Till exempel om du vill använda en Timer som utlösare installera `Microsoft.Azure.WebJobs.Extensions` och anropa `UseTimers` i den `Main` metod som i det här exemplet:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Du kan hitta paket för installation för en viss bindningstyp i den **paket** avsnitt i den bindningstypen [referensartikeln](#binding-reference-information) för Azure Functions. Ett undantag är utlösaren filer och bindningen (för det lokala filsystemet), vilket inte stöds av Azure Functions. använda filerna bindning, installera `Microsoft.Azure.WebJobs.Extensions` och anropa `UseFiles`.

### <a name="usecore"></a>UseCore

Den `Microsoft.Azure.WebJobs.Extensions` paket som tidigare nämnts innehåller också en särskild bindningstyp som du kan registrera genom att anropa den `UseCore` metoden. Den här bindningen kan du definiera en [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) parametern i funktionssignaturen. Context-objektet ger dig tillgång till anrops-ID som du kan använda för att korrelera alla loggar som genereras av en viss funktionsanrop. Här är ett exempel:

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

## <a name="binding-configuration"></a>Bindningskonfigurationen

Många utlöser och bindning typer kan du konfigurera deras beteende genom att ange egenskaper i ett konfigurationsobjekt som du anger för att den `JobHost`.

### <a name="queue-trigger-configuration"></a>Konfiguration av kön utlösare

Vilka inställningar du kan konfigurera för lagring kö utlösaren förklaras i Azure Functions [host.json referens](../azure-functions/functions-host-json.md#queues). Hur du ställer in dem i en WebJobs-SDK-projektet visas i följande exempel:

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

### <a name="configuration-for-other-bindings"></a>Konfigurationen för andra bindningar

Vissa typer av utlösare och bindningen definiera egna anpassade konfigurationstypen. Till exempel kan utlösaren fil du ange rotsökvägen för att övervaka:

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

Du kan använda uttryck som matchar värden från olika källor i attributet konstruktorparametrarna. Till exempel i följande kod, sökvägen för den `BlobTrigger` attribut skapar ett uttryck med namnet `filename`. När den används för utdata-bindningen `filename` matchar namnet på den utlösande blobben.
 
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

Mer information om bindande uttryck finns [bindande uttryck och mönster](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) i Azure Functions-dokumentationen.

### <a name="custom-binding-expressions"></a>Anpassade bindande uttryck

Du vill ibland ange en ny kö, ett blob-namn eller en behållare eller en tabell i stället för att hårdkoda ge den namnet. Du kan till exempel vill ange namnet på kön för den `QueueTrigger` attribut i en konfiguration av fil- eller miljö variabel.

Du kan göra det genom att passera i en `NameResolver` objekt i `JobHostConfiguration` objekt. Du inkludera platshållare i utlösare eller bindande attributet konstruktorn parametrar och dina `NameResolver` kod ger de faktiska värdena som ska användas i stället för dessa platshållare. Platshållarna identifieras genom att placera med procenttecken (%), som visas i följande exempel:
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Den här koden kan du använda en kö med namnet logqueuetest i testmiljön och en namngiven logqueueprod i produktion. I stället för en hårdkodad könamnet du anger namnet på en post i den `appSettings` samling. 

Det finns en standard NameResolver som påverkas om du inte anger en anpassad. Standard hämtar värden från app-inställningar eller miljövariabler.

Din `NameResolver` klassen hämtar könamnet från `appSettings` som visas i följande exempel:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Skicka din `NameResolver` klassen i att den `JobHost` objekt som visas i följande exempel:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions implementerar `INameResolver` att hämta värden från app-inställningar som visas i exemplet. När du använder WebJobs-SDK direkt, skriver du en anpassad implementering platshållare ersättningsvärden hämtas från den källa som du föredrar. 

## <a name="binding-at-runtime"></a>Bindning under körning

Om du behöver göra några resurser i din funktion innan du använder attributet bindning som `Queue`, `Blob`, eller `Table`, du kan använda den `IBinder` gränssnitt.

I följande exempel tar ett inkommande kö-meddelande och skapar ett nytt meddelande med samma innehåll i en utgående kö. Könamnet utdata anges av koden i själva funktionen.

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

Mer information finns i [bindning under körning](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) i Azure Functions-dokumentationen.

## <a name="binding-reference-information"></a>Referensinformation för bindning

Information om varje bindningstyp tillhandahålls i Azure Functions-dokumentationen. Med Storage-kö som exempel, hittar du följande information i varje bindning referens-artikel:

* [Paket](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) -vilket paket för att installera om du vill inkludera stöd för bindningen i WebJobs-SDK-projekt.
* [Exempel](../azure-functions/functions-bindings-storage-queue.md#trigger---example) -i C# class library exempel gäller WebJobs SDK; bara utelämna den `FunctionName` attribut.
* [Attribut](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) -attribut som ska användas för bindningstyp.
* [Konfigurationen](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) -förklaringar av egenskaper för attribut och parametrar som konstruktorn.
* [Användning](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) – vilka datatyper du kan binda till och information om hur den fungerar. Till exempel: avsökning algoritmen poison kön bearbetning.
  
En lista över bindning referensartiklar, se **stöds bindningar** i den [utlösare och bindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings) artikel för Azure Functions. I listan stöds HTTP och webhook händelse rutnätet bindningar endast av Azure Functions, inte av WebJobs-SDK.

## <a name="disable-attribute"></a>Inaktivera attributet 

Den [inaktivera](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribut kan du ange om en funktion kan aktiveras. 

I följande exempel, om appinställningen `Disable_TestJob` har värdet ”1” eller ”True” (skiftlägesokänsligt) funktionen inte körs. I så fall körningsmiljön skapar ett loggmeddelande *inaktiveras funktionen 'Functions.TestJob'*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

När du ändrar app inställningsvärden i Azure portal gör Webbjobb startas, upp den nya inställningen.

Attributet kan deklareras på parametern, metoden eller klassnivå. Inställningens namn kan också innehålla bindande uttryck.

## <a name="timeout-attribute"></a>Timeout-attributet

Den [Timeout](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) -attributet skapar en funktion som ska avbrytas om det inte slutföras inom en angiven tidsperiod. I följande exempel körs funktionen under en dag utan timeout. Med tidsgränsen avbryts funktionen efter 15 sekunder.

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

Du kan använda attributet Timeout på klass eller metod nivå och du kan ange en global timeout med hjälp av `JobHostConfiguration.FunctionTimeout`. Klass eller metod nivån tidsgränser åsidosätta den globala tidsgränsen.

## <a name="singleton-attribute"></a>Singleton-attribut

Använd den [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attribut för att se till att endast en instans av en funktion körs även när det finns flera instanser av webbprogram för värden. Detta åstadkoms genom att implementera [distribuerade låsning](#viewing-lease-blobs).

I följande exempel visas bara en instans av den `ProcessImage` funktionen körs samtidigt:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Vissa utlösare har inbyggt stöd för hantering av samtidighet:

* **QueueTrigger** – du kan ställa `JobHostConfiguration.Queues.BatchSize` till 1.
* **ServiceBusTrigger** – du kan ställa `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` till 1.
* **FileTrigger** – du kan ställa `FileProcessor.MaxDegreeOfParallelism` till 1.

Du kan använda dessa inställningar för att se till att din funktion körs som en singleton på en enda instans. För att säkerställa att bara en instans av funktionen körs när webbprogrammet skalas ut till flera instanser, tillämpa lyssnare nivån Singleton lås på funktionen (`[Singleton(Mode = SingletonMode.Listener)]`). Lyssnare Lås förvärvas vid start av JobHost. Om tre utskalat instanser alla startar på samma gång, endast en av instanserna hämtar Lås och endast en lyssnare startar.

### <a name="scope-values"></a>Scope-värden

Du kan ange en **omfånguttrycksvärdet/** på Singleton som säkerställer att alla körningar av funktionen på detta scope ska serialiseras. Implementera mer detaljerade låsning på så sätt kan tillåta för vissa nivå av parallellitet för din funktion vid serialiseringen andra anrop enligt dina krav. Till exempel i följande exempel scope-uttrycket Binder till den `Region` värdet för det inkommande meddelandet. Om kön innehåller 3 meddelanden i regioner ”Öst”, ”Öst” och ”Väst” respektive sedan de meddelanden som har ”Öst” körs seriellt när meddelandet med region ”Väst” körs parallellt med de region.

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

Standardomfattningen för lås är `SingletonScope.Function` vilket innebär att Lås omfång (blob lån sökväg) är knutna till fullständiga funktionsnamnet. Om du vill låsa över funktioner, ange `SingletonScope.Host` och använda ett scope-ID-namn som är samma för alla de funktioner som du inte vill köra samtidigt. I följande exempel visas bara en instans av `AddItem` eller `RemoveItem` körs samtidigt:

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

### <a name="viewing-lease-blobs"></a>Visa lån blobbar

WebJobs-SDK använder [Azure blob-lån](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) under försättsbladen att implementera distribuerade låsning. Lease-blobbar som används av Singleton kan hittas i den `azure-webjobs-host` behållare i den `AzureWebJobsStorage` storage-konto på sökvägen ”Lås”. Till exempel den lån blobbsökvägen för först `ProcessImage` kan till exempel som visades tidigare vara `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Alla sökvägar som innehåller JobHost-ID i det här fallet 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Async-funktion

Information om hur du koden asynkrona funktioner finns i dokumentationen för Azure Functions på [asynkrona funktioner](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Annullering token

Information om hur du hanterar annullering token finns i dokumentationen för Azure Functions på [annullering token och korrekt avslutning](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Flera instanser

Om ditt webbprogram som körs på flera instanser är ett kontinuerligt Webbjobb som körs på varje instans lyssnar efter utlösare och anropa funktioner. Olika utlösaren bindningar är utformade för att effektivt dela arbete kollektivt över instanser, kan så att skala ut till flera instanser du hantera mer belastning.

Kö- och blobbdata utlösare automatiskt förhindrar bearbetning av ett meddelande i kön av en funktion eller blob mer än en gång. funktioner har inte ska vara idempotent.

Timer-utlösaren säkerställer att endast en instans av timer-körs automatiskt så att du inte få mer än en instans av funktionen som körs vid en given tidpunkt för schemalagd.

Om du vill se till att endast en instans av en funktion körs även när det finns flera instanser av värden webbprogram, kan du använda den [Singleton](#singleton) attribut.
    
## <a name="filters"></a>Filter 

Funktionen filter (förhandsversion) ger dig ett sätt att anpassa WebJobs körningspipeline med din egen logik. Filter liknar [ASP.NET Core filter](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). De kan implementeras som deklarativ attribut som tillämpas på dina funktioner eller klasser. Mer information finns i [funktionen filter](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Vi rekommenderar att loggningsramverk som utvecklats för ASP.NET, och [Kom igång](webjobs-sdk-get-started.md) artikeln visar hur du använder den. 

### <a name="log-filtering"></a>Filtrering av logg

Varje logg som skapats av en `ILogger` instansen har en associerad `Category` och `Level`. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) är en uppräkning och koden heltal anger relativa betydelse:

|LogLevel    |Kod|
|------------|---|
|Spårning       | 0 |
|Felsökning       | 1 |
|Information | 2 |
|Varning     | 3 |
|Fel       | 4 |
|Kritiska    | 5 |
|Ingen        | 6 |

Varje kategori kan filtreras oberoende till en viss [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel). Du kanske exempelvis vill visa alla loggar för blob utlösaren bearbetning men endast `Error` och högre för alla andra.

Om du vill göra det lättare att ange filtreringsregler, WebJobs-SDK tillhandahåller den `LogCategoryFilter` som kan skickas till många av de befintliga loggning dataleverantörer, inklusive Application Insights och konsolen.

Den `LogCategoryFilter` har en `Default` egenskap med det första värdet `Information`, vilket innebär att alla meddelanden med `Information`, `Warning`, `Error`, eller `Critical` är inloggad, men alla meddelanden med `Debug` eller `Trace` filtreras bort.

Den `CategoryLevels` egenskapen kan du ange loggningsnivåer för specifika kategorier så att du kan finjustera loggning utdata. Om ingen matchning hittas inom de `CategoryLevels` ordlista filtret faller tillbaka till den `Default` värde när du bestämmer dig för att filtrera meddelandet.

I följande exempel skapas ett filter som standard filtrerar alla loggar vid den `Warning` nivå. Kategorier av `Function` eller `Host.Results` filtreras på den `Error` nivå. Den `LogCategoryFilter` jämför den aktuella kategorin för alla registrerade `CategoryLevels` och väljer den längsta matchningen. Detta innebär att den `Debug` nivå som registrerats för `Host.Triggers` matchar `Host.Triggers.Queue` eller `Host.Triggers.Blob`. På så sätt kan du styra bredare kategorier utan att behöva lägga till var och en.

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

Internt, den `TelemetryClient` skapas av Application Insights-providern för WebJobs-SDK använder den [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). När Application Insights-slutpunkten är inte tillgänglig eller begränsa inkommande förfrågningar, den här kanalen [sparar begäranden i webbappens file system och skickar dem senare](http://apmtips.com/blog/2015/09/03/more-telemetry-channels).

Den `TelemetryClient` skapas av en klass som implementerar `ITelemetryClientFactory`. Som standard är detta den [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Om du vill ändra någon del av Application Insights-pipeline kan du ange egna `ITelemetryClientFactory`, och värden använder klassen för att skapa en `TelemetryClient`. Till exempel den här koden åsidosätter den `DefaultTelemetryClientFactory` att ändra en egenskap hos den `ServerTelemetryChannel`:

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

Objektet SamplingPercentageEstimatorSettings konfigurerar [anpassningsbar provtagning](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Det innebär att i vissa fall stora volymer, App Insights skickar en valda delmängd av telemetridata till servern.

När du har skapat telemetri fabriken skickar du i till Application Insights loggning providern:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a>Nästa steg

Den här guiden tillhandahåller kodstycken som visar hur du hanterar vanliga scenarier för att arbeta med WebJobs SDK. Komplett exempel finns [azure-webjobs-sdk-exempel](https://github.com/Azure/azure-webjobs-sdk-samples).