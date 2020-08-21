---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 052e0c93732b99efa37b029cad29dc2efded78ee
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703318"
---
## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Visual Studio Code integreras med [Azure Functions Core tools](../articles/azure-functions/functions-run-local.md) så att du kan köra det här projektet på den lokala utvecklings datorn innan du publicerar till Azure.

1. Du anropar funktionen genom att trycka på F5 för att starta projektet för Function-appen. Utdata från Core Tools visas på panelen **Terminal**.

1. Om du inte redan har installerat Azure Functions Core Tools väljer du **Installera** vid prompten. När kärn verktygen är installerade startar din app på panelen **Terminal** . Du kan se URL-slutpunkten för din HTTP-utlöst funktion som körs lokalt. 

    ![Lokala Azure-utdata](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Med kärn verktyg som kör, navigerar du till följande URL för att köra en GET-begäran, som inkluderar `?name=Functions` frågesträng.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Ett svar returneras, vilket ser ut så här i en webbläsare:

    ![Svar för funktion-localhost i webbläsaren](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Information om begäran visas på panelen **Terminal** .

    ![Funktions körning i Terminal panel](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Tryck på CTRL + C för att stoppa kärn verktygen och koppla från fel söknings programmet.
