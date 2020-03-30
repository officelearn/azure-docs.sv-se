---
title: Använda REST API FÖR Azure Import/Export-tjänsten | Microsoft-dokument
description: Lär dig var du hittar resurser för att använda REST-API:et för Azure Import/Export-tjänsten, inklusive både instruktions- och referensmaterial.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978874"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Använda REST-API för Azure Import/Export-tjänsten

Microsoft Azure Import/Export-tjänsten exponerar ett REST API för att möjliggöra programmatisk kontroll av import-/exportjobb. Du kan använda REST API för att utföra alla import-/exportåtgärder som du kan utföra med [Azure-portalen](https://portal.azure.com/). Dessutom kan du använda REST API för att utföra vissa detaljerade åtgärder, till exempel fråga procent slutförande av ett jobb, som för närvarande inte är tillgänglig i Azure-portalen.

Se [Använda tjänsten Importera/exportera Microsoft Azure för överföring](../storage-import-export-service.md) av data till Blob Storage för en översikt över tjänsten Import/Export och en självstudiekurs som visar hur du använder portalen för att skapa och hantera import- och exportjobb.

## <a name="service-endpoints"></a>Tjänstslutpunkter

Azure Import/Export-tjänsten är en resursprovider för Azure Resource Manager och tillhandahåller en uppsättning REST-API:er vid följande HTTPS-slutpunkt för hantering av import-/exportjobb:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Versionshantering

Begäranden till tjänsten Import/Export `api-version` måste ange parametern och ange dess värde till `2016-11-01`.

## <a name="importexport-service-operations"></a>Åtgärder för import/exporttjänst

[Skapa ett importjobb](../storage-import-export-creating-an-import-job.md)

[Skapa ett exportjobb](../storage-import-export-creating-an-export-job.md)

[Hämta statusinformation för ett jobb](storage-import-export-retrieving-state-info-for-a-job.md)

[Räkna upp jobb](../storage-import-export-enumerating-jobs.md)

[Avbryta och ta bort jobb](storage-import-export-cancelling-and-deleting-jobs.md)

[Säkerhetskopiera enhetsmanifest](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostik och felåterställning av Import/Export-jobb](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Nästa steg

* [LAGRING Import/Export REST](/rest/api/storageimportexport)
