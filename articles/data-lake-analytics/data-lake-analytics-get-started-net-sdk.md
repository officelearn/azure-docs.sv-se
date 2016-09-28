<properties 
   pageTitle="Kom igång med Azure Data Lake Analytics med hjälp av .NET SDK | Azure" 
   description="Lär dig hur du använder .NET SDK för att skapa Data Lake Store-konton, skapa Data Lake Analytics-jobb och skicka jobb som skrivits i U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/22/2016"
   ms.author="edmaca"/>


# Självstudier: Kom igång med Azure Data Lake Analytics med hjälp av .NET SDK

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Lär dig hur du använder Azure .NET SDK för att skapa Azure Data Lake Analytics-konton, definiera Data Lake Analytics-jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-konton. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

I de här självstudierna utvecklar du ett C#-konsolprogram som innehåller ett U-SQL-skript som läser en fil med tabbavgränsade värden (TVS) och konverterar den till en fil med kommaavgränsade värden (CSV). Klicka på flikarna överst i det här avsnittet om du vill gå igenom samma självstudier med andra verktyg.

##Krav

Innan du påbörjar de här självstudierna måste du ha:

- **Visual Studio 2015, Visual Studio 2013 uppdatering 4 eller Visual Studio 2012 med Visual C++ installerat**.
- **Microsoft Azure SDK för .NET version 2.5 eller högre**.  Installera den med hjälp av [installationsprogrammet för webbplattformen](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Data Lake-verktyg för Visual Studio](http://aka.ms/adltoolsvs)**. 
- Skapa ett program med Azure Active Directory (AAD) och hämta dess **klient-ID**, **innehavar-ID** och **nyckel**. Mer information om AAD-program och instruktioner för hur du hämtar ett klient-ID finns i [Skapa Active Directory-program och tjänstobjekt med portalen](../resource-group-create-service-principal-portal.md). Svars-URI och nyckeln är också tillgängliga via portalen när du har skapat programmet och genererat nyckeln.


##Skapa konsolprogram

I självstudierna bearbetas vissa sökloggar.  Sökloggen kan lagras i Data Lake Store eller Azure Blob-lagring. 

Ett exempel på söklogg har kopierats till en offentlig Azure Blob-behållare. I programmet hämtar du filen till din arbetsstation och laddar sedan upp filen till Data Lake Store-standardkontot.

**Skapa ett program**

1. Öppna Visual Studio.
2. Skapa ett C#-konsolprogram
3. Öppna NuGet-pakethanteringskonsol och kör följande kommandon:

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
        Install-Package WindowsAzure.Storage

4. Lägg till en ny fil i projektet som kallas **SampleUSQLScript.txt**, och klistra in följande U-SQL-skript. Data Lake Analytics-jobb skrivs på U-SQL-språket. Läs mer om U-SQL i [Kom igång med U-SQL-språket](data-lake-analytics-u-sql-get-started.md) och [Referens för U-SQL-språket](http://go.microsoft.com/fwlink/?LinkId=691348).

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    U-SQL-skriptet läser källdatafilen med hjälp av **Extractors.Tsv()** och skapar sedan en CSV-fil med hjälp av **Outputters.Csv()**. 
    
    I C#-programmet måste du förbereda filen **/Samples/Data/SearchLog.tsv** och mappen **/Output/**.    
    
    Det är enklare att använda relativa sökvägar för filer lagrade i Data Lake-standardkonton. Du kan också använda absoluta sökvägar.  Exempel 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Du måste använda absoluta sökvägar för att få åtkomst till filer i länkade Storage-konton.  Syntaxen för filer som lagras i ett länkat Azure Storage-konto är:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Det finns ett känt problem med tjänsten Azure Data Lake.  Om exempelprogrammet avbryts eller påträffar ett fel kan du behöva manuellt ta bort de Data Lake Store- och Data Lake Analytics-konton som skriptet skapar.  Om du inte känner till portalen kan du komma igång med hjälp av guiden [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).       
       
5. I Program.cs, klistra in följande kod:

        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System;
        using System.Collections.Generic;
        using System.IO;
        
        namespace SdkSample
        {
          class Program
          {
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            private static string _adlaAccountName;
            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;
            private static string _tenantId;
            private static string _subId;
            private static string _clientId;
            private static string _clientKey;
        
            private static void Main(string[] args)
            {
              _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
              _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>"; // TODO: Replace this value with the name for a NEW Analytics account.
              _resourceGroupName = "<RESOURCE-GROUP>"; // TODO: Replace this value. This resource group should already exist.
              _location = "East US 2";
              _tenantId = "<TENANT-ID>";
              _subId = "<SUBSCRIPTION-ID>";
              _clientId = "<CLIENT-ID>";
              _clientKey = "<CLIENT-KEY>";
        
              string localFolderPath = @"c:\temp\"; // TODO: Make sure this exists and contains SampleUSQLScript.txt.
              var tokenCreds = Authenticate(_tenantId, _clientId, _clientKey);
        
              SetupClients(tokenCreds, _subId); 
        
              // Run sample scenarios
              WaitForNewline("Authenticated.", "Creating NEW accounts.");
              CreateAccounts();
              WaitForNewline("Accounts created.", "Preparing the source data file.");
        
              // Transfer the source file from a public Azure Blob container to Data Lake Store.
              CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
              blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
              UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
              WaitForNewline("Source data file prepared.", "Submitting a job.");
        
              // Submit the job
              Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
              WaitForNewline("Job submitted.", "Waiting for job completion.");
        
              // Wait for job completion
              WaitForJob(jobId);
              WaitForNewline("Job completed.", "Downloading job output.");
        
              // Download job output
              DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
              WaitForNewline("Job output downloaded.", "Deleting accounts.");
        
              // Delete accounts
              _adlaClient.Account.Delete(_resourceGroupName, _adlaAccountName);
              _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
        
              WaitForNewline("Accounts deleted. You can now exit.");
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
              Console.WriteLine(reason + "\r\nPress ENTER to continue...");
        
              Console.ReadLine();
        
              if (!String.IsNullOrWhiteSpace(nextAction))
                Console.WriteLine(nextAction);
            }
        
            public static TokenCredentials Authenticate(string tenantId, string clientId, string clientKey)
            {
              var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + _tenantId);
              var creds = new ClientCredential(_clientId, _clientKey);
              var tokenAuthResult = authContext.AcquireTokenAsync("https://management.core.windows.net/", creds).Result;
        
              return new TokenCredentials(tokenAuthResult.AccessToken);
            }
        
            public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
            {
              _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
              _adlaClient.SubscriptionId = subscriptionId;
        
              _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);
        
              _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(tokenCreds);
        
              _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
              _adlsClient.SubscriptionId = subscriptionId;
        
              _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }
        
            public static void CreateAccounts()
            {
              //ADLS account first, ADLA requires an ADLS account
              var adlsParameters = new DataLakeStoreAccount(location: _location);
              _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
        
              var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(_adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
              var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: _adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
              var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: _location);
              _adlaClient.Account.Create(_resourceGroupName, _adlaAccountName, adlaParameters);
            }
        
            public static Guid SubmitJobByPath(string scriptPath, string jobName)
            {
              var script = File.ReadAllText(scriptPath);
        
              var jobId = Guid.NewGuid();
              var properties = new USqlJobProperties(script);
              var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
              var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);
        
              return jobId;
            }
        
            public static JobResult WaitForJob(Guid jobId)
            {
              var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
              while (jobInfo.State != JobState.Ended)
              {
                jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
              }
              return jobInfo.Result.Value;
            }
        
            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
              var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
              var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
              var uploader = new DataLakeStoreUploader(parameters, frontend);
              uploader.Execute();
            }
        
            public static void DownloadFile(string srcPath, string destPath)
            {
              var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
              var fileStream = new FileStream(destPath, FileMode.Create);
        
              stream.CopyTo(fileStream);
              fileStream.Close();
              stream.Close();
            }
          }
        }

6. Tryck på **F5** för att köra programmet.

## Se även

- Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
- Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Information om att utveckla U-SQL-program finns i [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Mer information om U-SQL finns i [Kom igång med Azure Data Lake Analytics U-SQL-språket](data-lake-analytics-u-sql-get-started.md) och [U-SQL-språkreferens](http://go.microsoft.com/fwlink/?LinkId=691348).
- Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).
- Om du vill få en översikt över Data Lake Analytics, se [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).



<!--HONumber=Sep16_HO3-->


