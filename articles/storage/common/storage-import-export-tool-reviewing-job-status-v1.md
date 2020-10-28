---
title: Granska status för Azure import/export-jobb-v1 | Microsoft Docs
description: Lär dig hur du använder de loggfiler som skapats av import-eller export jobbet för att se jobbets status.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 7ef06bb9c5b5010f3fbbe413f98d77cc8519de00
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791808"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Granska status för import/export-jobb i Azure med kopiera loggfiler
När tjänsten Microsoft Azure Import/Export bearbetar de enheter som är associerade med ett import-eller export jobb skriver det kopian av loggfiler till det lagrings konto som du använde för att importera eller exportera blobbar. Logg filen innehåller detaljerad statusinformation om varje fil som har importer ATS eller exporter ATS. Tjänsten returnerar URL: en för varje kopierings logg fil när du frågar efter status för ett slutfört jobb. Mer information finns i [Hämta jobb](/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>Exempel-URL: er

Följande är exempel-URL: er för kopiering av loggfiler för ett import jobb med två enheter:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Se [logg fils formatet import/export-tjänst](/previous-versions/azure/storage/common/storage-import-export-file-format-log) för formatet på kopierings loggarna och den fullständiga listan med status koder.  

## <a name="next-steps"></a>Nästa steg

 * [Konfigurera verktyget Azure import/export](storage-import-export-tool-setup-v1.md)   
 * [Förbereda hårddiskar för ett importjobb](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import-v1)   
 * [Reparera ett importjobb](./storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparera ett exportjobb](./storage-import-export-tool-repairing-an-export-job-v1.md)