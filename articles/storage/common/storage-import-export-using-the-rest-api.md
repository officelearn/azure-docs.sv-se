---
title: "Med hjälp av tjänsten Azure Import/Export REST API | Microsoft Docs"
description: "Lär dig hitta resurser för att använda tjänsten Azure Import/Export REST-API, bland annat material som referens och anvisningar."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 9a5a97a5d9f06aa73f1ad521e112fa25f215724f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Använda REST-API för Azure Import/Export-tjänsten

Tjänsten Microsoft Azure Import/Export visar ett REST-API för att tillåta programmässiga kontroll över import/export av jobb. Du kan också använda REST API för att utföra alla import/export-åtgärder som du kan utföra med den [Azure-portalen](https://portal.azure.com/). Du kan dessutom använda REST API för att utföra vissa detaljerade åtgärder, till exempel fråga procentandel slutförandet av ett jobb som inte är tillgänglig i Azure-portalen.

Se [med hjälp av tjänsten Microsoft Azure Import/Export för att överföra Data till Blob Storage](../storage-import-export-service.md) en översikt över Import/Export-tjänsten och en genomgång som visar hur du använder portalen för att skapa och hantera importera och exportera jobben.

## <a name="service-endpoints"></a>slutpunkter

Tjänsten Azure Import/Export är resursprovidern för Azure Resource Manager och ger en uppsättning REST API: er på följande HTTPS-slutpunkt för hantering av import/export av jobb:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Versionshantering

Förfrågningar till tjänsten Import/Export måste ange den `api-version` parametern och ange värdet till `2016-11-01`.

## <a name="importexport-service-operations"></a>Importera och exportera tjänståtgärder

[Skapa ett importjobb](../storage-import-export-creating-an-import-job.md)

[Skapa ett exportjobb](../storage-import-export-creating-an-export-job.md)

[Hämta statusinformation för ett jobb](storage-import-export-retrieving-state-info-for-a-job.md)

[Räkna upp jobb](../storage-import-export-enumerating-jobs.md)

[Avbryta och ta bort jobb](storage-import-export-cancelling-and-deleting-jobs.md)

[Säkerhetskopiering av enheten manifest](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostik och felåterställning av Import/Export-jobb](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Nästa steg

* [Storage Import/Export REST](/rest/api/storageimportexport)
