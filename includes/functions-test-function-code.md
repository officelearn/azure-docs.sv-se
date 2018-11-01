---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f7e023bcfeaa07a4ee9a80ccf4ec17120605c1ba
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133811"
---
## <a name="test"></a>Testa funktionen

Använd cURL till att testa den distribuerade funktionen på en Mac- eller Linux-dator, eller Bash i Windows. Kör följande cURL-kommando och ersätt platshållaren `<app_name>` med namnet på din funktionsapp. Lägg till frågesträngen `&name=<yourname>` i webbadressen.

```bash
curl https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
```  

![Funktionssvaret visas i en webbläsare.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Om du inte har cURL tillgängligt på kommandoraden anger du samma webbadress i webbläsarens adressfält. På samma sätt ersätter du platshållaren `<app_name>` med namnet på funktionsappen och lägger till frågesträngen `&name=<yourname>` i webbadressen.

    https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
   
![Funktionssvaret visas i en webbläsare.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
