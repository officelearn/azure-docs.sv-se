---
title: Säkerhetskopiera Azure Import/Export enhet manifesten | Microsoft Docs
description: Lär dig mer om att din enhet manifest för tjänsten Microsoft Azure Import/Export säkerhetskopieras automatiskt.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 33eb8e1eea8f8aa7b79ef3e54f2b1ed88dc794ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873669"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Säkerhetskopiera enheten visar för Azure Import/Export-jobb

Enheten manifest kan automatiskt säkerhetskopieras till blobbar genom att ange den `BackupDriveManifest` egenskapen `true` i den [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) eller [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs#Jobs_Update) REST-API: et. Som standard säkerhetskopieras enhet manifesten inte. Enheten manifestet säkerhetskopiorna lagras som blockblobar i en behållare i storage-konto som är associerat med jobbet. Behållarens namn är som standard `waimportexport`, men du kan ange ett nytt namn i den `DiagnosticsPath` egenskapen när du anropar den `Put Job` eller `Update Job Properties` åtgärder. Säkerhetskopiering manifestet blob namnges i följande format: `waies/jobname_driveid_timestamp_manifest.xml`.

 Du kan hämta URI för enhet manifesten för ett jobb genom att anropa den [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) igen. Blob-URI returneras den `ManifestUri` egenskap för varje enhet.

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av tjänsten Import/Export REST API](storage-import-export-using-the-rest-api.md)
