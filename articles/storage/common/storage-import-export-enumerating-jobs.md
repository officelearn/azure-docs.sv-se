---
title: Lista över alla dina Azure Import/Export-jobb | MicrosoftDocs
description: Lär dig mer om att visa en lista över Azure Import/Export service jobb i en prenumeration.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873704"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Uppräkning av jobb i tjänsten Azure Import/Export
För att räkna upp alla jobb i en prenumeration anropar den [lista jobb](/rest/api/storageimportexport/jobs#Jobs_List) igen. `List Jobs`Returnerar en lista över jobb samt följande attribut:

-   Typ av jobb (importera och exportera)

-   Jobbets aktuella tillstånd

-   Projektet har kopplats till lagringskontot

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av tjänsten Import/Export REST API](storage-import-export-using-the-rest-api.md)
