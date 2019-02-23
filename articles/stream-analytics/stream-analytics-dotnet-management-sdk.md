---
title: Management .NET SDK för Azure Stream Analytics
description: Kom igång med Stream Analytics Management .NET SDK. Lär dig hur du konfigurerar och kör analytics-jobb. Skapa ett projekt, indata, utdata och transformationer.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 4fa4a9a8d01d499dc431c8b182401226aa72bf1f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732576"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Management .NET SDK: Konfigurera och köra analysjobb med hjälp av Azure Stream Analytics-API för .NET
Lär dig hur du konfigurerar och kör analytics-jobb med hjälp av Stream Analytics-API för .NET med hjälp av .NET SDK. Ställ in ett projekt, skapa inkommande och utgående källor, transformeringar och starta och stoppa jobb. Du kan strömma data från Blob storage eller från en händelsehubb för analytics-jobb.

Se den [management referensdokumentation för Stream Analytics-API för .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics är en helt hanterad tjänst som ger låg latens, hög tillgänglighet, skalbar, komplex händelsebearbetning över strömmande data i molnet. Stream Analytics ger kunder möjlighet att ställa in direktuppspelningsjobb för att analysera dataströmmar och låter dem att driva analys i nära realtid.  

> [!NOTE]
> Vi har uppdaterat exempelkoden i den här artikeln med Azure Stream Analytics Management .NET SDK v2.x version. Exempelkod med hjälp av SDK-version använder lagecy (1.x), finns i [Använd Management .NET SDK-v1.x för Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här artikeln måste du ha:

* Installera Visual Studio 2017 eller 2015.
* Ladda ned och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Skapa en Azure-resursgrupp i din prenumeration. Följande är ett exempelskript för Azure PowerShell. Azure PowerShell information finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Konfigurera ett inkommande käll- och utdata för jobbet att ansluta till.

## <a name="set-up-a-project"></a>Ställ in ett projekt
Om du vill skapa en analytics-jobbet använda Stream Analytics-API för .NET, först konfigurera ditt projekt.

1. Skapa ett Visual Studio C# .NET-konsolprogram.
2. Kör följande kommandon för att installera NuGet-paket i Package Manager-konsolen. Den första är Azure Stream Analytics Management .NET SDK. Den andra är för Azure-klientautentisering.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Lägg till följande **appSettings** avsnitt i filen App.config:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Ersätt värdena för **SubscriptionId** och **ActiveDirectoryTenantId** med din Azure-prenumeration och klient-ID: N. Du kan hämta dessa värden genom att köra följande Azure PowerShell-cmdlet:

   ```powershell
      Get-AzureAccount
   ```

4. Lägg till följande referens i filen .csproj:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Lägg till följande **med** instruktioner till källfilen (Program.cs) i projektet:
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Lägg till en helper autentiseringsmetod:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Skapa ett Stream Analytics management-klienten
En **StreamAnalyticsManagementClient** objekt kan du hantera jobbet och jobbet-komponenter, till exempel indata, utdata och omvandling.

Lägg till följande kod i början av den **Main** metoden:

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

Den **resourceGroupName** variabelns värde bör vara samma som namnet på resursgruppen som du har skapat eller valt i nödvändiga steg.

Om du vill automatisera credential presentation aspekt av skapande av jobb, som avser [autentisera tjänstens huvudnamn med Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

De återstående avsnitten i den här artikeln förutsätter att den här koden finns i början av den **Main** metod.

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb
Följande kod skapar ett Stream Analytics-jobb under den resursgrupp som du har definierat. Du lägger till ett indata, utdata och omvandling till jobbet senare.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Skapa ett Stream Analytics-indatakälla
Följande kod skapar ett Stream Analytics-Indatakällan med blob-Indatakällan typ och CSV-serialisering. Använd för att skapa en event hub indatakälla **EventHubStreamInputDataSource** i stället för **BlobStreamInputDataSource**. På samma sätt kan du anpassa serialisering typ av Indatakällan.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Indatakällor, som från Blob storage- eller en händelsehubb är knutna till ett specifikt jobb. Om du vill använda samma Indatakällan för olika jobb, måste du anropa metoden igen och ange ett annat jobbnamn.

## <a name="test-a-stream-analytics-input-source"></a>Testa en indatakälla för Stream Analytics
Den **TestConnection** metoden testar om Stream Analytics-jobb är ansluta till Indatakällan samt andra aspekter som är specifik för typ av indatakälla. Till exempel i blob Indatakällan du skapade i ett tidigare steg, kontrollerar metoden att det lagringskontonamn och nyckel kan användas till att ansluta till lagringskontot och kontrollera att den angivna behållaren finns.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Skapa ett mål för Stream Analytics-utdata
Skapar en utdatamål påminner mycket om att skapa ett Stream Analytics-Indatakällan. Som indatakällor, är utdata mål knutna till ett specifikt jobb. Om du vill använda samma utdatamål för olika jobb, måste du anropa metoden igen och ange ett annat jobbnamn.

Följande kod skapar ett utdatamål (Azure SQL-databas). Du kan anpassa utdata målets datatyp och/eller serialiseringstyp.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testa en utdatamål för Stream Analytics
En utdatamål för Stream Analytics har även den **TestConnection** metod för att testa anslutningar.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Skapa ett Stream Analytics-transformering
Följande kod skapar en Stream Analytics-omvandling med frågan ”Välj * från indata” och anger om du vill allokera en strömmande enhet för Stream Analytics-jobbet. Mer information om hur du justerar strömningsenheter finns i [skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

En omvandling är också kopplad till den specifika Stream Analytics-jobb som den skapades under som indata och utdata.

## <a name="start-a-stream-analytics-job"></a>Starta ett Stream Analytics-jobb
När du har skapat ett Stream Analytics-jobb och dess indata, utdata och omvandling, kan du starta jobbet genom att anropa den **starta** metod.

Följande exempel på kod startar ett Stream Analytics-jobb med en anpassad utdata starttid som har angetts till 12 December 2012 12:12:12 UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Stoppa ett Stream Analytics-jobb
Du kan stoppa ett Stream Analytics-jobb som körs genom att anropa den **stoppa** metod.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Ta bort ett Stream Analytics-jobb
Den **ta bort** metoden tar bort jobbet samt de underliggande underordnade resurser, inklusive indata, utdata och transformering av jobbet.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Få support
För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
Du har lärt dig grunderna i att använda en .NET SDK för att skapa och köra analysjobb. Läs mer i:

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
