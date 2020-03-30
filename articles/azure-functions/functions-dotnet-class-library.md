---
title: Azure Functions C#-utvecklarreferens
description: Förstå hur du utvecklar Azure-funktioner med C#.
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: cfa53fe2defca768196af595c1d088d41bc60f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277068"
---
# <a name="azure-functions-c-developer-reference"></a>Azure Functions C#-utvecklarreferens

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Den här artikeln är en introduktion till att utveckla Azure-funktioner med hjälp av C# i .NET-klassbibliotek.

Azure Functions stöder programmeringsspråk för C# och C#. Om du letar efter vägledning om [hur du använder C# i Azure-portalen](functions-create-function-app-portal.md)läser du [C#script (.csx) utvecklarreferens](functions-reference-csharp.md).

Den här artikeln förutsätter att du redan har läst följande artiklar:

* [Guide för Azure Functions-utvecklare](functions-reference.md)
* [Azure Functions Visual Studio 2019-verktyg](functions-develop-vs.md)

## <a name="supported-versions"></a>Versioner som stöds

Versioner av funktionskörningen fungerar med specifika versioner av .NET. I följande tabell visas den högsta nivån av .NET Core och .NET Framework och .NET Core som kan användas med en viss version av Funktioner i projektet. 

| Version av funktioner i körning | Max .NET-version |
| ---- | ---- |
| Funktioner 3.x | .NET-kärna 3.1 |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET Framework 4.6 |

Mer information finns i [översikt över Azure Functions runtime-versioner](functions-versions.md)

## <a name="functions-class-library-project"></a>Biblioteksprojekt för funktioner

I Visual Studio skapar **Projektmallen Azure Functions** ett C#-klassbiblioteksprojekt som innehåller följande filer:

* [host.json](functions-host-json.md) - lagrar konfigurationsinställningar som påverkar alla funktioner i projektet när du kör lokalt eller i Azure.
* [local.settings.json](functions-run-local.md#local-settings-file) - lagrar appinställningar och anslutningssträngar som används när du kör lokalt. Den här filen innehåller hemligheter och publiceras inte i din funktionsapp i Azure. Lägg i stället [till appinställningar i funktionsappen](functions-develop-vs.md#function-app-settings).

När du skapar projektet genereras en mappstruktur som ser ut som följande exempel i build-utdatakatalogen:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Den här katalogen är det som distribueras till din funktionsapp i Azure. De bindningstillägg som krävs i [version 2.x](functions-versions.md) av funktionskörningen [läggs till i projektet som NuGet-paket](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Byggprocessen skapar en *function.json-fil* för varje funktion. Den här *filen function.json* är inte avsedd att redigeras direkt. Du kan inte ändra bindningskonfigurationen eller inaktivera funktionen genom att redigera den här filen. Mer information om hur du inaktiverar en funktion finns i [Inaktivera funktioner](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Metoder som känns igen som funktioner

I ett klassbibliotek är en funktion `FunctionName` en statisk metod med ett och ett utlösarattribut, som visas i följande exempel:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Attributet `FunctionName` markerar metoden som en funktionsstartpunkt. Namnet måste vara unikt i ett projekt, börja med en `_`bokstav `-`och innehåller bara bokstäver, siffror och upp till 127 tecken i längd. Projektmallar skapar ofta `Run`en metod med namnet , men metodnamnet kan vara valfritt giltigt C#-metodnamn.

Utlösarattributet anger utlösartypen och binder indata till en metodparameter. Exempelfunktionen utlöses av ett kömeddelande och kömeddelandet skickas till `myQueueItem` metoden i parametern.

## <a name="method-signature-parameters"></a>Parametrar för metodsignatur

Metodsignaturen kan innehålla andra parametrar än den som används med utlösarattributet. Här är några av de ytterligare parametrar som du kan inkludera:

* [Indata- och utdatabindningar](functions-triggers-bindings.md) markerade som sådana genom att dekorera dem med attribut.  
* En `ILogger` `TraceWriter` eller[(version 1.x-only)](functions-versions.md#creating-1x-apps)för [loggning](#logging).
* En `CancellationToken` parameter för [graciös avstängning](#cancellation-tokens).
* [Parametrar för bindningsuttryck](./functions-bindings-expressions-patterns.md) för att hämta utlösa metadata.

Ordningen på parametrarna i funktionssignaturen spelar ingen roll. Du kan till exempel placera utlösarparametrar före eller efter andra bindningar och du kan placera loggerparametern före eller efter utlösar- eller bindningsparametrar.

### <a name="output-binding-example"></a>Exempel på utdatabindning

I följande exempel ändras det föregående genom att lägga till en utdataköbindning. Funktionen skriver kömeddelandet som utlöser funktionen till ett nytt kömeddelande i en annan kö.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Bindningsreferensartiklarna ([lagringsköer,](functions-bindings-storage-queue.md)till exempel) förklarar vilka parametertyper du kan använda med utlösar-, indata- eller utdatabindningsattribut.

### <a name="binding-expressions-example"></a>Exempel på bindningsuttryck

Följande kod får namnet på kön för att övervaka från en appinställning, `insertionTime` och den får kömeddelandeskapande tid i parametern.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Autogenererad funktion.json

Byggprocessen skapar en *function.json-fil* i en funktionsmapp i byggmappen. Som nämnts tidigare är den här filen inte avsedd att redigeras direkt. Du kan inte ändra bindningskonfigurationen eller inaktivera funktionen genom att redigera den här filen. 

Syftet med den här filen är att tillhandahålla information till skalningsstyrenheten som ska användas för [att skala beslut om förbrukningsplanen](functions-scale.md#how-the-consumption-and-premium-plans-work). Därför har filen bara utlösarinfo, inte indata- eller utdatabindningar.

Den genererade *function.json-filen* innehåller en `configurationSource` egenskap som talar om för körningen att använda .NET-attribut för bindningar i stället för *function.json-konfiguration.* Här är ett exempel:

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

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Funktioner

File generation *function.json* utförs av NuGet-paketet [Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Samma paket används för både version 1.x och 2.x av funktionskörningen. Målramen är det som skiljer ett 1.x-projekt från ett 2.x-projekt. Här är de relevanta delarna av *CSPROJ-filer* som `Sdk` visar olika målramverk och samma paket:

**Functions 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Bland `Sdk` paketberoendena finns utlösare och bindningar. Ett 1.x-projekt refererar till 1.x-utlösare och bindningar eftersom dessa utlösare och bindningar är inriktade på .NET Framework, medan 2.x-utlösare och bindningar är inriktade på .NET Core.

Paketet `Sdk` beror också på [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json), och indirekt på [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Dessa beroenden ser till att projektet använder de versioner av paketen som fungerar med den funktionskörningsversion som projektet riktar sig till. Har till `Newtonsoft.Json` exempel version 11 för .NET Framework 4.6.1, men körningen Funktioner som är inriktad på `Newtonsoft.Json` .NET Framework 4.6.1 är endast kompatibel med 9.0.1. Så din funktionskod i det `Newtonsoft.Json` projektet måste också använda 9.0.1.

Källkoden för `Microsoft.NET.Sdk.Functions` är tillgänglig i GitHub repo [azure-funktioner\-\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Runtime-version

Visual Studio använder [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) för att köra functions-projekt. Core Tools är ett kommandoradsgränssnitt för funktionskörningen.

Om du installerar Core Tools med npm påverkar det inte core tools-versionen som används av Visual Studio. För 1.x-versionen För Funktioner lagrar Visual Studio Core Tools-versioner i *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* och använder den senaste versionen som lagras där. För funktioner 2.x ingår core-verktygen i tillägget **Azure Functions och Web Jobs Tools.** För både 1.x och 2.x kan du se vilken version som används i konsolens utdata när du kör ett Functions-projekt:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Typer som stöds för bindningar

Varje bindning har sina egna typer som stöds. Till exempel kan ett blob-utlösarattribut tillämpas på en `CloudBlockBlob` strängparameter, en POCO-parameter, en parameter eller någon av flera andra typer som stöds. [Bindningsreferensartikeln för blob-bindningar listar](functions-bindings-storage-blob-trigger.md#usage) alla parametertyper som stöds. Mer information finns i [Utlösare och bindningar](functions-triggers-bindings.md) och [bindningsreferensdokument för varje bindningstyp](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Bindning till metodreturvärde

Du kan använda ett metodreturvärde för en utdatabindning genom att använda attributet på metodens returvärde. Exempel finns i [Utlösare och bindningar](./functions-bindings-return-value.md). 

Använd returvärdet endast om ett lyckat funktionskörning alltid resulterar i ett returvärde för att gå vidare till utdatabindningen. Annars använder `ICollector` `IAsyncCollector`eller , som visas i följande avsnitt.

## <a name="writing-multiple-output-values"></a>Skriva flera utdatavärden

Om du vill skriva flera värden till en utdatabindning, eller om en lyckad [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) funktionsyrkcation kanske inte resulterar i något som går över till utdatabindningen, använder du eller typerna. Dessa typer är skrivskyddade samlingar som skrivs till utdatabindningen när metoden är klar.

I det här exemplet skrivs flera `ICollector`kömeddelanden till samma kö med:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Loggning

Om du vill logga utdata till dina streamingloggar i C#, inkluderar du ett argument av typen [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Vi rekommenderar att `log`du namnger den, som i följande exempel:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Undvik `Console.Write` att använda i Azure Functions. Mer information finns [i Skriv loggar i C#-funktioner](functions-monitoring.md#write-logs-in-c-functions) i artikeln Övervaka Azure **Functions.**

## <a name="async"></a>Asynkrona

Om du vill göra en funktion `async` [asynkron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)använder du nyckelordet och returnerar ett `Task` objekt.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Du kan inte `out` använda parametrar i asynkronfunktioner. För utdatabindningar använder du [funktionens returvärde](#binding-to-method-return-value) eller ett [insamlarobjekt](#writing-multiple-output-values) i stället.

## <a name="cancellation-tokens"></a>Token för annullering

En funktion kan acceptera en [CancellationToken-parameter,](/dotnet/api/system.threading.cancellationtoken) som gör det möjligt för operativsystemet att meddela din kod när funktionen är på väg att avslutas. Du kan använda det här meddelandet för att se till att funktionen inte avslutas oväntat på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du söker efter förestående funktionsuppslutning.

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

Om du vill hämta en miljövariabel eller ett appinställningsvärde använder du `System.Environment.GetEnvironmentVariable`, som visas i följande kodexempel:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
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
}
```

Appinställningar kan läsas från miljövariabler både när du utvecklar lokalt och när du kör i Azure. När du utvecklar lokalt kommer `Values` appinställningarna från samlingen i filen *local.settings.json.* I båda miljöerna, lokala `GetEnvironmentVariable("<app setting name>")` och Azure, hämtar värdet för den namngivna appinställningen. När du till exempel kör lokalt returneras "Mitt webbplatsnamn" om filen *local.settings.json* innehåller `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

[Egenskapen System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) är ett alternativt API för att hämta `GetEnvironmentVariable` appinställningsvärden, men vi rekommenderar att du använder som visas här.

## <a name="binding-at-runtime"></a>Bindning vid körning

I C# och andra .NET-språk kan du använda ett [tvingande](https://en.wikipedia.org/wiki/Imperative_programming) bindningsmönster, i motsats till [*deklarativa*](https://en.wikipedia.org/wiki/Declarative_programming) bindningarna i attribut. Tvingande bindning är användbart när bindningsparametrar måste beräknas vid körning i stället för designtid. Med det här mönstret kan du binda till ingångs- och utdatabindningar som stöds i funktionskoden.

Definiera en tvingande bindning enligt följande:

- **Ta inte** med ett attribut i funktionssignaturen för önskade tvingande bindningar.
- Skicka in en [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)indataparameter eller .
- Använd följande C#-mönster för att utföra databindningen.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`är attributet .NET som definierar `T` bindningen och är en indata- eller utdatatyp som stöds av den bindningstypen. `T`kan inte `out` vara en `out JObject`parametertyp (till exempel ). Tabellutdatabindningen för mobilappar stöder till exempel [sex utdatatyper,](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)men du kan bara använda [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) eller [IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) med tvingande bindning.

### <a name="single-attribute-example"></a>Exempel på ett enda attribut

I följande exempelkod skapas en [Storage blob-utdatabindning](functions-bindings-storage-blob-output.md) med blob-sökväg som har definierats vid körning och sedan en sträng till bloben.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definierar [storage blob-indata](functions-bindings-storage-blob.md) eller utdatabindning och [TextWriter](/dotnet/api/system.io.textwriter) är en utdatabindningstyp som stöds.

### <a name="multiple-attribute-example"></a>Exempel på flera attribut

Det föregående exemplet hämtar appinställningen för funktionsappens huvudsakliga `AzureWebJobsStorage`anslutningssträng för lagringskonto (som är ). Du kan ange en anpassad appinställning som ska användas för lagringskontot genom `BindAsync<T>()`att lägga till [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) och skicka attributmatrisen till . Använd `Binder` en parameter, inte `IBinder`.  Ett exempel:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
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
> [Läs mer om utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Läs mer om metodtips för Azure Functions](functions-best-practices.md)
