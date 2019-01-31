---
title: Snabbreferens för Azure Import/Export-verktyget import jobbet kommandon | Microsoft Docs
description: Azure Import/Export-verktyget kommandoreferens för kommandon som används ofta import jobbet.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 7d21ab42188d5b8d6cb8c179a28d1b5bee822f05
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453594"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Snabbreferens för ofta använda kommandon för importjobb

Den här artikeln innehåller en översikt över för några vanliga kommandon. Detaljerad användning, se [förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="first-session"></a>Första sessionen

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>Andra session

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>Avbryt senaste sessionen

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>Återuppta senaste avbrutna session

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>Lägga till enheter i senaste sessionen

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Nästa steg

* [Exempelarbetsflöde för att förbereda hårddiskar för ett importjobb](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
