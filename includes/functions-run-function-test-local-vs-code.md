---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 43da0ea4ddfc5410425465d436522523739218fe
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408585"
---
## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Visual Studio Code integreras med [Azure Functions Core tools](../articles/azure-functions/functions-run-local.md) så att du kan köra det här projektet på den lokala utvecklings datorn innan du publicerar till Azure.

1. Du anropar funktionen genom att trycka på <kbd>F5</kbd> för att starta projektet för Function-appen. Utdata från Core Tools visas på panelen **Terminal**.

1. Om du inte redan har installerat Azure Functions Core Tools väljer du **Installera** vid prompten. När kärn verktygen är installerade startar din app på panelen **Terminal** . Du kan se URL-slutpunkten för din HTTP-utlöst funktion som körs lokalt.

    ![Lokal funktion kontra utdata från kod](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Med kärn verktyg som kör, navigerar du till följande URL för att köra en GET-begäran, som inkluderar `?name=Functions` frågesträng.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Ett svar returneras, vilket ser ut så här i en webbläsare:

    ![Webbläsare – exempel på localhost-utdata](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Information om begäran visas på panelen **Terminal** .

    ![Start-VS Code-utdata för aktivitets värd](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Tryck på <kbd>CTRL + C</kbd> för att stoppa kärn verktygen och koppla från fel söknings programmet.
