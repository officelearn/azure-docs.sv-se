---
title: Lista över alla dina Azure Import/Export-jobb | MicrosoftDocs
description: Lär dig mer om att lista alla jobb för Azure Import/Export-tjänsten i en prenumeration.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 017208c6fca7c4e55a45070f5aa21652e83e7e8d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462934"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Räkna upp jobb i tjänsten Azure Import/Export
Om du vill räkna upp alla jobb i en prenumeration, anropa den [lista jobb](/rest/api/storageimportexport/jobs#Jobs_List) igen. `List Jobs` Returnerar en lista över jobb samt följande attribut:

-   Vilken typ av jobb (Import eller Export)

-   Jobbets aktuella tillstånd

-   Jobbet Tjänstadministratörens storage-konto

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av REST-API för Import/Export-tjänsten](storage-import-export-using-the-rest-api.md)
