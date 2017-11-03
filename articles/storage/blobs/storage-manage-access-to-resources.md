---
title: "Aktivera offentlig läsbehörighet för behållare och blobbar i Azure Blob storage | Microsoft Docs"
description: "Lär dig hur du gör behållare och blobbar som är tillgängliga för anonym åtkomst och hur du kommer åt dem via programmering."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a2cffee6-3224-4f2a-8183-66ca23b2d2d7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: f52079c72be298daaa45074e516f911022780392
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Hantera anonym läsåtkomst till behållare och blob-objekt
Du kan aktivera anonym, offentlig läsbehörighet till en behållare och dess blobbar i Azure Blob storage. Då kan bevilja du skrivskyddad åtkomst till dessa resurser utan att dela din kontonyckel och utan att kräva en signatur för delad åtkomst (SAS).

Offentlig läsbehörighet är bäst för scenarier där du vill att vissa BLOB ska alltid vara tillgänglig för anonym läsbehörighet. Du kan skapa en signatur för delad åtkomst för mer detaljerad kontroll. Signaturer för delad åtkomst kan du ange begränsad åtkomst med olika behörigheter för en viss tidsperiod. Mer information om hur du skapar delad åtkomst till signaturer, se [använder signaturer för delad åtkomst (SAS) i Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Bevilja behörighet för anonyma användare till behållare och blobbar
Som standard kan en behållare och alla blobbar i den endast användas av ägaren till lagringskontot. Du kan ange behörigheter för behållaren att tillåta offentlig åtkomst om du vill ge anonyma användare som har läsbehörighet till en behållare och dess blobbar. Anonyma användare kan läsa blobbar i en behållare som är offentligt tillgänglig utan att autentisera begäran.

Du kan konfigurera en behållare med följande behörigheter:

* **Ingen offentlig läsbehörighet:** behållaren och dess blobbar kan endast användas av lagringskontoägaren. Detta är standard för alla nya behållare.
* **Offentlig läsbehörighet för blobbar endast:** Blobbar i behållaren kan läsas av anonym begäran, men behållardata är inte tillgänglig. Anonyma klienter kan inte räkna upp blobbar i behållaren.
* **Fullständig offentlig läsbehörighet:** alla behållare och blob-data kan läsas av anonym begäran. Klienter kan räkna upp blobbar i behållaren av anonym begäran, men det går inte att räkna upp behållare i lagringskontot.

Du kan använda följande för att ange behörigheter för behållaren:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI 2.0](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programmässigt med någon av storage-klientbibliotek eller REST API

### <a name="set-container-permissions-in-the-azure-portal"></a>Ange behörigheter för behållaren i Azure-portalen
Ange behörigheter för behållaren den [Azure-portalen](https://portal.azure.com), gör du följande:

1. Öppna din **lagringskonto** blad i portalen. Du kan hitta ditt lagringskonto genom att välja **lagringskonton** i bladet portal huvudmenyn.
1. Under **BLOB-tjänsten** på bladet menyn väljer **behållare**.
1. Högerklicka på behållaren raden eller välj på knappen Öppna behållarens **snabbmenyn**.
1. Välj **princip** på snabbmenyn.
1. Välj en **komma åt typen** från nedrullningsbara menyn.

    ![Metadata för behållaren dialogrutan Redigera](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Behörigheter för behållaren med .NET
Om du vill ange behörigheter för en behållare med C# och Storage-klientbiblioteket för .NET, först hämta behållarens befintliga behörigheter genom att anropa den **GetPermissions** metod. Ange den **PublicAccess** -egenskapen för den **BlobContainerPermissions** objekt som returneras av den **GetPermissions** metod. Slutligen anropa den **behörighetsgruppbehörighet** metod med de uppdaterade behörigheterna.

I följande exempel anger behållarens behörigheter till fullständig offentlig läsbehörighet. Att ställa in behörigheter till offentlig läsbehörighet för blobbar endast den **PublicAccess** egenskapen **BlobContainerPublicAccessType.Blob**. Ta bort alla behörigheter för anonyma användare genom att ange egenskapen till **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Åtkomst till behållare och blobbar anonymt
En klient som ansluter till behållare och blobbar anonymt kan använda konstruktorer som inte kräver autentiseringsuppgifter. Följande exempel visar några olika sätt att hänvisa till resurser för Blob-tjänsten anonymt.

### <a name="create-an-anonymous-client-object"></a>Skapa en anonym klient-objekt
Du kan skapa ett nytt objekt för tjänsten för anonym åtkomst genom att tillhandahålla Blob-tjänsteslutpunkt för kontot. Du måste dock också känna till namnet på en behållare i det konto som är tillgängliga för anonym åtkomst.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Referera till en behållare anonymt
Du kan använda den för att referera till behållaren direkt om du har rätt Webbadress till en behållare som är tillgänglig anonymt.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Referera till en blob anonymt
Om du har rätt Webbadress till en blobb som är tillgängliga för anonym åtkomst, kan du referera blob direkt med hjälp av denna Webbadress:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Funktioner som är tillgängliga för anonyma användare
I följande tabell visas vilka åtgärder som kan anropas av anonyma användare när en behållar-ACL är inställd på att tillåta offentlig åtkomst.

| REST-åtgärd | Behörighet med fullständig offentlig läsbehörighet | Offentlig läsbehörighet för endast BLOB-behörigheten |
| --- | --- | --- |
| Lista behållare |Endast ägare |Endast ägare |
| Skapa en behållare |Endast ägare |Endast ägare |
| Hämta egenskaper för behållaren |Alla |Endast ägare |
| Hämta Metadata för behållaren |Alla |Endast ägare |
| Ange Metadata för behållaren |Endast ägare |Endast ägare |
| Hämta behållar-ACL |Endast ägare |Endast ägare |
| Ange behållar-ACL |Endast ägare |Endast ägare |
| Ta bort behållaren |Endast ägare |Endast ägare |
| Lista över Blobbar |Alla |Endast ägare |
| Placera Blob |Endast ägare |Endast ägare |
| Hämta Blob |Alla |Alla |
| Hämta Blob-egenskaper |Alla |Alla |
| Ange Blob-egenskaper |Endast ägare |Endast ägare |
| Hämta Blobbmetadata |Alla |Alla |
| Ange Blobbmetadata |Endast ägare |Endast ägare |
| Placera Block |Endast ägare |Endast ägare |
| Hämta listan över blockerade (endast allokerad block) |Alla |Alla |
| Hämta listan över blockerade (ogenomförda block eller alla block) |Endast ägare |Endast ägare |
| Placera Blockeringslista |Endast ägare |Endast ägare |
| Ta bort blobben |Endast ägare |Endast ägare |
| Kopiera Blob |Endast ägare |Endast ägare |
| Snapshot-Blob |Endast ägare |Endast ägare |
| Lånet Blob |Endast ägare |Endast ägare |
| Placera sida |Endast ägare |Endast ägare |
| Get-sidintervall |Alla |Alla |
| Lägg till Blob |Endast ägare |Endast ägare |

## <a name="next-steps"></a>Nästa steg

* [Autentisering för Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Använda signaturer för delad åtkomst (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Delegera åtkomst med signatur för delad åtkomst](https://msdn.microsoft.com/library/azure/ee395415.aspx)
