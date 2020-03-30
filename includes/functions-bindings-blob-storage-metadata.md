---
title: ta med fil
description: ta med fil
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "69642141"
---
Blob-utlösaren innehåller flera metadataegenskaper. Dessa egenskaper kan användas som en del av bindningsuttryck i andra bindningar eller som parametrar i koden. Dessa värden har samma semantik som [CloudBlob-typen.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet)

|Egenskap  |Typ  |Beskrivning  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Sökvägen till den utlösande blobben.|
|`Uri`|`System.Uri`|BLOB:s URI för den primära platsen.|
|`Properties` |[BlobProperties (BlobProperties)](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blobens systemegenskaper. |
|`Metadata` |`IDictionary<string,string>`|De användardefinierade metadata för blobben.|

I följande C#-skript och JavaScript-exempel loggas sökvägen till den utlösande bloben, inklusive behållaren:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
