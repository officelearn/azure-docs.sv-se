---
title: "Azure Import/Export metadata och egenskaper för nytt filformat | Microsoft Docs"
description: "Lär dig hur du anger metadata och egenskaperna för en eller flera blobbar som är en del av en importera eller exportera jobb."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3f728ad94cdcbd32092b677f11a737ae91376720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Azure Import/Export service metadata och egenskaper filformat
Du kan ange egenskaper för en eller flera blobbar och metadata som en del av ett importjobb eller ett exportjobb. Om du vill ange egenskaper för blob som skapas som en del av ett importjobb eller metadata, kan du ange en egenskaper eller metadata fil på hårddisken som innehåller data som ska importeras. För ett exportjobb skrivs metadata och egenskaper till en fil för metadata eller egenskaper som ingår på hårddisken till dig.  
  
## <a name="metadata-file-format"></a>Filformat för metadata  
Formatet för en metadatafil är följande:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML-Element|Typ|Beskrivning|  
|-----------------|----------|-----------------|  
|`Metadata`|Rotelementet|Rotelementet i metadatafilen.|  
|`metadata-name`|Sträng|Valfri. XML-elementet anger namnet på metadata för blob och dess värde anger värdet för inställningen metadata.|  
  
## <a name="properties-file-format"></a>Filformatet för egenskaper  
Formatet för en egenskapsfil är följande:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML-Element|Typ|Beskrivning|  
|-----------------|----------|-----------------|  
|`Properties`|Rotelementet|Rotelementet i filen egenskaper.|  
|`Last-Modified`|Sträng|Valfri. Den senast ändrade tiden för blob. För exportjobb.|  
|`Etag`|Sträng|Valfri. Blobens ETag-värde. För exportjobb.|  
|`Content-Length`|Sträng|Valfri. Storleken på blobben i byte. För exportjobb.|  
|`Content-Type`|Sträng|Valfri. Innehållstypen för blob.|  
|`Content-MD5`|Sträng|Valfri. Blobens MD5-hash.|  
|`Content-Encoding`|Sträng|Valfri. Den blob-innehåll kodning.|  
|`Content-Language`|Sträng|Valfri. Blobens innehåll språk.|  
|`Cache-Control`|Sträng|Valfri. Kontroll av cache sträng för blob.|  

## <a name="next-steps"></a>Nästa steg

Se [ange egenskaper för blob](/rest/api/storageservices/set-blob-properties), [ange Blob-Metadata](/rest/api/storageservices/set-blob-metadata), och [inställningen och hämtning av egenskaper och metadata för blob-resurser](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) för detaljerade regler om inställningen blob-metadata och egenskaper.
