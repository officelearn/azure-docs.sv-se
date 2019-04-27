---
title: Kom igång med WebJobs-SDK - Azure
description: Introduktion till WebJobs-SDK för händelsedrivna Bakgrundsbearbetning. Lär dig hur du kommer åt data i Azure-tjänster och tjänster från tredje part.
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
ms.openlocfilehash: b824c99a015cfa2c1d1c75e2a1257eff482e8dd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60833308"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Kom igång med Azure WebJobs-SDK för händelsedrivna Bakgrundsbearbetning

Den här artikeln visar hur du använder Visual Studio 2017 för att skapa ett projekt med Azure WebJobs SDK, köra det lokalt och sedan distribuera den till [Azure App Service](overview.md). Du har skapat projektet är en .NET Core-konsolapp som använder version 3.x av WebJobs SDK. Om du är intresserad av version 2.x som använder .NET Framework, se [utveckla och distribuera WebJobs med hjälp av Visual Studio – Azure App Service](webjobs-dotnet-deploy-vs.md).

Mer information om hur du arbetar med WebJobs SDK finns [hur du använder Azure WebJobs SDK för händelsedrivna Bakgrundsbearbetning](webjobs-sdk-how-to.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Installera Visual Studio 2017](/visualstudio/install/) med den **Azure development** arbetsbelastning. Om du redan har Visual Studio men inte har den arbetsbelastningen, lägger du till arbetsbelastningen genom att välja **Verktyg > Hämta verktyg och funktioner**.

* Du måste ha [ett Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) att publicera projektet WebJobs SDK på Azure.

## <a name="create-a-project"></a>Skapa ett projekt

1. I Visual Studio väljer **fil > Nytt > projekt**.

2. Välj **.NET Core > konsolprogram (.NET Core)**.

3. Ge projektet namnet *WebJobsSDKSample*, och välj sedan **OK**.

   ![Dialogrutan Nytt projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>WebJobs-NuGet-paket

1. Installera de senaste stabila 3.x-versionerna av följande NuGet-paket:

   * `Microsoft.Azure.WebJobs`
   * `Microsoft.Azure.WebJobs.Extensions`

     Här är den **Pakethanterarkonsolen** kommandon för version 3.0.4:

     ```powershell
     Install-Package Microsoft.Azure.WebJobs -version 3.0.4
     Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
     ```

## <a name="create-the-host"></a>Skapa värden

Värden är en behållare för functions runtime som lyssnar efter utlösare och-anrop. Följande steg skapar du en värd som implementerar [ `IHost` ](/dotnet/api/microsoft.extensions.hosting.ihost), vilket är den allmänna värden i ASP.NET Core.

1. I *Program.cs*, lägga till en `using` instruktionen:

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. Ersätt metoden `Main` med följande kod:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
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

I ASP.NET Core värdkonfigurationer konfigureras genom att anropa metoder på det [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instans. Mer information finns i [.NET Allmänt värden](/aspnet/core/fundamentals/host/generic-host). Den `ConfigureWebJobs` tilläggsmetod initierar WebJobs-värden. I `ConfigureWebJobs`, du initiera specifika WebJobs-tillägg och ställa in egenskaperna för dessa tillägg.  

## <a name="enable-console-logging"></a>Aktivera loggning för konsolen

I det här avsnittet konfigurerar du konsolen loggning som använder den [ASP.NET Core loggningsramverk](/aspnet/core/fundamentals/logging).

1. Installera den senaste stabila versionen av följande NuGet-paket:

   * `Microsoft.Extensions.Logging` – Vilket loggningsramverk.
   * `Microsoft.Extensions.Logging.Console` -Konsolen-providern som skickar loggar till konsolen.

   Här är den **Pakethanterarkonsolen** kommandon för version 2.2.0:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.2.0
   ```

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. I *Program.cs*, lägga till en `using` instruktionen:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Anropa den [ `ConfigureLogging` ](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) metoden på [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Den [ `AddConsole` ](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) metoden lägger till konsolen loggning i konfigurationen.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    Den `Main` metoden ser nu ut så här:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Den här uppdateringen gör följande:

    * Inaktiverar [instrumentpanelen loggning](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Instrumentpanelen är en äldre övervakning verktyget och instrumentpanelen loggning rekommenderas inte för stora dataflöden produktionsscenarier.
    * Lägger till konsolen providern med standard [filtrering](webjobs-sdk-how-to.md#log-filtering).

Nu kan du kan lägga till en funktion som utlöses av meddelanden som inkommer på en [Azure Storage-kö](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>Installera tillägget för Storage-bindning

Från och med version 3.x, måste du uttryckligen installerar Storage bindningstillägget krävs av WebJobs SDK. I tidigare versioner av ingår Storage-bindningar i SDK.

1. Installera den senaste stabila versionen av den [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-paketet, version 3.x. 

    Här är den **Pakethanterarkonsolen** kommandot för version 3.0.3:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.3
    ```

2. I den `ConfigureWebJobs` tilläggsmetod, anrop den `AddAzureStorage` metoden på den [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instans att initiera Storage-tillägget. Nu kan den `ConfigureWebJobs` metoden ser ut som i följande exempel:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Skapa en funktion

1. Högerklicka på projektet, Välj **Lägg till** > **nytt objekt...** , Välj **klass**, namnge den nya C# klassfil *Functions.cs*, och välj **Lägg till**.

1. Ersätt den genererade mallen i Functions.cs, med följande kod:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   Den `QueueTrigger` attributet anger körning för att anropa den här funktionen när ett nytt meddelande skrivs i en Azure Storage-kö som kallas `queue`. Innehållet i kömeddelandet tillhandahålls för att metoden koden i den `message` parametern. Brödtexten i metoden är där du bearbeta data för utlösaren. I det här exemplet loggar koden bara meddelandet.

   Den `message` parametern behöver inte vara en sträng. Du kan också binda till en JSON-objekt, en bytematris eller en [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) objekt. [Se användning av Replikeringskö utlösaren](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Varje bindningstyp (till exempel köer, blobbar och tabeller) har en annan uppsättning parametertyper som du kan bindas till.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Azure Storage-emulatorn som körs lokalt har inte alla funktioner som krävs för WebJobs SDK. Så i det här avsnittet du skapar ett lagringskonto i Azure och konfigurera projektet för att använda den. Om du redan har ett lagringskonto kan du gå vidare till steg 6.

1. Öppna **Server Explorer** i Visual studio och logga in på Azure. Högerklicka på den **Azure** noden och välj sedan **Anslut till Microsoft Azure-prenumeration**.

   ![Logga in på Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Under den **Azure** nod i **Server Explorer**, högerklicka på **Storage**, och välj sedan **skapa lagringskonto**.

   ![Skapa Lagringskontots meny](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. I den **skapa Lagringskonto** dialogrutan, ange ett unikt namn för lagringskontot.

1. Välj samma **Region** att du har skapat din App Service-app i eller en region nära dig.

1. Välj **Skapa**.

   ![Skapa lagringskonto](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Under den **Storage** nod i **Server Explorer**, Välj det nya lagringskontot. I den **egenskaper** fönstret, Välj ellipsen (**...** ) längst till höger i den **Connection String** värdefält.

   ![Anslutningen sträng ellipsen](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Kopiera anslutningssträngen och spara det här värdet någonstans att du kan kopiera igen lätt.

   ![Kopiera anslutningssträngen](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Konfigurera lagring för att köra lokalt

WebJobs SDK söker efter lagringsanslutningssträngen i programinställningarna i Azure. När du kör lokalt, efter det här värdet i den lokala konfigurationsfilen eller miljövariabler.

1. Högerklicka på projektet, Välj **Lägg till** > **nytt objekt...** , Välj **JavaScript JSON-konfigurationsfil**, ge den nya filen namnet *appsettings.json* och väljer **Lägg till**. 

1. I den nya filen lägger du till en `AzureWebJobsStorage` fältet, som i följande exempel:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Ersätt *{lagringsanslutningssträng}* med den anslutningssträng som du kopierade tidigare.

1. Välj den *appsettings.json* filen i Solution Explorer och i den **egenskaper** ställer **kopiera till utdatakatalog** till **kopiera om nyare**.

Senare, lägger du till den samma inställningen för anslutningssträngen app i din app i Azure App Service.

## <a name="test-locally"></a>Testa lokalt

I det här avsnittet skapar du och köra projektet lokalt och Utlös funktionen genom att skapa ett kömeddelande.

1. Tryck på **Ctrl + F5** kör projektet.

   Konsolen visar att körningen gick att hitta din funktion och väntar på Kömeddelanden som utlöser den. Följande utdata genereras av v3.x värden:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Stäng konsolfönstret.

1. I **Server Explorer** expanderar du noden för det nya kontot i Visual Studio och högerklicka på **köer**.

1. Välj **Skapa kö**.

1. Ange *kö* som namn på kön och välj sedan **OK**.

   ![Skapa kö](./media/webjobs-sdk-get-started/create-queue.png)

1. Högerklicka på noden för den nya kön och välj sedan **Visa kö**.

1. Välj den **Lägg till meddelande** ikon.

   ![Skapa kö](./media/webjobs-sdk-get-started/create-queue-message.png)

1. I den **Lägg till meddelande** dialogrutan Ange *Hello World!* som den **meddelandetext**, och välj sedan **OK**. Det finns nu ett meddelande i kön.

   ![Skapa kö](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Kör projektet igen.

   Eftersom du har använt den `QueueTrigger` attribut i den `ProcessQueueMessage` funktion, WeJobs SDK-körning lyssnar efter meddelanden i kön när den startas. Ett nytt kömeddelande hittas i kön med namnet *kö* och anropar funktionen.

   På grund av [kö avsökning exponentiell backoff](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), det kan ta upp till 2 minuter för körning för att hitta meddelandet och anropa funktionen. Den här väntetid kan minskas genom att köra i [utvecklingsläge](webjobs-sdk-how-to.md#host-development-settings).

   Konsolens utdata ser ut så här:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Stäng konsolfönstret. 

1. Gå tillbaka till fönstret kön och uppdatera den. Meddelandet är borta, eftersom de har bearbetats av din funktion som körs lokalt. 

## <a name="add-application-insights-logging"></a>Lägg till Application Insights-loggning

När projektet körs i Azure, kan du övervaka körning av funktion genom att granska konsolutdata. Övervakningslösning som vi rekommenderar att du är [Application Insights](../azure-monitor/app/app-insights-overview.md). Mer information finns i [övervaka Azure Functions](../azure-functions/functions-monitoring.md).

I det här avsnittet ska göra du följande uppgifter för att konfigurera Application Insights loggning innan du distribuerar till Azure:

* Kontrollera att du har en App Service-app och en Application Insights-instans för att arbeta med.
* Konfigurera App Service-appen om du vill använda Application Insights-instans och storage-kontot som du skapade tidigare.
* Konfigurera projektet för loggning till Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Skapa App Service-app och Application Insights-instans

1. Om du inte redan har en App Service-app som du kan använda [skapar ett](app-service-web-get-started-dotnet-framework.md). När du skapar din app kan skapa du också en ansluten Application Insights-resurs. När du gör detta, den `APPINSIGHTS_INSTRUMENTATIONKEY` har angetts för dig i din app.

1. Om du inte redan har en Application Insights-resurs som du kan använda [skapar ett](../azure-monitor/app/create-new-resource.md ). Ange **programtyp** till **Allmänt**, och hoppa över avsnitten som följer **kopierar du instrumentationsnyckeln**.

1. Om du redan har en Application Insights-resurs som du vill använda, [kopierar du instrumentationsnyckeln](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurera appinställningar 

1. I **Server Explorer** i Visual Studio, expanderar den **Apptjänst** nod under **Azure**.

1. Expandera resursgruppen som din App Service-app och högerklicka på App Service-appen.

1. Välj **visa inställningarna för**.

1. I den **anslutningssträngar** lägger du till följande post.

   |Namn  |anslutningssträngen  |Databastyp|
   |---------|---------|------|
   |AzureWebJobsStorage | {lagringsanslutningssträngen som du kopierade tidigare}|Anpassat|

1. Om den **programinställningar** box har inte en Application Insights-instrumentation nyckeln, lägga till det som du kopierade tidigare. (Instrumenteringsnyckeln kan redan vara det, beroende på hur du skapade App Service-appen.)

   |Namn  |Värde  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumenteringsnyckeln} |

1. Ersätt *{instrumenteringsnyckeln}* med instrumenteringsnyckeln från Application Insights-resurs som du använder.

1. Välj **Spara**.

1. Lägg till Application Insights-anslutning till projektet så att du kan köra den lokalt. I den *appsettings.json* Lägg till en `APPINSIGHTS_INSTRUMENTATIONKEY` fältet, som i följande exempel:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Ersätt *{instrumenteringsnyckeln}* med instrumenteringsnyckeln från Application Insights-resurs som du använder.

1. Spara ändringarna.

### <a name="add-application-insights-logging-provider"></a>Lägg till Application Insights loggningsprovider

Att dra nytta av [Application Insights](../azure-monitor/app/app-insights-overview.md) loggning, uppdatera koden loggning för att göra följande:

* Lägg till en Application Insights loggningsprovider med standard [filtrering](webjobs-sdk-how-to.md#log-filtering); all Information och på högre nivå loggar går till både konsolen och Application Insights när du kör lokalt.
* Placera den `LoggerFactory` objekt i en `using` block så att loggutdata rensas när värden avslutas.

1. Installera den senaste stabila 3.x-versionen av NuGet-paketet för Application Insights loggningsprovider: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Här är den **Pakethanterarkonsolen** kommandot för version 3.0.2:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. Öppna *Program.cs* och Ersätt Koden i den `Main` metoden med följande kod:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsights(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Detta lägger till Application Insights-providern loggning, med hjälp av den nyckel som du tidigare lade till inställningarna för din app.

## <a name="test-application-insights-logging"></a>Testa Application Insights-loggning

I det här avsnittet ska köra du lokalt igen för att verifiera att loggningsdata nu kommer att Application Insights även om konsolen.

1. Använd **Server Explorer** i Visual Studio för att skapa ett kömeddelande, på samma sätt som du gjorde [tidigare](#trigger-the-function-in-azure), förutom att ange *Hello App Insights!* som meddelandetext.

1. Kör projektet.

   WebJobs SDK bearbetar kömeddelandet och du ser loggarna i konsolfönstret.

1. Stäng konsolfönstret.

1. Öppna den [Azure-portalen](https://portal.azure.com/), och gå till Application Insights-resursen.

1. Välj **Search**.

   ![Välj sökning](./media/webjobs-sdk-get-started/select-search.png)

1. Om du inte ser den *Hello App Insights!* meddelandet markerar **uppdatera** regelbundet i flera minuter. (Loggar inte visas direkt eftersom det tar en stund för Application Insights-klienten att tömma loggarna bearbetas.)

   ![Loggar i Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Stäng konsolfönstret.

## <a name="deploy-as-a-webjob"></a>Distribuera till Azure

Under distributionen kan skapa du en app service-instans där du kan köra dina funktioner. När du publicerar en .NET Core-konsolapp till App Service i Azure, hämtar det automatiskt köras som ett WebJob. Mer information om hur du publicerar finns [utveckla och distribuera WebJobs med hjälp av Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Utlös funktionen i Azure

1. Kontrollera att du inte kör lokalt (Stäng konsolfönstret om det fortfarande är öppen). Annars kan den lokala instansen var först med att bearbeta alla kömeddelanden som du skapar.

1. I den **kö** i Visual Studio, Lägg till ett meddelande till kön som tidigare.

1. Uppdatera den **kö** sidan och det nya meddelandet försvinner eftersom de har bearbetats av funktionen som körs i Azure.

   > [!TIP]
   > När du testar i Azure, använda [utvecklingsläge](webjobs-sdk-how-to.md#host-development-settings) att säkerställa att en funktion för en kö-utlösare anropas direkt och undvika förseningar på grund av [kö avsökning exponentiell backoff](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Visa loggar i Application Insights

1. Öppna den [Azure-portalen](https://portal.azure.com/), och gå till Application Insights-resursen.

1. Välj **Search**.

1. Om du inte ser den *Hello Azure!* meddelandet markerar **uppdatera** regelbundet i flera minuter.

   Du ser loggar från den funktion som körs i ett Webbjobb, inklusive den *Hello Azure!* text som du angav i föregående avsnitt.

## <a name="add-an-input-binding"></a>Lägg till en indatabindning

Indatabindningar förenkla kod som läser data. I det här exemplet kömeddelandet blir ett blobnamn och du använder blob-namnet för att söka efter och läsa en blob i Azure Storage.

1. I *Functions.cs*, ersätter den `ProcessQueueMessage` metoden med följande kod:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   I den här koden `queueTrigger` är en [bindning uttryck](../azure-functions/functions-bindings-expressions-patterns.md), vilket innebär att det matchar ett annat värde vid körning.  Vid körning har det innehållet i kömeddelandet.

1. Lägg till en `using`:

   ```cs
   using System.IO;
   ```

1. Skapa en blobbehållare i ditt storage-konto.

   a. I **Server Explorer** expanderar du noden för ditt lagringskonto i Visual Studio, högerklicka på **Blobar**, och välj sedan **skapa Blobbehållare**.

   b. I den **skapa Blobbehållare** dialogrutan Ange *behållare* som behållarens namn och klicka sedan på **OK**.

1. Ladda upp den *Program.cs* till blobbehållaren. (Den här filen används här som ett exempel; du kan ladda upp en textfil och skapa ett kömeddelande med filens namn.)

   a. I **Server Explorer**, dubbelklickar du på noden för den behållare som du skapade.

   b. I den **behållare** väljer den **överför** knappen.

   ![Ladda upp BLOB-knappen](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Hitta och välja *Program.cs*, och välj sedan **OK**.

1. Skapa ett kömeddelande i kön som du skapade tidigare, med *Program.cs* som meddelandets text.

   ![Kömeddelande Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Köra projektet lokalt.

   Kömeddelandet utlöser funktionen, som sedan läser blob och loggar dess längd. Konsolens utdata ser ut så här:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

Utdatabindningar förenkla kod som skriver data. Det här exemplet ändrar det föregående genom att skriva en kopia av blob i stället för loggning av dess storlek. BLOB storage-bindningar som ingår i tillägget Azure Storage-paketet som vi installerat sedan tidigare.

1. Ersätt metoden `ProcessQueueMessage` med följande kod:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Skapa en annan kömeddelande med *Program.cs* som meddelandets text.

1. Köra projektet lokalt.

   Kömeddelandet utlöser funktionen som sedan läser blob, loggar dess längd och skapar en ny blob. Konsolens utdata är detsamma, men när du gå till fönstret blob-behållare och väljer **uppdatera**, visas en ny blob med namnet *kopiera Program.cs.*

## <a name="republish-the-updates-to-azure"></a>Publicera uppdateringar till Azure

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. I den **publicera** dialogrutan, kontrollera att den aktuella profilen är markerat och välj sedan **publicera**. Resultatet av publicera beskrivs i den **utdata** fönster.
 
1. Kontrollera funktionen i Azure genom att ladda upp en fil till blobbehållaren och lägga till ett meddelande till den kö som är namnet på den överförda filen igen. Du ser meddelandet kom tas bort från kön och en kopia av filen som skapats i blob-behållaren. 

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du skapar, kör och distribuera en 3.x WebJobs-SDK-projektet.

> [!div class="nextstepaction"]
> [Läs mer om WebJobs-SDK](webjobs-sdk-how-to.md)
