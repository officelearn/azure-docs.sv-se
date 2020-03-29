---
title: Granska Azure Import/Export-jobbstatus – v1 | Microsoft-dokument
description: Lär dig hur du använder loggfilerna som skapades när import- eller exportjobbet kördes för att se statusen för jobbet Importera/exportera.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 33e6ecca0eb4ca05b1f6bf53cdb3bd4ac8c8d8a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978440"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Granska Azure Import/Export-jobbstatus med kopieringsloggfiler
När tjänsten Microsoft Azure Import/Export bearbetar enheter som är associerade med ett import- eller exportjobb skrivs loggfiler till lagringskontot till eller från vilka du importerar eller exporterar blobbar. Loggfilen innehåller detaljerad status för varje fil som importerades eller exporterades. URL:en till varje kopieringsloggfil returneras när du frågar statusen för ett slutfört jobb. Se [Hämta jobb](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) för mer information.  

## <a name="example-urls"></a>Exempel på webbadresser

Följande är exempel på URL:er för kopieringsloggfiler för ett importjobb med två enheter:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Se [Loggfilformat för import/export för](../storage-import-export-file-format-log.md) formatet för kopieringsloggar och en fullständig lista med statuskoder.  

## <a name="next-steps"></a>Nästa steg

 * [Konfigurera verktyget för import/export av Azure](storage-import-export-tool-setup-v1.md)   
 * [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparera ett importjobb](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
