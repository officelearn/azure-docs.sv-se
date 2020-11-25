---
title: 'Azure Data Lake Storage Gen2 Java SDK för filer & ACL: er'
description: Använd Azure Storage bibliotek för Java för att hantera kataloger och åtkomst kontrol listor för filer och kataloger (ACL) i lagrings konton med hierarkiskt namn område (HNS) aktiverat.
author: normesta
ms.service: storage
ms.date: 09/10/2020
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: f6e8219f744a91628f9860f0af133c07eddb4253
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913393"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Använd Java för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder Java för att skapa och hantera kataloger, filer och behörigheter i lagrings konton med hierarkiskt namn område (HNS) aktiverat. 

[Paket (maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [API-referens](/java/api/overview/azure/storage-file-datalake-readme)  |  [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](../common/storage-account-create.md) anvisningarna för att skapa en.

## <a name="set-up-your-project"></a>Konfigurera projektet

Kom igång genom att öppna [den här sidan](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) och hitta den senaste versionen av Java-biblioteket. Öppna sedan *pom.xml* -filen i text redigeraren. Lägg till ett beroende element som refererar till den versionen.

Om du planerar att autentisera ditt klient program med hjälp av Azure Active Directory (AD) lägger du till ett beroende till klient biblioteket för Azure Secret. Se  [lägga till det hemliga klient biblioteks paketet i projektet](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Lägg sedan till dessa import uttryck i din kod fil.

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

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av en konto nyckel.

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Anslut med hjälp av Azure Active Directory (Azure AD)

Du kan använda [klient biblioteket för Azure Identity för Java för](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) att autentisera ditt program med Azure AD.

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  Om du vill hämta dessa värden läser du [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md).

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
> Fler exempel finns i [klient biblioteket för Azure Identity-klienten för Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) -dokumentation.


## <a name="create-a-container"></a>Skapa en container

En behållare fungerar som ett fil system för dina filer. Du kan skapa en genom att anropa metoden **DataLakeServiceClient. createFileSystem** .

I det här exemplet skapas en behållare med namnet `my-file-system` . 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens genom att anropa metoden **DataLakeFileSystemClient. createDirectory** .

Det här exemplet lägger till en katalog med namnet `my-directory` i en behållare och lägger sedan till en under katalog med namnet `my-subdirectory` . 

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

Byt namn på eller flytta en katalog genom att anropa metoden **DataLakeDirectoryClient. Rename** . Skicka sökvägen till önskad katalog en parameter. 

I det här exemplet byter namn på en under katalog till namnet `my-subdirectory-renamed` .

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

I det här exemplet flyttas en katalog med namnet `my-subdirectory-renamed` till en under katalog till en katalog med namnet `my-directory-2` . 

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

Ta bort en katalog genom att anropa metoden **DataLakeDirectoryClient. deleteWithResponse** .

Det här exemplet tar bort en katalog med namnet `my-directory` .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Börja med att skapa en fil referens i mål katalogen genom att skapa en instans av klassen **DataLakeFileClient** . Ladda upp en fil genom att anropa metoden **DataLakeFileClient. append** . Se till att slutföra överföringen genom att anropa metoden **DataLakeFileClient. FlushAsync** .

I det här exemplet överförs en textfil till en katalog med namnet `my-directory` .

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new BufferedInputStream(new FileInputStream(file));

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Om din fil storlek är stor måste koden göra flera anrop till metoden **DataLakeFileClient. append** . Överväg att använda metoden **DataLakeFileClient. uploadFromFile** i stället. På så sätt kan du ladda upp hela filen i ett enda anrop. 
>
> Se nästa avsnitt för ett exempel.

## <a name="upload-a-large-file-to-a-directory"></a>Ladda upp en stor fil till en katalog

Använd metoden **DataLakeFileClient. uploadFromFile** för att överföra stora filer utan att behöva göra flera anrop till **DataLakeFileClient. append** -metoden.

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```

## <a name="download-from-a-directory"></a>Ladda ned från en katalog

Börja med att skapa en **DataLakeFileClient** -instans som representerar den fil som du vill ladda ned. Använd metoden **DataLakeFileClient. Read** för att läsa filen. Använd alla .NET-fil bearbetnings-API: er för att spara byte från data strömmen till en fil. 

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

Det här exemplet skriver ut namnen på varje fil som finns i en katalog med namnet `my-directory` .

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

## <a name="manage-access-control-lists-acls"></a>Hantera åtkomst kontrol listor (ACL: er)

Du kan hämta, ange och uppdatera åtkomst behörigheter för kataloger och filer.

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera åtkomst kontrollerar du att ditt säkerhets objekt har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Hantera en katalog-ACL

Det här exemplet hämtar och anger sedan ACL för en katalog med namnet `my-directory` . Det här exemplet ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs behörighet.

> [!NOTE]
> Om ditt program tillåter åtkomst genom att använda Azure Active Directory (Azure AD) måste du kontrol lera att det säkerhets objekt som programmet använder för att auktorisera åtkomst har tilldelats rollen som [lagrings-BLOB-dataägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

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

Du kan också hämta och ange ACL: en för rot katalogen för en behållare. Om du vill hämta rot katalogen skickar du en tom sträng ( `""` ) till metoden **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="manage-a-file-acl"></a>Hantera en fil-ACL

Det här exemplet hämtar och anger sedan ACL för en fil med namnet `upload-file.txt` . Det här exemplet ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs behörighet.

> [!NOTE]
> Om ditt program tillåter åtkomst genom att använda Azure Active Directory (Azure AD) måste du kontrol lera att det säkerhets objekt som programmet använder för att auktorisera åtkomst har tilldelats rollen som [lagrings-BLOB-dataägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

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

### <a name="set-an-acl-recursively"></a>Ange en ACL rekursivt

Du kan lägga till, uppdatera och ta bort ACL rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt. Mer information finns i [ange åtkomst kontrol listor (ACL) rekursivt för Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Se även

* [Referensdokumentation för API](/java/api/overview/azure/storage-file-datalake-readme)
* [Paket (maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)