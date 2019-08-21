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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642141"
---
BLOB-utlösaren innehåller flera egenskaper för metadata. De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. Dessa värden har samma semantik som [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) -typen.

|Egenskap  |Type  |Beskrivning  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Sökvägen till den Utlös ande blobben.|
|`Uri`|`System.Uri`|Blobens URI för den primära platsen.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blobens system egenskaper. |
|`Metadata` |`IDictionary<string,string>`|Användardefinierade metadata för blobben.|

Till exempel loggar följande C# skript och JavaScript-exempel sökvägen till den Utlös ande blobben, inklusive behållaren:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
