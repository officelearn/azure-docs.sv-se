---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148059"
---
## <a name="test"></a>Testa funktionen

Använd cURL för att testa den distribuerade funktionen på en Mac- eller Linux-dator, eller med PowerShell i Windows. Kör följande cURL-kommando och ersätt platshållaren `<app_name>` med namnet på din funktionsapp. Lägg till frågesträngen `&name=<yourname>` i webbadressen.

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Funktionssvaret visas i en webbläsare.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Om `cURL`eller `Invoke-WebRequest` inte finns på kommandoraden anger du samma webbadress i webbläsarens adressfält. På samma sätt ersätter du platshållaren `<app_name>` med namnet på funktionsappen och lägger till frågesträngen `&name=<yourname>` i webbadressen.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Funktionssvaret visas i en webbläsare.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
