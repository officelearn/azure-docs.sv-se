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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478794"
---
## <a name="deleting-personal-information"></a>Ta bort personlig information

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Personlig information som är relevant för import/export-tjänsten (via portalen och API) under importen och exportåtgärder. Data som används under de här processerna är:

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

När en import/export-jobbet skapas, kommer användare att ange kontaktinformation och en leveransadress. Personlig information lagras i upp till två olika platser: i jobbet och eventuellt i inställningarna för användarportalen. Personlig information lagras endast i portalinställningar om du markerar kryssrutan, **spara transportföretags- och returnera adress som standard** under den *returnera leveransinformation* avsnittet för exporten.

Informationen kan tas bort på följande sätt:

- Data som sparats med jobbet tas bort med jobbet. Användare kan ta bort jobb manuellt och slutförda jobb tas automatiskt bort efter 90 dagar. Du kan manuellt ta bort jobb via REST API eller Azure-portalen. Om du vill ta bort jobbet i Azure-portalen, går du till import/export-jobbet och klicka på *ta bort* i kommandofältet. Information om hur du tar bort en import-/ exportjobb via REST-API i [ta bort import/export-jobbet](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Kontaktinformation som sparats i portalinställningar kan tas bort genom att ta bort inställningarna för användarportalen. Du kan ta bort portalinställningar genom att följa dessa steg:
  - Logga in på [Azure Portal](https://portal.azure.com).
  - Klicka på den *inställningar* ikonen ![Azure Inställningsikonen](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Klicka på *exportera alla inställningar* (att spara dina aktuella inställningar till en `.json` fil).
  - Klicka på *ta bort alla inställningar och privata instrumentpaneler* att ta bort alla inställningar som bland annat sparade kontaktinformation.

Mer information finns i Microsoft Privacy-policy på [Säkerhetscenter](https://www.microsoft.com/trustcenter)