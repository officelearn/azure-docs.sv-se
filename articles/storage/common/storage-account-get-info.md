---
title: Hämta lagrings konto typ och SKU-namn med .NET-Azure Storage
description: Lär dig hur du hämtar Azure Storage konto typ och SKU-namn med hjälp av .NET-klient biblioteket.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 21ecaae679ad4a5f21107ef53d3899cf03593c9d
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829208"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Hämta lagrings konto typ och SKU-namn med .NET

Den här artikeln visar hur du hämtar Azure Storage konto typ och SKU-namn för en blob med hjälp av [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage/client).

Konto information finns på tjänst versioner som börjar med version 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Om kontotyp och SKU-namn

**Kontotyp**: Giltiga konto typer är `BlobStorage` `BlockBlobStorage` `FileStorage`,,, och`StorageV2`. `Storage` [Översikt över Azure Storage-kontot](storage-account-overview.md) innehåller mer information, inklusive beskrivningar av de olika lagrings kontona.

**SKU-namn**: Giltiga `Premium_LRS`SKU -`Standard_GZRS` `Premium_ZRS` namnär`Standard_RAGRS`,,,,,, och`Standard_ZRS`. `Standard_GRS` `Standard_LRS` `Standard_RAGZRS` SKU-namn är Skift läges känsliga och är sträng fält i [klassen SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Hämta konto information

Om du vill hämta lagrings konto typen och det SKU-namn som är associerat med en BLOB anropar du metoden [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) eller [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) .

I följande kod exempel hämtas och visas de skrivskyddade konto egenskaperna.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra åtgärder som du kan utföra på ett lagrings konto via [Azure Portal](https://portal.azure.com) och Azure-REST API.

- [Hantera ett lagrings konto](storage-account-manage.md)
- [Uppgradera ett lagrings konto](storage-account-upgrade.md)
- [Hämta konto informations åtgärd (REST)](/rest/api/storageservices/get-account-information)
