---
title: "Hantera förfallodatum för Azure Storage-blobbar i Azure CDN | Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d4741921806e443d92c385a04b781cec296c2ae8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="manage-expiration-of-azure-storage-blobs-in-azure-cdn"></a>Hantera förfallodatum för Azure Storage-blobbar i Azure CDN
> [!div class="op_single_selector"]
> * [Azure Web Apps/molntjänster, ASP.NET och IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Storage blob-tjänst](cdn-manage-expiration-of-blob-content.md)
> 
> 

Den [blob-tjänst](../storage/common/storage-introduction.md#blob-storage) i [Azure Storage](../storage/common/storage-introduction.md) är en av flera Azure-baserade ursprung integrerat med Azure CDN.  Alla offentliga blob-innehåll cachelagras i Azure CDN tills dess time to live (TTL) går ut.  TTL-värdet bestäms av den [ *Cache-Control* huvud](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) i HTTP-svaret från Azure Storage.

> [!TIP]
> Du kan välja att ange inga TTL-värde för en blob.  I det här fallet gäller Azure CDN automatiskt en standard-TTL sju dagar.
> 
> Mer information om hur Azure CDN fungerar för att påskynda åtkomst till blobbar och andra filer finns i [översikt över Azure CDN](cdn-overview.md).
> 
> Mer information om Azure Storage blob-tjänsten finns [Blob-Tjänstkoncept](https://msdn.microsoft.com/library/dd179376.aspx). 
> 
> 

Den här kursen visar flera olika sätt som du kan ange TTL-värdet för en blobb i Azure Storage.  

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) är en av de snabbaste, mest kraftfulla sätten att administrera din Azure-tjänster.  Använd den `Get-AzureStorageBlob` för att hämta en referens till blob, ange den `.ICloudBlob.Properties.CacheControl` egenskapen. 

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

## <a name="azure-storage-client-library-for-net"></a>Azure Storage-klientbibliotek för .NET
Om du vill ange en blob TTL-värde med hjälp av .NET använder den [Azure Storage-klientbibliotek för .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) att ange den [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) egenskapen.

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
> Det finns många fler .NET-kodexempel i den [Azure Blob Storage-exempel för .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 
> 

## <a name="other-methods"></a>Andra metoder
* [Azure kommandoradsgränssnittet](../cli-install-nodejs.md)
  
    När du hämtar blob, ange den *cacheControl* egenskapen med det `-p` växla.  Det här exemplet anger TTL-värdet till en timme (3600 sekunder).
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
  
    Uttryckligen ställa in den *x-ms-blob-cache-control* -egenskapen i en [placera Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [placera Blockeringslista](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx), eller [ange egenskaper för Blob](https://msdn.microsoft.com/library/azure/ee691966.aspx) begäran.
* Verktyg för lagringshantering från tredje part
  
    Vissa tredjeparts-hanteringsverktyg för Azure Storage kan du ange den *CacheControl* egenskapen för BLOB. 

## <a name="testing-the-cache-control-header"></a>Testa den *Cache-Control* sidhuvud
Du kan enkelt kontrollera TTL-värde på dina blobbar.  Använda din webbläsare [utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test som din blob inklusive den *Cache-Control* Svarsrubrik.  Du kan också använda ett verktyg som **wget**, [Postman](https://www.getpostman.com/), eller [Fiddler](http://www.telerik.com/fiddler) att undersöka svarshuvuden.

## <a name="next-steps"></a>Nästa steg
* [Läs mer om den *Cache-Control* sidhuvud](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [Lär dig hur du hanterar du förfallodatum för Molntjänsten innehåll i Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)

