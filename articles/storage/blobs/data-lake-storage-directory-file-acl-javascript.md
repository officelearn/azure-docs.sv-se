---
title: Använda JavaScript för filer & ACL:er i Azure Data Lake Storage Gen2
description: Använd Azure Storage Data Lake-klientbiblioteket för JavaScript för att hantera kataloger och ACL (File and Directory Access Control List) i lagringskonton som har aktiverat hierarkiskt namnområde (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061546"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Använda JavaScript för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder JavaScript för att skapa och hantera kataloger, filer och behörigheter i lagringskonton som har aktiverat hierarkiskt namnområde (HNS). 

[Paketprover](https://www.npmjs.com/package/@azure/storage-file-datalake) | [Samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | (nodpakethanteraren)[ger feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagringskonto med hierarkiskt namnområde (HNS) aktiverat. Följ [dessa](data-lake-storage-quickstart-create-account.md) instruktioner för att skapa en.
> * Om du använder det här paketet i ett Node.js-program behöver du Node.js 8.0.0 eller senare.

## <a name="set-up-your-project"></a>Konfigurera projektet

Installera DataSjöklientbibliotek för JavaScript genom att öppna ett terminalfönster och sedan skriva följande kommando.

```javascript
npm install @azure/storage-file-datalake
```

Importera `storage-file-datalake` paketet genom att placera den här policyn högst upp i kodfilen. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Anslut till kontot 

Om du vill använda kodavsnitten i den här artikeln måste du skapa en **DataLakeServiceClient-instans** som representerar lagringskontot. 

### <a name="connect-by-using-an-account-key"></a>Ansluta med hjälp av en kontonyckel

Det här är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient-instans** med hjälp av en kontonyckel.

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
> Den här metoden för auktorisering fungerar endast för Node.js-program. Om du planerar att köra koden i en webbläsare kan du auktorisera med hjälp av Azure Active Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med Hjälp av Azure Active Directory (AD)

Du kan använda [Azure identity-klientbiblioteket för JS för](https://www.npmjs.com/package/@azure/identity) att autentisera ditt program med Azure AD.

I det här exemplet skapas en **DataLakeServiceClient-instans** med hjälp av ett klient-ID, en klienthemlighet och ett klient-ID.  Information om hur du hämtar dessa värden finns [i Hämta en token från Azure AD för att godkänna begäranden från ett klientprogram](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Fler exempel finns i [Azure identity-klientbiblioteket för JS-dokumentation.](https://www.npmjs.com/package/@azure/identity)

## <a name="create-a-file-system"></a>Skapa ett filsystem

Ett filsystem fungerar som en behållare för dina filer. Du kan skapa en genom att hämta en **FileSystemClient-instans** och sedan anropa metoden **FileSystemClient.Create.**

I det här exemplet `my-file-system`skapas ett filsystem med namnet . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalogreferens genom att hämta en **DirectoryClient-instans** och anropa sedan metoden **DirectoryClient.create.**

I det här `my-directory` exemplet läggs en katalog som heter till i ett filsystem. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog genom att anropa metoden **DirectoryClient.rename.** Skicka sökvägen till den önskade katalogen en parameter. 

I det här exemplet byter du `my-directory-renamed`namn på en underkatalog till namnet .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

I det här `my-directory-renamed` exemplet flyttas en katalog med `my-directory-2`namnet till en underkatalog till en katalog med namnet . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog genom att anropa metoden **DirectoryClient.delete.**

I det här exemplet `my-directory`tas en katalog med namnet .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Hantera en ACL för katalog

Det här exemplet hämtar och anger sedan `my-directory`åtkomstkontrollistan för en katalog med namnet . Det här exemplet ger den givande användaren läs-, skriv- och körningsbehörigheter, ger den ägande gruppen endast läs- och körningsbehörighet och ger alla andra läsbehörighet.

> [!NOTE]
> Om ditt program godkänner åtkomst med hjälp av Azure Active Directory (Azure AD) kontrollerar du att säkerhetsobjektet som programmet använder för att auktorisera åtkomst har tilldelats [rollen Lagringsblobbdataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

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

Läs först en fil. I det här exemplet används `fs` modulen Node.js. Skapa sedan en filreferens i målkatalogen genom att skapa en **FileClient-instans** och anropa sedan metoden **FileClient.create.** Ladda upp en fil genom att anropa metoden **FileClient.addd.** Se till att slutföra överföringen genom att anropa **fileclient.flush-metoden.**

I det här exemplet överförs en `my-directory`textfil till en katalog med namnet .'

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

## <a name="manage-a-file-acl"></a>Hantera en ACL-fil

Det här exemplet hämtar och anger sedan `upload-file.txt`åtkomstkontrollistan för en fil med namnet . Det här exemplet ger den givande användaren läs-, skriv- och körningsbehörigheter, ger den ägande gruppen endast läs- och körningsbehörighet och ger alla andra läsbehörighet.

> [!NOTE]
> Om ditt program godkänner åtkomst med hjälp av Azure Active Directory (Azure AD) kontrollerar du att säkerhetsobjektet som programmet använder för att auktorisera åtkomst har tilldelats [rollen Lagringsblobbdataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

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

## <a name="download-from-a-directory"></a>Hämta från en katalog

Skapa först en **FileSystemClient-instans** som representerar den fil som du vill hämta. Använd metoden **FileSystemClient.read** för att läsa filen. Skriv sedan filen. I det här exemplet används `fs` modulen Node.js för att göra det. 

> [!NOTE]
> Den här metoden för att hämta en fil fungerar bara för Node.js-program. Om du planerar att köra koden i en webbläsare läser du [klientbiblioteket Azure Storage File Data Lake för](https://www.npmjs.com/package/@azure/storage-file-datalake) JavaScript-readme-fil i ett exempel på hur du gör detta i en webbläsare. 

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

I det här exemplet skrivs namnen på varje katalog `my-directory`och fil som finns i en katalog med namnet .

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

* [Paket (Nod package manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Prover](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)