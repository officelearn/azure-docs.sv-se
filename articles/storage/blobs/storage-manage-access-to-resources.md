---
title: Hantera offentlig läsåtkomst för behållare och blobbar
titleSuffix: Azure Storage
description: Lär dig hur du gör behållare och blobbar tillgängliga för anonym åtkomst och hur du kommer åt dem programmässigt.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255488"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Hantera anonym läsåtkomst till containrar och blob-objekt

Du kan aktivera anonym, offentlig läsåtkomst till en container och dess blobar i Azure Blob Storage. Genom att göra det kan du bevilja skrivskyddad åtkomst till dessa resurser utan att dela din kontonyckel och utan att kräva en signatur för delad åtkomst (SAS).

Offentlig läsåtkomst är bäst för scenarier där du vill att vissa blobbar alltid ska vara tillgängliga för anonym läsåtkomst. För mer detaljerad kontroll kan du skapa en signatur för delad åtkomst. Med signaturer för delad åtkomst kan du ange begränsad åtkomst med hjälp av olika behörigheter under en viss tidsperiod. Mer information om hur du skapar signaturer för delad åtkomst finns i [Använda SIGNATURER för delad åtkomst (SAS) i Azure Storage](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Bevilja anonyma användare behörigheter till behållare och blobbar

Som standard kan en container och alla blobar i den endast nås av användare som har fått rätt behörighet. Om du vill ge anonyma användare läsåtkomst till en container och dess blobar kan du ange containerns nivå för offentlig åtkomst. När du beviljar offentlig åtkomst till en container kan anonyma användare läsa blobar i en offentligt tillgänglig container utan att auktorisera begäran.

Du kan konfigurera en behållare med följande behörigheter:

- **Ingen offentlig läsåtkomst:** Behållaren och dess blobbar kan endast nås av lagringskontots ägare. Detta är standard för alla nya behållare.
- **Endast offentlig läsåtkomst för blobbar:** Blobbar i behållaren kan läsas av anonym begäran, men behållardata är inte tillgängliga. Anonyma klienter kan inte räkna upp blobbar i behållaren.
- **Offentlig läsåtkomst för behållare och dess blobbar:** Alla behållar- och blobdata kan läsas av anonym begäran. Klienter kan räkna upp blobbar i behållaren via anonym begäran, men kan inte räkna upp behållare i lagringskontot.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Ange informationsnivå för behållaren offentlig åtkomst i Azure-portalen

Från [Azure-portalen](https://portal.azure.com)kan du uppdatera den offentliga åtkomstnivån för en eller flera behållare:

1. Navigera till din översikt över lagringskonto i Azure-portalen.
1. Under **Blob-tjänsten** på menybladet väljer du **Blobbar**.
1. Välj de behållare som du vill ange den offentliga åtkomstnivån för.
1. Använd knappen **Ändra åtkomstnivå** för att visa inställningarna för offentlig åtkomst.
1. Välj önskad nivå för offentlig åtkomst i listrutan **Offentlig åtkomstnivå** och klicka på KNAPPEN OK för att tillämpa ändringen på de valda behållarna.

Följande skärmbild visar hur du ändrar nivån för allmänhetens åtkomst för de valda behållarna.

![Skärmbild som visar hur du anger nivån för offentlig åtkomst i portalen](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Du kan inte ändra den offentliga åtkomstnivån för en enskild blob. Offentlig åtkomstnivå anges endast på behållarnivå.

### <a name="set-container-public-access-level-with-net"></a>Ange nivå för offentlig åtkomst till behållare med .NET

Om du vill ange behörigheter för en behållare med hjälp av Azure Storage-klientbiblioteket för .NET hämtar du först behållarens befintliga behörigheter genom att anropa någon av följande metoder:

- [FåPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Ange sedan egenskapen **PublicAccess** för objektet [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) som returneras av metoden **GetPermissions.**

Anropa slutligen någon av följande metoder för att uppdatera behållarens behörigheter:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

I följande exempel anges behållarens behörigheter för fullständig offentlig läsåtkomst. Om du vill ange behörigheter för offentlig läsåtkomst endast för blobbar anger du egenskapen **PublicAccess** till **BlobContainerPublicAccessType.Blob**. Om du vill ta bort alla behörigheter för anonyma användare anger du egenskapen till **BlobContainerPublicAccessType.Off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Få tillgång till behållare och blobbar anonymt

En klient som kommer åt behållare och blobbar anonymt kan använda konstruktorer som inte kräver autentiseringsuppgifter. Följande exempel visar några olika sätt att referera till behållare och blobbar anonymt.

### <a name="create-an-anonymous-client-object"></a>Skapa ett anonymt klientobjekt

Du kan skapa ett nytt tjänstklientobjekt för anonym åtkomst genom att ange slutpunkten för Blob-lagring för kontot. Du måste dock också känna till namnet på en behållare i det kontot som är tillgängligt för anonym åtkomst.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Referera till en behållare anonymt

Om du har URL:en till en behållare som är anonymt tillgänglig kan du använda den för att referera till behållaren direkt.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Referera till en blob anonymt

Om du har url:en till en blob som är tillgänglig för anonym åtkomst kan du referera till blobben direkt med den webbadressen:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>Nästa steg

- [Auktorisera åtkomst till Azure Storage](../common/storage-auth.md)
- [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SIGNATURER för delad åtkomst (SAS)](../common/storage-sas-overview.md)
- [Blob-tjänstens REST-API](/rest/api/storageservices/blob-service-rest-api)
