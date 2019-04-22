---
title: Hur du kör varaktiga funktioner som WebJobs – Azure
description: Lär dig mer om att koda och konfigurera varaktiga funktioner för att köra i WebJobs med WebJobs SDK.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: df12639aaafaf3df7ae2b755d635d4fba83d846e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905103"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Hur du kör varaktiga funktioner som WebJobs

Som standard använder Azure Functions-körningen till värd-orkestreringar i varaktiga funktioner. Det kan dock finnas vissa scenarier där du behöver mer kontroll över den kod som lyssnar efter händelser. Den här artikeln visar hur du implementerar din orchestration med hjälp av WebJobs SDK. Om du vill se en mer detaljerad jämförelse mellan Functions och WebJobs, se [jämföra Functions och WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) och [varaktiga funktioner](durable-functions-overview.md) tillägget bygger på den [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). Jobb-värden i WebJobs SDK är runtime i Azure Functions. Om du vill kontrollera beteende på sätt som inte var möjliga i Azure Functions kan du utveckla och kör varaktiga funktioner med hjälp av WebJobs SDK själv.

I version 3.x av WebJobs SDK värden är en implementering av `IHost`, och i version 2.x som du använder den `JobHost` objekt.

Chaining varaktiga funktioner-exempel finns i en WebJobs SDK 2.x-version: ladda ned eller klona den [varaktiga funktioner databasen](https://github.com/azure/azure-functions-durable-extension/), och gå till den *exempel\\webjobssdk\\länkning* mapp.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här artikeln förutsätter att du är bekant med grunderna för WebJobs SDK, C#-klass biblioteksutveckling för Azure Functions och varaktiga funktioner. Om du behöver en introduktion till de här ämnena finns i följande resurser:

* [Kom igång med WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Skapa din första funktion med Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Varaktiga funktioner](durable-functions-sequence.md)

För att slutföra stegen i den här artikeln:

* [Installera Visual Studio 2017 version 15,6 eller senare](https://docs.microsoft.com/visualstudio/install/) med den **Azure development** arbetsbelastning.

  Om du redan har Visual Studio, men inte har den arbetsbelastningen, lägger du till arbetsbelastningen genom att välja **verktyg** > **hämta verktyg och funktioner**.

  (Du kan använda [Visual Studio Code](https://code.visualstudio.com/) i stället, men vissa av anvisningarna är specifika för Visual Studio.)

* Installera och köra [Azure Storage-emulatorn](../../storage/common/storage-use-emulator.md) version 5.2 eller senare. Ett alternativ är att uppdatera den *App.config* fil med en anslutningssträng för Azure Storage.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-versioner

Den här artikeln förklarar hur du utvecklar ett WebJobs-SDK 2.x-projekt (motsvarar Azure Functions-version 1.x). Information om version 3.x, se [WebJobs SDK 3.x](#webjobs-sdk-3x) senare i den här artikeln.

## <a name="create-a-console-app"></a>Skapa en konsolapp

Om du vill köra varaktiga funktioner som WebJobs, måste du först skapa en konsolapp. Ett WebJobs-SDK-projekt är bara ett konsolen app-projekt med lämpligt NuGet-paket installeras.

I Visual Studio **nytt projekt** dialogrutan **Windows Classic Desktop** > **Konsolapp (.NET Framework)**. I projektfilen, den `TargetFrameworkVersion` ska vara `v4.6.1`.

Visual Studio har också en projektmall för WebJob som du kan använda genom att välja **molnet** > **Azure WebJob (.NET Framework)**. Den här mallen installerar många paket, vilket inte kanske behövs.

## <a name="install-nuget-packages"></a>Installera NuGet-paket

Du behöver NuGet-paket för WebJobs SDK, core-bindningar, vilket loggningsramverk och tillägget varaktiga uppgift. Här följer **Pakethanterarkonsolen** kommandon för dessa paket, med den senaste stabila versionen siffror från och med det datum då den här artikeln skrevs:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Du måste också loggning providers. Följande kommandon installerar du Azure Application Insights-providern och `ConfigurationManager`. Den `ConfigurationManager` kan du hämta Application Insights-instrumentationsnyckeln från appen inställningar.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Konsol-providern installeras med följande kommando:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kod

Att ha skapat konsolappen och installerat NuGet-paket du behöver, är du redo att använda varaktiga funktioner. Du kan göra det med hjälp av JobHost kod.

Om du vill använda tillägget varaktiga funktioner, anropa `UseDurableTask` på den `JobHostConfiguration` objekt i din `Main` metoden:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

En lista över egenskaper som du kan ange i den `DurableTaskExtension` objekt, se [host.json](../functions-host-json.md#durabletask).

Den `Main` metoden är också plats för att ställa in loggning providers. I följande exempel konfigureras konsolen och Application Insights-providers.

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

## <a name="functions"></a>Functions

Varaktiga funktioner i samband med WebJobs skiljer sig något från varaktiga funktioner i samband med Azure Functions. Det är viktigt att vara medveten om skillnaderna när du skriver din kod.

WebJobs SDK stöder inte följande funktioner i Azure Functions:

* [FunctionName attribut](#functionname-attribute)
* [HTTP-utlösare](#http-trigger)
* [Varaktiga funktioner HTTP hanterings-API](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName attribut

I ett WebJobs-SDK-projekt är för en funktion funktionsnamnet. Den `FunctionName` attributet används endast i Azure Functions.

### <a name="http-trigger"></a>HTTP-utlösare

WebJobs SDK har inte en HTTP-utlösare. Den exempelprojektet orchestration-klienten använder en timer som utlösare:

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

Eftersom den har ingen HTTP-utlösare WebJobs SDK inte har någon [HTTP-hanterade API: er](durable-functions-http-api.md).

I ett WebJobs-SDK-projekt kan du anropa metoder på orchestration-klientobjekt, i stället för genom att skicka HTTP-begäranden. Följande metoder motsvarar de tre uppgifter som du kan göra med API för HTTP-hantering:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Funktionen orchestration-klienten i exempelprojektet startar orchestrator-funktion och sedan går in i en loop som anropar `GetStatusAsync` varannan sekund:

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

Du har varaktiga funktioner ställa in att köras som ett WebJob och nu har du en förståelse för hur detta skiljer sig från att köras varaktiga funktioner som fristående Azure Functions. Nu kan ser det fungerar i ett exempel vara användbart.

Det här avsnittet innehåller en översikt över hur du kör den [exempelprojektet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Detaljerade instruktioner som förklarar hur du kör ett WebJobs-SDK-projekt lokalt och distribuera den till ett Azure WebJob finns i [Kom igång med WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Lokal körning

1. Kontrollera att Storage-emulatorn körs (se [krav](#prerequisites)).

1. Om du vill se loggar i Application Insights när du kör projektet lokalt:

    a. Skapa en Application Insights-resurs och använda den **Allmänt** apptypen för den.

    b. Spara instrumenteringsnyckeln i den *App.config* fil.

1. Kör projektet.

### <a name="run-in-azure"></a>Kör i Azure

1. Skapa en webbapp och ett lagringskonto.

1. Spara anslutningssträng för lagring i en app som inställning med namnet i web-app `AzureWebJobsStorage`.

1. Skapa en Application Insights-resurs och använda den **Allmänt** apptypen för den.

1. Spara instrumenteringsnyckeln i en app som inställning med namnet `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Distribuera som ett WebJob.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

Den här artikeln förklarar hur du utvecklar ett WebJobs-SDK 2.x-projekt. Om du utvecklar en [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) -projektet i det här avsnittet hjälper dig att förstå skillnaderna.

Den huvudsakliga ändringen introducerades är användningen av .NET Core i stället för .NET Framework. Om du vill skapa ett projekt för WebJobs SDK 3.x är instruktionerna likadana, med följande undantag:

1. Skapa en .NET Core-konsolapp. I Visual Studio **nytt projekt** dialogrutan **.NET Core** > **Konsolapp (.NET Core)**. Projektfilen anger att `TargetFramework` är `netcoreapp2.x`.

1. Välj den utgivna versionen WebJobs SDK 3.x av följande paket:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Ange anslutningssträng för lagring och Application Insights-instrumenteringsnyckeln i en *appsettings.json* filen med hjälp av .NET Core configuration framework. Här är ett exempel:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Ändra den `Main` koden för att göra detta. Här är ett exempel:

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

Läs mer om WebJobs-SDK i [hur du använder WebJobs-SDK](../../app-service/webjobs-sdk-how-to.md).
