<properties
   pageTitle="Använd Data Lake Store .NET SDK för att utveckla program | Azure"
   description="Använd Azure Data Lake Store .NET SDK för att utveckla program"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/27/2016"
   ms.author="nitinme"/>

# Kom igång med Azure Data Lake Store med hjälp av .NET SDK

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lär dig mer om att använda Azure Data Lake Store .NET SDK för att skapa ett Azure Data Lake-konto och utföra grundläggande åtgärder som att skapa mappar, ladda upp och hämta datafiler, ta bort ditt konto, osv. Mer information om Data Lake finns i [Azure Data Lake Store](data-lake-store-overview.md).

## Förutsättningar

* Visual Studio 2013 eller 2015 Anvisningarna nedan använder Visual Studio 2015.
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Aktivera din Azure-prenumeration** för en förhandsversion av Data Lake Store. Se [instruktioner](data-lake-store-get-started-portal.md#signup).
* **Skapa ett program i Azure Active Directory**. Det finns två sätt att autentisera med Azure Active Directory – **interaktivt** och **icke-interaktivt**. Det finns olika förutsättningar baserat på hur du vill autentisera.
    * **För interaktiv autentisering** (används i den här artikeln) – Du måste skapa ett **Native Client-program** i Azure Active Directory. När du har skapat programmet, hämtar du följande värden som är relaterade till programmet.
        - Hämta **klient-ID** och **omdirigerings-URI** för programmet
        - Ange delegerade behörigheter

    * **För icke-interaktiv autentisering** – Du måste skapa en **Webbapp** i Azure Active Directory . När du har skapat programmet, hämtar du följande värden som är relaterade till programmet.
        - Hämta **klient-ID**, **klienthemlighet** och **omdirigerings-URI** för programmet
        - Ange delegerade behörigheter
        - Tilldela Azure Active Directory-programmet till en roll. Rollen kan vara för den nivå av omfång för vilken du vill ge behörighet till Azure Active Directory-programmet. Du kan till exempel tilldela programmet på prenumerationsnivån eller på en resursgruppsnivå. 

    Se [Skapa Active Directory-program och tjänstens huvudnamn med hjälp av portalen](../resource-group-create-service-principal-portal.md) för anvisningar om hur du hämtar dessa värden, anger behörigheter och tilldelar roller.

## Skapa ett .NET-program

1. Öppna Visual Studio och skapa ett konsolprogram.

2. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**.

3. Från **Nytt projekt** anger eller väljer du följande värden:

  	| Egenskap | Värde                       |
  	|----------|-----------------------------|
  	| Kategori | Mallar/Visual C#/Windows |
  	| Mall | Konsolprogram         |
  	| Namn     | CreateADLApplication        |

4. Klicka på **OK** för att skapa projektet.

5. Lägg till Nuget-paketen i projektet.

    1. Högerklicka på projektnamnet i Solution Explorer och klicka på **Hantera NuGet-paket**.
    2. I fliken **Nuget Package Manager** ser du till att **Paketkällan** har angetts som **nuget.org** och att kryssrutan **Inkludera förhandsversion** är markerad.
    3. Sök efter och installera följande Data Lake Store-paket:

        * `Microsoft.Azure.Management.DataLake.Store`
        * `Microsoft.Azure.Management.DataLake.StoreUploader`

        ![Lägg till en Nuget-källa](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Create a new Azure Data Lake account")

    4. Installera `Microsoft.IdentityModel.Clients.ActiveDirectory`-paketet för Azure Active Directory-autentisering. Se till att du *avmarkerar* kryssrutan **Inkludera förhandsversion** så att du installerar en stabil version av det här paketet.

        ![Lägg till en Nuget-källa](./media/data-lake-store-get-started-net-sdk/adl.install.azure.auth.png "Create a new Azure Data Lake account")


    5. Stäng **Nuget Package Manager**.

7. Öppna **Program.cs**, ta bort den befintliga koden och lägg sedan till följande instruktioner för att lägga till referenser till namnområden.

        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;

8. Deklarera variablerna enligt nedan och ange värden för Data Lake Store-namnet och resursgruppens namn. Det Data Lake Store-namn du anger kommer att skapas av programmet. Resursgruppen som du anger här ska redan finnas. Kontrollera också att den lokala sökvägen och filnamnet som du anger här finns på datorn. Lägg till följande kodfragment efter namnområdesdeklarationerna.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

I de återstående avsnitten i artikeln kan du se hur du använder tillgängliga .NET-metoder för att utföra åtgärder, till exempel autentisera användare, skapa ett Data Lake Store-konto, ladda upp filer, osv. Ett fullständigt exempel på hur du arbetar med Data Lake Store finns i [Bilagan](#appendix-sample-code) längst ned i den här artikeln.

## Autentisera användaren

Det finns två sätt att autentisera med hjälp av Azure Active Directory:

* **Interaktivt**, där en användare loggar in med hjälp av programmet. Detta är implementerat i metoden `AuthenticateUser` i kodfragmentet nedan.

* **Icke-interaktivt**, där programmet tillhandahåller egna autentiseringsuppgifter. Detta är implementerat i metoden `AuthenticateAppliaction` i kodfragmentet nedan.

### Interaktiv autentisering

I följande fragment visas en `AuthenticateUser`-metod som du kan använda för en interaktiv inloggning.

    // Authenticate the user with AAD through an interactive popup.
    // You need to have an application registered with AAD in order to authenticate.
    //   For more information and instructions on how to register your application with AAD, see:
    //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
    public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
    {
        var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
    
        var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
            PromptBehavior.Auto, UserIdentifier.AnyUser);
    
        return new TokenCredentials(tokenAuthResult.AccessToken);
    }

### Icke-interaktiv autentisering

I följande fragment visas en `AuthenticateApplication`-metod som du kan använda för en icke-interaktiv inloggning.

    // Authenticate the application with AAD through the application's secret key.
    // You need to have an application registered with AAD in order to authenticate.
    //   For more information and instructions on how to register your application with AAD, see:
    //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
    public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
    {
        var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
        var credential = new ClientCredential(appClientId, clientSecret);
    
        var tokenAuthResult = authContext.AcquireToken(resource, credential);
    
        return new TokenCredentials(tokenAuthResult.AccessToken);
    }
    
## Skapa ett Data Lake Store-konto

I följande fragment visas en `CreateAccount`-metod som du kan använda för att skapa ett Data Lake Store-konto.

    // Create Data Lake Store account
    public static void CreateAccount()
    {
        var adlsParameters = new DataLakeStoreAccount(location: _location);
        _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
    } 

## Lista över alla Data Lake Store-konton inom en prenumeration

Följande fragment visar en `ListAdlStoreAccounts`-metod som du kan använda för att sammanställa en lista över alla Data Lake Store-konton inom en viss Azure-prenumeration.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccount>(response);
    
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
    
        return accounts;
    }

## Skapa en katalog

I följande fragment visas en `CreateDirectory`-metod som du kan använda för att skapa en katalog i ett Data Lake Store-konto.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## Ladda upp en fil till Data Lake Store

I följande fragment visas en `UploadFile`-metod som du kan använda för att ladda upp filer till ett Data Lake Store-konto.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

## Hämta fil- eller kataloginformation

I följande fragment visas en `GetItemInfo`-metod som du kan använda för att hämta information om en fil eller katalog som är tillgänglig i Data Lake Store. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## Lista över fil eller kataloger

I följande fragment visas en `ListItem`-metod som du kan använda för att sammanställa en lista över filen och katalogerna i ett Data Lake Store-konto.
    
    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## Sammanfoga filer

I följande fragment visas en `ConcatenateFiles`-metod som du kan använda för att sammanfoga filer. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## Lägg till till en fil

I följande fragment visas en `AppendToFile`-metod som du kan använda för att lägga till data till en fil som redan finns i ett Data Lake Store-konto.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## Hämta en fil

I följande fragment visas en `DownloadFile`-metod som du kan använda för att hämta en fil från ett Data Lake Store-konto.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);

        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## Ta bort ett Data Lake Store-konto

I följande fragment visas en `DeleteAccount`-metod som du kan använda för att ta bort ett Data Lake Store-konto.

    // Delete account
    public static void DeleteAccount()
    {
        _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
    }

## Bilaga: Exempelkod

Följande fragment är ett omfattande kodexempel som du kan kopiera och klistra in i ditt program för att se en åtgärd för slutpunkt till slutpunkt på Data Lake Store. Kontrollera innan du kör kodfragmentet att du anger nödvändiga värden såsom Data Lake Store-namn, resursgruppens namn, osv. Du måste även ange de värden som krävs för Azure Active Directory-autentisering, till exempel **\<APPLICATION-CLIENT-ID>** , **\<APPLICATION-REPLY-URI>**och **\<SUBSCRIPTION-ID>**.

Även om kodfragmentet nedan ger metoder för båda dessa vägar, interaktiv och icke-interaktiv, har det interaktiva kodblocket markerats som en kommentar. Den interaktiva metoden kräver att du tillhandahåller klient-ID och omdirigerings-URI för AAD-programmet. Länken i förutsättningarna innehåller instruktioner om hur du skaffar dessa.

>[AZURE.NOTE] Om du vill ändra kodfragmentet och använda den icke-interaktiva (`AuthenticateApplication`) metoden i stället, måste du även ange klientautentiseringsnyckel utöver klient-ID och klientens svars-URI som indata för metoden. Artikeln [Skapa Active Directory-program och tjänstens huvudnamn med hjälp av portalen](../resource-group-create-service-principal-portal.md) innehåller även information om hur du skapar och hämtar klientautentiseringsnyckeln.
    
Kontrollera slutligen att den lokala sökvägen och filnamnet som du anger här finns på datorn. Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).



    using System;
    using System.IO;
    using System.Security;
    using System.Text;
    using System.Collections.Generic;
    using System.Linq;

    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;

    namespace SdkSample
    {
        class Program
        {
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;

            private static void Main(string[] args)
            {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                _location = "East US 2";

                string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                string remoteFolderPath = "/data_lake_path/";
                string remoteFilePath = remoteFolderPath + "file.txt";

                // Authenticate the user
                var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                    "<APPLICATION-CLIENT-ID>", new Uri("<APPLICATION-REPLY-URI>")); // TODO: Replace bracketed values.

                SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.

                // Run sample scenarios
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Creating a directory.");

                // Create a directory in the Data Lake Store
                CreateDirectory(remoteFolderPath);
                WaitForNewline("Directory created.", "Showing directory info.");

                // Get info about the directory in the Data Lake Store
                var itemInfo = GetItemInfo(remoteFolderPath);
                Console.WriteLine("Type: " + itemInfo.Type);
                Console.WriteLine("Last modified (UTC): " +
                                  new DateTime(1970, 1, 1, 0, 0, 0, 0, DateTimeKind.Utc).AddMilliseconds(
                                      itemInfo.ModificationTime.Value));
                WaitForNewline("Directory info shown.", "Uploading a file.");

                // Upload a file to the Data Lake Store
                UploadFile(localFilePath, remoteFilePath);
                WaitForNewline("File uploaded.", "Listing files and directories.");

                // List the files in the Data Lake Store
                var itemList = ListItems(remoteFolderPath);
                var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
                Console.WriteLine(String.Join("\r\n", fileMenuItems));
                WaitForNewline("Files and directories listed.", "Appending content to a file.");

                // Append to a file in the Data Lake Store
                AppendToFile(remoteFilePath, "123");
                WaitForNewline("Content appended.", "Downloading a file.");

                // Download a file from the Data Lake Store
                DownloadFile(remoteFilePath, localFilePath);
                WaitForNewline("File downloaded.", "Deleting account.");

                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted. You can now exit.");
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                if (!String.IsNullOrWhiteSpace(nextAction))
                {
                    Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                    Console.ReadLine();
                    Console.WriteLine(nextAction);
                }
                else
                {
                    Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                    Console.ReadLine();
                }
            }

            // Authenticate the user with AAD through an interactive popup.
            // You need to have an application registered with AAD in order to authenticate.
            //   For more information and instructions on how to register your application with AAD, see:
            //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
            public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);

                var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
                    PromptBehavior.Auto, UserIdentifier.AnyUser);

                return new TokenCredentials(tokenAuthResult.AccessToken);
            }
            
            /*
            // Authenticate the application with AAD through the application's secret key.
            // You need to have an application registered with AAD in order to authenticate.
            //   For more information and instructions on how to register your application with AAD, see:
            //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
            public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
                var credential = new ClientCredential(appClientId, clientSecret);

                var tokenAuthResult = authContext.AcquireToken(resource, credential);

                return new TokenCredentials(tokenAuthResult.AccessToken);
            }
            */

            //Set up clients
            public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
            {
                _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                _adlsClient.SubscriptionId = subscriptionId;

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }

            // Create account
            public static void CreateAccount()
            {
                // Create ADLS account
                var adlsParameters = new DataLakeStoreAccount(location: _location);
                _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }

            // Delete account
            public static void DeleteAccount()
            {
                _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
            }

            // List all ADLS accounts within the subscription
            public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
            {
                var response = _adlsClient.Account.List(_adlsAccountName);
                var accounts = new List<DataLakeStoreAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlsClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }

            // Upload a file
            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
                var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                var uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.Execute();
            }

            // Concatenate files
            public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
            {
                _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
            }

            // Get file or directory info
            public static FileStatusProperties GetItemInfo(string path)
            {
                return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
            }

            // List files and directories
            public static List<FileStatusProperties> ListItems(string directoryPath)
            {
                return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
            }

            // Download file
            public static void DownloadFile(string srcPath, string destPath)
            {
                var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
                var fileStream = new FileStream(destPath, FileMode.Create);

                stream.CopyTo(fileStream);
                fileStream.Close();
                stream.Close();
            }

            // Append to file
            public static void AppendToFile(string path, string content)
            {
                var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

                _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
            }

            // Create a directory
            public static void CreateDirectory(string path)
            {
                _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
            }
        }
    }


## Nästa steg

- [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
- [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Jun16_HO2-->


