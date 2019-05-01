---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867321"
---
## <a name="test"></a>Testa funktionen i Azure

Använda cURL för att testa den distribuerade funktionen. Med hjälp av Webbadressen som du kopierade i föregående steg, Lägg till frågesträngen `&name=<yourname>` till URL: en, som i följande exempel:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![med cURL för att anropa funktionen i Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Du kan också klistra in den kopierade Webbadressen i adressen för din webbläsare. Igen, Lägg till frågesträngen `&name=<yourname>` i URL: en innan du kör begäran.

![Använda en webbläsare för att anropa funktionen.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
