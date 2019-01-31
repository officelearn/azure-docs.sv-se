---
title: Azure Import/metadata och egenskapers filformat för Export | Microsoft Docs
description: Lär dig hur du anger metadata och egenskaperna för en eller flera blobbar som en del av en import eller export jobbet.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 2066d4a2ed6db97285d92d15e14dbd21629dbdfa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457011"
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
  
|XML Element|Type|Beskrivning|  
|-----------------|----------|-----------------|  
|`Metadata`|Rotelement|Rotelementet filens metadata.|  
|`metadata-name`|String|Valfri. XML-elementet anger namnet på metadata för bloben och dess värde anger värdet för inställningen metadata.|  
  
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
  
|XML Element|Type|Beskrivning|  
|-----------------|----------|-----------------|  
|`Properties`|Rotelement|Rotelementet filens egenskaper.|  
|`Last-Modified`|String|Valfri. Tid för senaste ändring för bloben. För export-jobb.|  
|`Etag`|String|Valfri. Blobens ETag-värdet. För export-jobb.|  
|`Content-Length`|String|Valfri. Storleken på blobben i byte. För export-jobb.|  
|`Content-Type`|String|Valfri. Innehållstypen för blobben.|  
|`Content-MD5`|String|Valfri. Blobens MD5-hash.|  
|`Content-Encoding`|String|Valfri. Blobens innehåll kodning.|  
|`Content-Language`|String|Valfri. Blobens innehåll språk.|  
|`Cache-Control`|String|Valfri. Kontroll av cache sträng för bloben.|  

## <a name="next-steps"></a>Nästa steg

Se [ange egenskaper för blob](/rest/api/storageservices/set-blob-properties), [ange Blobbmetadata](/rest/api/storageservices/set-blob-metadata), och [inställningen och hämta egenskaper och metadata för blob-resurser](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) för detaljerade regler om inställningen blob-metadata och egenskaper.
