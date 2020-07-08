---
title: inkludera fil
description: inkludera fil
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "69642141"
---
BLOB-utlösaren innehåller flera egenskaper för metadata. Dessa egenskaper kan användas som en del av bindnings uttryck i andra bindningar eller som parametrar i koden. Dessa värden har samma semantik som [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) -typen.

|Egenskap  |Typ  |Beskrivning  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Sökvägen till den Utlös ande blobben.|
|`Uri`|`System.Uri`|Blobens URI för den primära platsen.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blobens system egenskaper. |
|`Metadata` |`IDictionary<string,string>`|Användardefinierade metadata för blobben.|

Till exempel loggar följande C#-skript och JavaScript-exempel sökvägen till den Utlös ande blobben, inklusive behållaren:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
