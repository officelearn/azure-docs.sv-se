---
title: Aktivera offentlig läsbehörighet för behållare och blobbar i Azure Blob storage | Microsoft Docs
description: Lär dig hur du gör behållare och blobbar som är tillgängliga för anonym åtkomst och hur du kommer åt dem via programmering.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: bf7dabc1c3765d86e7a0f87acaa6f06a68d3d530
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782016"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Hantera anonym läsåtkomst till containrar och blob-objekt
Du kan aktivera anonym, offentlig läsbehörighet till en behållare och dess blobbar i Azure Blob storage. Då kan ge du skrivskyddad åtkomst till dessa resurser utan att dela din kontonyckel och utan en signatur för delad åtkomst (SAS).

Offentlig läsbehörighet passar bäst för scenarier där du vill att vissa BLOB-och alltid vara tillgänglig för anonym läsbehörighet. För mer detaljerad kontroll kan skapa du en signatur för delad åtkomst. Signaturer för delad åtkomst kan du begränsad åtkomst med olika behörigheter för en viss tidsperiod. Mer information om hur du skapar delade åtkomstsignaturer, se [använda signaturer för delad åtkomst (SAS) i Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Bevilja behörigheter för anonyma användare till behållare och blobbar
Som standard kan en behållare och alla blobbar i den endast användas av ägaren av storage-konto. Om du vill ge anonyma användare som har läsbehörighet till en behållare och dess blobbar, kan du ange behörigheter till behållaren att tillåta offentlig åtkomst. Anonyma användare kan läsa blobbar i en offentligt tillgänglig behållare utan att autentisera begäran.

Du kan konfigurera en behållare med följande behörigheter:

* **Ingen offentlig läsbehörighet:** behållaren och dess blobbar kan endast användas av lagringskontoägaren. Det här är standard för alla nya behållare.
* **Offentlig läsbehörighet endast för blobbar:** Blobbar i behållaren kan läsas av anonym begäran, men behållardata är inte tillgänglig. Anonym klienter kan inte räkna upp blobbar i behållaren.
* **Fullständig offentlig läsbehörighet:** alla behållare och blob-data kan läsas av anonym begäran. Klienter kan räkna upp blobbar i behållaren av anonym begäran, men det går inte att räkna upp behållare i lagringskontot.

Du kan använda följande för att ange behörigheter för behållare:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI 2.0](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programmässigt med någon av storage-klientbibliotek eller REST API

### <a name="set-container-permissions-in-the-azure-portal"></a>Ange behörigheter för behållare i Azure portal
Ange behörigheter för behållare i den [Azure-portalen](https://portal.azure.com), Följ dessa steg:

1. Öppna din **lagringskonto** -bladet i portalen. Du hittar ditt storage-konto genom att välja **lagringskonton** på bladet portal huvudmenyn.
1. Under **BLOBTJÄNSTEN** på menyn-bladet och välj **Blobar**.
1. Högerklicka på behållaren raden eller ellipserna att öppna behållarens **snabbmenyn**.
1. Välj **princip** på snabbmenyn.
1. Välj en **åtkomsttyp** från nedrullningsbara menyn.

    ![Metadata för behållaren dialogrutan Redigera](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Ange behörigheter för behållaren med .NET
Om du vill ange behörigheter för en behållare med C# och Storage-klientbiblioteket för .NET, först hämta behållarens befintliga behörigheter genom att anropa den **GetPermissions** metod. Ange sedan den **PublicAccess** -egenskapen för den **BlobContainerPermissions** objekt som returneras av den **GetPermissions** metod. Anropa slutligen den **behörighetsgruppbehörighet** metod med uppdaterade behörigheter.

I följande exempel anger behållarens behörigheter till fullständig offentlig läsbehörighet. Att ställa in behörigheter till offentlig läsbehörighet för blobar endast den **PublicAccess** egenskap **BlobContainerPublicAccessType.Blob**. Ta bort alla behörigheter för anonyma användare genom att ange egenskapen till **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Få åtkomst till behållare och blobbar anonymt
En klient som har åtkomst till behållare och blobbar anonymt kan använda konstruktorer som inte kräver autentiseringsuppgifter. I följande exempel visas ett antal olika sätt att referera till anonymt Blob service-resurser.

### <a name="create-an-anonymous-client-object"></a>Skapa ett anonym-klientobjekt
Du kan skapa en ny tjänsten klientobjekt för anonym åtkomst genom att tillhandahålla Blob service-slutpunkt för kontot. Du måste också veta namnet på en behållare i det konto som är tillgängliga för anonym åtkomst.

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
Om du har URL: en till en behållare som finns på anonymt, kan du använda det att direkt referera till behållaren.

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
Om du har rätt Webbadress till en blob som är tillgängliga för anonym åtkomst kan du referera till blob direkt med URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Funktioner som är tillgängliga för anonyma användare
I följande tabell visas vilka åtgärder som kan anropas av anonyma användare när en behållare ACL har angetts att tillåta offentlig åtkomst.

| REST-åtgärden | Behörigheten med fullständig offentlig läsbehörighet | Behörigheten med offentlig läsbehörighet endast för blobbar |
| --- | --- | --- |
| Lista behållare |Endast ägare |Endast ägare |
| Skapa behållare |Endast ägare |Endast ägare |
| Hämta egenskaper för behållare |Alla |Endast ägare |
| Hämta Metadata för behållaren |Alla |Endast ägare |
| Ställ in Metadata för behållaren |Endast ägare |Endast ägare |
| Hämta ACL-behållare |Endast ägare |Endast ägare |
| Ange behållaren ACL |Endast ägare |Endast ägare |
| Ta bort behållare |Endast ägare |Endast ägare |
| Lista Blobar |Alla |Endast ägare |
| Placera Blob |Endast ägare |Endast ägare |
| Hämta Blob |Alla |Alla |
| Hämta Blobegenskaper |Alla |Alla |
| Ange Blob-egenskaper |Endast ägare |Endast ägare |
| Hämta blob-metadata |Alla |Alla |
| Ange Blob-Metadata |Endast ägare |Endast ägare |
| Placera Block |Endast ägare |Endast ägare |
| Hämta lista över blockerade (endast allokerade blockeras) |Alla |Alla |
| Hämta lista över blockerade (ogenomförda block eller alla block) |Endast ägare |Endast ägare |
| Placera Blockeringslista |Endast ägare |Endast ägare |
| Ta bort Blob |Endast ägare |Endast ägare |
| Kopiera Blob |Endast ägare |Endast ägare |
| Ta ögonblicksbild av Blob |Endast ägare |Endast ägare |
| Lånet Blob |Endast ägare |Endast ägare |
| Placera sidan |Endast ägare |Endast ägare |
| Get Page Ranges |Alla |Alla |
| Bifoga Blob |Endast ägare |Endast ägare |

## <a name="next-steps"></a>Nästa steg

* [Autentisering för Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Använda signaturer för delad åtkomst (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Delegera åtkomst med signatur för delad åtkomst](https://msdn.microsoft.com/library/azure/ee395415.aspx)
