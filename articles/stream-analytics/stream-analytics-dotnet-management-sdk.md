---
title: Hantering av .NET SDK för Azure Stream Analytics | Microsoft Docs
description: Kom igång med Stream Analytics Management .NET SDK. Lär dig hur du ställer in och kör analytics-jobb. Skapa ett projekt, indata, utdata och transformationer.
keywords: .net SDK, analytics API
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 5e93de87-0c6f-4f4b-be98-08d63f832897
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: jeanb
ms.openlocfilehash: 269491beb2acb854098f6513007c5390f318f945
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Hantering av .NET SDK: Konfigurera och köra analytics-jobb med hjälp av Azure Stream Analytics-API: et för .NET
Lär dig hur du ställer in och kör analytics-jobb med hjälp av Stream Analytics-API för .NET med hantering av .NET SDK. Ställ in ett projekt, skapa inkommande och utgående källor, omvandlingar och starta och stoppa jobb. För analytics-jobb kan du strömma data från Blob-lagring eller från en händelsehubb.

Finns det [management referensdokumentationen för Stream Analytics-API för .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics är en helt hanterad tjänst som tillhandahåller låg latens, hög tillgänglighet, skalbarhet, komplexa händelsebearbetning över strömmande data i molnet. Stream Analytics ger kunder möjlighet att konfigurera direktuppspelningsjobb för att analysera dataströmmar, vilket gör att enheten nära analys i realtid.  

> [!NOTE]
> Vi har uppdaterat exempelkoden i den här artikeln med Azure Stream Analytics Management .NET SDK v2.x version. Exempelkod med SDK-version använder lagecy (1.x), se [använder Management .NET SDK-v1.x för Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här artikeln måste du ha:

* Installera Visual Studio 2017 eller 2015.
* Hämta och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Skapa en Azure-resursgrupp i din prenumeration. Följande är ett exempel Azure PowerShell-skript. Azure PowerShell information finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview);  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* Konfigurera ett inkommande käll- och utdata för användning. Mer information finns i [lägga till indata](stream-analytics-add-inputs.md) att ställa in en Exempelindata och [lägga till utdata](stream-analytics-add-outputs.md) att ställa in ett exempel på utdata.

## <a name="set-up-a-project"></a>Ställ in ett projekt
Använd Stream Analytics-API för .NET, först konfigurera ditt projekt för att skapa analytics-jobbet.

1. Skapa ett konsolprogram i Visual Studio C# .NET.
2. Kör följande kommandon för att installera NuGet-paket i Package Manager-konsolen. Den första är Azure Stream Analytics Management .NET SDK. Den andra är för Azure klientautentisering.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
3. Lägg till följande **appSettings** avsnitt i filen App.config:
   
        <appSettings>
          <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
          <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
        </appSettings>

    Ersätt värdena för **SubscriptionId** och **ActiveDirectoryTenantId** med din Azure-prenumeration och klient-ID: N. Du kan hämta dessa värden genom att köra följande Azure PowerShell-cmdlet:

        Get-AzureAccount

4. Lägg till följande referens i filen .csproj:

        <Reference Include="System.Configuration" />

5. Lägg till följande **med** instruktioner till källfilen (Program.cs) i projektet:
   
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.Threading;
        using System.Threading.Tasks;
        
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Rest;
6. Lägg till en helper autentiseringsmetod:

   ```
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Skapa ett Stream Analytics management-klienten
En **StreamAnalyticsManagementClient** objektet kan du hantera jobbet och jobbet-komponenter, till exempel indata, utdata och omvandling.

Lägg till följande kod i början av den **Main** metoden:

   ```
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

Den **resourceGroupName** variabelns värde ska vara samma som namnet på resursgruppen som du har skapat eller plockats i förkraven.

Om du vill automatisera autentiseringsuppgifter presentation aspekt av skapa jobb, referera till [autentiserar ett huvudnamn för tjänsten med Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).

De återstående avsnitten i den här artikeln förutsätter att den här koden är i början av den **Main** metod.

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb
Följande kod skapar en Stream Analytics-jobbet under den resursgrupp som du har definierat. Du lägger till indata, utdata och omvandling i jobbet senare.

   ```
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

## <a name="create-a-stream-analytics-input-source"></a>Skapa en inkommande Stream Analytics-källa
Följande kod skapar en Stream Analytics-Indatakällan med Indatakällan blobbtypen och CSV-serialisering. Så här skapar du en hub inkommande händelsekälla **EventHubStreamInputDataSource** i stället för **BlobStreamInputDataSource**. På liknande sätt kan du anpassa serialisering typ av Indatakällan.

   ```
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

Indatakällor, som från Blob-lagring eller en händelsehubb är knutna till ett visst jobb. Om du vill använda samma Indatakällan för olika jobb, måste du anropa metoden igen och ange ett annat jobbnamn.

## <a name="test-a-stream-analytics-input-source"></a>Testa en Stream Analytics indatakälla
Den **TestConnection** metoden testar om Stream Analytics-jobbet är ansluta till Indatakällan samt andra aspekter som är specifika för inkommande källtypen. Till exempel i blob Indatakällan du skapade tidigare, kontrollerar metoden att lagringskontonamn och nyckel kan användas för att ansluta till lagringskontot samt kontrollera att den angivna behållaren finns.

   ```
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Skapa ett mål för Stream Analytics-utdata
Skapa ett mål för utdata liknar att skapa ett Stream Analytics-Indatakällan. Som indatakällor, är utdata mål knutna till ett visst jobb. Om du vill använda samma utdata mål för olika jobb, måste du anropa metoden igen och ange ett annat jobbnamn.

Följande kod skapar ett utgående mål (Azure SQL database). Du kan anpassa utdata målets datatyp och/eller serialiseringstyp.

   ```
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

## <a name="test-a-stream-analytics-output-target"></a>Testa ett mål för Stream Analytics-utdata
Ett Stream Analytics utdata mål har även den **TestConnection** metod för att testa anslutningar.

   ```
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Skapa Stream Analytics
Följande kod skapar en Stream Analytics-omvandling med frågan ”Välj * från indata” och anger om du vill allokera en strömmande enhet för Stream Analytics-jobbet. Läs mer om hur du justerar strömningsenheter [skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md).

   ```
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

En omvandling är även kopplad till specifika Stream Analytics-jobbet den skapades under som indata och utdata.

## <a name="start-a-stream-analytics-job"></a>Starta ett Stream Analytics-jobb
När du har skapat ett Stream Analytics-jobb och dess input(s), utdata och omvandling av du startar jobbet genom att anropa den **starta** metod.

Följande exempel kod startar ett Stream Analytics-jobb med anpassade utdata starttid inställd på 12 December 2012 12:12:12 UTC:

   ```
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

   ```
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Ta bort ett Stream Analytics-jobb
Den **ta bort** metoden tar bort jobbet samt de underliggande underordnade resurser, inklusive input(s), utdata och omvandling av jobbet.

   ```
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Få support
För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
Du har lärt dig grunderna i att använda en .NET SDK för att skapa och köra analytics-jobb. Läs mer i:

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
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
