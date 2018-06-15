---
title: Hur du kör varaktiga fungerar som WebJobs - Azure
description: Lär dig hur du code och konfigurera beständiga funktioner som ska köras i WebJobs med hjälp av WebJobs-SDK.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 3fc84d1492d2855ffa3bb5538226da049a928339
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33767324"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Hur du kör varaktiga fungerar som WebJobs

[Azure Functions](functions-overview.md) och [varaktiga funktioner](durable-functions-overview.md) tillägget bygger på den [WebJobs SDK](../app-service/web-sites-create-web-jobs.md). Den `JobHost` WebJobs-SDK är körningen i Azure Functions. Om du behöver för att styra `JobHost` beteende på sätt som inte är möjligt i Azure Functions, som du kan utveckla och kör varaktiga funktioner med hjälp av WebJobs-SDK själv. Sedan kan du köra dina beständiga funktioner i en Azure-Webbjobb eller var som helst ett konsolprogram körs.

Chaining varaktiga funktioner-exempel finns i en WebJobs-SDK-version: ladda ned eller klona den [varaktiga funktioner databasen](https://github.com/azure/azure-functions-durable-extension/) och navigera till den *exempel\\webjobssdk\\länkning* mapp.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du känner till grunderna för WebJobs-SDK C#-klassen biblioteket-utveckling för Azure-funktioner och varaktig funktioner. Om du behöver en introduktion till de här ämnena finns i följande resurser:

* [Kom igång med WebJobs SDK](../app-service/webjobs-sdk-get-started.md)
* [Skapa din första funktion med Visual Studio](functions-create-your-first-function-visual-studio.md)
* [Beständiga funktioner](durable-functions-sequence.md)

Så här slutför stegen i den här artikeln:

* [Installera Visual Studio 2017 15,6 eller senare](https://docs.microsoft.com/visualstudio/install/) med den **Azure-utveckling** arbetsbelastning.

  Om du redan har Visual Studio inte men att arbetsbelastningar, lägger du till arbetsbelastningen genom att välja **Verktyg > Hämta verktyg och funktioner**. 

  (Du kan använda [Visual Studio Code](https://code.visualstudio.com/) i stället, men några av instruktionerna som är specifika för Visual Studio.)

* Installera och köra [Azure Storage-emulatorn](../storage/common/storage-use-emulator.md) version 5.2 eller senare. Ett alternativ är att uppdatera den *App.config* fil med en Azure Storage-anslutningssträng.

## <a name="webjobs-sdk-versions"></a>WebJobs-SDK-versioner

Den här artikeln förklarar hur du utvecklar ett WebJobs SDK 2.x-projekt (motsvarar Azure Functions version 1.x). Mer information om version 3.x finns [WebJobs SDK 3.x](#webjobs-sdk-3x) senare i den här artikeln. 

## <a name="create-console-app"></a>Skapa konsolprogram

Ett WebJobs-SDK-projekt är bara ett konsolen app-projekt med lämpligt NuGet-paket installeras.

I Visual Studio **nytt projekt** markerar **klassiska skrivbordet i Windows > konsolprogram (.NET Framework)**. I projektfilen, den `TargetFrameworkVersion` ska vara `v4.6.1`.

Visual Studio har också en Webbjobb projektmall som du kan använda genom att välja **molntjänster > Azure Webjobs (.NET Framework)**. Den här mallen installerar många paket, vilket inte kanske du behöver.

## <a name="install-nuget-packages"></a>Installera NuGet-paket

Du behöver NuGet-paket för WebJobs SDK, core bindningar, ramverket loggning och varaktig Task-tillägget. Här följer **Pakethanterarkonsolen** kommandon för dessa paket, med senaste stabil versionsnummer vid datumet i den här artikeln skrevs:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Du måste också loggning providers. Följande kommandon installera Application Insights-providern och `ConfigurationManager`. Den `ConfigurationManager` kan du få Application Insights instrumentation nyckeln från app-inställningar.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Konsol-providern installeras med följande kommando:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kod

Om du vill använda tillägget varaktiga funktioner anropa `UseDurableTask` på den `JobHostConfiguration` objekt i din `Main` metoden:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

En lista över egenskaper som du kan ange i den `DurableTaskExtension` objekt, se [host.json](functions-host-json.md#durabletask).

Den `Main` metoden är också ställa in loggning providers. I följande exempel konfigureras konsolen och leverantörer av Application Insights.

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

Det finns några skillnader i koden du skriver för WebJobs SDK-funktioner jämfört med att du skriver för tjänsten Azure Functions.

WebJobs SDK stöder inte följande funktioner i Azure Functions:

* [FunctionName attribut](#functionname-attribute)
* [HTTP-utlösare](#http-trigger)
* [Beständiga funktioner HTTP hanterings-API](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName attribut

I WebJobs-SDK-projekt är för en funktion namnet på funktionen. Den `FunctionName` attributet används endast i Azure Functions.

### <a name="http-trigger"></a>HTTP-utlösare

WebJobs SDK har inte en HTTP-utlösare. Den exempelprojektet orchestration klienten använder en timer som utlösare:

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

Eftersom den har ingen HTTP-utlösaren WebJobs SDK inte har någon [API för hantering av HTTP-](durable-functions-http-api.md).

Du kan anropa metoder i orchestration klienten objektet i stället för att skicka HTTP-begäranden i WebJobs-SDK-projekt. Följande metoder motsvarar de tre uppgifter som du kan göra med API för HTTP-hantering:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Funktionen orchestration-klienten i exempelprojektet startas orchestrator-funktionen och sedan in i en loop som anropar `GetStatusAsync` varannan sekund:

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

Det här avsnittet innehåller en översikt över hur du kör den [exempelprojektet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Detaljerade instruktioner som förklarar hur du kör en WebJobs SDK-projektet lokalt och distribuera den till en Azure-Webbjobb finns [Kom igång med WebJobs SDK](../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Lokal körning

1. Se till att Storage-emulatorn kör (se [krav](#prerequisites)).

1. Om du vill se loggar i Application Insights när du kör lokalt:

  a. Skapa en Application Insights-resurs, typ av app **allmänna**.

  b. Spara nyckeln instrumentation i den *App.config* fil.

1. Kör projektet.

### <a name="run-in-azure"></a>Kör i Azure

1. Skapa en webbapp och ett lagringskonto.

1. Spara anslutningssträngen för lagring i en appinställning med namnet AzureWebJobsStorage i webbapp.

1. Skapa en Application Insights-resurs, typ av app **allmänna**.

1. Spara nyckeln instrumentation i en appinställning med namnet APPINSIGHTS_INSTRUMENTATIONKEY.

1. Distribuera som ett Webbjobb.

## <a name="webjobs-sdk-3x"></a>WebJobs-SDK 3.x

Huvudsakliga ändringen introducerades av 3.x är att använda .NET Core i stället för .NET Framework. Om du vill skapa ett 3.x-projekt är instruktionerna desamma, med följande undantag:

1. Skapa en konsolapp med .NET Core. I Visual Studio **nytt projekt** markerar **.NET Core > konsolprogram (.NET Core)**. Projektfilen anger att `TargetFramework` är `netcoreapp2.0`.

1. Välj förhandsversionen 3.x följande paket:

  * `Microsoft.Azure.WebJobs.Extensions`
  * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

2. Ändra `Main` koden för att hämta anslutningssträngen för lagring och Application Insights instrumentation nyckeln från ett *appsettings.json* fil, använda .NET Core configuration ramen.  Här är ett exempel:

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

Läs mer om WebJobs-SDK i [hur du använder WebJobs-SDK](../app-service/webjobs-sdk-how-to.md).

