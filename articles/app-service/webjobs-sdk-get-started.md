---
title: Kom igång med Azure WebJobs SDK
description: Introduktion till WebJobs-SDK för händelsedriven Bakgrundsbearbetning. Lär dig hur du kommer åt data i Azure-tjänster och tjänster från tredje part.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: d0afc8b6f8e0b7ef73e5d1c3cbabf2e1542f47f4
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234534"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Kom igång med Azure WebJobs-SDK för händelsedriven Bakgrundsbearbetning

Den här artikeln visar hur du skapar ett Azure WebJobs SDK-projekt, kör lokalt och distribuera den till Azure App Service.

Anvisningarna gäller [Visual Studio 2017](https://www.visualstudio.com/vs/), men samma uppgifter kan utföras med andra verktyg som [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-azure-webjobs-sdk"></a>Vad är Azure WebJobs-SDK?

Azure WebJobs-SDK är ett ramverk som gör det enklare att skriva bakgrund bearbetningskoden som ansluter till data i Azure-tjänster. SDK innehåller deklarativa syntaxen för att ange händelser som ska utlösa en funktion, t.ex ett nytt meddelande som lagts till i en kö. Liknande deklarativa syntaxen styr läsning och skrivning av data när en funktion som har utlösts. Det här systemet för utlösare och bindningar hand tar om de flesta lågnivå-kodning uppgifter som är associerade med åtkomst till Azure och tjänster från tredje part.

### <a name="functions-triggers-and-bindings"></a>Funktioner, utlösare och bindningar

En WebJobs-SDK-projektet definierar en eller flera *funktioner*. En funktion är en metod som har attributet utlösare i dess metodsignatur. Ange villkor för att anropa en funktion för utlösare och bindningar anger vad som kan läsa och skriva. Till exempel utlösaren-attributet i följande funktion anger runtime att anropa funktionen när ett meddelande i kön visas i den `items` kön. Den `Blob` attributet anger att körningen ska använda kömeddelandet för att läsa en blobb i den *workitems* behållare. Innehållet i kön meddelandet &mdash; enligt den `queueTrigger` parametern &mdash; är namnet på blob.

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

Instruktionerna för hur du skapar ett WebJobs SDK version 2.x-projekt med information om vad som är olika för 3.x (under förhandsgranskning). Huvudsakliga ändringen introducerades av 3.x är att använda .NET Core i stället för .NET Framework.

### <a name="azure-functions"></a>Azure Functions

[Azure Functions](../azure-functions/functions-overview.md) baseras på WebJobs-SDK och är ett alternativ när du inte behöver använda WebJobs-SDK direkt. Azure Functions 1.x använder WebJobs SDK 2.x. Mer information finns i [jämförelse mellan Azure Functions och WebJobs SDK](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har [ett Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) och erfarenhet av [appar i Azure App Service](app-service-web-overview.md). Så här slutför stegen i den här artikeln:

* [Installera Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) med den **Azure-utveckling** arbetsbelastning. Om du redan har Visual Studio inte men att arbetsbelastningar, lägger du till arbetsbelastningen genom att välja **Verktyg > Hämta verktyg och funktioner**.
* [Skapa en Apptjänst-app](app-service-web-get-started-dotnet-framework.md). Om du redan har en som du kan distribuera ett Webbjobb att kan du använda som i stället för att skapa en ny.

## <a name="create-a-project"></a>Skapa ett projekt

1. I Visual Studio väljer **Arkiv > Nytt projekt**.

2. Välj **klassiska Windows-skrivbordet > konsolen App (.NET Framework)**.

   Om du vill skapa ett 3.x-projekt, Välj **.NET Core > konsolprogram (.NET Core)**.

3. Namnge projektet *WebJobsSDKSample*, och välj sedan **OK**.

   ![Dialogrutan Nytt projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Lägg till WebJobs-NuGet-paketet

1. Installera den senaste stabila 2.x-versionen av NuGet-paketet `Microsoft.Azure.WebJobs`. (För WebJobs SDK 3.x, väljer du den senaste versionen av 3.x.)
 
   Här är den **Pakethanterarkonsolen** kommandot för version 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>Skapa JobHost

Den `JobHost` objektet är körningsbehållaren för funktioner: den lyssnar efter utlösare och anrop funktioner. 

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

## <a name="enable-console-logging"></a>Aktivera loggning i konsolen

Det finns flera alternativ för att logga in WebJobs SDK-projektet. En rekommenderar vi den [loggningsramverk som utvecklats för ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging). Det här ramverket ger bättre prestanda och mer flexibilitet i lagringsmedia och filtrering. 

I det här avsnittet kan du ställa in konsolen loggning som använder det nya ramverket.

1. Installera den senaste säkra versionen för följande NuGet-paket:

   * `Microsoft.Extensions.Logging` -Loggning ramen.
   * `Microsoft.Extensions.Logging.Console` -Konsolen *provider*. En provider skickar loggar till ett visst mål i det här fallet till konsolen. 
 
   Här är den **Pakethanterarkonsolen** kommandon för version 2.0.1:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

2. I *Program.cs*, lägga till en `using` instruktionen:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

3. I den `Main` metoden lägger du till kod för att uppdatera den `JobHostConfiguration` innan du skapar den `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Den här koden gör följande ändringar:

   * Inaktiverar [instrumentpanelen loggning](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Instrumentpanelen är en äldre övervakning verktyget och instrumentpanelen loggning rekommenderas inte för scenarier med hög genomströmning produktion.
   * Lägger till konsolen provider med standard [filtrering](webjobs-sdk-how-to.md#log-filtering). 

   Den `Main` metoden nu ser ut så här:

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

1. Skapa *Functions.cs* i projektmappen och Ersätt Koden mallen med den här koden:

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

   Den `QueueTrigger` attributet anger runtime att anropa den här funktionen när ett nytt meddelande skrivs på ett Azure Storage-kö som kallas `queue`. Innehållet i kön meddelandet har angetts till metoden koden i den `message` parameter. Meddelandetexten för metoden är där du bearbetar data för utlösare. I det här exemplet loggar koden bara meddelandet.

   Den `message` parametern behöver inte vara en sträng. Du kan också binda till en JSON-objekt, en bytematris eller en [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) objekt. [Se kö utlösaren användning](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Varje bindningstyp (till exempel köer, blobbar och tabeller) har olika parametertyper som du kan binda till.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Azure Storage-emulatorn som körs lokalt har inte alla funktioner som WebJobs SDK behöver. Så i det här avsnittet du skapar ett lagringskonto i Azure och konfigurera projektet för att använda den.

1. Öppna **Server Explorer** och logga in på Azure. Högerklicka på den **Azure** och sedan väljer **Anslut till Microsoft Azure-prenumeration**.

   ![Logga in på Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Under den **Azure** nod i **Server Explorer**, högerklicka på **lagring**, och välj sedan **skapa lagringskonto**.

   ![Skapa Storage-konto-menyn](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

2. I den **skapa Lagringskonto** dialogrutan Ange ett unikt namn för lagringskontot.

3. Välj samma **Region** att du har skapat din Apptjänst-app i eller en region Stäng till dig.

1. Välj **Skapa**.

   ![Skapa lagringskonto](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Under den **lagring** nod i **Server Explorer**, Välj det nya lagringskontot. I den **egenskaper** fönstret Välj tre punkter (**...** ) till höger i den **anslutningssträngen** värdefältet.

   ![Anslutningen sträng ellips](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

2. Kopiera anslutningssträngen och spara det här värdet någonstans att du kan kopiera lätt igen.

   ![Kopiera anslutningssträngen](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>Konfigurera lagring för körs lokalt

WebJobs SDK söker efter lagringsanslutningssträngen i samlingen App-inställningar. När du kör lokalt för att hitta det här värdet i den *App.config* fil eller miljö variabler.

1. Lägg till följande XML-filen till den *App.config* filen direkt efter inledandet `<configuration>` tagg.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

2. Ersätt *{lagringsanslutningssträng}* med den anslutningssträng som du kopierade tidigare.

   Senare ska du använda anslutningssträngen igen när du konfigurerar Apptjänst-app i Azure.

## <a name="test-locally"></a>Testa lokalt

I det här avsnittet skapar du och köra projektet lokalt och utlösa funktionen genom att skapa ett meddelande i kön.

1. Tryck på Ctrl + F5 för att köra projektet.

   Konsolen visar att körningen hitta din funktion och väntar på Kömeddelanden som utlöser den.

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

   Du kan se en varning visas en `ServicePointManager` inställningen. Du kan ignorera varningen för testning du kommer att göra med det här projektet. Mer information om varningen finns [hur du använder WebJobs-SDK](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

2. Stäng fönstret.

1. I **Server Explorer**, expanderar du noden för det nya kontot och högerklicka sedan på **köer**. 

2. Välj **Skapa kö**. 

3. Ange *kön* som namn på kön och välj sedan **OK**.

   ![Skapa kö](./media/webjobs-sdk-get-started/create-queue.png)

4. Högerklicka på noden för den nya kön och välj sedan **Visa kö**.

5. Välj den **lägga till meddelandet** ikon.

   ![Skapa kö](./media/webjobs-sdk-get-started/create-queue-message.png)

6. I den **lägga till meddelandet** dialogrutan Ange *Hello World!* som det **meddelandetext**, och välj sedan **OK**.

   ![Skapa kö](./media/webjobs-sdk-get-started/hello-world-text.png)

7. Kör projektet igen.

   Eftersom du har använt den `QueueTrigger` attribut i den `ProcessQueueMessage` funktion, WeJobs SDK runtime lyssnar efter meddelanden i kö när den startas. Ett nytt kömeddelande hittas i kön med namnet *kön* och anropar funktionen.

   På grund av att [kön avsökning exponentiell backoff](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), kan det ta upp till 2 minuter för körningsmiljön att hitta meddelandet och anropa funktionen. Väntetiden kan minskas genom att köra i [utvecklingsläge](webjobs-sdk-how-to.md#jobhost-development-settings).

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

8. Stäng fönstret.

## <a name="add-application-insights-logging"></a>Lägg till Application Insights-loggning

När projektet körs i Azure, kan du övervaka körning av funktionen genom att visa konsolens utdata. Övervakningslösning rekommenderar vi [Programinsikter](../application-insights/app-insights-overview.md). Mer information finns i [övervakaren Azure Functions](../azure-functions/functions-monitoring.md).

I det här avsnittet kan du göra följande uppgifter för att konfigurera Application Insights loggning innan du distribuerar till Azure:

* Kontrollera att du har en Apptjänst-app och en Application Insights-instans för att arbeta med.
* Konfigurera App Service-appen för att använda Application Insights-instans och lagringskontot som du skapade tidigare.
* Konfigurera projektet för loggning till Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Skapa App Service-appen och Application Insights-instans

1. Om du inte redan har en Apptjänst-app som du kan använda [skapar du en](app-service-web-get-started-dotnet-framework.md).

2. Om du inte redan har en Application Insights-resurs som du kan använda [skapar du en](../application-insights/app-insights-create-new-resource.md). Ange **programtyp** till **allmänna**, och hoppa över avsnitten som följer **kopiera nyckeln instrumentation**.

3. Om du redan har en Application Insights-resurs som du vill använda, [kopiera nyckeln instrumentation](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurera appinställningar 

1. I **Server Explorer**, expandera den **Apptjänst** nod under **Azure**.

1. Expandera den resursgrupp som din App Service-appen är i och högerklicka Apptjänst-app.

3. Välj **visa inställningarna för**.

4. I den **anslutningssträngar** Lägg till följande post.

   |Namn  |Anslutningssträng  |Databastyp|
   |---------|---------|------|
   |AzureWebJobsStorage | {lagringsanslutningssträngen som du kopierade tidigare}|Anpassat|
   
6. Om den **programinställningar** rutan inte har en Application Insights instrumentation nyckeln, Lägg till en som du kopierade tidigare. (Instrumentation nyckeln kan redan vara det, beroende på hur du skapade App Service-appen.)

   |Namn  |Värde  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentation nyckeln} |

2. Ersätt *{instrumentation nyckeln}* med instrumentation nyckel från Application Insights-resurs som du använder.

2. Välj **Spara**.

1. Lägg till följande XML-filen till den *App.config* filen omedelbart efter anslutningen strängar samlingen.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

2. Ersätt *{instrumentation nyckeln}* med instrumentation nyckel från Application Insights-resurs som du använder.

   Att lägga till den här informationen till den *App.config* filen kan du testa Application Insights-anslutning när du kör projektet lokalt. 

3. Spara ändringarna.

### <a name="add-application-insights-logging-provider"></a>Lägg till Application Insights loggning provider

1. Installera den senaste stabila 2.x-versionen av NuGet-paket för Application Insights loggning-provider: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`. (För WebJobs SDK 3.x, Välj den senaste 3.x-versionen av paketet.)

   Här är den **Pakethanterarkonsolen** kommandot för version 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Installera den senaste stabila 4.x-versionen av NuGet-paketet för .NET configuration manager: `System.Configuration.ConfigurationManager`.

   Här är den **Pakethanterarkonsolen** kommandot för version 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

2. Öppna *Program.cs* och lägga till en `using` -instruktion för configuration manager:

   ```csharp
   using System.Configuration;
   ```

2. Ersätt Koden i den `Main` metoden med följande kod:

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

   * Lägger till en provider för Application Insights loggning med standard [filtrering](webjobs-sdk-how-to.md#log-filtering); all Information och högre nivå loggar nu går till både konsolen och Application Insights när du kör lokalt. 
   * Placerar den `LoggerFactory` objekt i en `using` block så att loggutdata rensas när värden avslutas. 

## <a name="test-application-insights-logging"></a>Testa Application Insights-loggning

I det här avsnittet kör du lokalt igen för att verifiera att loggningsdata nu kommer att Application Insights samt om konsolen.

1. Använd **Server Explorer** för att skapa ett kömeddelande, på samma sätt som du gjorde [tidigare](#trigger-the-function), förutom ange *Hello App Insights!* som meddelandetext.

1. Kör projektet.

   WebJobs SDK bearbetar kömeddelandet och du finns i loggarna i konsolfönstret.

1. Stäng fönstret.

1. Öppna den [Azure-portalen](https://portal.azure.com/), och gå till Application Insights-resurs.

2. Välj **Sök**.

   ![Välj sökning](./media/webjobs-sdk-get-started/select-search.png)

1. Om du inte ser den *Hello App Insights!* meddelandet markerar **uppdatera** regelbundet i flera minuter. (Loggar visas inte direkt eftersom det tar ett tag för Application Insights-klienten att rensa loggarna bearbetas.)

   ![Loggar i Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

3. Stäng fönstret.

## <a name="deploy-as-a-webjob"></a>Distribuera som ett Webbjobb

I det här avsnittet distribuera projektet som ett Webbjobb. Du distribuerar den till en Apptjänst-app som du [skapade tidigare](#create-app-service-app-and-application-insights-instance). Om du vill testa din kod medan den körs i Azure, ska du utlösa ett funktionsanrop genom att skapa ett meddelande i kön.

1. I **Solution Explorer**, högerklicka på projektet och välj sedan **Publicera som Azure Webjobs**.

1. I den **lägga till Azure Webjobs** markerar **OK**.

   ![Lägg till Azure Webbjobbet](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio installeras automatiskt NuGet-paketet för Webbjobb publicering.

1. I den **profil** steg i den **publicera** väljer **Microsoft Azure App Service**.

   ![Dialogrutan Publicera](./media/webjobs-sdk-get-started/publish-dialog.png)

1. I den **Apptjänst** markerar **resursgruppen > din Apptjänst-app**, och välj sedan **OK**.

   ![Dialogrutan för Apptjänst](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. I den **anslutning** steg i guiden väljer **publicera**.

## <a name="trigger-the-function-in-azure"></a>Utlösa funktionen i Azure

1. Kontrollera att du inte kör lokalt (Stäng fönstret om det fortfarande är öppet). Annars kanske den lokala instansen först med att bearbeta alla meddelanden i kö som du skapar.

1. Använd **Server Explorer** för att skapa ett kömeddelande, på samma sätt som du gjorde [tidigare](#trigger-the-function), förutom ange *Hello Azure!*.

7. Uppdatera den **kön** sida i Visual Studio och det nya meddelandet har tagits bort eftersom funktionen körs i Azure App Service bearbetas den.

   > [!TIP]
   > När du testar i Azure, använda [utvecklingsläge](webjobs-sdk-how-to.md#jobhost-development-settings) att säkerställa att en kö Utlösarfunktion anropas direkt och undvika fördröjning på grund av att [kön avsökning exponentiell backoff](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Visa loggfiler i Application Insights

1. Öppna den [Azure-portalen](https://portal.azure.com/), och gå till Application Insights-resurs.

2. Välj **Sök**.

1. Om du inte ser den *Hello Azure!* meddelandet markerar **uppdatera** regelbundet i flera minuter.

   Du se loggar från funktionen körs i ett Webbjobb, inklusive den *Hello Azure!* den text som du angav i föregående avsnitt.

## <a name="add-an-input-binding"></a>Lägg till en indatabindning

Inkommande bindningar förenkla kod som läser data. I det här exemplet kö-meddelande är ett blob-namn och blobbnamnet ska du använda för att hitta och läsa en blobb i Azure Storage.

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

   I den här koden `queueTrigger` är en [bindande uttryck](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), vilket innebär att det matchar ett annat värde vid körning.  Den har innehållet i kön meddelandet vid körning.

2. Lägg till en `using`:

   ```cs
   using System.IO;
   ```

3. Skapa en blob-behållare i ditt lagringskonto.

   a. I **Server Explorer**expanderar du noden för ditt lagringskonto, högerklicka på **Blobbar**, och välj sedan **skapa Blob-behållaren**.

   b. I den **skapa Blob-behållaren** dialogrutan Ange *behållare* som behållarens namn och klicka sedan på **OK**.

4. Överför den *Program.cs* filen till blob-behållare. (Den här filen används här som ett exempel; du kan ladda upp en textfil och skapa ett kömeddelande med filnamnet.)

   a. I **Server Explorer**, dubbelklickar du på noden för den behållare som du nyss skapade.

   b. I den **behållare** väljer den **överför** knappen.

   ![Knappen Skicka för BLOB](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Leta upp och markera *Program.cs*, och välj sedan **OK**.

5. Skapa ett kömeddelande i kön som du skapade tidigare, med *Program.cs* som text i meddelandet.

   ![Kömeddelande Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

6. Kör projektet.

   Kömeddelandet utlöser den funktion som sedan läser blob och loggar dess längd. Konsolens utdata ser ut så här:

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

Utdata bindningar förenklar koden som skriver data. Det här exemplet ändrar det föregående genom att skriva en kopia av blob i stället för loggning av dess storlek.

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

5. Skapa ett nytt meddelande i kön med *Program.cs* som text i meddelandet.

6. Kör projektet.

   Kömeddelandet utlöser den funktion som sedan läser blob, loggar dess längd och skapar en ny blob. Konsolens utdata är detsamma, men när du gå till fönstret blob-behållaren och väljer **uppdatera**, visas en ny blob med namnet *kopiera Program.cs.*

## <a name="next-steps"></a>Nästa steg

Den här guiden visar hur du skapar, köra och distribuera en WebJobs-SDK-projektet.

Om du vill visa allt som försätts i ett projekt med WebJobs SDK hade instruktionerna du skapa ett projekt från grunden. Men när du skapar ditt nästa projekt, Överväg att använda den **Azure Webjobs** mallen i den **moln** kategori. Den här mallen skapar ett projekt med NuGet-paket och exempelkod som redan har konfigurerat. Observera att exempelkoden kan behöva ändras för att använda det nya ramverket för loggning.

Mer information finns i [hur du använder WebJobs-SDK](webjobs-sdk-how-to.md).
