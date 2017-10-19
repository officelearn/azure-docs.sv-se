---
title: ".NET SDK: Filsystemsåtgärder på Azure Data Lake Store | Microsoft Docs"
description: "Använda Azure Data Lake Store .NET SDK till att utföra filsystemsåtgärder på Data Lake Store, som att skapa mappar osv."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: f525bd81ebbc27a4730bdced5c8778b6f2ac69b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>Filsystemsåtgärder på Azure Data Lake Store med hjälp av .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

I den här artikeln får du lära dig att utföra filsystemsåtgärder i Data Lake Store med .NET SDK. Filsystemsåtgärder är bland annat att skapa mappar på ett Data Lake Store-konto, ladda upp filer, ladda ned filer osv.

Instruktioner för hur du utför kontohanteringsåtgärder på Data Lake Store med .NET SDK finns i [Kontohanteringsåtgärder på Data Lake Store med hjälp av .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Krav
* **Visual Studio 2013, 2015 eller 2017**. Anvisningarna nedan använder Visual Studio 2017.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Store-konto**. Mer information om hur du skapar ett konto finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)

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
5. Lägg till NuGet-paketen i projektet.

   1. Högerklicka på projektnamnet i Solution Explorer och klicka på **Hantera NuGet-paket**.
   2. På fliken **NuGet Package Manager** ser du till att **Paketkälla** har angetts som **nuget.org** och att kryssrutan **Inkludera förhandsversion** är markerad.
   3. Sök efter och installera följande NuGet-paket:

      * `Microsoft.Azure.Management.DataLake.Store` – I den här självstudiekursen används v2.1.3-förhandsversionen.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` –I den här självstudiekursen används v2.2.12.

        ![Lägg till en NuGet-källa](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Skapa ett nytt Azure Data Lake-konto")
   4. Stäng **NuGet Package Manager**.
6. Öppna **Program.cs**, ta bort den befintliga koden och lägg sedan till följande instruktioner för att lägga till referenser till namnområden.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

7. Deklarera variablerna enligt nedan och ange värden för platshållarna. Kontrollera också att den lokala sökvägen och filnamnet som du anger här finns på datorn.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static async void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

I de återstående avsnitten i artikeln kan du se hur du använder tillgängliga .NET-metoder för att utföra åtgärder som autentisering, ladda upp filer, osv.

## <a name="authentication"></a>Autentisering

* Information om slutanvändarautentisering för programmet finns i [Slutanvändarautentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Information om tjänst-till-tjänst-autentisering för programmet finns i [Tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-objects"></a>Skapa klientobjekt
Följande fragment skapar Data Lake Store-kontot och filsystemklientobjekten, som används för att skicka begäranden till tjänsten.

    // Create client objects
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## <a name="create-a-directory"></a>Skapa en katalog
Lägg till följande metod i klassen. I fragmentet visas en `CreateDirectory()`-metod som du kan använda för att skapa en katalog i ett Data Lake Store-konto.

    // Create a directory
    public static void CreateDirectory(string path)
    {
            _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

Lägg till följande fragment i `Main()`-metoden för att anropa `CreateDirectory()`-metoden.

    CreateDirectory(remoteFolderPath);
    Console.WriteLine("Created a directory in the Data Lake Store account. Press any key to continue ...");
    Console.ReadLine();

## <a name="upload-a-file"></a>Överför en fil
Lägg till följande metod i klassen. I fragmentet visas en `UploadFile()`-metod som du kan använda för att ladda upp filer till ett Data Lake Store-konto. SDK har stöd för rekursiv överföring och hämtning mellan en lokal filsökväg och en Data Lake Store-sökväg.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

Lägg till följande fragment i `Main()`-metoden för att anropa `UploadFile()`-metoden.

    UploadFile(localFilePath, remoteFilePath, true);
    Console.WriteLine("Uploaded file in the directory. Press any key to continue...");
    Console.ReadLine();
    

## <a name="get-file-or-directory-info"></a>Hämta fil- eller kataloginformation
I följande fragment visas en `GetItemInfo()`-metod som du kan använda för att hämta information om en fil eller katalog som är tillgänglig i Data Lake Store.

    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

Lägg till följande fragment i `Main()`-metoden för att anropa `GetItemInfo()`-metoden.

    
    var fileProperties = GetItemInfo(remoteFilePath);
    Console.WriteLine("The owner of the file at the path is:", fileProperties.Owner);
    Console.WriteLine("Press any key to continue...");
    Console.ReadLine();

## <a name="list-file-or-directories"></a>Lista över fil eller kataloger
I följande fragment visas en `ListItems()`-metod som du kan använda för att sammanställa en lista över filen och katalogerna i ett Data Lake Store-konto.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

Lägg till följande fragment i `Main()`-metoden för att anropa `ListItems()`-metoden.

    var itemList = ListItems(remoteFolderPath);
    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
    Console.WriteLine(String.Join("\r\n", fileMenuItems));
    Console.WriteLine("Files and directories listed. Press any key to continue ...");
    Console.ReadLine();

## <a name="concatenate-files"></a>Sammanfoga filer
I följande fragment visas en `ConcatenateFiles()`-metod som du kan använda för att sammanfoga filer.

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

Lägg till följande fragment i `Main()`-metoden för att anropa `ConcatenateFiles()`-metoden. Det här fragmentet förutsätter att du har laddat upp ytterligare en fil till Data Lake Store-kontot och att filens sökväg anges i strängen *anotherRemoteFilePath*.

    string[] stringOfFiles = new String[] {remoteFilePath, anotherRemoteFilePath};
    string destFilePath = Path.Combine(remoteFolderPath, "Concatfile.txt");
    ConcatenateFiles(stringOfFiles, destFilePath);
    Console.WriteLine("Files concatinated. Press any key to continue ...");
    Console.ReadLine();

## <a name="append-to-a-file"></a>Lägg till till en fil
I följande fragment visas en `AppendToFile()`-metod som du kan använda för att lägga till data till en fil som redan finns i ett Data Lake Store-konto.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

Lägg till följande fragment i `Main()`-metoden för att anropa `AppendToFile()`-metoden.

    AppendToFile(remoteFilePath, "123");
    Console.WriteLine("Content appended. Press any key to continue ...");
    Console.ReadLine();

## <a name="download-a-file"></a>Hämta en fil
I följande fragment visas en `DownloadFile()`-metod som du kan använda för att hämta en fil från ett Data Lake Store-konto.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:true);
    }

Lägg till följande fragment i `Main()`-metoden för att anropa `DownloadFile()`-metoden.

    DownloadFile(destFilePath, localFilePath);
    Console.WriteLine("File downloaded. Press any key to continue ...");
    Console.ReadLine();

## <a name="see-also"></a>Se även
* [Kontohanteringsåtgärder på Azure Data Lake Store med hjälp av .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store .NET SDK-referens](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
