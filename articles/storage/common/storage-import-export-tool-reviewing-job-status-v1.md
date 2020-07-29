---
title: Granska status för Azure import/export-jobb-v1 | Microsoft Docs
description: Lär dig hur du använder loggfilerna som skapades när import-eller export jobbet kördes för att se status för import/export-jobbet.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 5ab0dd7f0e0ed90c205c37ddbb7ea3a9fca74ae9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512262"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Granska status för import/export-jobb i Azure med kopiera loggfiler
När Microsoft Azure Import/Exports tjänsten bearbetar enheter som är associerade med ett import-eller export jobb skriver det kopiera loggfiler till lagrings kontot till eller från vilket du importerar eller exporterar blobbar. Logg filen innehåller detaljerad statusinformation om varje fil som har importer ATS eller exporter ATS. URL: en till varje kopierings logg fil returneras när du frågar efter status för ett slutfört jobb. Mer information finns i [Hämta jobb](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) .  

## <a name="example-urls"></a>Exempel-URL: er

Följande är exempel-URL: er för kopiering av loggfiler för ett import jobb med två enheter:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Se [logg fils formatet import/export-tjänst](../storage-import-export-file-format-log.md) för formatet på kopierings loggarna och den fullständiga listan med status koder.  

## <a name="next-steps"></a>Nästa steg

 * [Konfigurera verktyget Azure import/export](storage-import-export-tool-setup-v1.md)   
 * [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparera ett importjobb](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
