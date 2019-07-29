---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592906"
---
1. Tryck på **F5**för att köra funktionen. Du kan behöva aktivera ett brand Väggs undantag så att verktygen kan hantera HTTP-förfrågningar. Tillstånds nivåer tillämpas aldrig när de körs lokalt.

2. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Lokal Azure-körningsmiljö](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `?name=<YOUR_NAME>` i webbadressen och kör din begäran. Nedan visas svaret på den lokala GET-begäran som returnerades av funktionen i webbläsaren: 

    ![Svar för funktion-localhost i webbläsaren](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Tryck på **Skift + F5** för att stoppa felsökningen.