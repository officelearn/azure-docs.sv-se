---
title: Lista över alla dina Azure Import/Export-jobb | MicrosoftDocs
description: Lär dig mer om att lista alla jobb för Azure Import/Export-tjänsten i en prenumeration.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 69daac71b69969a7ad9acfeb7095053f8138bf53
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520888"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Räkna upp jobb i tjänsten Azure Import/Export
Om du vill räkna upp alla jobb i en prenumeration, anropa den [lista jobb](/rest/api/storageimportexport/jobs#Jobs_List) igen. `List Jobs` Returnerar en lista över jobb samt följande attribut:

-   Vilken typ av jobb (Import eller Export)

-   Jobbets aktuella tillstånd

-   Jobbet Tjänstadministratörens storage-konto

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av REST-API för Import/Export-tjänsten](storage-import-export-using-the-rest-api.md)
