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
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313888"
---
## <a name="deleting-personal-information"></a>Ta bort personlig information

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Personlig information som är relevant för import/export-tjänst (via portalen och API) vid import och export operations. Data som används under de här processerna är:

- Kontaktnamn
- Telefonnummer
- E-post
- Gatuadress
- Ort
- Postnummer
- Status
- Region/land
- Enhets-ID
- Operatör kontonummer
- Leverans spårning tal

När ett import-/ exportjobb skapas anger användare kontaktinformation och en leveransadress. Personlig information lagras i upp till två olika platser: i jobbet och du kan också portalinställningar. Personlig information lagras i portalinställningar endast om du markerar kryssrutan, **spara operatör och gå sedan tillbaka adressen som standard** under den *returnera leverans info* avsnitt i exporten.

Personlig kontaktinformation kan tas bort på följande sätt:

- Data som sparas med jobbet tas bort med jobbet. Användare kan ta bort jobb manuellt och slutförda jobb tas automatiskt bort efter 90 dagar. Du kan manuellt ta bort jobb via REST API- eller Azure-portalen. Ta bort jobbet i Azure portal, gå till import-/ exportjobb och klicka *ta bort* från kommandofältet. Mer information om hur du tar bort ett import-/ exportjobb via REST-API finns i [ta bort ett import-/ exportjobb](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Informationen sparas i portalinställningar kan tas bort genom att ta bort portalinställningar. Du kan ta bort portalinställningar genom att följa dessa steg:
  - Logga in på [Azure Portal](https://portal.azure.com).
  - Klicka på den *inställningar* ikonen ![ikonen för inställningar av Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Klicka på *exportera alla inställningar* (spara de aktuella inställningarna till en `.json` fil).
  - Klicka på *ta bort alla inställningar och privata instrumentpaneler* att ta bort alla inställningar som bland annat sparat kontaktinformation.

Mer information finns i Microsoft Privacy-policy på [Säkerhetscenter](https://www.microsoft.com/trustcenter)