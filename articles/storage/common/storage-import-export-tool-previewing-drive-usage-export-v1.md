---
title: Förhandsgranska diskanvändning för ett Azure Import/Export-exportjobb - v1 | Microsoft Docs
description: Lär dig mer om att förhandsgranska listan över blobbar som du har valt för ett exportjobb i tjänsten Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 7707d744-7ec7-4de8-ac9b-93a18608dc9a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 6ec74ae0b0931f3fed99a43f4f7e58f9d425b138
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873648"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Förhandsgranska diskanvändning för ett exportjobb
Innan du skapar ett exportjobb måste du välja en uppsättning blobbar som ska exporteras. Tjänsten Microsoft Azure Import/Export kan du använda en lista över blob-sökvägar eller blob-prefix för att representera blobbar som du har valt.  
  
Därefter måste du bestämma hur många enheter som du måste skicka. Verktyget Import/Export ger den `PreviewExport` kommando för att förhandsgranska diskanvändning för blobbar som du har valt, baserat på storleken på enheterna som du ska använda.

## <a name="command-line-parameters"></a>Kommandoradsparametrar

Du kan använda följande parametrar när du använder den `PreviewExport` kommandot i verktyget Import/Export.

|Kommandoradsparametern|Beskrivning|  
|--------------------------|-----------------|  
|**/logdir:**< LogDirectory\>|Valfri. Loggkatalogen. Utförlig loggfilerna skrivs till den här katalogen. Om inga loggkatalogen anges, används den aktuella katalogen som loggkatalogen.|  
|**/SN:**< StorageAccountName\>|Krävs. Namnet på lagringskontot för exportjobbet.|  
|**/Sk:**< StorageAccountKey\>|Krävs endast om en behållare SAS inte har angetts. Kontonyckel för lagringskontot för exportjobbet.|  
|**/csas:**< ContainerSas\>|Krävs endast om en lagringskontonyckel inte har angetts. Behållare SAS för att visa en lista över blobbar som ska exporteras i exportjobbet.|  
|**/ ExportBlobListFile:**< ExportBlobListFile\>|Krävs. Sökvägen till XML-fil som innehåller listan över blob-sökvägar eller blob sökväg prefix för blob som ska exporteras. Filformat som används i den `BlobListBlobPath` element i den [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) driften av tjänsten Import/Export REST API.|  
|**/ DriveSize:**< DriveSize\>|Krävs. Storleken på enheter som ska användas för ett exportjobb *t.ex.*, 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Kommandoradsverktyget exempel

I följande exempel visas den `PreviewExport` kommando:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Exportfilen blob listan kan innehålla blobbnamnen och blob-prefix, som visas här:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Verktyget Azure Import/Export listar alla BLOB som ska exporteras och beräknar så pack dem till enheter i den angivna storleken med hänsyn till alla nödvändiga kostnader och sedan beräknar antalet enheter som behövs för blobbar och användningsinformation för enheten.  
  
Här är ett exempel på utdata med informativt loggar utelämnas:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>Nästa steg

* [Referens för Azure Import/Export-verktyg](../storage-import-export-tool-how-to-v1.md)
