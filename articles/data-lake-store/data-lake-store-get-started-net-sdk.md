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
ms.date: 11/21/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 8ec76c597dfb59860b456e42a78239c67d289f13


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
* **Visual Studio 2013 eller 2015**. Anvisningarna nedan använder Visual Studio 2015.
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
      
      * `Microsoft.Azure.Management.DataLake.Store` - De här självstudierna använder v0.12.5-förhandsversion.
      * `Microsoft.Azure.Management.DataLake.StoreUploader` - De här självstudierna använder v0.10.6-förhandsversion.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - De här självstudierna använder v2.2.8-förhandsversion.
        
        ![Lägg till en Nuget-källa](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Skapa ett nytt Azure Data Lake-konto")
   4. Stäng **Nuget Package Manager**.
6. Öppna **Program.cs**, ta bort den befintliga koden och lägg sedan till följande instruktioner för att lägga till referenser till namnområden.
   
        using System;
        using System.Threading;
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
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
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

I de återstående avsnitten i artikeln kan du se hur du använder tillgängliga .NET-metoder för att utföra åtgärder som autentisering, ladda upp filer, osv.

## <a name="authentication"></a>Autentisering
### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Om du använder autentisering för slutanvändare (rekommenderas för den här kursen)
Använd det här med ett befintligt ”internt Azure AD-klientprogram”. Ett sådant anges nedan. Om du vill få hjälp att slutföra den här kursen snabbare, rekommenderar vi att du använder den här metoden.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Några saker att känna till om följande utdrag.

* För att hjälpa dig att slutföra kursen snabbare använder utdraget en Azure AD-domän och ett klient-ID som är tillgängligt som standard för alla Azure-prenumerationer. Så kan du **använder detta utdrag i befintligt skick i ditt program**.
* Men om du vill använda en egen Azure AD-domän och program klient-ID måste du skapa ett enhetligt Azure AD-program och sedan använda Azure AD-domänen, klient-ID och omdirigerings-URI för det program som du skapade. I [Create an Active Directory Application (Skapa ett program i Active Directory)](data-lake-store-end-user-authenticate-using-active-directory.md) finns instruktioner.

> [!NOTE]
> Anvisningarna i länkarna ovan är för ett Azure AD-webbprogram. Stegen är dock exakt samma även om du väljer att skapa ett internt klientprogram i stället. 
> 
> 

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Om du använder serviceautentisering med klienthemlighet
Du kan använda följande fragment för att autentisera ditt program icke-interaktivt, med hjälp av klienthemlighet/nyckel för ett program/tjänstobjekt. Använd det här med ett befintligt [Azure AD-"webbapp"-program](../azure-resource-manager/resource-group-create-service-principal-portal.md).

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Om du använder serviceautentisering med certifikat
Ett tredje alternativ är att använda följande fragment för att autentisera ditt program icke-interaktivt, med hjälp av certifikatet för ett program/tjänstobjekt. Använd det här med ett befintligt [Azure AD-"webbapp"-program](../azure-resource-manager/resource-group-create-service-principal-portal.md).

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>Skapa klientobjekt
Följande fragment skapar Data Lake Store-kontot och filsystemklientobjekten, som används för att skicka begäranden till tjänsten.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Lista över alla Data Lake Store-konton inom en prenumeration
Följande fragment visar en lista över alla Data Lake Store-konton inom en viss Azure-prenumeration.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
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
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
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
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
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
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Lägg till till en fil
I följande fragment visas en `AppendToFile`-metod som du kan använda för att lägga till data till en fil som redan finns i ett Data Lake Store-konto.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>Hämta en fil
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

## <a name="next-steps"></a>Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Store .NET SDK-referens](https://msdn.microsoft.com/library/mt581387.aspx)
* [Data Lake Store REST-referens](https://msdn.microsoft.com/library/mt693424.aspx)




<!--HONumber=Jan17_HO4-->


