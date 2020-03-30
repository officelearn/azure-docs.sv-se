---
title: Hantering av .NET SDK för Azure Stream Analytics
description: Kom igång med Stream Analytics Management .NET SDK. Läs om hur du konfigurerar och kör analysjobb. Skapa ett projekt, indata, utdata och omvandlingar.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 20be2c56635faa4f77ae8e8e6afc3c1ece6d4942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426264"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Hantering .NET SDK: Konfigurera och köra analysjobb med Hjälp av Azure Stream Analytics API för .NET
Lär dig hur du konfigurerar och kör analysjobb med hjälp av Stream Analytics API för .NET med hjälp av Management .NET SDK. Ställ in ett projekt, skapa indata- och utdatakällor, omvandlingar och starta och stoppa jobb. För dina analysjobb kan du strömma data från Blob-lagring eller från en händelsehubb.

Se [dokumentationen för hanteringsreferens för Stream Analytics API för .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics är en fullständigt hanterad tjänst som ger låg latens, högtillgänglig, skalbar, komplex händelsebearbetning över strömmande data i molnet. Stream Analytics gör det möjligt för kunder att konfigurera direktuppspelningsjobb för att analysera dataströmmar och gör det möjligt för dem att köra analyser i nära realtid.  

> [!NOTE]
> Vi har uppdaterat exempelkoden i den här artikeln med Azure Stream Analytics Management .NET SDK v2.x-versionen. För exempelkod som använder sdk-versionen (använd lagecy) finns i [Använda hantering .NET SDK v1.x för Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Krav
Innan du börjar den här artikeln måste du ha följande krav:

* Installera Visual Studio 2019 eller 2015.
* Hämta och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Skapa en Azure Resource Group i din prenumeration. Följande exempel är ett exempel på Azure PowerShell-skript. Information om Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview);  

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

* Ställ in en indatakälla och utdatamål som jobbet kan ansluta till.

## <a name="set-up-a-project"></a>Skapa ett projekt
Om du vill skapa ett analysjobb använder du Stream Analytics API för .NET och konfigurerar projektet först.

1. Skapa ett Visual Studio C# .NET-konsolprogram.
2. I Package Manager-konsolen kör du följande kommandon för att installera NuGet-paketen. Den första är Azure Stream Analytics Management .NET SDK. Den andra är för Azure-klientautentisering.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Lägg till följande avsnitt av **appSettings** i filen App.config:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Ersätt värden för **SubscriptionId** och **ActiveDirectoryTenantId** med din Azure-prenumeration och klient-ID: er. Du kan hämta dessa värden genom att köra följande Azure PowerShell-cmdlet:

   ```powershell
      Get-AzureAccount
   ```

4. Lägg till följande referens i CSproj-filen:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Lägg till följande **med hjälp av** satser i källfilen (Program.cs) i projektet:
   
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

6. Lägg till en autentiseringshjälpmetod:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Skapa en Stream Analytics-hanteringsklient
Med ett **StreamAnalyticsManagementClient-objekt** kan du hantera jobbet och jobbkomponenterna, till exempel indata, utdata och omvandling.

Lägg till följande kod i **Main** början av huvudmetoden:

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

**ResursGroupName-variabelns** värde ska vara samma som namnet på resursgruppen som du skapade eller plockade i de nödvändiga stegen.

Om du vill automatisera presentationen av autentiseringsuppgifter för att skapa arbetstillfällen läser du [Autentisera ett tjänsthuvudnamn med Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

De återstående avsnitten i den här artikeln förutsätter **Main** att den här koden finns i början av huvudmetoden.

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb
Följande kod skapar ett Stream Analytics-jobb under den resursgrupp som du har definierat. Du kommer att lägga till en indata, utdata och omvandling till jobbet senare.

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

## <a name="create-a-stream-analytics-input-source"></a>Skapa en Stream Analytics-inmatningskälla
Följande kod skapar en Stream Analytics-indatakälla med blob-indatakälltypen och CSV-serialisering. Om du vill skapa en indatakälla för händelsehubben använder du **EventHubStreamInputDataSource** i stället för **BlobStreamInputDataSource**. På samma sätt kan du anpassa serialiseringstypen för indatakällan.

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

Indatakällor, antingen från Blob-lagring eller en händelsenav, är knutna till ett visst jobb. Om du vill använda samma indatakälla för olika jobb måste du anropa metoden igen och ange ett annat jobbnamn.

## <a name="test-a-stream-analytics-input-source"></a>Testa en Stream Analytics-inmatningskälla
**TestConnection-metoden** testar om Stream Analytics-jobbet kan ansluta till indatakällan samt andra aspekter som är specifika för indatakälltypen. I blob-indatakällan som du skapade i ett tidigare steg kontrollerar metoden till exempel att lagringskontonamnet och nyckelparet kan användas för att ansluta till lagringskontot samt kontrollera att den angivna behållaren finns.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Skapa ett utdatamål för Stream Analytics
Att skapa ett utdatamål påminner om att skapa en Stream Analytics-indatakälla. Precis som indatakällor är utdatamålen kopplade till ett visst jobb. Om du vill använda samma utdatamål för olika jobb måste du anropa metoden igen och ange ett annat jobbnamn.

Följande kod skapar ett utdatamål (Azure SQL-databas). Du kan anpassa utdatamålets datatyp och/eller serialiseringstyp.

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

## <a name="test-a-stream-analytics-output-target"></a>Testa ett utdatamål för Stream Analytics
Ett Stream Analytics-utdatamål har också **TestConnection-metoden** för att testa anslutningar.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Skapa en Stream Analytics-omvandling
Följande kod skapar en Stream Analytics-omvandling med frågan "välj * från indata" och anger att en strömningsenhet ska allokeras för Stream Analytics-jobbet. Mer information om hur du justerar enheter för direktuppspelning finns i [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Precis som indata och utdata är en omvandling också knuten till det specifika Stream Analytics-jobb som den skapades under.

## <a name="start-a-stream-analytics-job"></a>Starta ett Stream Analytics-jobb
När du har skapat ett Stream Analytics-jobb och dess indata, utdata och omvandling kan du starta jobbet genom att anropa **Start-metoden.**

Följande exempelkod startar ett Stream Analytics-jobb med en anpassad starttid för utdata inställd på 12 december 2012, 12:12:12 UTC:

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
Du kan stoppa ett Stream Analytics-jobb som körs genom att anropa **stop-metoden.**

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Ta bort ett Stream Analytics-jobb
Metoden **Ta bort** tar bort jobbet samt underliggande underresurser, inklusive indata, utdata och omvandling av jobbet.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Få support
Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
Du har lärt dig grunderna i att använda en .NET SDK för att skapa och köra analysjobb. Mer information finns i följande artiklar:

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)

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
