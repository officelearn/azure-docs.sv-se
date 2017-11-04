---
title: Granska jobbstatus Azure Import/Export - v1 | Microsoft Docs
description: "Lär dig hur du använder loggfilerna som skapades när importera och exportera jobbet kördes för att se status för jobbet Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.openlocfilehash: bdb30bc28c36ab9e969efc8be3b87b97e4027b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Granska Azure Import/Export jobbstatus med copy-loggfiler
När tjänsten Microsoft Azure Import/Export bearbetar enheter som är kopplade till ett jobb som importeras eller exporteras, skriver kopia loggfiler till storage-konto till eller från vilken du importerar eller exporterar BLOB. Loggfilen innehåller detaljerad statusinformation om varje fil som importeras eller exporteras. URL till en loggfil för varje kopia returneras när du frågar status för slutförda jobb. Se [Get Job](/rest/api/storageservices/Get-Job3) för mer information.  

## <a name="example-urls"></a>Exempel-URL: er

Följande är exempel URL: er för kopiera loggfiler för ett importjobb med två enheter:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Se [Import/Export service loggfilsformat](../storage-import-export-file-format-log.md) för kopierar loggar och en fullständig lista över statuskoder format.  
  
## <a name="next-steps"></a>Nästa steg
 
 * [Konfigurera verktyget Azure Import/Export](storage-import-export-tool-setup-v1.md)   
 * [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparera ett importjobb](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
