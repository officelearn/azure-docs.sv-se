---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76964153"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Visual Studio Code integreras med [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) så att du kan köra det här projektet på din lokala utvecklingsdator innan du publicerar i Azure.

1. Om du vill anropa funktionen trycker du på F5 för att starta funktionsappprojektet. Utdata från Core Tools visas på panelen **Terminal**.

1. Om du inte redan har installerat Azure Functions Core Tools väljer du **Installera** vid prompten. När core-verktygen är installerade startar **Terminal** appen på terminalpanelen. Du kan se URL-slutpunkten för din HTTP-utlösta funktion som körs lokalt. 

    ![Lokala Azure-utdata](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. När Core Tools körs navigerar du till följande URL `?name=Functions` för att köra en GET-begäran, som innehåller frågesträngen.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. Ett svar returneras, som ser ut som följande i en webbläsare:

    ![Svar för funktion-localhost i webbläsaren](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Information om begäran visas **Terminal** på terminalpanelen.

    ![Funktionskörning på terminalpanelen](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Tryck på Ctrl + C för att stoppa core tools och koppla bort felsökningsprogrammet.
