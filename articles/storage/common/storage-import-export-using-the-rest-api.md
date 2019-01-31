---
title: Med hjälp av REST-API för tjänsten Azure Import/Export | Microsoft Docs
description: Lär dig hitta resurser för att använda REST-API, inklusive både detaljerade förklaringar och referensdokumentation material för Azure Import/Export-tjänsten.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 1e8b60f37cefb81fbbbbb7823be7752dd1188dc3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471745"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Använda REST-API för Azure Import/Export-tjänsten

Microsoft Azure Import/Export-tjänsten Exponerar en REST-API för att aktivera programmässiga kontroll av import/export-jobb. Du kan använda REST API för att utföra alla import/export-åtgärder som du kan utföra med den [Azure-portalen](https://portal.azure.com/). Du kan dessutom använda REST API för att utföra vissa detaljerade åtgärder, t.ex frågekörning procentsats slutförandet av ett jobb som inte är tillgänglig i Azure-portalen.

Se [med Microsoft Azure Import/Export-tjänsten för att överföra Data till Blob Storage](../storage-import-export-service.md) för en översikt över Import/Export-tjänsten och en självstudie som visar hur du använder portalen för att skapa och hantera importera och exportera jobb.

## <a name="service-endpoints"></a>Tjänstslutpunkter

Azure Import/Export-tjänsten är en resursprovider för Azure Resource Manager och innehåller en uppsättning av REST API: er på följande HTTPS-slutpunkten för hantering av import/export-jobb:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Versionshantering

Begäranden till Import/Export-tjänsten måste ange den `api-version` parametern och ange värdet `2016-11-01`.

## <a name="importexport-service-operations"></a>Import/Export tjänståtgärder

[Skapa ett importjobb](../storage-import-export-creating-an-import-job.md)

[Skapa ett exportjobb](../storage-import-export-creating-an-export-job.md)

[Hämta statusinformation för ett jobb](storage-import-export-retrieving-state-info-for-a-job.md)

[Räkna upp jobb](../storage-import-export-enumerating-jobs.md)

[Avbryta och ta bort jobb](storage-import-export-cancelling-and-deleting-jobs.md)

[Säkerhetskopiera enhetsmanifest](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostik och felåterställning av Import/Export-jobb](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Nästa steg

* [Storage Import/Export-REST](/rest/api/storageimportexport)
