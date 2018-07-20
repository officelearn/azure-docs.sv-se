---
title: Azure Functions C#-utvecklarreferens
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
ms.openlocfilehash: 542e3563a31a181db8eb7c97a90b61e1a503f2cb
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162209"
---
# <a name="azure-functions-c-developer-reference"></a>Azure Functions C#-utvecklarreferens

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Den här artikeln är en introduktion till utvecklar Azure Functions med hjälp av C# i .NET-klassbibliotek.

Azure Functions har stöd för C# och C#-skript som programmeringsspråk. Om du letar efter information [med C# i Azure-portalen](functions-create-function-app-portal.md), se [utvecklarreferens för C#-skript (.csx)](functions-reference-csharp.md).

Den här artikeln förutsätter att du redan har läst i följande artiklar:

* [Azure Functions-guide för utvecklare](functions-reference.md)
* [Visual Studio 2017-verktyg för Azure Functions](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Functions klassbiblioteksprojektet

I Visual Studio i **Azure Functions** projektmall skapar en C# klassbiblioteksprojektet som innehåller följande filer:

* [Host.JSON](functions-host-json.md) -lagrar konfigurationsinställningar som påverkar alla funktioner i projektet när du kör lokalt eller i Azure.
* [Local.Settings.JSON](functions-run-local.md#local-settings-file) -lagrar appinställningar och anslutningssträngar som används när du kör lokalt.

> [!IMPORTANT]
> Skapandeprocessen skapar en *function.json* fil för varje funktion. Detta *function.json* filen är inte avsedd att redigeras direkt. Du kan inte ändra bindningskonfigurationen eller inaktivera funktionen genom att redigera den här filen. Om du vill inaktivera en funktion, använda den [inaktivera](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attribut. Till exempel lägga till en boolesk MY_TIMER_DISABLED för appinställningen och tillämpa `[Disable("MY_TIMER_DISABLED")]` till din funktion. Du kan aktivera och inaktivera den genom att ändra appinställningen.

## <a name="methods-recognized-as-functions"></a>Metoder som utsetts till funktioner

I klassbiblioteket, en funktion är en statisk metod med en `FunctionName` och ett utlösarattribut, som visas i följande exempel:

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

Den `FunctionName` attributet markerar metoden som en startpunkt för funktionen. Namnet måste vara unika inom ett projekt, börja med en bokstav och endast innehålla bokstäver, siffror, `_` och `-`, högst 127 tecken. Projektmallar ofta skapa en metod med namnet `Run`, men metodnamnet kan vara ett giltigt C#-metodnamn.

Attributet utlösaren anger typen av utlösare och Binder indata till en metodparameter. Exempel-funktion som utlöses av ett kömeddelande och kömeddelandet skickas till-metoden i det `myQueueItem` parametern.

## <a name="method-signature-parameters"></a>Metodparametrar signatur

Metodsignaturen kan innehålla parametrar än den som används med attributet utlösaren. Här är några av de ytterligare parametrar som du kan inkludera:

* [Indata och utdata bindningar](functions-triggers-bindings.md) markerad som sådan genom att dekorera dem med attribut.  
* En `ILogger` eller `TraceWriter` parametern för [loggning](#logging).
* En `CancellationToken` parametern för [avslutning](#cancellation-tokens).
* [Bindning uttryck](functions-triggers-bindings.md#binding-expressions-and-patterns) parametrar för att visa utlösa metadata.

Ordningen på parametrar i funktionssignaturen spelar ingen roll. Exempel: du kan ange parametrar för objektutlösare före eller efter andra bindningar och du kan ange parametern loggaren före eller efter lagringsutlösare eller bindning för parametrar.

### <a name="output-binding-example"></a>Exempel på utdata-bindning

I följande exempel ändrar det föregående genom att lägga till en utdatabindning för kön. Funktionen skriver kömeddelandet som utlöser funktionen till ett nytt kömeddelande i en annan kö.

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

Referensartiklar bindning ([lagringsköer](functions-bindings-storage-queue.md), till exempel) förklarar vilka parametertyper som du kan använda med utlösare, indata eller utdata binda attribut.

### <a name="binding-expressions-example"></a>Bindningen uttryck exempel

Följande kod hämtar namnet på kön som ska övervaka från en appinställning och hämtar Kötid meddelande skapas i den `insertionTime` parametern.

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

## <a name="autogenerated-functionjson"></a>Automatiskt skapade function.json

Skapandeprocessen skapar en *function.json* fil i en mapp för funktionen i build-mappen. Som tidigare nämnts är den här filen inte avsedd att redigeras direkt. Du kan inte ändra bindningskonfigurationen eller inaktivera funktionen genom att redigera den här filen. 

Syftet med den här filen är att ge information för att skala kontrollanten ska användas för [skalning beslut på förbrukningsplanen](functions-scale.md#how-the-consumption-plan-works). Därför har filen bara utlösaren info, inte indata eller utdatabindningar.

Den genererade *function.json* -filen innehåller en `configurationSource` egenskap som meddelar körning för att använda .NET-attribut för bindningar, snarare än *function.json* konfiguration. Här är ett exempel:

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

Den *function.json* filen utförs av NuGet-paketet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Samma paket som ska användas för både version 1.x och 2.x av Functions-körning. Målramverk är vad särskiljer ett 1.x-projekt från ett 2.x-projekt. Här följer de relevanta delarna av *.csproj* filer, som visar olika mål ramverk och samma `Sdk` paketet:

**1.x-funktioner**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**2.x-funktioner**

```xml
<PropertyGroup>
  <TargetFramework>netstandard2.0</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Bland de `Sdk` paketberoenden är utlösare och bindningar. Ett 1.x-projekt som refererar till 1.x-utlösare och bindningar eftersom de mål .NET Framework, medan 2.x utlösare och bindningar rikta .NET Core.

Den `Sdk` paketet beror också på [Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json), indirekt på [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage). Dessa beroenden se till att projektet använder versioner av de paket som fungerar med Functions runtime-versionen som mål för projektet. Till exempel `Newtonsoft.Json` har version 11 för .NET Framework 4.6.1, men Functions-körning som riktas mot .NET Framework 4.6.1 är endast kompatibel med `Newtonsoft.Json` 9.0.1. Så att Funktionskoden i projektet har också att använda `Newtonsoft.Json` 9.0.1.

Källkoden för `Microsoft.NET.Sdk.Functions` finns i GitHub-lagringsplatsen [azure\-functions\-vs\-skapa\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Körningsversion

Visual Studio använder den [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) att köra Functions-projekt. De viktigaste verktygen är ett kommandoradsgränssnitt för att funktionskörningen.

Om du installerar de viktigaste verktygen med hjälp av npm påverkar som inte den Core Tools version som används av Visual Studio. För Functions runtime version 1.x, Visual Studio lagrar Core Tools-versioner i *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* och använder den senaste versionen lagras det. För Functions 2.x, de viktigaste verktygen som ingår i den **Azure Functions och Webjobs-verktyg** tillägget. Du kan se vilken version som används i konsolens utdata när du kör en Functions-projekt för både 1.x och 2.x:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Typer som stöds för bindningar

Varje bindning har sin egen typer som stöds. till exempel ett attribut för blob-utlösare kan tillämpas på en strängparameter, en POCO-parameter, en `CloudBlockBlob` parametern eller någon av flera andra typer som stöds. Den [bindning referensartikeln för blobbindningar](functions-bindings-storage-blob.md#trigger---usage) visar alla stöds parametertyper. Mer information finns i [utlösare och bindningar](functions-triggers-bindings.md) och [bindning referensdokument för varje bindningstyp](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Bindning till returvärdet för metoden

Du kan använda metoden returvärdet för en utdatabindning genom att använda attributet returvärdet för metoden. Exempel finns i [utlösare och bindningar](functions-triggers-bindings.md#using-the-function-return-value). 

Använd det returnera värdet bara om lyckad körning av en funktion alltid resulterar i ett returvärde ska skickas till utdata-bindning. Annars kan du använda `ICollector` eller `IAsyncCollector`, vilket visas i följande avsnitt.

## <a name="writing-multiple-output-values"></a>Skriva flera utdatavärden

Skriva flera värden till en utdatabindning eller om en lyckad funktionsanrop inte kan resultera i att skicka till utdata-bindning kan du använda den [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) eller [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typer. Dessa typer är lässkyddad samlingar som har skrivits till utdata-bindning när metoden har slutförts.

Det här exemplet skriver flera Kömeddelanden i samma kö med `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Loggning

Om du vill logga utdata till din direktuppspelningsloggarna i C#, innehålla ett argument av typen `TraceWriter`. Vi rekommenderar att du namnger den `log`. Undvik att använda `Console.Write` i Azure Functions. 

`TraceWriter` har definierats i den [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Loggningsnivå för `TraceWriter` kan konfigureras i [host.json](functions-host-json.md).

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
> Information om ett nyare loggningsramverk som du kan använda i stället för `TraceWriter`, se [skriva loggar i C#-funktioner](functions-monitoring.md#write-logs-in-c-functions) i den **övervaka Azure Functions** artikeln.

## <a name="async"></a>Async

Att göra en funktion [asynkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), använda den `async` nyckelord och returnera en `Task` objekt.

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

Du kan inte använda `out` parametrar i async-funktioner. Utdatabindningar, använda den [fungera returvärdet](#binding-to-method-return-value) eller en [insamlingsobjektet](#writing-multiple-output-values) i stället.

## <a name="cancellation-tokens"></a>Annulleringstoken

En funktion kan acceptera en [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) parametern, som gör det möjligt för operativsystemet för att meddela din kod när funktionen ska avslutas. Du kan använda det här meddelandet för att kontrollera att funktionen inte avslutas oväntat på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du kontrollerar om nära förestående uppsägning av funktionen.

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

Hämta en miljövariabel eller en app som inställningsvärde `System.Environment.GetEnvironmentVariable`, vilket visas i följande kodexempel:

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

Appinställningar kan läsas från miljövariabler, både när du utvecklar lokalt och när du kör i Azure. När du utvecklar lokalt appinställningar kommer från den `Values` samling i den *local.settings.json* fil. I båda miljöerna lokala och Azure, `GetEnvironmentVariable("<app setting name>")` hämtar värdet för den namngivna app-inställningen. Till exempel när du kör lokalt, ”Mina Site-Name” skulle returneras om din *local.settings.json* filen innehåller `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

Den [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) egenskapen är en alternativ API för att få appen inställningsvärden, men vi rekommenderar att du använder `GetEnvironmentVariable` som visas här.

## <a name="binding-at-runtime"></a>Bindningen vid körning

I C# och andra .NET-språk, kan du använda en [tvingande](https://en.wikipedia.org/wiki/Imperative_programming) bindning mönster, inte den [ *deklarativa* ](https://en.wikipedia.org/wiki/Declarative_programming) bindningar i attribut. Tvingande bindning är användbar när bindande parametrar behöver beräknas vid körning i stället för design tidpunkt. Med det här mönstret kan du binda till stöds indata och utdata bindningar på direkt i funktionskoden.

Definiera konkurrensavseende bindning på följande sätt:

- **Inte** inkludera ett attribut i funktionssignaturen för din önskade tvingande bindningar.
- Pass i en indataparameter [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) eller [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Använd följande C#-mönster för att utföra databindningen.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` är .NET-attribut som definierar din bindningen och `T` är en inkommande eller utgående typ som stöds av den bindningstypen. `T` får inte vara en `out` parametertypen (till exempel `out JObject`). Till exempel Mobile Apps-tabell utdata har stöd för bindning [sex utdata typer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), men du kan bara använda [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) eller [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)med tvingande bindning.

### <a name="single-attribute-example"></a>Attribut-exempel

Följande exempelkod skapar en [blob Storage-utdatabindning](functions-bindings-storage-blob.md#output) med blob sökväg som anges vid körningstillfället, sedan skriver en sträng till bloben.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definierar den [lagringsblob](functions-bindings-storage-blob.md) indata eller utdata bindning, och [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) är av typen stöds utdata-bindning.

### <a name="multiple-attribute-example"></a>Flera attribut-exempel

I föregående exempel hämtar appinställningen för function-appens main anslutningssträng för Lagringskonto (vilket är `AzureWebJobsStorage`). Du kan ange en anpassad app-inställning som ska användas för lagringskontot genom att lägga till den [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) och för att skicka attributet matrisen i `BindAsync<T>()`. Använd en `Binder` parametern inte `IBinder`.  Exempel:

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
> [Läs mer om bästa praxis för Azure Functions](functions-best-practices.md)
