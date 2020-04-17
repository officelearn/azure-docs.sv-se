---
title: Komma igång med WebJobs SDK
description: Introduktion till WebJobs SDK för händelsedriven bakgrundsbearbetning. Lär dig hur du kommer åt data i Azure-tjänster och tjänster från tredje part.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4976be485a9b7609c6e8d23f6b897092217663fc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535680"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Komma igång med Azure WebJobs SDK för händelsedriven bakgrundsbearbetning

Den här artikeln visar hur du använder Visual Studio 2019 för att skapa ett Azure WebJobs SDK-projekt, köra det lokalt och sedan distribuera det till [Azure App Service](overview.md). Version 3.x av WebJobs SDK stöder både .NET Core- och .NET Framework-konsolappar. Mer information om hur du arbetar med WebJobs SDK finns i [Så här använder du Azure WebJobs SDK för händelsedriven bakgrundsbearbetning](webjobs-sdk-how-to.md).

I den här artikeln beskrivs hur du distribuerar WebJobs som en .NET Core-konsolapp. Information om hur du distribuerar WebJobs som en .NET Framework-konsolapp finns i [WebJobs som .NET Framework-konsolappar](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Om du är intresserad av WebJobs SDK version 2.x, som bara stöder .NET Framework, läser [du Utveckla och distribuera WebJobs med Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Krav

* [Installera Visual Studio 2019](/visualstudio/install/) med **Azure-utvecklingsarbetsbelastningen.** Om du redan har Visual Studio men inte har den arbetsbelastningen lägger du till arbetsbelastningen genom att välja **Verktyg > Hämta verktyg och funktioner**.

* Du måste ha [ett Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) för att kunna publicera ditt WebJobs SDK-projekt i Azure.

## <a name="create-a-project"></a>Skapa ett projekt

1. Välj **Skapa ett nytt projekt i**Visual Studio .

2. Välj **Konsolapp (.NET Core)**.

3. Namnge projektet *WebJobsSDKSample*och välj sedan **Skapa**.

   ![Dialogrutan Nytt projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>WebJobs NuGet paket

1. Installera den senaste stabila 3.x-versionen av `Microsoft.Azure.WebJobs` [ `Microsoft.Azure.WebJobs.Extensions` NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/), som innehåller .

     Här är kommandot **Package Manager Console:**

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    Ersätt `<3_X_VERSION>` med en version av paketet som stöds i det här kommandot. 

## <a name="create-the-host"></a>Skapa värden

Värden är körningsbehållaren för funktioner som lyssnar efter utlösare och anropsfunktioner. I följande steg skapas [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost)en värd som implementerar , som är den allmänna värden i ASP.NET Core.

1. I *Program.cs*lägger `using` du till följande påståenden:

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Ersätt metoden `Main` med följande kod:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

I ASP.NET Core ställs värdkonfigurationer in genom att [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) anropa metoder för instansen. Mer information finns i [.NET Generic Host](/aspnet/core/fundamentals/host/generic-host). Tilläggsmetoden `ConfigureWebJobs` initierar WebJobs-värden. I `ConfigureWebJobs`initierar du specifika WebJobs-tillägg och anger egenskaper för dessa tillägg.  

## <a name="enable-console-logging"></a>Aktivera konsolloggning

I det här avsnittet konfigurerar du konsolloggning som använder [ASP.NET Core-loggningsramverket](/aspnet/core/fundamentals/logging).

1. Installera den senaste stabila versionen av `Microsoft.Extensions.Logging` [ `Microsoft.Extensions.Logging.Console` NuGet-paketet](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/), som innehåller .

   Här är kommandot **Package Manager Console:**

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```

1. I *Program.cs*lägger du `using` till ett uttryck:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

    I det här `<3_X_VERSION>` kommandot ersätter du med en 3.x-version av paketet som stöds.

1. Anropa [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) metoden [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)på . Metoden [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) lägger till konsolloggning i konfigurationen.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    Metoden `Main` ser nu ut så här:

    ```cs
    static async Task Main()
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
            await host.RunAsync();
        }
    }
    ```

    Den här uppdateringen gör följande:

    * Inaktiverar [instrumentpanelsloggning](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Instrumentpanelen är ett äldre övervakningsverktyg och instrumentpanelsloggning rekommenderas inte för produktionsscenarier med högt dataflöde.
    * Lägger till konsolleverantören med [standardfiltrering](webjobs-sdk-how-to.md#log-filtering).

Nu kan du lägga till en funktion som utlöses av meddelanden som anländer i en [Azure Storage-kö](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>Installera tillägget för Storage-bindning

Från och med version 3.x måste du uttryckligen installera det lagringsbindningstillägg som krävs av WebJobs SDK. I tidigare versioner inkluderades lagringsbindningarna i SDK.

1. Installera den senaste stabila versionen av [Paketet Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet, version 3.x. 

    Här är kommandot **Package Manager Console:**

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    Ersätt `<3_X_VERSION>` med en version av paketet som stöds i det här kommandot. 

2. Anropa `ConfigureWebJobs` metoden på instansen i [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) tilläggsmetoden `AddAzureStorage` för att initiera lagringstillägget. Nu ser `ConfigureWebJobs` metoden ut som följande exempel:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Skapa en funktion

1. Högerklicka på projektet, välj **Lägg till** > **nytt objekt...**, välj **Klass**, namnge den nya C#-klassfilen *Functions.cs*och välj **Lägg till**.

1. I Functions.cs ersätter du den genererade mallen med följande kod:
    
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

   Attributet `QueueTrigger` talar om för körningen att anropa den här funktionen `queue`när ett nytt meddelande skrivs på en Azure Storage-kö som heter . Innehållet i kömeddelandet anges till metodkoden `message` i parametern. Brödtexten i metoden är där du bearbetar utlösardata. I det här exemplet loggar koden bara meddelandet.

   Parametern `message` behöver inte vara en sträng. Du kan också binda till ett JSON-objekt, en bytematris eller ett [CloudQueueMessage-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) [Se Användning av köutlösare](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Varje bindningstyp (till exempel köer, blobbar eller tabeller) har en annan uppsättning parametertyper som du kan binda till.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Azure Storage-emulatorn som körs lokalt har inte alla funktioner som WebJobs SDK behöver. Så i det här avsnittet skapar du ett lagringskonto i Azure och konfigurerar projektet för att använda det. Om du redan har ett lagringskonto hoppar du ner till steg 6.

1. Öppna **Server Explorer** i Visual Studio och logga in på Azure. Högerklicka på **Azure-noden** och välj sedan **Anslut till Microsoft Azure-prenumeration**.

   ![Logga in på Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Högerklicka på **Lagring**under **Azure-noden** i Server **Explorer**och välj sedan Skapa **lagringskonto**.

   ![Menyn Skapa lagringskonto](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Ange ett unikt namn för lagringskontot i dialogrutan **Skapa lagringskonto.**

1. Välj samma **region** som du skapade apptjänstens app i eller en region nära dig.

1. Välj **Skapa**.

   ![Skapa lagringskonto](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Välj det nya lagringskontot under **noden Lagring** i **Server Explorer.** Välj ellipsen (**...**) till höger i värdefältet **Anslutningssträng** i fönstret **Egenskaper.**

   ![Ellips för anslutningssträng](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Kopiera anslutningssträngen och spara det här värdet någonstans där du enkelt kan kopiera det igen.

   ![Kopiera anslutningssträng](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Konfigurera lagring så att den körs lokalt

WebJobs SDK söker efter lagringsanslutningssträngen i programinställningarna i Azure. När du kör lokalt söker den efter det här värdet i den lokala konfigurationsfilen eller i miljövariabler.

1. Högerklicka på projektet, välj **Lägg till** > **nytt objekt...**, välj **JavaScript JSON-konfigurationsfil**, namnge den nya filen *appsettings.json* och välj **Lägg till**. 

1. Lägg till ett `AzureWebJobsStorage` fält i den nya filen, som i följande exempel:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Ersätt *{storage connection string}* med anslutningssträngen som du kopierade tidigare.

1. Markera *filen appsettings.json* i Solution Explorer och ange **Kopiera till utdatakatalogen** i **fönstret** **Nyare**.

Senare lägger du till samma inställning för anslutningssträngappar i din app i Azure App Service.

## <a name="test-locally"></a>Testa lokalt

I det här avsnittet skapar och kör du projektet lokalt och utlöser funktionen genom att skapa ett kömeddelande.

1. Tryck på **Ctrl+F5** för att köra projektet.

   Konsolen visar att körningen hittade din funktion och väntar på att kömeddelanden ska utlösa den. Följande utdata genereras av v3.x-värden:

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

1. Expandera noden för det nya lagringskontot i **Server Explorer** i Visual Studio och högerklicka sedan på **Köer**.

1. Välj **Skapa kö**.

1. Ange *kön* som namnet på kön och välj sedan **OK**.

   ![Skapa kö](./media/webjobs-sdk-get-started/create-queue.png)

1. Högerklicka på noden för den nya kön och välj sedan **Visa kö**.

1. Välj ikonen **Lägg till meddelande.**

   ![Skapa kö](./media/webjobs-sdk-get-started/create-queue-message.png)

1. I dialogrutan **Lägg till meddelande** anger du Hello *World!* som **meddelandetext**och välj sedan **OK**. Det finns nu ett meddelande i kön.

   ![Skapa kö](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Kör projektet igen.

   Eftersom du `QueueTrigger` har använt `ProcessQueueMessage` attributet i funktionen lyssnar WeJobs SDK-körningen efter kömeddelanden när det startar. Den hittar ett nytt kömeddelande i kön med namnet *kö* och anropar funktionen.

   På grund av [köavsökning exponentiell backoff](../azure-functions/functions-bindings-storage-queue-trigger.md#polling-algorithm)kan det ta upp till 2 minuter för körningen att hitta meddelandet och anropa funktionen. Den här väntetiden kan minskas genom att köra i [utvecklingsläge](webjobs-sdk-how-to.md#host-development-settings).

   Konsolen utgång ser ut så här:

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

1. Gå tillbaka till köfönstret och uppdatera det. Meddelandet är borta, eftersom det har bearbetats av din funktion som körs lokalt. 

## <a name="add-application-insights-logging"></a>Lägga till programinsiktsloggning

När projektet körs i Azure kan du inte övervaka funktionskörningen genom att visa konsolutdata. Den övervakningslösning vi rekommenderar är [Application Insights](../azure-monitor/app/app-insights-overview.md). Mer information finns i [Övervaka Azure-funktioner](../azure-functions/functions-monitoring.md).

I det här avsnittet gör du följande uppgifter för att konfigurera Application Insights-loggning innan du distribuerar till Azure:

* Kontrollera att du har en App Service-app och en Application Insights-instans att arbeta med.
* Konfigurera App Service-appen så att den använder application insights-instansen och lagringskontot som du skapade tidigare.
* Ställ in projektet för loggning till Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Skapa apptjänstapp och application insights-instans

1. Om du inte redan har en App Service-app som du kan använda skapar du [en](app-service-web-get-started-dotnet-framework.md). När du skapar appen kan du också skapa en ansluten Application Insights-resurs. När du gör `APPINSIGHTS_INSTRUMENTATIONKEY` detta är den inställd för dig i din app.

1. Om du inte redan har en Application Insights-resurs som du kan använda skapar du [en](../azure-monitor/app/create-new-resource.md ). Ange **programtyp** till **Allmänt**och hoppa över de avsnitt som följer **Kopiera instrumenteringsnyckeln**.

1. Om du redan har en Application Insights-resurs som du vill använda [kopierar du instrumenteringsnyckeln](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurera appinställningar 

1. Expandera **noden App Service** i **Server Explorer** i Visual Studio under **Azure**.

1. Expandera resursgruppen som apptjänstappen finns i och högerklicka sedan på appen App Service.

1. Välj **Visa inställningar**.

1. Lägg till följande post i rutan **Anslutningssträngar.**

   |Namn  |Anslutningssträng  |Databastyp|
   |---------|---------|------|
   |AzureWebJobsStorage | {Anslutningssträngen för lagring som du kopierade tidigare}|Anpassat|

1. Om rutan **Programinställningar** inte har instrumenteringsnyckeln Application Insights lägger du till den som du kopierade tidigare. (Instrumenteringsnyckeln kanske redan finns där, beroende på hur du skapade App Service-appen.)

   |Namn  |Värde  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumenteringsnyckel} |

1. Ersätt *{instrumenteringsnyckel}* med instrumenteringsnyckeln från application insights-resursen som du använder.

1. Välj **Spara**.

1. Lägg till Application Insights-anslutningen i projektet så att du kan köra den lokalt. Lägg till ett `APPINSIGHTS_INSTRUMENTATIONKEY` fält i filen *appsettings.json,* som i följande exempel:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Ersätt *{instrumenteringsnyckel}* med instrumenteringsnyckeln från application insights-resursen som du använder.

1. Spara ändringarna.

### <a name="add-application-insights-logging-provider"></a>Lägga till loggningsleverantör för programinsikter

Om du vill dra nytta av [application insights-loggning](../azure-monitor/app/app-insights-overview.md) uppdaterar du loggningskoden så att du kan göra följande:

* Lägga till en programstatistikloggningsleverantör med [standardfiltrering](webjobs-sdk-how-to.md#log-filtering). När du kör lokalt skrivs all information och loggar på högre nivå till både konsolen och Application Insights.
* Placera [LoggerFactory-objektet](./webjobs-sdk-how-to.md#logging-and-monitoring) `using` i ett block för att säkerställa att loggutdata rensas när värden avslutas.

1. Installera den senaste stabila 3.x-versionen av [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

   Här är kommandot **Package Manager Console:**

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    Ersätt `<3_X_VERSION>` med en version av paketet som stöds i det här kommandot.

1. Öppna *Program.cs* och ersätt koden `Main` i metoden med följande kod:

    ```cs
    static async Task Main()
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
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Detta lägger till Application Insights-leverantören i loggningen med hjälp av nyckeln som du lade till tidigare i appinställningarna.

## <a name="test-application-insights-logging"></a>Logga in i Loggning av testprograminsikter

I det här avsnittet kör du lokalt igen för att kontrollera att loggningsdata nu går till Application Insights samt till konsolen.

1. Använd **Server Explorer** i Visual Studio för att skapa ett kömeddelande som du gjorde [tidigare](#test-locally), förutom att ange Hello *App Insights!* som meddelandetexten.

1. Kör projektet.

   WebJobs SDK bearbetar kömeddelandet och loggarna i konsolfönstret.

1. Stäng konsolfönstret.

1. Gå till [Azure-portalen](https://portal.azure.com/) för att visa din Application Insights-resurs. Sök efter och välj **Application Insights**.

1. Välj din Application Insights-instans.

1. Välj **Sök**.

   ![Välj Sök](./media/webjobs-sdk-get-started/select-search.png)

1. Om du inte ser *Hello App Insights!* väljer du **Uppdatera** regelbundet i flera minuter. (Loggar visas inte omedelbart, eftersom det tar ett tag för Application Insights-klienten att rensa loggarna som bearbetas.)

   ![Loggar in Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Stäng konsolfönstret.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Distribuera till Azure

Under distributionen skapar du en apptjänstinstans där du kan köra dina funktioner. När du publicerar en .NET Core-konsolapp till App Service i Azure körs den automatiskt som ett WebJob.When you publish a .NET Core console app to App Service in Azure, it automatically gets run as a WebJob. Mer information om publicering finns i [Utveckla och distribuera WebJobs med Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Utlösa funktionen i Azure

1. Kontrollera att du inte kör lokalt (stäng konsolfönstret om det fortfarande är öppet). Annars kan den lokala instansen vara den första att bearbeta alla kömeddelanden som du skapar.

1. Lägg till ett meddelande i kön som tidigare på **sidan Kö** i Visual Studio.

1. Uppdatera **sidan Kö** och det nya meddelandet försvinner eftersom det har bearbetats av funktionen som körs i Azure.

   > [!TIP]
   > När du testar i Azure använder du [utvecklingsläge](webjobs-sdk-how-to.md#host-development-settings) för att säkerställa att en köutlösarefunktion anropas direkt och undvik förseningar på grund av [köavsökning exponentiell backoff](../azure-functions/functions-bindings-storage-queue-trigger.md#polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Visa loggar i Application Insights

1. Öppna [Azure-portalen](https://portal.azure.com/)och gå till din Application Insights-resurs.

1. Välj **Sök**.

1. Om du inte ser *Hello Azure!* väljer du **Uppdatera** regelbundet i flera minuter.

   Du ser loggarna från funktionen som körs i ett WebJob, inklusive *Hello Azure!* text som du angav i föregående avsnitt.

## <a name="add-an-input-binding"></a>Lägga till en indatabindning

Indatabindningar förenklar kod som läser data. I det här exemplet blir kömeddelandet ett blobnamn och du använder blobnamnet för att hitta och läsa en blob i Azure Storage.

1. I *Functions.cs*ersätter du `ProcessQueueMessage` metoden med följande kod:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   I den `queueTrigger` här koden är ett [bindande uttryck](../azure-functions/functions-bindings-expressions-patterns.md), vilket innebär att det löser till ett annat värde vid körning.  Vid körning har den innehållet i kömeddelandet.

1. Lägg `using`till en :

   ```cs
   using System.IO;
   ```

1. Skapa en blob-behållare i ditt lagringskonto.

   a. Expandera noden för ditt lagringskonto i **Server Explorer** i Visual Studio, högerklicka på **Blobbar**och välj sedan **Skapa Blob Container**.

   b. Ange *behållaren* som behållarnamn i dialogrutan **Skapa blob-behållare** och klicka sedan på **OK**.

1. Ladda upp *Program.cs-filen* till blob-behållaren. (Den här filen används här som ett exempel, du kan ladda upp en textfil och skapa ett kömeddelande med filens namn.)

   a. Dubbelklicka på noden för behållaren som du skapade i **Server Explorer.**

   b. Välj knappen **Ladda upp** i **fönstret Behållare.**

   ![Knappen Blob-uppladdning](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Sök efter och välj *Program.cs*och välj sedan **OK**.

1. Skapa ett kömeddelande i kön som du skapade tidigare, med *Program.cs* som texten i meddelandet.

   ![Kömeddelande Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Kör projektet lokalt.

   Kömeddelandet utlöser funktionen, som sedan läser bloben och loggar dess längd. Konsolen utgång ser ut så här:

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

Utdatabindningar förenklar kod som skriver data. Det här exemplet ändrar det föregående genom att skriva en kopia av blobben i stället för att logga dess storlek. Blob-lagringsbindningar ingår i Azure Storage-tilläggspaketet som vi installerade tidigare.

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

1. Skapa ett annat kömeddelande med *Program.cs* som texten i meddelandet.

1. Kör projektet lokalt.

   Kömeddelandet utlöser funktionen, som sedan läser bloben, loggar dess längd och skapar en ny blob. Konsolutdata är desamma, men när du går till blob-behållarfönstret och väljer **Uppdatera**visas en ny blob med namnet *copy-Program.cs.*

## <a name="republish-the-updates-to-azure"></a>Publicera om uppdateringarna till Azure

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. Kontrollera att den aktuella profilen är markerad i dialogrutan **Publicera** och välj sedan **Publicera**. Resultaten av publiceringen beskrivs i **utdatafönstret.**
 
1. Verifiera funktionen i Azure genom att ladda upp en fil igen till blob-behållaren och lägga till ett meddelande i kön som är namnet på den uppladdade filen. Meddelandet tas bort från kön och en kopia av filen som skapats i blob-behållaren. 

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du skapar, kör och distribuerar ett WebJobs SDK 3.x-projekt.

> [!div class="nextstepaction"]
> [Läs mer om WebJobs SDK](webjobs-sdk-how-to.md)
