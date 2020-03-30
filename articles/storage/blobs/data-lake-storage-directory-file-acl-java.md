---
title: Azure Data Lake Storage Gen2 Java SDK för filer & ACL:er
description: Använd Azure Storage-bibliotek för Java för att hantera kataloger och ACL (File and Directory Access Control List) i lagringskonton som har aktiverat hierarkiskt namnområde (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061565"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Använda Java för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder Java för att skapa och hantera kataloger, filer och behörigheter i lagringskonton som har aktiverat hierarkiskt namnområde (HNS). 

[Paket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Prover](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [Gen1 till Gen2 mappning](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagringskonto med hierarkiskt namnområde (HNS) aktiverat. Följ [dessa](data-lake-storage-quickstart-create-account.md) instruktioner för att skapa en.

## <a name="set-up-your-project"></a>Konfigurera projektet

För att komma igång, öppna [den här sidan](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) och hitta den senaste versionen av Java-biblioteket. Öppna sedan *filen pom.xml* i textredigeraren. Lägg till ett beroendeelement som refererar till den versionen.

Om du planerar att autentisera klientprogrammet med hjälp av Azure Active Directory (AD) lägger du till ett beroende i Azure Secret Client Library. Se [Lägga till paketet för det hemliga klientbiblioteket i projektet](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Lägg sedan till dessa importsatser i kodfilen.

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Anslut till kontot 

Om du vill använda kodavsnitten i den här artikeln måste du skapa en **DataLakeServiceClient-instans** som representerar lagringskontot. 

### <a name="connect-by-using-an-account-key"></a>Ansluta med hjälp av en kontonyckel

Det här är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient-instans** med hjälp av en kontonyckel.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Ansluta med Hjälp av Azure Active Directory (Azure AD)

Du kan använda [Azure identity-klientbiblioteket för Java för](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) att autentisera ditt program med Azure AD.

I det här exemplet skapas en **DataLakeServiceClient-instans** med hjälp av ett klient-ID, en klienthemlighet och ett klient-ID.  Information om hur du hämtar dessa värden finns [i Hämta en token från Azure AD för att godkänna begäranden från ett klientprogram](../common/storage-auth-aad-app.md).

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Fler exempel finns i [Azure identity-klientbiblioteket för Java-dokumentation.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)


## <a name="create-a-file-system"></a>Skapa ett filsystem

Ett filsystem fungerar som en behållare för dina filer. Du kan skapa en genom att anropa metoden **DataLakeServiceClient.createFileSystem.**

I det här exemplet `my-file-system`skapas ett filsystem med namnet . 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalogreferens genom att anropa metoden **DataLakeFileSystemClient.createDirectory.**

I det här `my-directory` exemplet läggs en katalog som heter till `my-subdirectory`i ett filsystem och sedan läggs till en underkatalog med namnet . 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog genom att anropa metoden **DataLakeDirectoryClient.rename.** Skicka sökvägen till den önskade katalogen en parameter. 

I det här exemplet byter du `my-subdirectory-renamed`namn på en underkatalog till namnet .

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

I det här `my-subdirectory-renamed` exemplet flyttas en katalog med `my-directory-2`namnet till en underkatalog till en katalog med namnet . 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog genom att anropa metoden **DataLakeDirectoryClient.deleteWithResponse.**

I det här exemplet `my-directory`tas en katalog med namnet .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Hantera en ACL för katalog

Det här exemplet hämtar och anger sedan `my-directory`åtkomstkontrollistan för en katalog med namnet . Det här exemplet ger den givande användaren läs-, skriv- och körningsbehörigheter, ger den ägande gruppen endast läs- och körningsbehörighet och ger alla andra läsbehörighet.

> [!NOTE]
> Om ditt program godkänner åtkomst med hjälp av Azure Active Directory (Azure AD) kontrollerar du att säkerhetsobjektet som programmet använder för att auktorisera åtkomst har tilldelats [rollen Lagringsblobbdataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Skapa först en filreferens i målkatalogen genom att skapa en instans av klassen **DataLakeFileClient.** Ladda upp en fil genom att anropa metoden **DataLakeFileClient.addd.** Se till att slutföra överföringen genom att anropa metoden **DataLakeFileClient.FlushAsync.**

I det här exemplet överförs en `my-directory`textfil till en katalog med namnet .'

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Om filstorleken är stor måste koden ringa flera samtal till metoden **DataLakeFileClient.addd.** Överväg att använda metoden **DataLakeFileClient.uploadFromFile** i stället. På så sätt kan du ladda upp hela filen i ett enda samtal. 
>
> Se nästa avsnitt för ett exempel.

## <a name="upload-a-large-file-to-a-directory"></a>Ladda upp en stor fil till en katalog

Använd metoden **DataLakeFileClient.uploadFromFile** för att ladda upp stora filer utan att behöva ringa flera samtal till metoden **DataLakeFileClient.addd.**

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>Hantera en ACL-fil

Det här exemplet hämtar och anger sedan `upload-file.txt`åtkomstkontrollistan för en fil med namnet . Det här exemplet ger den givande användaren läs-, skriv- och körningsbehörigheter, ger den ägande gruppen endast läs- och körningsbehörighet och ger alla andra läsbehörighet.

> [!NOTE]
> Om ditt program godkänner åtkomst med hjälp av Azure Active Directory (Azure AD) kontrollerar du att säkerhetsobjektet som programmet använder för att auktorisera åtkomst har tilldelats [rollen Lagringsblobbdataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Hämta från en katalog

Skapa först en **DataLakeFileClient-instans** som representerar den fil som du vill hämta. Använd metoden **DataLakeFileClient.read** för att läsa filen. Använd valfriT .NET-filbearbetnings-API för att spara byte från dataströmmen till en fil. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

I det här exemplet skrivs namnen på varje `my-directory`fil som finns i en katalog med namnet .

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>Se även

* [Referensdokumentation för API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [Paket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Prover](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 till Gen2 mappning](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)