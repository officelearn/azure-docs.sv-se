---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "72329551"
---
Du har redan skapat en funktionsapp i Azure, tillsammans med det nödvändiga lagringskontot. Anslutningssträngen för det här kontot lagras säkert i appinställningar i Azure. I den här artikeln skriver du meddelanden till en lagringskö i samma konto. Om du vill ansluta till ditt Lagringskonto när du kör funktionen lokalt måste du hämta appinställningarna till filen local.settings.json. 

Från projektets rot kör du följande Azure Functions Core Tools-kommando för att `<APP_NAME>` hämta inställningar till local.settings.json och ersätter med namnet på din funktionsapp från föregående artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Du kan behöva logga in på ditt Azure-konto.

> [!IMPORTANT]  
> Det här kommandot skriver över alla befintliga inställningar med värden från din funktionsapp i Azure.  
>
> Eftersom den innehåller hemligheter publiceras aldrig filen local.settings.json, och den bör uteslutas från källkontrollen.  
> 

Du behöver `AzureWebJobsStorage`värdet , som är anslutningssträngen för lagringskonto. Du använder den här anslutningen för att kontrollera att utdatabindningen fungerar som förväntat.
