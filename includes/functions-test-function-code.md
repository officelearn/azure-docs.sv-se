---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ae88a6132c79d9d9f930f6445e53b0f0452c9f2a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262615"
---
## <a name="test"></a>Testa funktionen

Använd cURL till att testa den distribuerade funktionen på en Mac- eller Linux-dator, eller Bash i Windows. Kör följande cURL-kommando och ersätt platshållaren `<app_name>` med namnet på din funktionsapp. Lägg till frågesträngen `&name=<yourname>` i webbadressen.

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Funktionssvaret visas i en webbläsare.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Om du inte har cURL tillgängligt på kommandoraden anger du samma webbadress i webbläsarens adressfält. På samma sätt ersätter du platshållaren `<app_name>` med namnet på funktionsappen och lägger till frågesträngen `&name=<yourname>` i webbadressen.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Funktionssvaret visas i en webbläsare.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
