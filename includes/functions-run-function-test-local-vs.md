---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "80056628"
---
1. Tryck på F5 i Visual Studio för att köra funktionen. Du kan behöva aktivera ett brand Väggs undantag så att verktygen kan hantera HTTP-förfrågningar. Tillstånds nivåer tillämpas aldrig när du kör en funktion lokalt.

2. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Lokal Azure-körningsmiljö](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `?name=<YOUR_NAME>` till denna URL och kör begäran. Följande bild visar svaret i webbläsaren till den lokala GET-begäran som returnerades av funktionen: 

    ![Svar för funktion-localhost i webbläsaren](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Stoppa fel sökningen genom att trycka på Skift + F5 i Visual Studio.