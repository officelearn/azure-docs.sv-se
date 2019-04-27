---
title: Granska jobbstatus för Azure Import/Export - v1 | Microsoft Docs
description: Lär dig hur du använder loggfilerna som skapades när import eller export-jobbet kördes för att se status för Import/Export-jobbet.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306e3ccf19ba8db2de01e4b20a52707215a4a040
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320714"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Granska Azure Import/Export jobbstatus med kopiera loggfiler
När Microsoft Azure Import/Export-tjänsten bearbetar enheter som är associerade med ett import eller export-jobb, skriver kopiera loggfiler till storage-kontot till eller från vilken du importerar eller exporterar blobar. Loggfilen innehåller detaljerad statusinformation om varje fil som importeras eller exporteras. URL: en loggfil varje kopia returneras när du frågar status för ett slutfört jobb; Se [Get Job](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) för mer information.  

## <a name="example-urls"></a>Exempel-URL: er

Följande är exempel URL: er för kopiera loggfiler för ett importjobb med två enheter:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Se [tjänsten Import/Export-loggfilsformat](../storage-import-export-file-format-log.md) för kopiera loggar och en fullständig lista över statuskoder.  
  
## <a name="next-steps"></a>Nästa steg
 
 * [Konfigurera Azure Import/Export-verktyget](storage-import-export-tool-setup-v1.md)   
 * [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparera ett importjobb](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
