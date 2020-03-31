---
title: Hantera förfallodatum för Azure Blob-lagring
titleSuffix: Azure Content Delivery Network
description: Lär dig mer om alternativen för att styra tid att leva för blobbar i Azure CDN-cachelagring.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f28282a802e4b38fadc05c7090fa2a2af154de54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083160"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Hantera förfallodatum för Azure Blob-lagring i Azure CDN
> [!div class="op_single_selector"]
> * [Azure-webbinnehåll](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob-lagring](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Blob-lagringstjänsten](../storage/common/storage-introduction.md#blob-storage) i Azure Storage är ett av flera Azure-baserade ursprung som är integrerat med CDN (Azure Content Delivery Network). Allt offentligt tillgängligt blob-innehåll kan cachelagras i Azure CDN tills dess tid att leva (TTL) förflyter. TTL bestäms av `Cache-Control` huvudet i HTTP-svaret från ursprungsservern. I den `Cache-Control` här artikeln beskrivs flera sätt som du kan ange huvudet på en blob i Azure Storage.

Du kan också styra cacheinställningar från Azure-portalen genom att ange CDN-cachelagringsregler. Om du skapar en cachelagringsregel och anger cachelagringsbeteendet för **cachelagring** av **cachelagring**av cachelagringar ignoreras cacheinställningarna som beskrivs i den här artikeln. Information om allmänna cachelagringsbegrepp finns i [Så här fungerar cachelagring](cdn-how-caching-works.md).

> [!TIP]
> Du kan välja att inte ange någon TTL på en blob. I det här fallet tillämpar Azure CDN automatiskt en standard-TTL på sju dagar, såvida du inte har ställt in cachelagringsregler i Azure-portalen. Den här standard-TTL gäller endast för allmänna webbleveransoptimeringar. För stora filoptimeringar är standardTL en dag, och för optimering av media är standardTL ett år.
> 
> Mer information om hur Azure CDN fungerar för att öka åtkomsten till blobbar och andra filer finns [i Översikt över Azure Content Delivery Network](cdn-overview.md).
> 
> Mer information om Azure Blob-lagring finns i [Introduktion till Blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ange cachekontrollrubriker med hjälp av CDN-cachelagringsregler
Den metod som föredras för `Cache-Control` att ange en blobhuvud är att använda cachelagringsregler i Azure-portalen. Mer information om CDN-cachelagringsregler finns i [Kontrollera Azure CDN-cachelagring med cachelagringsregler](cdn-caching-rules.md).

> [!NOTE] 
> Cachelagringsregler är endast tillgängliga för **Azure CDN Standard från Verizon** och Azure **CDN Standard från Akamai-profiler.** För **Azure CDN Premium från Verizon-profiler** måste du använda [Azure CDN-regelmotorn](cdn-rules-engine.md) i **hantera-portalen** för liknande funktioner.

**Så här navigerar du till cdn-cachelagringsreglerna:**

1. I Azure-portalen väljer du en CDN-profil och väljer sedan slutpunkten för blobben.

2. I det vänstra fönstret under inställningar, väljer du **Cachelagringsregler**.

   ![Knappen CDN-cachelagringsregler](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Sidan **Cachelagringsregler** visas.

   ![Cdn-cachelagringssida](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Så här anger du en Blob-lagringstjänsts cachekontrollhuvuden med hjälp av globala cachelagringsregler:**

1. Under **Globala cachelagringsregler**anger du **frågesträngcachelagringsbeteende** till **Ignorera frågesträngar** och ange **cachelagringsbeteende** till **Åsidosättning**.
      
2. Ange 3600 i rutan **Sekunder** eller 1 i rutan **Timmar** för att gälla för **slutdatum**för cache. 

   ![Exempel på globala cachelagringsregler för CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Den här globala cachelagringsregeln anger en cachevaraktighet på en timme och påverkar alla begäranden till slutpunkten. Den åsidosätter `Cache-Control` `Expires` alla eller HTTP-huvuden som skickas av ursprungsservern som anges av slutpunkten.   

3. Välj **Spara**.
 
**Så här anger du cachekontrollrubriker för en blob-fil med hjälp av anpassade cachelagringsregler:**

1. Skapa två matchningsvillkor under **Anpassade cachelagringsregler:**

     A. För det första matchningsvillkoret anger `/blobcontainer1/*` du Matcha **villkor** till **Sökväg** och anger för Matcha **värde**. Ange **cachelagringsbeteendet** för **åsidosättning** och ange 4 i rutan **Timmar.**

    B. För det andra matchningsvillkoret anger `/blobcontainer1/blob1.txt` du **Matchningsvillkor** till **Sökväg** och anger för Matcha **värde**. Ange **cachelagringsbeteendet** för **åsidosättning** och ange 2 i rutan **Timmar.**

    ![Exempel på anpassade cachelagringsregler för CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Den första anpassade cachelagringsregeln anger en cachevaraktighet `/blobcontainer1` på fyra timmar för alla blob-filer i mappen på ursprungsservern som anges av slutpunkten. Den andra regeln åsidosätter den `blob1.txt` första regeln för blob-filen och anger en cachevaraktighet på två timmar för den.

2. Välj **Spara**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Ange cachekontrollhuvuden med hjälp av Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/overview) är ett av de snabbaste och mest kraftfulla sätten att administrera dina Azure-tjänster. Använd `Get-AzStorageBlob` cmdleten för att hämta en referens `.ICloudBlob.Properties.CacheControl` till blobben och ange sedan egenskapen. 

Ett exempel:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Du kan också använda PowerShell för att [hantera CDN-profiler och slutpunkter](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Ange cachekontrollrubriker med hjälp av .NET
Om du vill ange `Cache-Control` en blob-rubrik med hjälp av .NET-kod använder du [Azure Storage Client Library for .NET för](../storage/blobs/storage-dotnet-how-to-use-blobs.md) att ange egenskapen [CloudBlob.Properties.CacheControl.](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol)

Ett exempel:

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
> Det finns fler .NET-kodexempel i [Azure Blob Storage Samples för .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Ange cachekontrollrubriker med andra metoder

### <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren
Med [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)kan du visa och redigera dina blob-lagringsresurser, inklusive egenskaper som egenskapen *CacheControl.* 

Så här uppdaterar du *cachecontrol-egenskapen* för en blob med Azure Storage Explorer:
   1. Välj en blob och välj sedan **Egenskaper** på snabbmenyn. 
   2. Bläddra ned till egenskapen *CacheControl.*
   3. Ange ett värde och välj sedan **Spara**.


![Egenskaper för Azure Storage Explorer](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure-kommandoradsgränssnittet
Med [AZURE Command-Line Interface](https://docs.microsoft.com/cli/azure) (CLI) kan du hantera Azure-blob-resurser från kommandoraden. Om du vill ange cachekontrollhuvudet när du laddar upp en blob `-p` med Azure CLI anger du egenskapen *cacheControl* med hjälp av växeln. I följande exempel visas hur du ställer in TTL till en timme (3600 sekunder):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>REST-API för Azure-lagringstjänster
Du kan använda [REST-APIN](/rest/api/storageservices/) för Azure Storage Services för att uttryckligen ange egenskapen *x-ms-blob-cache-control* genom att använda följande åtgärder på en begäran:
  
   - [Placera blob](/rest/api/storageservices/Put-Blob)
   - [Placera blocklista](/rest/api/storageservices/Put-Block-List)
   - [Ange blob-egenskaper](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testa cachekontrollhuvudet
Du kan enkelt verifiera TTL-inställningarna för dina blobbar. Med webbläsarens [utvecklarverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)testar du att bloben innehåller `Cache-Control` svarshuvudet. Du kan också använda ett verktyg som [Wget,](https://www.gnu.org/software/wget/) [Postman](https://www.getpostman.com/)eller [Fiddler](https://www.telerik.com/fiddler) för att undersöka svarsrubrikerna.

## <a name="next-steps"></a>Efterföljande moment
* [Lär dig hur du hanterar förfallodatum för Cloud Service-innehåll i Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Lär dig mer om cachelagringskoncept](cdn-how-caching-works.md)

