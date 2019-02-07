---
title: Hanterar du förfallodatum för Azure Blob storage i Azure Content Delivery Network | Microsoft Docs
description: Läs mer om alternativ för att styra time-to-live för blobbar i Azure CDN-cachelagring.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 29e9bee5f7712252d95b9416ad5523b4dfdd4b94
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814324"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Hanterar du förfallodatum för Azure Blob-lagring i Azure CDN
> [!div class="op_single_selector"]
> * [Azure-webbinnehåll](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

Den [Blob storage-tjänsten](../storage/common/storage-introduction.md#blob-storage) i Azure Storage är en av flera Azure-baserade ursprung integrerat med Azure Content Delivery Network (CDN). Alla offentliga blob-innehåll kan cachelagras i Azure CDN tills dess time to live (TTL) nätverksförbindelse. TTL-Perioden bestäms av den `Cache-Control` rubriken i HTTP-svaret från den ursprungliga servern. Den här artikeln beskrivs olika sätt som du kan ange den `Cache-Control` rubrik för en blob i Azure Storage.

Du kan också styra inställningar för cachelagring i Azure Portal med inställningen CDN-cachelagringsregler. Om du skapar en regel för cachelagring och anger dess funktionssättet för cachelagring **åsidosätta** eller **kringgå cache**, ignoreras de angivna ursprunget cachelagringsinställningarna som beskrivs i den här artikeln. Information om allmänna begrepp för cachelagring finns i [så här fungerar cachelagring](cdn-how-caching-works.md).

> [!TIP]
> Du kan välja att ange inga TTL för en blob. I det här fallet gäller Azure CDN automatiskt en standard-TTL på sju dagar, såvida inte du har angett cachelagringsregler i Azure-portalen. Det här standardvärdet TTL gäller enbart för allmän leverans optimeringar. Standard-TTL är en dag för optimering av stora filer, och för direktuppspelning optimeringar, standard-TTL är ett år.
> 
> Mer information om hur Azure CDN fungerar för att påskynda åtkomst till blobar och andra filer finns i [översikt över Azure Content Delivery Network](cdn-overview.md).
> 
> Läs mer om Azure Blob storage, [introduktion till Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ange Cache-Control-huvuden genom att använda CDN-cachelagringsregler
Den bästa metoden för att ställa in en blob `Cache-Control` rubrik är att använda cachelagringsregler i Azure-portalen. Läs mer om CDN-cachelagringsregler [Kontrollera Cachelagringsbeteendet med cachelagringsregler](cdn-caching-rules.md).

> [!NOTE] 
> Cachelagringsregler är endast tillgängliga för **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profiler. För **Azure CDN Premium från Verizon** profiler, måste du använda den [Azure CDN regelmotor](cdn-rules-engine.md) i den **hantera** portal för liknande funktionalitet.

**Navigera till sidan CDN cachelagring regler**:

1. Välj en CDN-profil i Azure-portalen och väljer du slutpunkten för bloben.

2. I det vänstra fönstret under inställningar, väljer du **Cachelagringsregler**.

   ![CDN-cachelagring regler knappen](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Sidan **Cachelagringsregler** visas.

   ![CDN-cachelagring sidan](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Ange en Blob lagringstjänst Cache-Control-huvuden med globala cachelagringsregler:**

1. Under **globala cachelagringsregler**anger **cachelagring av frågesträngar** till **Ignorera frågesträngar** och ange **Cachelagringsbeteende** till  **Åsidosätt**.
      
2. För **giltighetstid för Cache**, ange 3600 i den **sekunder** box eller 1 i den **timmar** box. 

   ![CDN globala cachelagring regler-exempel](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Den här globala cachelagringsregeln anger en cachevaraktighet på en timme och påverkar alla förfrågningar till slutpunkten. Åsidosätter eventuella `Cache-Control` eller `Expires` HTTP-huvuden som skickas av den ursprungliga servern som anges av slutpunkten.   

3. Välj **Spara**.
 
**Ange en blob filens Cache-Control-huvuden med hjälp av anpassade cachelagringsreglerna:**

1. Under **anpassade cachelagringsregler**, skapa två matchningsvillkor:

     A. Ange för den första matchningsvillkor **matchningsvillkor** till **sökväg** och ange `/blobcontainer1/*` för **matchar värdet**. Ange **Cachelagringsbeteende** till **åsidosätta** och ange 4 i den **timmar** box.

    B. För andra matchningsvillkor ställer du in **matchningsvillkor** till **sökväg** och ange `/blobcontainer1/blob1.txt` för **matchar värdet**. Ange **Cachelagringsbeteende** till **åsidosätta** och anger 2 i den **timmar** box.

    ![CDN anpassad cachelagring regler-exempel](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Den första anpassa cachelagringsregeln anger en cachelagringens varaktighet på fyra timmar för alla blob-filer i den `/blobcontainer1` mapp på den ursprungliga servern som anges av slutpunkten. Den andra regeln åsidosätter den första regeln för den `blob1.txt` blob-fil och anger en cachelagringens varaktighet på två timmar för den.

2. Välj **Spara**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Anger Cache-Control-huvuden med hjälp av Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) är ett av de snabbaste och mest kraftfulla sätt att administrera dina Azure-tjänster. Använd den `Get-AzureStorageBlob` cmdlet för att hämta en referens till bloben, ange den `.ICloudBlob.Properties.CacheControl` egenskapen. 

Exempel:

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Du kan också använda PowerShell för att [hantera dina CDN-profiler och slutpunkter](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Inställningen Cache-Control-huvuden med hjälp av .NET
Ange en blob `Cache-Control` rubrik med hjälp av .NET-kod, Använd den [Azure Storage-klientbiblioteket för .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) att ställa in den [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) egenskapen.

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
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Det finns fler .NET-kodexempel i [Azure Blob Storage-exempel för .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Inställningen Cache-Control-huvuden genom att använda andra metoder

### <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren
Med [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), du kan visa och redigera din blob storage-resurser, inklusive egenskaper som den *CacheControl* egenskapen. 

Att uppdatera den *CacheControl* egenskapen för en blob med Azure Storage Explorer:
   1. Välj en blob och välj sedan **egenskaper** på snabbmenyn. 
   2. Rulla ned till den *CacheControl* egenskapen.
   3. Ange ett värde och välj sedan **spara**.


![Azure Storage Explorer-egenskaper](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure-kommandoradsgränssnittet
Med den [Azure-kommandoradsgränssnittet](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) (CLI), kan du hantera Azure blob-resurser från kommandoraden. Att ställa in cache-control-huvudet när du laddar upp en blob med Azure CLI i *cacheControl* egenskapen genom att använda den `-p` växla. I följande exempel visas hur du ställer in TTL-värdet till en timme (3 600 sekunder):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Azure storage services REST API
Du kan använda den [Azure storage services REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) att explicit ange det *x-ms-blob-cache-control* egenskapen med hjälp av följande åtgärder på en begäran:
  
   - [Placera Blob](https://msdn.microsoft.com/library/azure/dd179451.aspx)
   - [Placera Blockeringslista](https://msdn.microsoft.com/library/azure/dd179467.aspx)
   - [Ange Blob-egenskaper](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>Testa Cache-Control-huvudet
Du kan enkelt kontrollera TTL-inställningarna för dina blobar. Med din webbläsare [utvecklarverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test som innehåller din blob i `Cache-Control` svarshuvudet. Du kan också använda ett verktyg som [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/), eller [Fiddler](http://www.telerik.com/fiddler) att undersöka svarshuvuden.

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du hanterar du förfallodatum för Cloud Service-innehåll i Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Lär dig mer om cachelagring begrepp](cdn-how-caching-works.md)

