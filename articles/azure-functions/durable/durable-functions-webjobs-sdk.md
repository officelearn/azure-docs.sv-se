---
title: Så här kör du varaktiga funktioner som WebJobs - Azure
description: Lär dig hur du kodar och konfigurerar varaktiga funktioner så att de körs i WebJobs med hjälp av WebJobs SDK.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232741"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Så här kör du varaktiga funktioner som WebJobs

Som standard använder varaktiga funktioner körningen Azure Functions som värd för orkestreringar. Det kan dock finnas vissa scenarier där du behöver mer kontroll över koden som lyssnar efter händelser. I den här artikeln visas hur du implementerar din orkestrering med WebJobs SDK. Mer detaljerad jämförelse mellan Funktioner och WebJobs finns i [Jämför funktioner och WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) och tillägget [Varaktiga funktioner](durable-functions-overview.md) finns på [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). Jobbvärden i WebJobs SDK är körningen i Azure Functions. Om du behöver styra beteendet på ett sätt som inte är möjligt i Azure Functions kan du utveckla och köra varaktiga funktioner med hjälp av WebJobs SDK själv.

I version 3.x av WebJobs SDK är `IHost`värden en implementering av och i `JobHost` version 2.x använder du objektet.

Exemplet med varaktiga funktioner i kedjan finns i en WebJobs SDK 2.x-version: hämta eller klona [arkivet Varaktiga funktioner](https://github.com/azure/azure-functions-durable-extension/)och gå till *\\exempelmappen webjobssdk.\\*

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du är bekant med grunderna i WebJobs SDK, C# klassbiblioteksutveckling för Azure-funktioner och varaktiga funktioner. Om du behöver en introduktion till följande avsnitt läser du följande resurser:

* [Komma igång med WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Skapa din första funktion med Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Bestående funktioner](durable-functions-sequence.md)

Så här utför du stegen i den här artikeln:

* [Installera Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) med **Azure-utvecklingsarbetsbelastningen.**

  Om du redan har Visual Studio, men inte har den arbetsbelastningen, lägger du till arbetsbelastningen genom att välja **Verktyg** > **hämta verktyg och funktioner**.

  (Du kan använda [Visual Studio-kod](https://code.visualstudio.com/) i stället, men vissa instruktioner är specifika för Visual Studio.)

* Installera och kör [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) version 5.2 eller senare. Ett alternativ är att uppdatera *filen App.config* med en Azure Storage-anslutningssträng.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-versioner

I den här artikeln beskrivs hur du utvecklar ett WebJobs SDK 2.x-projekt (motsvarande Azure Functions version 1.x). Mer information om version 3.x finns i [WebJobs SDK 3.x](#webjobs-sdk-3x) senare i den här artikeln.

## <a name="create-a-console-app"></a>Skapa en konsolapp

Om du vill köra varaktiga funktioner som WebJobs måste du först skapa en konsolapp. Ett WebJobs SDK-projekt är bara ett konsolappprojekt med lämpliga NuGet-paket installerade.

I dialogrutan Nytt **projekt i** Visual Studio väljer du Windows **Classic Desktop** > **Console App (.NET Framework).** I projektfilen ska `TargetFrameworkVersion` den `v4.6.1`vara .

Visual Studio har också en WebJob-projektmall som du kan använda genom att välja **Cloud** > **Azure WebJob (.NET Framework).** Den här mallen installerar många paket, varav vissa kanske inte behöver.

## <a name="install-nuget-packages"></a>Installera NuGet-paket

Du behöver NuGet-paket för WebJobs SDK, kärnbindningar, loggningsramverket och tillägget Varaktig aktivitet. Här är **Package Manager Console** kommandon för dessa paket, med de senaste stabila versionsnummer från och med det datum den här artikeln skrevs:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Du behöver också loggningsleverantörer. Följande kommandon installerar Azure Application Insights-providern `ConfigurationManager`och . Med `ConfigurationManager` den kan du hämta instrumenteringsnyckeln Application Insights från appinställningarna.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Följande kommando installerar konsolleverantören:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost-kod

Efter att ha skapat konsolappen och installerat NuGet-paketen du behöver är du redo att använda varaktiga funktioner. Du gör det med jobhost-kod.

Om du vill använda `UseDurableTask` tillägget `JobHostConfiguration` Varaktiga `Main` funktioner anropar du objektet i metoden:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

En lista över egenskaper som du `DurableTaskExtension` kan ange i objektet finns i [host.json](../functions-host-json.md#durabletask).

Metoden `Main` är också platsen för att ställa in loggningsleverantörer. I följande exempel konfigureras konsol- och Application Insights-leverantörerna.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Funktioner

Varaktiga funktioner i samband med WebJobs skiljer sig något från varaktiga funktioner i samband med Azure-funktioner. Det är viktigt att vara medveten om skillnaderna när du skriver din kod.

WebJobs SDK stöder inte följande Azure Functions-funktioner:

* [Attributet FunctionName](#functionname-attribute)
* [HTTP-utlösare](#http-trigger)
* [HTTP-hanterings-API för varaktiga funktioner](#http-management-api)

### <a name="functionname-attribute"></a>Attributet FunctionName

I ett WebJobs SDK-projekt är metodnamnet för en funktion funktionsnamnet. Attributet `FunctionName` används endast i Azure Functions.

### <a name="http-trigger"></a>HTTP-utlösare

WebJobs SDK har ingen HTTP-utlösare. Exempelprojektets orkestreringsklient använder en timerutlösare:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API för HTTP-hantering

Eftersom webjobs SDK inte har någon HTTP-utlösare har den inget [HTTP-hanterings-API.](durable-functions-http-api.md)

I ett WebJobs SDK-projekt kan du anropa metoder för orchestration-klientobjektet i stället för genom att skicka HTTP-begäranden. Följande metoder motsvarar de tre uppgifter du kan utföra med HTTP-hanterings-API:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Orchestration-klientfunktionen i exempelprojektet startar orchestrator-funktionen och går sedan `GetStatusAsync` in i en loop som anropar varannan sekund:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Kör exemplet

Du har konfigurerat varaktiga funktioner för att köras som ett WebJob- och du har nu en förståelse för hur detta skiljer sig från att köra varaktiga funktioner som fristående Azure-funktioner. Nu kan det vara till hjälp att se det fungera i ett exempel.

Det här avsnittet innehåller en översikt över hur du kör [exempelprojektet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Detaljerade instruktioner som förklarar hur du kör ett WebJobs SDK-projekt lokalt och distribuerar det till ett Azure WebJob finns [i Komma igång med WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Lokal körning

1. Kontrollera att lagringsemulatorn körs (se [Förutsättningar](#prerequisites)).

1. Om du vill se loggar i Application Insights när du kör projektet lokalt:

    a. Skapa en application insights-resurs och använd den **allmänna** apptypen för den.

    b. Spara instrumenteringsnyckeln i *filen App.config.*

1. Kör projektet.

### <a name="run-in-azure"></a>Kör i Azure

1. Skapa en webbapp och ett lagringskonto.

1. Spara lagringsanslutningssträngen i en appinställning `AzureWebJobsStorage`med namnet .

1. Skapa en application insights-resurs och använd den **allmänna** apptypen för den.

1. Spara instrumenteringsnyckeln i `APPINSIGHTS_INSTRUMENTATIONKEY`en appinställning med namnet .

1. Distribuera som ett WebJob.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

I den här artikeln beskrivs hur du utvecklar ett WebJobs SDK 2.x-projekt. Om du utvecklar ett [WebJobs SDK 3.x-projekt](../../app-service/webjobs-sdk-get-started.md) hjälper det här avsnittet dig att förstå skillnaderna.

Den viktigaste ändringen som introduceras är användningen av .NET Core i stället för .NET Framework. Om du vill skapa ett WebJobs SDK 3.x-projekt är instruktionerna desamma, med följande undantag:

1. Skapa en .NET Core-konsolapp. Välj **.NET Core** > **Console App (.NET Core) i**dialogrutan Nytt **projekt** i Visual Studio. Projektfilen anger att `TargetFramework` `netcoreapp2.x`den är .

1. Välj utgivningsversionen WebJobs SDK 3.x av följande paket:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Ange lagringsanslutningssträngen och instrumenteringsnyckeln Application Insights i en *appsettings.json-fil* med hjälp av konfigurationsramverket .NET Core. Här är ett exempel:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Ändra `Main` metodkoden för att göra detta. Här är ett exempel:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Nästa steg

Mer information om WebJobs SDK finns i [Så här använder du WebJobs SDK](../../app-service/webjobs-sdk-how-to.md).
