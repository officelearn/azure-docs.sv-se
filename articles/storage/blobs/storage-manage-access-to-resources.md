---
title: Aktivera offentlig läsbehörighet för behållare och blobbar i Azure Blob storage | Microsoft Docs
description: Lär dig hur du gör behållare och blobbar som är tillgängliga för anonym åtkomst och hur du kommer åt dem via programmering.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: e0f93b0a95a228b26fae266129aea4b595b05e0f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148362"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Hantera anonym läsåtkomst till containrar och blob-objekt

Du kan aktivera anonym, offentlig läsbehörighet till en behållare och dess blobbar i Azure Blob storage. Då kan ge du skrivskyddad åtkomst till dessa resurser utan att dela din kontonyckel och utan en signatur för delad åtkomst (SAS).

Offentlig läsbehörighet passar bäst för scenarier där du vill att vissa BLOB-och alltid vara tillgänglig för anonym läsbehörighet. För mer detaljerad kontroll kan skapa du en signatur för delad åtkomst. Signaturer för delad åtkomst kan du begränsad åtkomst med olika behörigheter för en viss tidsperiod. Mer information om hur du skapar delade åtkomstsignaturer, se [använda signaturer för delad åtkomst (SAS) i Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Bevilja behörigheter för anonyma användare till behållare och blobbar

Som standard kan en behållare och alla blobbar i den endast användas av en användare som har rätt behörighet. För att bevilja läsbehörighet för anonyma användare till en behållare och dess blobbar, kan du ange behållare offentlig åtkomstnivå. När du beviljar offentlig åtkomst till en behållare kan kan sedan anonyma användare läsa blobbar i en offentligt tillgänglig behållare utan begäran.

Du kan konfigurera en behållare med följande behörigheter:

* **Ingen offentlig läsbehörighet:** Behållaren och dess blobbar kan endast användas av lagringskontoägaren. Det här är standard för alla nya behållare.
* **Offentlig läsbehörighet endast för objekt:** Blobbar i behållaren kan läsas av anonym begäran, men behållardata är inte tillgänglig. Anonym klienter kan inte räkna upp blobbar i behållaren.
* **Offentlig läsbehörighet för behållaren och dess blobbar:** Alla behållare och blob-data kan läsas av anonym begäran. Klienter kan räkna upp blobbar i behållaren av anonym begäran, men det går inte att räkna upp behållare i lagringskontot.

Du kan använda följande för att ange behörigheter för behållare:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programmässigt med någon av storage-klientbibliotek eller REST API

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Ange offentlig åtkomstnivå för behållare i Azure portal

Från den [Azure-portalen](https://portal.azure.com), kan du uppdatera offentlig åtkomstnivå för en eller flera behållare:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Under **Blobtjänst** på menyn-bladet och välj **Blobar**.
1. Välj de behållare som du vill ange offentlig åtkomstnivå.
1. Använd den **ändra åtkomstnivå** knappen för att visa inställningar för offentlig åtkomst.
1. Välj den önskade offentlig åtkomstnivån från den **offentlig åtkomstnivå** listrutan och klicka på OK om du vill tillämpa ändringen för de valda behållarna.

Följande skärmbild visar hur du ändrar offentlig åtkomstnivå för de valda behållarna.

![Skärmbild som visar hur du ställer in offentlig åtkomstnivå i portalen](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Du kan inte ändra offentlig åtkomstnivå för en enskild blob. Offentlig åtkomstnivå anges endast på behållarenivån.

### <a name="set-container-public-access-level-with-net"></a>Ange behållaren offentlig åtkomstnivå med .NET

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

En klient som har åtkomst till behållare och blobbar anonymt kan använda konstruktorer som inte kräver autentiseringsuppgifter. I följande exempel visas ett antal olika sätt att referera till behållare och blobbar anonymt.

### <a name="create-an-anonymous-client-object"></a>Skapa ett anonym-klientobjekt

Du kan skapa en ny tjänsten klientobjekt för anonym åtkomst genom att tillhandahålla Blob storage-slutpunkten för kontot. Du måste också veta namnet på en behållare i det konto som är tillgängliga för anonym åtkomst.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
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

I följande tabell visas vilka åtgärder som kan anropas anonymt när en behållare är konfigurerad för offentlig åtkomst.

| REST-åtgärden | Offentlig läsbehörighet till behållare | Endast offentlig läsbehörighet för blobar |
| --- | --- | --- |
| Lista behållare | Auktoriserade begäranden | Auktoriserade begäranden |
| Skapa behållare | Auktoriserade begäranden | Auktoriserade begäranden |
| Hämta egenskaper för behållare | Anonyma begäranden som tillåts | Auktoriserade begäranden |
| Hämta Metadata för behållaren | Anonyma begäranden som tillåts | Auktoriserade begäranden |
| Ställ in Metadata för behållaren | Auktoriserade begäranden | Auktoriserade begäranden |
| Hämta ACL-behållare | Auktoriserade begäranden | Auktoriserade begäranden |
| Ange behållaren ACL | Auktoriserade begäranden | Auktoriserade begäranden |
| Ta bort behållare | Auktoriserade begäranden | Auktoriserade begäranden |
| Lista Blobar | Anonyma begäranden som tillåts | Auktoriserade begäranden |
| Put Blob | Auktoriserade begäranden | Auktoriserade begäranden |
| Hämta Blob | Anonyma begäranden som tillåts | Anonyma begäranden som tillåts |
| Hämta Blobegenskaper | Anonyma begäranden som tillåts | Anonyma begäranden som tillåts |
| Ange Blob-egenskaper | Auktoriserade begäranden | Auktoriserade begäranden |
| Hämta blob-metadata | Anonyma begäranden som tillåts | Anonyma begäranden som tillåts |
| Ange Blob-Metadata | Auktoriserade begäranden | Auktoriserade begäranden |
| Placera Block | Auktoriserade begäranden | Auktoriserade begäranden |
| Hämta lista över blockerade (endast allokerade blockeras) | Anonyma begäranden som tillåts | Anonyma begäranden som tillåts |
| Hämta lista över blockerade (ogenomförda block eller alla block) | Auktoriserade begäranden | Auktoriserade begäranden |
| Placera Blockeringslista | Auktoriserade begäranden | Auktoriserade begäranden |
| Ta bort Blob | Auktoriserade begäranden | Auktoriserade begäranden |
| Kopiera Blob | Auktoriserade begäranden | Auktoriserade begäranden |
| Ta ögonblicksbild av Blob | Auktoriserade begäranden | Auktoriserade begäranden |
| Lånet Blob | Auktoriserade begäranden | Auktoriserade begäranden |
| Placera sidan | Auktoriserade begäranden | Auktoriserade begäranden |
| Get Page Ranges | Anonyma begäranden som tillåts | Anonyma begäranden som tillåts |
| Bifoga blob | Auktoriserade begäranden | Auktoriserade begäranden |

## <a name="next-steps"></a>Nästa steg

* [Auktorisering för Azure Storage-tjänster](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [Använda signaturer för delad åtkomst (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)