---
title: Snabbreferens för Azure Import/Export-verktyget import jobbet kommandon - v1 | Microsoft Docs
description: Azure Import/Export-verktyget kommandoreferens för kommandon som används ofta import jobbet. Detta refererar till v1 av Import/Export-verktyget.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 44ee23a510bc15d5cb52a338be1652180b922db9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451620"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Snabbreferens för ofta använda kommandon för importjobb
Det här avsnittet innehåller en översikt över för några vanliga kommandon. Detaljerad användning, se [förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Förbered en hårddisk när data har redan kopierats till hårddisken
 Följande kommando förbereder en hårddisk när data som redan har kopierats till den, men ännu inte har krypterats med BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Kopiera en enskild katalog till en hårddisk  
 Följande kommando kopierar en enda källkatalog till en hårddisk som ännu inte har krypterats med BitLocker:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Kopiera två kataloger till en hårddisk  
 Om du vill kopiera två kataloger för källan till en enhet använder du följande kommandon:  
  
 Det första kommandot anger loggkatalogen, lagringskontonyckel, mål enhetsbeteckning, `format/encrypt` krav och gemensamma parametrar:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 Det andra kommandot anger journal-fil, en ny session-ID och käll- och målplatserna:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Kopiera en stor fil till en hårddisk i en andra kopia-session  
 Följande kommando kopierar en enda stor fil till en hårddisk som förbereddes i en tidigare session för kopiering:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Nästa steg

* [Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
