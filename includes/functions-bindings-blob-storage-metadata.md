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
ms.openlocfilehash: f4e6d5fb41769544b7be0f689447364988d0380d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95998859"
---
BLOB-utlösaren innehåller flera egenskaper för metadata. Dessa egenskaper kan användas som en del av bindnings uttryck i andra bindningar eller som parametrar i koden. Dessa värden har samma semantik som [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) -typen.

|Egenskap  |Typ  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Sökvägen till den Utlös ande blobben.|
|`Uri`|`System.Uri`|Blobens URI för den primära platsen.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blobens system egenskaper. |
|`Metadata` |`IDictionary<string,string>`|Användardefinierade metadata för blobben.|

Till exempel loggar följande C#-skript och JavaScript-exempel sökvägen till den Utlös ande blobben, inklusive behållaren:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```