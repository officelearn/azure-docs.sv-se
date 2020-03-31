---
title: Hämta lagringskontotyp och SKU-namn med .NET
titleSuffix: Azure Storage
description: Lär dig hur du hämtar Azure Storage-kontotyp och SKU-namn med hjälp av .NET-klientbiblioteket.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1495ed55c24a0f94b911d60d1db0f32940ea134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137066"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Hämta lagringskontotyp och SKU-namn med .NET

Den här artikeln visar hur du hämtar azure storage-kontotypen och SKU-namnet för en blob med hjälp av [Azure Storage-klientbiblioteket för .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

Kontoinformation finns på tjänstversioner som börjar med version 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Om kontotyp och SKU-namn

**Kontotyp**: Giltiga `BlobStorage`kontotyper `FileStorage` `Storage`inkluderar `StorageV2`, `BlockBlobStorage`, , och . [Översikt över Azure-lagringskonto](storage-account-overview.md) innehåller mer information, inklusive beskrivningar av de olika lagringskontona.

**SKU-namn**: Giltiga `Premium_LRS`SKU-namn `Standard_RAGRS`inkluderar `Standard_RAGZRS`, `Standard_ZRS` `Premium_ZRS`, `Standard_GRS`, `Standard_GZRS`, `Standard_LRS`, , och . SKU-namn är skiftlägeskänsliga och är strängfält i [SkuName-klassen](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Hämta kontoinformation

Om du vill hämta lagringskontotypen och SKU-namnet som är associerat med en blob anropar du metoden [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) eller [GetAccountPropertiesAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet)

Följande kodexempel hämtar och visar de skrivskyddade kontoegenskaperna.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra åtgärder som du kan utföra på ett lagringskonto via [Azure-portalen](https://portal.azure.com) och Azure REST API.

- [Hämta åtgärd för kontoinformation (REST)](/rest/api/storageservices/get-account-information)
