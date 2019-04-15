---
title: Övervaka och hantera Azure Stream Analytics-jobb programmässigt
description: 'Den här artikeln beskriver hur du programmässigt övervaka Stream Analytics-jobb som skapats via REST API: er, Azure SDK eller PowerShell.'
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: eaeb2b4decc7da4caa75cb2af68829b4bf7ce64d
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563854"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Skapa en Övervakare för Stream Analytics-jobb programmässigt

Den här artikeln visar hur du aktiverar övervakning av ett Stream Analytics-jobb. Stream Analytics-jobb som skapas via REST API: er, Azure SDK eller PowerShell har inte övervakning aktiverad som standard. Du kan manuellt aktivera det i Azure portal genom att gå till övervakaren jobbsidan och klicka på Aktivera-knappen eller du kan automatisera processen genom att följa stegen i den här artikeln. Övervakningsdata visas i området mått i Azure-portalen för ditt Stream Analytics-jobb.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här processen, måste du ha följande krav:

* Visual Studio 2017 eller 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/) hämtas och installeras
* Ett befintligt Stream Analytics-jobb som måste ha övervakning har aktiverats

## <a name="create-a-project"></a>Skapa ett projekt

1. Skapa ett Visual Studio C# .NET-konsolprogram.
2. Kör följande kommandon för att installera NuGet-paket i Package Manager-konsolen. Den första är Azure Stream Analytics Management .NET SDK. Den andra är Azure Monitor SDK som ska användas för att aktivera övervakning. Den sista som är Azure Active Directory-klient som ska användas för autentisering.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Lägg till avsnittet appSettings i filen App.config.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Ersätt värdena för *SubscriptionId* och *ActiveDirectoryTenantId* med din Azure-prenumeration och klient-ID: N. Du kan hämta dessa värden genom att köra följande PowerShell-cmdlet:
   
   ```powershell
   Get-AzureAccount
   ```
4. Lägg till följande using-satser till källfilen (Program.cs) i projektet.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Lägg till en helper autentiseringsmetod.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Skapa hanteringsklienter

Följande kod ställer in nödvändiga variabler och av hanteringsklienter.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Aktivera övervakning av ett befintligt Stream Analytics-jobb

Följande kod möjliggör övervakning för en **befintliga** Stream Analytics-jobb. Den första delen av kod som utför en GET-begäran mot Stream Analytics-tjänsten att hämta information om Stream Analytics-jobbet. Den använder den *ID* egenskapen (hämtades från GET-begäran) som en parameter för Put-metoden i den andra hälften av den kod som skickar en PUT begäran till Insights-tjänsten för att aktivera övervakning för Stream Analytics-jobbet.

> [!WARNING]
> Om du redan har aktiverat övervakning för ett annat Stream Analytics-jobb, antingen via Azure portal eller programmässigt via den nedan kod, **rekommenderar vi att du anger samma lagringskontonamn som du använde när du tidigare aktivera övervakning.**
> 
> Lagringskontot är länkad till regionen som du skapade ditt Stream Analytics-jobb i, inte specifikt för själva jobbet.
> 
> Alla Stream Analytics-jobb (och alla andra Azure-resurser) i samma regionen dela det här lagringskontot för att lagra övervakningsdata. Om du anger ett annat lagringskonto kan orsaka oväntade sidoeffekter för övervakningen av andra Stream Analytics-jobb eller andra Azure-resurser.
> 
> Lagringskontonamn som används för att ersätta `<YOUR STORAGE ACCOUNT NAME>` i följande kod ska vara ett storage-konto som tillhör samma prenumeration som Stream Analytics-jobb som du aktiverar övervakning för.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Få support

För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
