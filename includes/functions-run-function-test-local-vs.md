---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056628"
---
1. Om du vill köra funktionen trycker du på F5 i Visual Studio. Du kan behöva aktivera ett brandväggsundantag så att verktygen kan hantera HTTP-begäranden. Auktoriseringsnivåer tillämpas aldrig när du kör en funktion lokalt.

2. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Lokal Azure-körningsmiljö](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `?name=<YOUR_NAME>` i den här URL:en och kör begäran. Följande bild visar svaret i webbläsaren på den lokala GET-begäran som returneras av funktionen: 

    ![Svar för funktion-localhost i webbläsaren](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Om du vill sluta felsöka trycker du på Skift+F5 i Visual Studio.