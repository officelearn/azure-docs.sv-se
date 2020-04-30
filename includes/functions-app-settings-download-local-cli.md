---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "72329551"
---
Du har redan skapat en Function-app i Azure, tillsammans med det lagrings konto som krävs. Anslutnings strängen för det här kontot lagras på ett säkert sätt i appinställningar i Azure. I den här artikeln skriver du meddelanden till en lagrings kö i samma konto. För att ansluta till ditt lagrings konto när funktionen körs lokalt måste du hämta appinställningar till filen Local. Settings. JSON. 

Kör följande Azure Functions Core Tools-kommando från roten i projektet för att ladda ned inställningar till Local. Settings. JSON och Ersätt `<APP_NAME>` med namnet på din Function-app från föregående artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Du kan behöva logga in på ditt Azure-konto.

> [!IMPORTANT]  
> Det här kommandot skriver över alla befintliga inställningar med värden från din Function-app i Azure.  
>
> Eftersom den innehåller hemligheter publiceras inte filen Local. Settings. JSON och den bör undantas från käll kontrollen.  
> 

Du behöver värdet `AzureWebJobsStorage`, vilket är anslutnings strängen för lagrings kontot. Använd den här anslutningen för att kontrol lera att utgående bindning fungerar som förväntat.
