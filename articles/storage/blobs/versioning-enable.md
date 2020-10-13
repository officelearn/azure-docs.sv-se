---
title: Aktivera och hantera BLOB-versioner
titleSuffix: Azure Storage
description: Lär dig hur du aktiverar BLOB-versioner i Azure Portal eller genom att använda en Azure Resource Manager mall.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 1df7afb5a029ff7770a64d6bf698a462c8ab9735
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230678"
---
# <a name="enable-and-manage-blob-versioning"></a>Aktivera och hantera BLOB-versioner

Du kan aktivera Blob Storage-versioner för att automatiskt underhålla tidigare versioner av ett objekt.  När BLOB-versioner har Aktiver ATS kan du återställa en tidigare version av en BLOB för att återställa dina data om de felaktigt ändras eller tas bort.

Den här artikeln visar hur du aktiverar eller inaktiverar BLOB-versioner för lagrings kontot med hjälp av Azure Portal eller en Azure Resource Manager mall. Mer information om BLOB-versioner finns i [BLOB-versioner](versioning-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Aktivera blobversionshantering

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Så här aktiverar du Blob-versioner i Azure Portal:

1. Navigera till ditt lagrings konto i portalen.
1. Under **BLOB service**väljer du **data skydd**.
1. I avsnittet **versions hantering** väljer du **aktive rad**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Skärm bild som visar hur du aktiverar BLOB-versioner i Azure Portal":::

# <a name="template"></a>[Mall](#tab/template)

Om du vill aktivera BLOB-versioner med en mall skapar du en mall med egenskapen **IsVersioningEnabled** till **True**. Följande steg beskriver hur du skapar en mall i Azure Portal.

1. I Azure Portal väljer du **skapa en resurs**.
1. I **Sök på Marketplace**skriver du **mall distribution**och trycker sedan på **RETUR**.
1. Välj **malldistribution**, Välj **skapa**och välj sedan **skapa en egen mall i redigeraren**.
1. I redigeraren för mallar klistrar du in följande JSON. Ersätt platshållaren `<accountName>` med namnet på ditt lagringskonto.
1. Spara mallen.
1. Ange resurs gruppen för kontot och välj sedan knappen **köp** för att distribuera mallen och aktivera BLOB-versioner.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Mer information om hur du distribuerar resurser med mallar i Azure Portal finns i [distribuera resurser med Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Ändra en BLOB för att utlösa en ny version

Följande kod exempel visar hur du utlöser skapandet av en ny version med Azure Storage-klient biblioteket för .NET, version [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) eller senare. Kontrol lera att du har aktiverat versions hantering för ditt lagrings konto innan du kör det här exemplet.

Exemplet skapar en Block-Blob och uppdaterar sedan blobens metadata. Uppdatering av blobens metadata utlöser skapandet av en ny version. Exemplet hämtar den ursprungliga versionen och den aktuella versionen och visar att endast den aktuella versionen innehåller metadata.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>Nästa steg

- [BLOB-versioner](versioning-overview.md)
- [Mjuk borttagning för Azure Storage-blobar](soft-delete-overview.md)
