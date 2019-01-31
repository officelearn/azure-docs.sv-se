---
title: Förhandsgranska diskanvändning för ett Azure Import/Export-exportjobb - v1 | Microsoft Docs
description: Lär dig hur du förhandsgranskar listan över blobar som du har valt för ett exportjobb i tjänsten Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 53ab1e28c5864b403d52bf5e73f0c5c41b8f18a8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454750"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Förhandsgranska diskanvändning för ett exportjobb
Innan du skapar ett exportjobb, måste du välja en uppsättning blobbar exporteras. Microsoft Azure Import/Export-tjänsten kan du använda en lista över blob-sökvägar eller blob-prefix för att representera de blobar som du har valt.  
  
Därefter måste du bestämma hur många enheter som du behöver skicka. Import/Export-verktyget erbjuder den `PreviewExport` kommando för att förhandsgranska diskanvändning för BLOB-objekt som du har valt, baserat på storleken på enheterna som du tänker använda.

## <a name="command-line-parameters"></a>Kommandoradsparametrar

Du kan använda följande parametrar när du använder den `PreviewExport` kommandot Import/Export-verktyget.

|Kommandoradsparametern|Beskrivning|  
|--------------------------|-----------------|  
|**/logdir:**< LogDirectory\>|Valfri. Loggkatalogen. Utförliga loggfiler ska skrivas till den här katalogen. Om inga loggkatalogen anges, används den aktuella katalogen som log-katalogen.|  
|**/sn:**<StorageAccountName\>|Krävs. Namnet på lagringskontot för export-jobbet.|  
|**/sk:**<StorageAccountKey\>|Krävs endast om en SAS-behållare inte har angetts. Kontonyckel för lagringskontot för export-jobbet.|  
|**/csas:**< ContainerSas\>|Krävs endast om en lagringskontonyckel inte har angetts. Behållaren SAS för att lista blobbar exporteras i export-jobbet.|  
|**/ExportBlobListFile:**<ExportBlobListFile\>|Krävs. Sökväg till XML-Datatypen filen som innehåller listan över blob-sökvägar eller blob-prefix som sökväg för BLOB-objekt som ska exporteras. Filformatet som används i den `BlobListBlobPath` elementet i den [placera jobbet](/rest/api/storageimportexport/jobs) driften av REST-API för Import/Export-tjänsten.|  
|**/ DriveSize:**< DriveSize\>|Krävs. Storleken på enheter som ska användas för ett exportjobb *t.ex.*, 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Exempel-kommandorad

Exemplet nedan visar den `PreviewExport` kommando:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Export-blobblistfil kan innehålla blobnamn och blob-adressprefix, som visas här:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Azure Import/Export-verktyget visar en lista över alla blobbar exporteras och beräknar hur att bygga dem i enheter av den angivna storleken med hänsyn till eventuella nödvändiga arbetet och sedan beräknar antalet enheter som behövs för att lagra blobar och enhetsinformationen för användning.  
  
Här är ett exempel på utdata med endast i informationssyfte loggar utelämnas:  
  
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

* [Referens för Azure Import/Export-verktyget](../storage-import-export-tool-how-to-v1.md)
