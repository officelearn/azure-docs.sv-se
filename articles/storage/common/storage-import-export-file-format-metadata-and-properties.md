---
title: Azure Import/metadata och egenskapers filformat för Export | Microsoft Docs
description: Lär dig hur du anger metadata och egenskaperna för en eller flera blobbar som en del av en import eller export jobbet.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 5a886244b43ad006a95e9be0350d9c69fd987ad9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526240"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Azure Import/Export-tjänstens metadata och egenskapers filformat
Du kan ange egenskaper för en eller flera blobbar och metadata som en del av ett importjobb eller en export-jobbet. Om du vill ange egenskaper för BLOB-objekt som skapas som en del av ett importjobb eller metadata, kan du ange en metadata eller egenskaper-fil på hårddisken som innehåller data som ska importeras. För ett exportjobb skrivs metadata och egenskapers till en fil för metadata eller egenskaper som ingår på hårddisken till dig.  
  
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
|`Metadata`|Rotelement|Rotelementet filens metadata.|  
|`metadata-name`|Sträng|Valfri. XML-elementet anger namnet på metadata för bloben och dess värde anger värdet för inställningen metadata.|  
  
## <a name="properties-file-format"></a>Egenskaper för filformat  
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
|`Properties`|Rotelement|Rotelementet filens egenskaper.|  
|`Last-Modified`|Sträng|Valfri. Tid för senaste ändring för bloben. För export-jobb.|  
|`Etag`|Sträng|Valfri. Blobens ETag-värdet. För export-jobb.|  
|`Content-Length`|Sträng|Valfri. Storleken på blobben i byte. För export-jobb.|  
|`Content-Type`|Sträng|Valfri. Innehållstypen för blobben.|  
|`Content-MD5`|Sträng|Valfri. Blobens MD5-hash.|  
|`Content-Encoding`|Sträng|Valfri. Blobens innehåll kodning.|  
|`Content-Language`|Sträng|Valfri. Blobens innehåll språk.|  
|`Cache-Control`|Sträng|Valfri. Kontroll av cache sträng för bloben.|  

## <a name="next-steps"></a>Nästa steg

Se [ange egenskaper för blob](/rest/api/storageservices/set-blob-properties), [ange Blobbmetadata](/rest/api/storageservices/set-blob-metadata), och [inställningen och hämta egenskaper och metadata för blob-resurser](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) för detaljerade regler om inställningen blob-metadata och egenskaper.
