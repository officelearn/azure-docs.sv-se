---
title: ta med fil
description: ta med fil
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187556"
---
## <a name="deleting-personal-information"></a>Ta bort personlig information

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Personlig information är relevant för import/exporttjänsten (via portalen och API: et) under import- och exportåtgärder. Data som används under dessa processer inkluderar:

- Kontaktnamn
- Telefonnummer
- E-post
- Gatuadress
- Ort
- Postnummer
- Status
- Land/region
- Enhets-ID
- Transportföretagets kontonummer
- Spårningsnummer för leveransen

När ett import-/exportjobb skapas anger användarna kontaktinformation och en leveransadress. Personlig information lagras på upp till två olika platser: i jobbet och eventuellt i portalinställningarna. Personlig information lagras bara i portalinställningarna om du markerar kryssrutan med etiketten Spara **operatör och returadress som standard** under avsnittet *Returleveransinformation* i exportprocessen.

Personlig kontaktinformation kan raderas på följande sätt:

- Data som sparats med jobbet tas bort med jobbet. Användare kan ta bort jobb manuellt och slutförda jobb tas bort automatiskt efter 90 dagar. Du kan manuellt ta bort jobben via REST API eller Azure-portalen. Om du vill ta bort jobbet i Azure-portalen går du till ditt import-/exportjobb och klickar på *Ta bort* från kommandofältet. Mer information om hur du tar bort ett import-/exportjobb via REST API finns [i Ta bort ett import-/exportjobb](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Kontaktinformation som sparats i portalinställningarna kan tas bort genom att ta bort portalinställningarna. Du kan ta bort portalinställningarna genom att följa dessa steg:
  - Logga in på [Azure-portalen](https://portal.azure.com).
  - Klicka på *Settings* ikonen ![Inställningar Azure Settings](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Klicka på *Exportera alla inställningar* (om `.json` du vill spara de aktuella inställningarna i en fil).
  - Klicka på *Ta bort alla inställningar och privata instrumentpaneler* om du vill ta bort alla inställningar, inklusive sparad kontaktinformation.

Mer information finns i Microsofts sekretesspolicy på [Trust Center](https://www.microsoft.com/trustcenter)