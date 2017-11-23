---
title: "Hantera förfallodatum för Azure Blob-lagring i Azure Content Delivery Network | Microsoft Docs"
description: "Läs mer om alternativen för att styra time to live för BLOB i Azure CDN cachelagring."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: 8c15d198e92b1478b84b2140df416df3909ba141
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Hantera förfallodatum för Azure Blob-lagring i Azure Content Delivery Network
> [!div class="op_single_selector"]
> * [Azure webbinnehåll](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

Den [Blob storage-tjänst](../storage/common/storage-introduction.md#blob-storage) i Azure Storage är en av flera Azure-baserade ursprung integrerat med Azure Content Delivery Network (CDN). Alla offentliga blob-innehåll cachelagras i Azure CDN tills dess time to live (TTL) går ut. TTL-värdet bestäms av den `Cache-Control` rubriken i HTTP-svaret från den ursprungliga servern. Den här artikeln beskriver flera olika sätt som du kan ange den `Cache-Control` sidhuvud på en blobb i Azure Storage.

> [!TIP]
> Du kan välja att ange inga TTL-värde för en blob. I det här fallet gäller Azure CDN automatiskt en standard-TTL sju dagar. Den här standardinställningen TTL-värde gäller enbart för Internet leverans optimeringar. Standard-TTL är en dag för stora filer optimeringar och för direktuppspelning av optimeringar, standard TTL-värde är ett år.
> 
> Mer information om hur Azure CDN fungerar för att påskynda åtkomst till blobbar och andra filer finns [översikt över Azure innehållsleveransnätverk](cdn-overview.md).
> 
> Mer information om Azure Blob storage finns [introduktion till Blob storage](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Inställningen Cache-Control-huvuden med hjälp av Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) är en av de snabbaste och mest kraftfulla sätten att administrera din Azure-tjänster. Använd den `Get-AzureStorageBlob` för att hämta en referens till blob, ange den `.ICloudBlob.Properties.CacheControl` egenskapen. 

Exempel:

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Du kan också använda PowerShell för att [hantera dina CDN-profiler och slutpunkter](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Inställningen Cache-Control-huvuden med hjälp av .NET
Ange en blob `Cache-Control` sidhuvud med hjälp av .NET-kod, Använd den [Azure Storage-klientbibliotek för .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) att ange den [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) egenskapen.

Exempel:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Det finns flera .NET-kodexempel i [Azure Blob Storage-exempel för .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Inställningen Cache-Control huvuden genom att använda andra metoder

### <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren
Med [Azure Lagringsutforskaren](https://azure.microsoft.com/en-us/features/storage-explorer/), kan du visa och redigera din blob storage-resurser, inklusive egenskaper som den *CacheControl* egenskapen. 

### <a name="azure-command-line-interface"></a>Azure-kommandoradsgränssnittet
När du överför en blob kan du ange den *cacheControl* egenskap med det `-p` växla i den [Azure-kommandoradsgränssnittet](../cli-install-nodejs.md). I följande exempel visas hur du ställer in TTL-värdet till 1 timme (3600 sekunder):
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```

### <a name="azure-storage-services-rest-api"></a>Azure storage services REST API
Du kan använda den [Azure storage services REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) att explicit ange det *x-ms-blob-cache-control* egenskapen med hjälp av följande åtgärder på en begäran:
  
   - [Placera Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)
   - [Placera Blockeringslista](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)
   - [Ange Blob-egenskaper](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>Testa Cache-Control-huvudet
Du kan enkelt kontrollera TTL-inställningarna för dina blobbar. Med din webbläsare [utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test som din blob innehåller den `Cache-Control` Svarsrubrik. Du kan också använda ett verktyg som [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/), eller [Fiddler](http://www.telerik.com/fiddler) att undersöka svarshuvuden.

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du hanterar du förfallodatum för Molntjänsten innehåll i Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)

