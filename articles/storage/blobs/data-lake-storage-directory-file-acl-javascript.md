---
title: 'Använda Java Script för filer & ACL: er i Azure Data Lake Storage Gen2 (förhands granskning)'
description: Använd Azure Storage Data Lake klient bibliotek för Java Script för att hantera kataloger och åtkomst kontrol listor för filer och kataloger (ACL) i lagrings konton med hierarkiskt namn område (HNS) aktiverat.
author: normesta
ms.service: storage
ms.date: 12/18/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 8fd63adc76422b7fd9978e626208aa90593f8604
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154873"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Använd Java Script för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2 (för hands version)

Den här artikeln visar hur du använder Java Script för att skapa och hantera kataloger, filer och behörigheter i lagrings konton som har hierarkiskt namn område (HNS) aktiverat. 

> [!IMPORTANT]
> JavaScript-biblioteket som finns i den här artikeln är för närvarande en offentlig för hands version.

[Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](data-lake-storage-quickstart-create-account.md) anvisningarna för att skapa en.
> * Om du använder det här paketet i ett Node. js-program behöver du Node. js-8.0.0 eller högre.

## <a name="set-up-your-project"></a>Konfigurera projektet

Installera Data Lake klient bibliotek för Java Script genom att öppna ett terminalfönster och sedan skriva in följande kommando.

```javascript
npm install @azure/storage-file-datalake
```

Importera `storage-file-datalake`-paketet genom att placera den här instruktionen överst i din kod fil. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Anslut till kontot 

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. Det enklaste sättet att hämta ett är att använda en konto nyckel. 

I det här exemplet skapas en instans av **DataLakeServiceClient** med hjälp av en konto nyckel.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Den här metoden för auktorisering fungerar bara för Node. js-program. Om du planerar att köra din kod i en webbläsare kan du godkänna med hjälp av Azure Active Directory (AD). Information om hur du gör finns i Readme-filen [för Azure Storage fil data Lake klient bibliotek för Java Script](https://www.npmjs.com/package/@azure/storage-file-datalake) . 

## <a name="create-a-file-system"></a>Skapa ett filsystem

Ett fil system fungerar som en behållare för dina filer. Du kan skapa en genom att hämta en **FileSystemClient** -instans och sedan anropa metoden **FileSystemClient. Create** .

I det här exemplet skapas ett fil system med namnet `my-file-system`. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens genom att hämta en **DirectoryClient** -instans och anropa sedan metoden **DirectoryClient. Create** .

I det här exemplet läggs en katalog med namnet `my-directory` till i ett fil system. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog genom att anropa metoden **DirectoryClient. Rename** . Skicka sökvägen till önskad katalog en parameter. 

I det här exemplet byter namn på en under katalog till namnet `my-directory-renamed`.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

I det här exemplet flyttas en katalog med namnet `my-directory-renamed` till en under katalog till en katalog med namnet `my-directory-2`. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog genom att anropa metoden **DirectoryClient. Delete** .

I det här exemplet tas en katalog med namnet `my-directory`bort.   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Hantera en katalog-ACL

Det här exemplet hämtar och anger sedan ACL för en katalog med namnet `my-directory`. Det här exemplet ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs behörighet.

> [!NOTE]
> Om ditt program tillåter åtkomst genom att använda Azure Active Directory (Azure AD) måste du kontrol lera att det säkerhets objekt som programmet använder för att auktorisera åtkomst har tilldelats rollen som [lagrings-BLOB-dataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Börja med att läsa en fil. I det här exemplet används Node. js `fs`-modulen. Skapa sedan en fil referens i mål katalogen genom att skapa en **FileClient** -instans och sedan anropa metoden **FileClient. Create** . Ladda upp en fil genom att anropa metoden **FileClient. append** . Se till att slutföra överföringen genom att anropa metoden **FileClient. flush** .

I det här exemplet överförs en textfil till en katalog med namnet `my-directory`.

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Hantera en fil-ACL

Det här exemplet hämtar och anger sedan ACL för en fil med namnet `upload-file.txt`. Det här exemplet ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs behörighet.

> [!NOTE]
> Om ditt program tillåter åtkomst genom att använda Azure Active Directory (Azure AD) måste du kontrol lera att det säkerhets objekt som programmet använder för att auktorisera åtkomst har tilldelats rollen som [lagrings-BLOB-dataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>Ladda ned från en katalog

Börja med att skapa en **FileSystemClient** -instans som representerar den fil som du vill ladda ned. Använd metoden **FileSystemClient. Read** för att läsa filen. Skriv sedan filen. I det här exemplet används Node. js-`fs` modulen för att göra det. 

> [!NOTE]
> Den här metoden för att hämta en fil fungerar bara för Node. js-program. Om du planerar att köra din kod i en webbläsare, se filen [Azure Storage fil data Lake klient bibliotek för Java Script](https://www.npmjs.com/package/@azure/storage-file-datalake) för ett exempel på hur du gör detta i en webbläsare. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Det här exemplet skriver ut namnen på varje katalog och fil som finns i en katalog med namnet `my-directory`.

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Se även

* [Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)