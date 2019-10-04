---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839039"
---
Du har redan skapat en Function-app i Azure, tillsammans med det lagrings konto som krävs. Anslutnings strängen för det här kontot lagras på ett säkert sätt i appinställningar i Azure. I den här artikeln skriver du meddelanden till en lagrings kö i samma konto. För att ansluta till ditt lagrings konto när funktionen körs lokalt måste du hämta appinställningar till filen Local. Settings. JSON. Kör följande Azure Functions Core Tools-kommando för att ladda ned inställningar till Local. Settings. JSON, och ersätta `<APP_NAME>` med namnet på din Function-app från föregående artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Du kan behöva logga in på ditt Azure-konto.

> [!IMPORTANT]  
> Eftersom den innehåller hemligheter publiceras inte filen Local. Settings. JSON och den bör undantas från käll kontrollen.

Du behöver värdet `AzureWebJobsStorage`, vilket är anslutnings strängen för lagrings kontot. Använd den här anslutningen för att kontrol lera att utgående bindning fungerar som förväntat.
