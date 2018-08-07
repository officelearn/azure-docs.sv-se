---
title: Säkerhetskopiera enhetsmanifest för Azure Import/Export | Microsoft Docs
description: Lär dig hur din enhetsmanifest för Microsoft Azure Import/Export-tjänsten som säkerhetskopieras automatiskt.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 933c0121a4f718ff812fc921bd6e04983fc69931
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520545"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Säkerhetskopiering av enhet manifest för Azure Import/Export-jobb

Enhetsmanifest kan automatiskt säkerhetskopieras till BLOB-genom att ange den `BackupDriveManifest` egenskap `true` i den [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) eller [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs#Jobs_Update) REST API-åtgärder. Som standard säkerhetskopieras enhetsmanifest inte. Enheten manifest säkerhetskopiorna lagras som blockblobar i en behållare i lagringskontot som associerats med jobbet. Behållarens namn är som standard `waimportexport`, men du kan ange ett nytt namn i den `DiagnosticsPath` egenskapen när du anropar den `Put Job` eller `Update Job Properties` åtgärder. Säkerhetskopiering manifest blob namnges i följande format: `waies/jobname_driveid_timestamp_manifest.xml`.

 Du kan hämta URI för säkerhetskopiering enhetsmanifest för ett jobb genom att anropa den [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) igen. Blob URI returneras i de `ManifestUri` egenskapen för varje enhet.

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av REST-API för Import/Export-tjänsten](storage-import-export-using-the-rest-api.md)
