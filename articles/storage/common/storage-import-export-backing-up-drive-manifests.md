---
title: Säkerhetskopiera enhetsmanifest för Azure Import/Export | Microsoft Docs
description: Lär dig hur din enhetsmanifest för Microsoft Azure Import/Export-tjänsten som säkerhetskopieras automatiskt.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ea223ea3ccd113014ceabff34cc4d0174abb1ddf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483136"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Säkerhetskopiering av enhet manifest för Azure Import/Export-jobb

Enhetsmanifest kan automatiskt säkerhetskopieras till BLOB-genom att ange den `BackupDriveManifest` egenskap `true` i den [placera jobbet](/rest/api/storageimportexport/jobs) eller [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs) REST API-åtgärder. Som standard säkerhetskopieras enhetsmanifest inte. Enheten manifest säkerhetskopiorna lagras som blockblobar i en behållare i lagringskontot som associerats med jobbet. Behållarens namn är som standard `waimportexport`, men du kan ange ett nytt namn i den `DiagnosticsPath` egenskapen när du anropar den `Put Job` eller `Update Job Properties` åtgärder. Säkerhetskopiering manifest blob namnges i följande format: `waies/jobname_driveid_timestamp_manifest.xml`.

 Du kan hämta URI för säkerhetskopiering enhetsmanifest för ett jobb genom att anropa den [Get Job](/rest/api/storageimportexport/jobs) igen. Blob URI returneras i de `ManifestUri` egenskapen för varje enhet.

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av REST-API för Import/Export-tjänsten](storage-import-export-using-the-rest-api.md)
