---
title: Kom igång med WebJobs-SDK - Azure
description: Introduktion till WebJobs-SDK för händelsedrivna Bakgrundsbearbetning. Lär dig hur du kommer åt data i Azure-tjänster och tjänster från tredje part.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: d9f3685e0a3961631f1577827cc415cb91d38a15
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074248"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Kom igång med Azure WebJobs-SDK för händelsedrivna Bakgrundsbearbetning

Den här artikeln visar hur du skapar ett projekt med Azure WebJobs SDK, köra det lokalt och distribuera den till Azure App Service.

Anvisningarna är för [Visual Studio 2017](https://www.visualstudio.com/vs/), men samma uppgifter kan utföras med andra verktyg, till exempel [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-azure-webjobs-sdk"></a>Vad är Azure WebJobs-SDK?

Azure WebJobs SDK är ett ramverk som förenklar uppgiften att skriva kod i bakgrunden bearbetning som har åtkomst till data i Azure-tjänster. SDK: N har en deklarativ syntax för att ange händelser som ska utlösa en funktion, till exempel ett nytt meddelande som lagts till i en kö. Liknande deklarativ syntax styr läsning och skrivning av data när en funktion som har utlösts. Det här systemet av utlösare och bindningar hand tar om de flesta på låg nivå kodning uppgifter som är associerade med att komma åt Azure och tjänster från tredje part.

### <a name="functions-triggers-and-bindings"></a>Funktioner, utlösare och bindningar

Ett WebJobs-SDK-projekt definierar en eller flera *functions*. En funktion är en metod som har en utlösare-attributet i dess signaturen för metoden. Ange villkor för att anropa en funktion för utlösare och bindningar ange vad du kan läsa och skriva. Till exempel utlösaren attributet i följande funktion talar om för körningen att anropa funktionen när ett kömeddelande som visas i den `items` kö. Den `Blob` attributet anger att körningen kan använda kömeddelandet för att läsa en blob i den *workitems* behållare. Innehållet i kömeddelandet &mdash; i den `queueTrigger` parametern &mdash; är namnet på blobben.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>Versioner 2.x och 3.x

Anvisningarna för hur du skapar ett projekt med WebJobs SDK version 2.x. Den senaste versionen av WebJobs SDK är 3.x, men det är för närvarande preliminärt och den här artikeln har inte instruktioner för den här versionen ännu. Den huvudsakliga ändringen som introduceras med version 3.x är användningen av .NET Core i stället för .NET Framework.

### <a name="azure-functions"></a>Azure Functions

[Azure Functions](../azure-functions/functions-overview.md) bygger på WebJobs SDK och är ett alternativ när du inte behöver använda WebJobs-SDK direkt. Azure Functions 1.x använder WebJobs SDK 2.x. Mer information finns i [jämförelse mellan Azure Functions och WebJobs SDK](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har [ett Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) och erfarenhet av [appar i Azure App Service](overview.md). För att slutföra stegen i den här artikeln:

* [Installera Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) med den **Azure development** arbetsbelastning. Om du redan har Visual Studio men inte har den arbetsbelastningen, lägger du till arbetsbelastningen genom att välja **Verktyg > Hämta verktyg och funktioner**.
* [Skapa en App Service-app](app-service-web-get-started-dotnet-framework.md). Om du redan har en som du kan distribuera ett Webbjobb ska använda du som istället för att skapa en ny.

## <a name="create-a-project"></a>Skapa ett projekt

1. I Visual Studio väljer **Arkiv > Nytt projekt**.

2. Välj **Windows Classic Desktop > konsolprogram (.NET Framework)**.

3. Ge projektet namnet *WebJobsSDKSample*, och välj sedan **OK**.

   ![Dialogrutan Nytt projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Lägg till WebJobs-NuGet-paketet

1. Installera den senaste stabila 2.x-versionen av NuGet-paketet `Microsoft.Azure.WebJobs`.
 
   Här är den **Pakethanterarkonsolen** kommandot för version 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>Skapa JobHost

Den `JobHost` objektet är körningsbehållare för functions: den lyssnar efter utlösare och-anrop. 

1. I *Program.cs*, lägga till en `using` instruktionen:

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. Ersätt metoden `Main` med följande kod:

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="enable-console-logging"></a>Aktivera loggning för konsolen

Det finns flera alternativ för att logga in WebJobs-SDK-projektet. Den som vi rekommenderar är den [loggningsramverk som har utvecklats specifikt för ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging). Det här ramverket erbjuder bättre prestanda och mer flexibilitet vid lagringsmedia och filtrering. 

I det här avsnittet ska ställa du in loggning i konsolen som använder det nya ramverket.

1. Installera den senaste stabila versionen av följande NuGet-paket:

   * `Microsoft.Extensions.Logging` – Vilket loggningsramverk.
   * `Microsoft.Extensions.Logging.Console` -Konsolen *provider*. En provider skickar loggar till ett visst mål i det här fallet till konsolen. 
 
   Här är den **Pakethanterarkonsolen** kommandon för version 2.0.1:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

1. I *Program.cs*, lägga till en `using` instruktionen:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. I den `Main` metoden lägger du till kod för att uppdatera den `JobHostConfiguration` innan du skapar den `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Den här koden gör följande ändringar:

   * Inaktiverar [instrumentpanelen loggning](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Instrumentpanelen är en äldre övervakning verktyget och instrumentpanelen loggning rekommenderas inte för stora dataflöden produktionsscenarier.
   * Lägger till konsolen providern med standard [filtrering](webjobs-sdk-how-to.md#log-filtering). 

   Den `Main` metoden ser nu ut så här:

   ```
   var config = new JobHostConfiguration();
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   var host = new JobHost(config);
   host.RunAndBlock();
   ```
   
## <a name="create-a-function"></a>Skapa en funktion

1. Skapa *Functions.cs* i projektmappen, och Ersätt mallkoden med den här koden:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Host;
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

Azure Storage-emulatorn som körs lokalt har inte alla funktioner som krävs för WebJobs SDK. Så i det här avsnittet du skapar ett lagringskonto i Azure och konfigurera projektet för att använda den.

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

## <a name="configure-storage-for-running-locally"></a>Konfigurera lagring för körs lokalt

WebJobs SDK söker efter lagringsanslutningssträngen i samlingen Appinställningar. När du kör lokalt, det ser ut för det här värdet i den *App.config* fil eller miljö variabler.

1. Lägg till följande XML-filen till den *App.config* filen omedelbart efter att `<configuration>` tagg.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. Ersätt *{lagringsanslutningssträng}* med den anslutningssträng som du kopierade tidigare.

   Senare ska du använda anslutningssträngen igen när du konfigurerar App Service-app i Azure.

## <a name="test-locally"></a>Testa lokalt

I det här avsnittet skapar du och köra projektet lokalt och Utlös funktionen genom att skapa ett kömeddelande.

1. Tryck på Ctrl + F5 för att köra projektet.

   Konsolen visar att körningen gick att hitta din funktion och väntar på Kömeddelanden som utlöser den.

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   ```

   Du kan se ett varningsmeddelande om en `ServicePointManager` inställningen. För testning du kommer att göra med det här projektet, kan du ignorera varningen. Mer information om varningen finns [hur du använder WebJobs-SDK](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

1. Stäng konsolfönstret.

1. I **Server Explorer** expanderar du noden för det nya kontot i Visual Studio och högerklicka på **köer**. 

1. Välj **Skapa kö**. 

1. Ange *kö* som namn på kön och välj sedan **OK**.

   ![Skapa kö](./media/webjobs-sdk-get-started/create-queue.png)

1. Högerklicka på noden för den nya kön och välj sedan **Visa kö**.

1. Välj den **Lägg till meddelande** ikon.

   ![Skapa kö](./media/webjobs-sdk-get-started/create-queue-message.png)

1. I den **Lägg till meddelande** dialogrutan Ange *Hello World!* som den **meddelandetext**, och välj sedan **OK**.

   ![Skapa kö](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Kör projektet igen.

   Eftersom du har använt den `QueueTrigger` attribut i den `ProcessQueueMessage` funktion, WeJobs SDK-körning lyssnar efter meddelanden i kön när den startas. Ett nytt kömeddelande hittas i kön med namnet *kö* och anropar funktionen.

   På grund av [kö avsökning exponentiell backoff](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), det kan ta upp till 2 minuter för körning för att hitta meddelandet och anropa funktionen. Den här väntetid kan minskas genom att köra i [utvecklingsläge](webjobs-sdk-how-to.md#jobhost-development-settings).

  Konsolens utdata ser ut så här:

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   info: Function[0]
         Hello World!
   info: Host.Results[0]
         Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   ```

1. Stäng konsolfönstret.

## <a name="add-application-insights-logging"></a>Lägg till Application Insights-loggning

När projektet körs i Azure, kan du övervaka körning av funktion genom att granska konsolutdata. Övervakningslösning som vi rekommenderar att du är [Application Insights](../application-insights/app-insights-overview.md). Mer information finns i [övervaka Azure Functions](../azure-functions/functions-monitoring.md).

I det här avsnittet ska göra du följande uppgifter för att konfigurera Application Insights loggning innan du distribuerar till Azure:

* Kontrollera att du har en App Service-app och en Application Insights-instans för att arbeta med.
* Konfigurera App Service-appen om du vill använda Application Insights-instans och storage-kontot som du skapade tidigare.
* Konfigurera projektet för loggning till Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Skapa App Service-app och Application Insights-instans

1. Om du inte redan har en App Service-app som du kan använda [skapar ett](app-service-web-get-started-dotnet-framework.md).

1. Om du inte redan har en Application Insights-resurs som du kan använda [skapar ett](../azure-monitor/app/create-new-resource.md ). Ange **programtyp** till **Allmänt**, och hoppa över avsnitten som följer **kopierar du instrumentationsnyckeln**.

1. Om du redan har en Application Insights-resurs som du vill använda, [kopierar du instrumentationsnyckeln](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key).

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

1. Lägg till följande XML-filen till den *App.config* filen omedelbart efter anslutningen strängar samlingen.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

1. Ersätt *{instrumenteringsnyckeln}* med instrumenteringsnyckeln från Application Insights-resurs som du använder.

   Att lägga till dessa data till den *App.config* filen kan du testa Application Insights-anslutning när du kör projektet lokalt. 

1. Spara ändringarna.

### <a name="add-application-insights-logging-provider"></a>Lägg till Application Insights loggningsprovider

1. Installera den senaste stabila 2.x-versionen av NuGet-paketet för Application Insights loggningsprovider: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Här är den **Pakethanterarkonsolen** kommandot för version 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Installera den senaste stabila 4.x-versionen av NuGet-paketet för .NET configuration manager: `System.Configuration.ConfigurationManager`.

   Här är den **Pakethanterarkonsolen** kommandot för version 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. Öppna *Program.cs* och Lägg till en `using` instruktionen för configuration manager:

   ```csharp
   using System.Configuration;
   ```

1. Ersätt Koden i den `Main` metoden med följande kod:

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

   Den här koden gör följande ändringar:

   * Lägger till en Application Insights loggningsprovider med standard [filtrering](webjobs-sdk-how-to.md#log-filtering); all Information och högre nivån loggar går nu både konsolen och Application Insights när du kör lokalt. 
   * Placerar den `LoggerFactory` objekt i en `using` block så att loggutdata rensas när värden avslutas. 

## <a name="test-application-insights-logging"></a>Testa Application Insights-loggning

I det här avsnittet kör du lokalt igen för att verifiera att loggningsdata nu kommer att Application Insights även om konsolen.

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

## <a name="deploy-as-a-webjob"></a>Distribuera som ett WebJob

I det här avsnittet distribuerar du projektet som ett WebJob. Du distribuerar den till en App Service-app som du [skapade tidigare](#create-app-service-app-and-application-insights-instance). Om du vill testa din kod medan den körs i Azure, kommer du utlöser ett funktionsanrop genom att skapa ett kömeddelande.

1. I **Solution Explorer**, högerklicka på projektet och välj sedan **Publicera som Azure WebJob**.

1. I den **lägga till Azure-Webbjobb** dialogrutan **OK**.

   ![Lägg till Azure WebJob](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio installeras automatiskt NuGet-paket för WebJob publicering.

1. I den **profil** steg i den **publicera** väljer **Microsoft Azure App Service**.

   ![Dialogrutan Publicera](./media/webjobs-sdk-get-started/publish-dialog.png)

1. I den **Apptjänst** dialogrutan **din resursgrupp > App Service-appen**, och välj sedan **OK**.

   ![Dialogrutan för Apptjänst](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. I den **anslutning** steg i guiden väljer **publicera**.

## <a name="trigger-the-function-in-azure"></a>Utlös funktionen i Azure

1. Kontrollera att du inte kör lokalt (Stäng konsolfönstret om det fortfarande är öppen). Annars kan den lokala instansen var först med att bearbeta alla kömeddelanden som du skapar.


1. Uppdatera den **kö** sida i Visual Studio och det nya meddelandet har tagits bort eftersom den funktion som körs i Azure App Service bearbetas den.

   > [!TIP]
   > När du testar i Azure, använda [utvecklingsläge](webjobs-sdk-how-to.md#jobhost-development-settings) att säkerställa att en funktion för en kö-utlösare anropas direkt och undvika förseningar på grund av [kö avsökning exponentiell backoff](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

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

   I den här koden `queueTrigger` är en [bindning uttryck](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), vilket innebär att det matchar ett annat värde vid körning.  Vid körning har innehållet i kömeddelandet.

1. Lägg till en `using`:

   ```cs
   using System.IO;
   ```

1. Skapa en blobbehållare i ditt storage-konto.

   a. I **Server Explorer** expanderar du noden för ditt lagringskonto i Visual Studio, högerklicka på **Blobar**, och välj sedan **skapa Blobbehållare**.

   b. I den **skapa Blobbehållare** dialogrutan Ange *behållare* som behållarens namn och klicka sedan på **OK**.

1. Ladda upp den *Program.cs* till blobbehållaren. (Den här filen används här som ett exempel; du kan ladda upp en textfil och skapa ett kömeddelande med filens namn.)

   a. I **Server Explorer**, dubbelklickar du på noden för den behållare som du nyss skapade.

   b. I den **behållare** väljer den **överför** knappen.

   ![Ladda upp BLOB-knappen](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Hitta och välja *Program.cs*, och välj sedan **OK**.

1. Skapa ett kömeddelande i kön som du skapade tidigare, med *Program.cs* som meddelandets text.

   ![Kömeddelande Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Kör projektet.

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

Utdatabindningar förenkla kod som skriver data. Det här exemplet ändrar det föregående genom att skriva en kopia av blob i stället för loggning av dess storlek.

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

1. Kör projektet.

   Kömeddelandet utlöser funktionen som sedan läser blob, loggar dess längd och skapar en ny blob. Konsolens utdata är detsamma, men när du gå till fönstret blob-behållare och väljer **uppdatera**, visas en ny blob med namnet *kopiera Program.cs.*

## <a name="next-steps"></a>Nästa steg

Den här guiden visar hur du skapar, kör och distribuera ett WebJobs-SDK-projekt.

För att visa allt som hamnar i ett projekt med WebJobs SDK, hade anvisningarna du skapa ett projekt från grunden. Men när du skapar ditt nästa projekt kan du överväga att använda den **Azure WebJob** mallen i den **molnet** kategori. Den här mallen skapar ett projekt med NuGet-paket och exempelkod som redan har konfigurerat. Observera att exempelkoden kan behöva ändras för att använda den nya loggningsramverk.

Mer information finns i [hur du använder WebJobs-SDK](webjobs-sdk-how-to.md).
