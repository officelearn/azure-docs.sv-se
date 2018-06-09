---
title: Azure Functions C# för utvecklare
description: Förstå hur du utvecklar Azure Functions med C#.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: tdykstra
ms.openlocfilehash: 53eaef775647795acf3e8e6fcd127181414b1c0e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234503"
---
# <a name="azure-functions-c-developer-reference"></a>Azure Functions C# för utvecklare

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Den här artikeln ger en introduktion till utveckla Azure Functions med hjälp av C# i .NET-klassbibliotek.

Azure Functions stöder C# och C# skript programmeringsspråk. Om du letar efter information [med C# i Azure portal](functions-create-function-app-portal.md), se [C#-skript (.csx) för utvecklare](functions-reference-csharp.md).

Den här artikeln förutsätter att du redan har läst i följande artiklar:

* [Azure Functions-guide för utvecklare](functions-reference.md)
* [Azure Functions verktyg för Visual Studio 2017](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Funktioner för klassbiblioteksprojektet

I Visual Studio den **Azure Functions** projektmall skapar en C# klassbiblioteksprojektet som innehåller följande filer:

* [Host.JSON](functions-host-json.md) -lagrar konfigurationsinställningar som påverkar alla funktioner i projektet när du kör lokalt eller i Azure.
* [Local.Settings.JSON](functions-run-local.md#local-settings-file) -lagrar app-inställningar och anslutningssträngar som används när körs lokalt.

> [!IMPORTANT]
> Skapar skapar en *function.json* fil för varje funktion. Detta *function.json* filen är inte avsedd som ska redigeras direkt. Du kan inte ändra bindningskonfigurationen eller inaktivera funktionen genom att redigera den här filen. Inaktivera en funktion med det [inaktivera](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribut. Till exempel lägga till en boolesk app inställningen MY_TIMER_DISABLED och använda `[Disable("MY_TIMER_DISABLED")]` till funktionen. Du kan aktivera och inaktivera det genom att ändra inställningen för appen.

## <a name="methods-recognized-as-functions"></a>Metoder som funktioner

I klassbiblioteket, en funktion är en statisk metod med en `FunctionName` och attributet utlösare som visas i följande exempel:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

Den `FunctionName` attributet markerar metoden som en startpunkt för funktionen. Namnet måste vara unika inom ett projekt, börja med en bokstav och endast innehålla bokstäver, siffror, `_` och `-`, upp till 127 tecken. Projektmallar ofta skapa en metod med namnet `Run`, men metodnamnet kan vara en giltig C#-metodnamn.

Trigger-attribut anger typen av utlösare och bindningar indata till en metodparameter. Funktionen exempel utlöses av ett kömeddelande och kön meddelandet skickas till metoden i den `myQueueItem` parameter.

## <a name="method-signature-parameters"></a>Metodparametrarna för signatur

Metodsignaturen kan innehålla andra parametrar än den som används med attributet utlösare. Här följer några ytterligare parametrar som du kan inkludera:

* [Indata och utdata bindningar](functions-triggers-bindings.md) märkas som sådana av pynta dem med attribut.  
* En `ILogger` eller `TraceWriter` parameter för [loggning](#logging).
* En `CancellationToken` parameter för [korrekt avslutning](#cancellation-tokens).
* [Bindningsuttryck](functions-triggers-bindings.md#binding-expressions-and-patterns) parametrar för att visa utlösa metadata.

Ordningen för parametrar i funktionssignaturen spelar ingen roll. Exempelvis kan du placera utlösaren parametrar före eller efter andra bindningar och du kan ange parametern loggaren före eller efter utlösare eller bindande parametrar.

### <a name="output-binding-example"></a>Exempel på utdata-bindning

I följande exempel ändrar det föregående genom att lägga till en utgående kö-bindning. Funktionen skriver kömeddelandet som utlöser funktionen för att ett nytt meddelande i kön i en annan kö.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Bindningen referensartiklar ([lagringsköer](functions-bindings-storage-queue.md), till exempel) förklarar vilka parametertyper som du kan använda med utlösare, indata eller utdata binda attribut.

### <a name="binding-expressions-example"></a>Bindande uttryck exempel

Följande kod hämtar namnet på kön att övervaka en appinställning och hämtar tiden för skapandet av kön meddelandet i den `insertionTime` parameter.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        TraceWriter log)
    {
        log.Info($"Message content: {myQueueItem}");
        log.Info($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automatiskt genererade function.json

Skapar skapar en *function.json* fil i en function-mappen i mappen build. Som tidigare nämnts är den här filen inte avsedd som ska redigeras direkt. Du kan inte ändra bindningskonfigurationen eller inaktivera funktionen genom att redigera den här filen. 

Syftet med den här filen är att ge information till skala controller ska använda för [skalning beslut förbrukning planen](functions-scale.md#how-the-consumption-plan-works). Därför har filen bara utlösaren info, inte indata eller utdata bindningar.

Den genererade *function.json* filen innehåller en `configurationSource` egenskap som talar om körningen ska använda .NET-attribut för bindningar, i stället *function.json* konfiguration. Här är ett exempel:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

Den *function.json* Filgenerering utförs av NuGet-paketet [Microsoft\.NET\.Sdk\.funktioner](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Samma paket som används för både version 1.x och 2.x för Functions-runtime. Målversionen av framework är vad särskiljer ett 1.x-projekt från ett 2.x-projekt. Här följer de relevanta delarna av *.csproj* filer, med olika mål ramverk och samma `Sdk` paketet:

**Fungerar 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Fungerar 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netstandard2.0</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Bland de `Sdk` paketberoenden är utlösare och bindningar. En instruktion i 1.x-projektet refererar till 1.x utlösare och bindningar eftersom de mål .NET Framework, medan 2.x utlösare och bindningar mål .NET Core.

Den `Sdk` paketet beror också på [Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json), och indirekt på [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage). Dessa beroenden se till att projektet använder versioner av de paket som fungerar med versionen av körningsmiljön funktioner som mål för projektet. Till exempel `Newtonsoft.Json` har version 11 för .NET Framework 4.6.1, men Functions-runtime som riktar sig till .NET Framework 4.6.1 är endast kompatibel med `Newtonsoft.Json` 9.0.1. Så att Funktionskoden i projektet har även att använda `Newtonsoft.Json` 9.0.1.

Källkoden för `Microsoft.NET.Sdk.Functions` finns i GitHub-repo [azure\-funktioner\-vs\-skapa\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Körningsversion

Visual Studio använder den [Azure Functions grundläggande verktyg](functions-run-local.md#install-the-azure-functions-core-tools) att köra funktioner projekt. Grundläggande verktyg är ett kommandoradsgränssnitt för Functions-runtime.

Om du installerar verktygen Core via npm, inte som påverkar den grundläggande verktyg-version som används av Visual Studio. För versionen av körningsmiljön funktioner 1.x, Visual Studio lagrar grundläggande verktyg versioner i *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* och använder den senaste versionen lagras det. För funktioner 2.x, grundläggande verktyg ingår i den **Azure Functions och jobb Webbverktyg** tillägg. Du kan se vilken version som används i konsolens utdata när du kör ett projekt med funktioner för både 1.x och 2.x:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Typer som stöds för bindningar

Varje bindning har sin egen typer som stöds. till exempel ett attribut för blob-utlösare kan tillämpas på en strängparameter, en POCO-parameter en `CloudBlockBlob` parametern eller någon av flera andra typer som stöds. Den [bindning referensartikeln om principer för blob-bindningar](functions-bindings-storage-blob.md#trigger---usage) visar alla stöds parametertyper. Mer information finns i [utlösare och bindningar](functions-triggers-bindings.md) och [bindning referens docs för varje bindning](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Bindning till metoden returvärde

Du kan använda ett returvärde för metoden för en output-bindning genom att använda attributet returvärdet för metoden. Exempel finns i [utlösare och bindningar](functions-triggers-bindings.md#using-the-function-return-value).

## <a name="writing-multiple-output-values"></a>Skrivning av flera utdatavärden

Om du vill skriva flera värden till en bindning för utdata, Använd den [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) eller [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typer. Dessa typer är lässkyddad samlingar som har skrivits till utdata bindningen när metoden har slutförts.

Det här exemplet skriver flera Kömeddelanden till samma kö med `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 1: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Loggning

Om du vill logga utdata till din direktuppspelningsloggar i C#, innehålla ett argument av typen `TraceWriter`. Vi rekommenderar att du namnger den `log`. Undvik att använda `Console.Write` i Azure Functions. 

`TraceWriter` har definierats i den [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Loggningsnivån för `TraceWriter` kan konfigureras i [host.json](functions-host-json.md).

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

> [!NOTE]
> Information om en nyare loggningsramverk som du kan använda i stället för `TraceWriter`, se [skrivåtgärder loggar i C#-funktioner](functions-monitoring.md#write-logs-in-c-functions) i den **övervakaren Azure Functions** artikel.

## <a name="async"></a>Asynkrona

Att göra en funktion [asynkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), använda den `async` nyckelord och återställa en `Task` objekt.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        TraceWriter log)
    {
        log.Info($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="cancellation-tokens"></a>Annullering token

En funktion kan acceptera en [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) parametern som gör operativsystemet för att meddela din kod när funktionen håller på att avslutas. Du kan använda det här meddelandet för att kontrollera att funktionen inte avslutas oväntat på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du kontrollerar att funktionen avslutning.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
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
}
```

## <a name="environment-variables"></a>Miljövariabler

För att få en miljövariabel eller en app som inställningsvärde kan använda `System.Environment.GetEnvironmentVariable`som visas i följande kodexempel:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
    {
        log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
        log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Den [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/en-us/dotnet/api/system.configuration.configurationmanager.appsettings) egenskapen är en alternativ API för att hämta appen inställningsvärden, men vi rekommenderar att du använder `GetEnvironmentVariable` som visas här.

## <a name="binding-at-runtime"></a>Bindning under körning

I C# och andra .NET-språk, kan du använda en [tvingande](https://en.wikipedia.org/wiki/Imperative_programming) bindning mönster, inte den [ *deklarativa* ](https://en.wikipedia.org/wiki/Declarative_programming) bindningar i attribut. Tvingande bindning är användbar när bindande parametrar måste beräknas vid körning i stället för design tidpunkt. Med det här mönstret binder du till stöds indata och utdata bindningar på avbrott i funktionskoden.

Definiera en tvingande bindning på följande sätt:

- **Inte** innehåller ett attribut i en funktionssignaturen för din önskade tvingande bindningar.
- Använd en indataparameter [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) eller [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Använd följande C#-mönster för att utföra databindningen.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` är .NET-attribut som definierar en bindning och `T` är en typ av inkommande eller utgående som stöds av denna bindning. `T` får inte vara en `out` parametertypen (exempelvis `out JObject`). Till exempel tabellen Mobile Apps spara bindningen stöder [sex utdata typer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), men du kan bara använda [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) eller [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)med tvingande bindning.

### <a name="single-attribute-example"></a>Attribut-exempel

Följande exempelkod skapar en [lagringsblob utdatabindning](functions-bindings-storage-blob.md#output) med blob sökväg som har definierats vid körning, sedan skriver en sträng till blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        TraceWriter log)
    {
        log.Info($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definierar den [lagringsblob](functions-bindings-storage-blob.md) indata och utdata bindning, och [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) är av bindningstypen stöds utdata.

### <a name="multiple-attribute-example"></a>Flera attribut-exempel

Föregående exempel hämtar appinställningen för appen funktionen main konto anslutningssträngen för lagring (vilket är `AzureWebJobsStorage`). Du kan ange en anpassad appinställning som ska användas för lagringskontot genom att lägga till den [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) och passerar attributet matrisen i `BindAsync<T>()`. Använd en `Binder` parametern inte `IBinder`.  Exempel:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            TraceWriter log)
    {
        log.Info($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Utlösare och bindningar 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mer information om utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Läs mer om metodtips för Azure Functions](functions-best-practices.md)
