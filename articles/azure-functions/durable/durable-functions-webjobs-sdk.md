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
ms.openlocfilehash: c1a778a006c7743b6ea19062d79cff3cfeaba3d8
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713280"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Hur du kör varaktiga funktioner som WebJobs

[Azure Functions](../functions-overview.md) och [varaktiga funktioner](durable-functions-overview.md) tillägget bygger på den [WebJobs SDK](../../app-service/webjobs-create.md). Den `JobHost` WebJobs SDK är runtime i Azure Functions. Om du behöver för att styra `JobHost` beteende på sätt som inte var möjliga i Azure Functions, som du kan utveckla och köra varaktiga funktioner med hjälp av WebJobs SDK själv. Du kan sedan köra din varaktiga funktioner i ett Azure WebJob eller var som helst ett konsolprogram körs.

Chaining varaktiga funktioner-exempel finns i en WebJobs-SDK-version: ladda ned eller klona den [varaktiga funktioner databasen](https://github.com/azure/azure-functions-durable-extension/) och navigera till den *exempel\\webjobssdk\\länkning* mapp.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med grunderna för WebJobs SDK, C#-klass biblioteksutveckling för Azure Functions och varaktiga funktioner. Om du behöver en introduktion till de här ämnena finns i följande resurser:

* [Kom igång med WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Skapa din första funktion med Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Varaktiga funktioner](durable-functions-sequence.md)

För att slutföra stegen i den här artikeln:

* [Installera Visual Studio 2017 version 15,6 eller senare](https://docs.microsoft.com/visualstudio/install/) med den **Azure development** arbetsbelastning.

  Om du redan har Visual Studio men inte har den arbetsbelastningen, lägger du till arbetsbelastningen genom att välja **Verktyg > Hämta verktyg och funktioner**.

  (Du kan använda [Visual Studio Code](https://code.visualstudio.com/) i stället, men vissa av anvisningarna är specifika för Visual Studio.)

* Installera och köra [Azure Storage-emulatorn](../../storage/common/storage-use-emulator.md) version 5.2 eller senare. Ett alternativ är att uppdatera den *App.config* fil med en anslutningssträng för Azure Storage.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-versioner

Den här artikeln förklarar hur du utvecklar ett WebJobs-SDK 2.x-projekt (motsvarar Azure Functions-version 1.x). Information om version 3.x, se [WebJobs SDK 3.x](#webjobs-sdk-3x) senare i den här artikeln.

## <a name="create-console-app"></a>Skapa-konsolapp

Ett WebJobs-SDK-projekt är bara ett konsolen app-projekt med lämpligt NuGet-paket installeras.

I Visual Studio **nytt projekt** dialogrutan **Windows Classic Desktop > konsolprogram (.NET Framework)**. I projektfilen, den `TargetFrameworkVersion` ska vara `v4.6.1`.

Visual Studio har också en projektmall för WebJob som du kan använda genom att välja **molnet > Azure-Webbjobb (.NET Framework)**. Den här mallen installerar många paket, vilket inte kanske behövs.

## <a name="install-nuget-packages"></a>Installera NuGet-paket

Du behöver NuGet-paket för WebJobs SDK, core-bindningar, vilket loggningsramverk och tillägget varaktiga uppgift. Här följer **Pakethanterarkonsolen** kommandon för dessa paket, med senaste stabila versionen siffror från och med det datum då den här artikeln skrevs:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Du måste också loggning providers. Följande kommandon installerar du Application Insights-providern och `ConfigurationManager`. Den `ConfigurationManager` kan du hämta Application Insights-instrumentationsnyckeln från appen inställningar.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Konsol-providern installeras med följande kommando:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kod

Om du vill använda tillägget varaktiga funktioner, anropa `UseDurableTask` på den `JobHostConfiguration` objekt i din `Main` metoden:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

En lista över egenskaper som du kan ange i den `DurableTaskExtension` objekt, se [host.json](../functions-host-json.md#durabletask).

Den `Main` metoden är också plats för att ställa in loggning providers. I följande exempel konfigureras konsolen och Application Insights-leverantörer.

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

Det finns några skillnader i koden du skriver för WebJobs-SDK-funktioner jämfört med vad du skriver för Azure Functions-tjänsten.

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

I ett WebJobs-SDK-projekt kan du anropa metoder på orchestration-klientobjekt i stället för att skicka HTTP-förfrågningar. Följande metoder motsvarar de tre uppgifter som du kan göra med API för HTTP-hantering:

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

Det här avsnittet innehåller en översikt över hur du kör den [exempelprojektet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Detaljerade instruktioner som förklarar hur du kör ett WebJobs-SDK-projekt lokalt och distribuera den till ett Azure WebJob finns i [Kom igång med WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Lokal körning

1. Kontrollera att Storage-emulatorn körs (se [krav](#prerequisites)).

1. Om du vill se loggar i Application Insights när du kör lokalt:

    a. Skapa en Application Insights-resurs, typ av app **Allmänt**.

    b. Spara instrumenteringsnyckeln i den *App.config* fil.

1. Kör projektet.

### <a name="run-in-azure"></a>Kör i Azure

1. Skapa en webbapp och ett lagringskonto.

1. Spara anslutningssträng för lagring i en appinställning med namnet AzureWebJobsStorage i web-app.

1. Skapa en Application Insights-resurs, typ av app **Allmänt**.

1. Spara instrumenteringsnyckeln i en appinställning med namnet APPINSIGHTS_INSTRUMENTATIONKEY.

1. Distribuera som ett WebJob.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

Den huvudsakliga ändringen som introducerades av 3.x är användningen av .NET Core i stället för .NET Framework. För att skapa ett 3.x-projekt är anvisningarna likadana, med följande undantag:

1. Skapa en .NET Core-konsolapp. I Visual Studio **nytt projekt** dialogrutan **.NET Core > konsolprogram (.NET Core)**. Projektfilen anger att `TargetFramework` är `netcoreapp2.0`.

1. Välj förhandsversionen 3.x av följande paket:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Ändra `Main` koden för att hämta anslutningssträngen för lagring och Application Insights-instrumentationsnyckeln från en *appsettings.json* fil, med hjälp av .NET Core configuration-ramverket.  Här är ett exempel:

   ```cs
   static void Main(string[] args)
   {
       var builder = new ConfigurationBuilder()
           .SetBasePath(Directory.GetCurrentDirectory())
           .AddJsonFile("appsettings.json");

       var appSettingsConfig = builder.Build();

       using (var loggerFactory = new LoggerFactory())
       {
           var config = new JobHostConfiguration();

           config.DashboardConnectionString = "";
           config.StorageConnectionString =
               appSettingsConfig.GetConnectionString("AzureWebJobsStorage");
           var instrumentationKey =
               appSettingsConfig["APPINSIGHTS_INSTRUMENTATIONKEY"];

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

## <a name="next-steps"></a>Nästa steg

Läs mer om WebJobs-SDK i [hur du använder WebJobs-SDK](../../app-service/webjobs-sdk-how-to.md).