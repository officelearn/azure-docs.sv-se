---
title: Azure Data Lake Storage Gen2 .NET SDK för filer & ACL:er
description: Använd Azure Storage-klientbiblioteket för att hantera kataloger och ACL (File and Directory Access Control List) i lagringskonton som har aktiverat hierarkiskt namnområde (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061572"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Använda .NET för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder .NET för att skapa och hantera kataloger, filer och behörigheter i lagringskonton som har aktiverat hierarkiskt namnområde (HNS). 

[Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Prover](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API-referens](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1 till Gen2 mappning](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Ge feedback](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagringskonto med hierarkiskt namnområde (HNS) aktiverat. Följ [dessa](data-lake-storage-quickstart-create-account.md) instruktioner för att skapa en.

## <a name="set-up-your-project"></a>Konfigurera projektet

För att komma igång installerar du [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet-paketet.

Mer information om hur du installerar NuGet-paket finns [i Installera och hantera paket i Visual Studio med NuGet Package Manager](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Lägg sedan till dessa med hjälp av satser överst i kodfilen.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Anslut till kontot

Om du vill använda kodavsnitten i den här artikeln måste du skapa en [DataLakeServiceClient-instans](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) som representerar lagringskontot. 

### <a name="connect-by-using-an-account-key"></a>Ansluta med hjälp av en kontonyckel

Det här är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en [DataLakeServiceClient-instans](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) med hjälp av en kontonyckel.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med Hjälp av Azure Active Directory (AD)

Du kan använda [Azure identity-klientbiblioteket för .NET för](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) att autentisera ditt program med Azure AD.

I det här exemplet skapas en [DataLakeServiceClient-instans](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) med hjälp av ett klient-ID, en klienthemlighet och ett klient-ID.  Information om hur du hämtar dessa värden finns [i Hämta en token från Azure AD för att godkänna begäranden från ett klientprogram](../common/storage-auth-aad-app.md).

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> Fler exempel finns i [Azure identity-klientbiblioteket för .NET documentation..](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)

## <a name="create-a-file-system"></a>Skapa ett filsystem

Ett filsystem fungerar som en behållare för dina filer. Du kan skapa en genom att anropa metoden [DataLakeServiceClient.CreateFileSystem.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync)

I det här exemplet `my-file-system`skapas ett filsystem med namnet . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalogreferens genom att anropa metoden [DataLakeFileSystemClient.CreateDirectoryAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync)

I det här `my-directory` exemplet läggs en katalog som heter till `my-subdirectory`i ett filsystem och sedan läggs till en underkatalog med namnet . 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog genom att anropa metoden [DataLakeDirectoryClient.RenameAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) Skicka sökvägen till den önskade katalogen en parameter. 

I det här exemplet byter du `my-subdirectory-renamed`namn på en underkatalog till namnet .

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

I det här `my-subdirectory-renamed` exemplet flyttas en katalog med `my-directory-2`namnet till en underkatalog till en katalog med namnet . 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog genom att anropa metoden [DataLakeDirectoryClient.Delete.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete)

I det här exemplet `my-directory`tas en katalog med namnet .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Hantera en ACL för katalog

Hämta åtkomstkontrollistan (ACL) för en katalog genom att anropa metoden [DataLakeDirectoryClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) och ange ACL genom att anropa metoden [DataLakeDirectoryClient.SetAccessControlList.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)

> [!NOTE]
> Om ditt program godkänner åtkomst med hjälp av Azure Active Directory (Azure AD) kontrollerar du att säkerhetsobjektet som programmet använder för att auktorisera åtkomst har tilldelats [rollen Lagringsblobbdataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Det här exemplet hämtar och anger `my-directory`åtkomstkontrollistan för en katalog med namnet . Strängen `user::rwx,group::r-x,other::rw-` ger den givande användaren läs-, skriv- och körningsbehörigheter, ger den ägande gruppen endast läs- och körningsbehörighet och ger alla andra läs- och skrivbehörighet.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Skapa först en filreferens i målkatalogen genom att skapa en instans av klassen [DataLakeFileClient.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) Ladda upp en fil genom att anropa metoden [DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) Se till att slutföra överföringen genom att anropa metoden [DataLakeFileClient.FlushAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync)

I det här exemplet överförs en `my-directory`textfil till en katalog med namnet .    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

> [!TIP]
> Om filstorleken är stor måste koden ringa flera samtal till [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Överväg att använda metoden [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) i stället. På så sätt kan du ladda upp hela filen i ett enda samtal. 
>
> Se nästa avsnitt för ett exempel.

## <a name="upload-a-large-file-to-a-directory"></a>Ladda upp en stor fil till en katalog

Använd metoden [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) för att ladda upp stora filer utan att behöva ringa flera anrop till metoden [DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>Hantera en ACL-fil

Hämta åtkomstkontrollistan (ACL) för en fil genom att anropa metoden [DataLakeFileClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) och ange ACL genom att anropa metoden [DataLakeFileClient.SetAccessControlList.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist)

> [!NOTE]
> Om ditt program godkänner åtkomst med hjälp av Azure Active Directory (Azure AD) kontrollerar du att säkerhetsobjektet som programmet använder för att auktorisera åtkomst har tilldelats [rollen Lagringsblobbdataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Det här exemplet hämtar och anger `my-file.txt`åtkomstkontrollistan för en fil med namnet . Strängen `user::rwx,group::r-x,other::rw-` ger den givande användaren läs-, skriv- och körningsbehörigheter, ger den ägande gruppen endast läs- och körningsbehörighet och ger alla andra läs- och skrivbehörighet.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Hämta från en katalog 

Skapa först en [DataLakeFileClient-instans](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) som representerar den fil som du vill hämta. Använd metoden [DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) och tolka returvärdet för att hämta ett [Stream-objekt.](https://docs.microsoft.com/dotnet/api/system.io.stream) Använd valfriT .NET-filbearbetnings-API för att spara byte från dataströmmen till en fil. 

I det här exemplet används en [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) och en [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) för att spara byte i en fil. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Ange kataloginnehåll genom att anropa metoden [FileSystemClient.GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) och sedan räkna upp resultatet.

I det här exemplet skrivs namnen på varje `my-directory`fil som finns i en katalog med namnet .

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>Se även

* [Referensdokumentation för API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Prover](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 till Gen2 mappning](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Ge feedback](https://github.com/Azure/azure-sdk-for-net/issues)

