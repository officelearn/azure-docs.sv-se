---
title: "Använda .NET SDK för att utveckla program i Azure Data Lake Store | Microsoft Docs"
description: "Använda Azure Data Lake Store .NET SDK för att skapa ett Data Lake Store-konto och utföra grundläggande åtgärder i Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/07/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 1fd8fe3847299d98a55a16ab400b43be074a5f33
ms.lasthandoff: 03/21/2017


---
# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Kom igång med Azure Data Lake Store med hjälp av .NET SDK
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Lär dig mer om att använda [Azure Data Lake Store .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx) för att utföra grundläggande åtgärder som att skapa mappar, ladda upp och hämta datafiler, osv. Mer information om Data Lake finns i [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Krav
* **Visual Studio 2013, 2015 eller 2017**. Anvisningarna nedan använder Visual Studio 2015 Update 2.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Store-konto**. Mer information om hur du skapar ett konto finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)

* **Skapa ett program i Azure Active Directory**. Du kan använda Azure AD-program för att autentisera Data Lake Store-program med Azure AD. Det finns olika sätt att autentisera med Azure AD: **slutanvändarens autentisering** eller **serviceautentisering**. Instruktioner och mer information om hur du autentiserar finns i [Autentisera med Data Lake Store med Azure Active Directory (Authenticate with Data Lake Store using Azure Active Directory)](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Skapa ett .NET-program
1. Öppna Visual Studio och skapa ett konsolprogram.
2. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**.
3. Från **Nytt projekt** anger eller väljer du följande värden:
   
   | Egenskap | Värde |
   | --- | --- |
   | Kategori |Mallar/Visual C#/Windows |
   | Mall |Konsolprogram |
   | Namn |CreateADLApplication |
4. Klicka på **OK** för att skapa projektet.
5. Lägg till Nuget-paketen i projektet.
   
   1. Högerklicka på projektnamnet i Solution Explorer och klicka på **Hantera NuGet-paket**.
   2. I fliken **Nuget Package Manager** ser du till att **Paketkällan** har angetts som **nuget.org** och att kryssrutan **Inkludera förhandsversion** är markerad.
   3. Sök efter och installera följande NuGet-paket:
      
      * `Microsoft.Azure.Management.DataLake.Store` - I den här självstudien används v1.0.4.
      * `Microsoft.Azure.Management.DataLake.StoreUploader` - I den här självstudien används förhandsutgåvan av v1.0.1.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` -I den här självstudien används v2.2.11.
        
        ![Lägg till en Nuget-källa](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Skapa ett nytt Azure Data Lake-konto")
   4. Stäng **Nuget Package Manager**.
6. Öppna **Program.cs**, ta bort den befintliga koden och lägg sedan till följande instruktioner för att lägga till referenser till namnområden.
   
        using System;
        using System.IO;
    using System.Security.Cryptography.X509Certificates; // Krävas endast om du använder en Azure AD-app som skapats med certifikat      using System.Threading;
   
        using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;  using Microsoft.Azure.Management.DataLake.StoreUploader;  using Microsoft.IdentityModel.Clients.ActiveDirectory;  using Microsoft.Rest.Azure.Authentication;

7. Deklarera variablerna enligt nedan och ange värden för Data Lake Store-namnet och resursgruppens namn som redan finns. Kontrollera också att den lokala sökvägen och filnamnet som du anger här finns på datorn. Lägg till följande kodfragment efter namnområdesdeklarationerna.
   
        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
   
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

I de återstående avsnitten i artikeln kan du se hur du använder tillgängliga .NET-metoder för att utföra åtgärder som autentisering, ladda upp filer, osv.

## <a name="authentication"></a>Autentisering

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Om du använder autentisering för slutanvändare (rekommenderas för den här kursen)

Använd detta med ett befintligt internt Azure AD-program för att autentisera programmet **interaktivt**, vilket innebär att du blir ombedd att ange dina autentiseringsuppgifter för Azure. 

För att underlätta användningen använder kodavsnittet nedan de standardvärden för klient-ID och omdirigerings-URI som fungerar med alla Azure-prenumerationen. Om du vill få hjälp att slutföra den här kursen snabbare, rekommenderar vi att du använder den här metoden. I kodavsnittet nedan anger du bara värdet för ditt klient-ID. Du kan hämta den med hjälp av anvisningarna i [Create an Active Directory Application](data-lake-store-end-user-authenticate-using-active-directory.md) (Skapa ett Active Directory-program).

    // User login via interactive popup
    // Use the client ID of an existing AAD Web application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;

Några saker att känna till om följande utdrag.

* För att hjälpa dig att slutföra kursen snabbare använder utdraget en Azure AD-domän och ett klient-ID som är tillgängligt som standard för alla Azure-prenumerationer. Så kan du **använder detta utdrag i befintligt skick i ditt program**.
* Men om du vill använda en egen Azure AD-domän och ett eget programklient-ID måste du skapa ett eget Azure AD-program och sedan använda Azure AD-domänens ID, klient-ID och omdirigerings-URI för det program som du skapade. Se [Create an Active Directory Application for end-user authentication with Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md) (Skapa ett Active Directory-program för slutanvändarautentisering med Data Lake Store) för anvisningar.

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Om du använder serviceautentisering med klienthemlighet
Följande kodavsnitt kan användas för att autentisera ditt program **icke-interaktivt** med hjälp av klienthemlighet/-nyckeln för ett program-/tjänsthuvudnamn. Använd det här med ett befintligt Azure AD-program av typen webbapp. Anvisningar för att skapa Azure AD-webbappen och hur hämta klient-ID:t och klienthemligheten som krävs i kodavsnittet nedan finns i [Create an Active Directory Application for service-to-service authentication with Data Lake Store](data-lake-store-authenticate-using-active-directory.md) (Skapa ett Active Directory-program för autentisering mellan tjänster med Data Lake Store).

    // Service principal / appplication authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = await ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential);

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Om du använder serviceautentisering med certifikat
Ett tredje alternativ är att använda följande kodavsnitt för att autentisera ditt program **icke-interaktivt**, med hjälp av certifikatet för ett huvudnamn till Azure Active Directory-program eller -tjänst. Använd det här med ett befintligt [Azure AD-program med certifikat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = await ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate);

## <a name="create-client-objects"></a>Skapa klientobjekt
Följande fragment skapar Data Lake Store-kontot och filsystemklientobjekten, som används för att skicka begäranden till tjänsten.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds) { SubscriptionId = _subId };
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Lista över alla Data Lake Store-konton inom en prenumeration
Följande fragment visar en lista över alla Data Lake Store-konton inom en viss Azure-prenumeration.

    // List all ADLS accounts within the subscription
    public static async Task<List<DataLakeStoreAccount>> ListAdlStoreAccounts()
    {
        var response = await _adlsClient.Account.ListAsync();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="create-a-directory"></a>Skapa en katalog
I följande fragment visas en `CreateDirectory`-metod som du kan använda för att skapa en katalog i ett Data Lake Store-konto.

    // Create a directory
    public static async Task CreateDirectory(string path)
    {
        await _adlsFileSystemClient.FileSystem.MkdirsAsync(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Överför en fil
I följande fragment visas en `UploadFile`-metod som du kan använda för att ladda upp filer till ett Data Lake Store-konto.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader` har stöd för rekursiv överföring och hämtning mellan en lokal filsökväg och en Data Lake Store-sökväg.    

## <a name="get-file-or-directory-info"></a>Hämta fil- eller kataloginformation
I följande fragment visas en `GetItemInfo`-metod som du kan använda för att hämta information om en fil eller katalog som är tillgänglig i Data Lake Store. 

    // Get file or directory info
    public static async Task<FileStatusProperties> GetItemInfo(string path)
    {
        return await _adlsFileSystemClient.FileSystem.GetFileStatusAsync(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Lista över fil eller kataloger
I följande fragment visas en `ListItem`-metod som du kan använda för att sammanställa en lista över filen och katalogerna i ett Data Lake Store-konto.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Sammanfoga filer
I följande fragment visas en `ConcatenateFiles`-metod som du kan använda för att sammanfoga filer. 

    // Concatenate files
    public static Task ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        await _adlsFileSystemClient.FileSystem.ConcatAsync(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Lägg till till en fil
I följande fragment visas en `AppendToFile`-metod som du kan använda för att lägga till data till en fil som redan finns i ett Data Lake Store-konto.

    // Append to file
    public static async Task AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            await _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

## <a name="download-a-file"></a>Hämta en fil
I följande fragment visas en `DownloadFile`-metod som du kan använda för att hämta en fil från ett Data Lake Store-konto.

    // Download file
    public static async Task DownloadFile(string srcPath, string destPath)
    {
        using (var stream = await _adlsFileSystemClient.FileSystem.OpenAsync(_adlsAccountName, srcPath))
        using (var fileStream = new FileStream(destPath, FileMode.Create))
        {
            await stream.CopyToAsync(fileStream);
        }
    }

## <a name="next-steps"></a>Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Store .NET SDK-referens](https://msdn.microsoft.com/library/mt581387.aspx)
* [Data Lake Store REST-referens](https://msdn.microsoft.com/library/mt693424.aspx)


