---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842224"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Visual Studio Code integreras med [Azure Functions Core tools](../articles/azure-functions/functions-run-local.md) så att du kan köra det här projektet på den lokala utvecklings datorn innan du publicerar till Azure.

1. Du anropar funktionen genom att trycka på F5 för att starta projektet för Function-appen. Utdata från Core Tools visas på panelen **Terminal**.

1. Om du inte redan har installerat Azure Functions Core Tools väljer du **Installera** vid prompten. När kärn verktygen är installerade startar din app på panelen **Terminal** .

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen. 

    ![Lokala Azure-utdata](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` till den här URL: en och kör GET-begäran. 

1. Ett svar returneras, vilket ser ut så här i en webbläsare:

    ![Svar för funktion-localhost i webbläsaren](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Tryck på Shift + F5 för att stoppa kärn verktygen och koppla från fel söknings programmet.
