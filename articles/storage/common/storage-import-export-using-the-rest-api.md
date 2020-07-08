---
title: Använda Azure import/export-tjänsten REST API | Microsoft Docs
description: Lär dig var du hittar resurser för att använda Azures import-/export tjänst REST API, inklusive både instruktions material och referens material.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: ae80c00fd81d0966167d2adf00764a0b1089c163
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85514118"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Använda REST-API för Azure Import/Export-tjänsten

Microsoft Azure Import/Exports tjänsten exponerar en REST API för att aktivera programmerings styrning av import/export-jobb. Du kan använda REST API för att utföra alla import-/export åtgärder som du kan utföra med [Azure Portal](https://portal.azure.com/). Du kan också använda REST API för att utföra vissa detaljerade åtgärder, t. ex. för att skicka frågor till procent färdigt jobb för ett jobb som för närvarande inte är tillgängligt i Azure Portal.

Mer information om hur du använder portalen för att skapa och hantera import-och export jobb finns i [använda Microsoft Azure import/export tjänsten för att överföra data till Blob Storage](../storage-import-export-service.md) .

## <a name="service-endpoints"></a>Tjänstslutpunkter

Tjänsten Azure import/export är en resurs leverantör för Azure Resource Manager och innehåller en uppsättning REST API: er i följande HTTPS-slutpunkt för att hantera import/export-jobb:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Versionshantering

Begär anden till import/export-tjänsten måste ange `api-version` parametern och ange dess värde till `2016-11-01` .

## <a name="importexport-service-operations"></a>Åtgärder för att importera/exportera tjänster

[Skapa ett importjobb](../storage-import-export-creating-an-import-job.md)

[Skapa ett exportjobb](../storage-import-export-creating-an-export-job.md)

[Hämta statusinformation för ett jobb](storage-import-export-retrieving-state-info-for-a-job.md)

[Räkna upp jobb](../storage-import-export-enumerating-jobs.md)

[Avbryta och ta bort jobb](storage-import-export-cancelling-and-deleting-jobs.md)

[Säkerhetskopiera enhets manifest](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostik och felåterställning av Import/Export-jobb](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Nästa steg

* [Lagrings import/-export REST](/rest/api/storageimportexport)
