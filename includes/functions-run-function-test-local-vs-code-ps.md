---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: ca8da227c97a44abe14354a5c530d508ce357884
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703733"
---
## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Azure Functions Core Tools integreras med Visual Studio Code så att du kan köra och felsöka ett Azure Functions-projekt lokalt. Mer information om hur du felsöker i Visual Studio Code finns i [Felsöka PowerShell Azure Functions lokalt](../articles/azure-functions/functions-debug-powershell-local.md). 

1. Tryck på F5 för att starta projektet för Function-appen. Utdata från Core Tools visas på panelen **Terminal**.

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Lägg till frågesträngen `?name=<yourname>` till denna URL och Använd sedan `Invoke-RestMethod` i en andra PowerShell kommando tolk för att köra begäran, enligt följande:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Du kan också köra GET-begäran från en webbläsare från följande URL:

    `http://localhost:7071/api/HttpExample?name=PowerShell`

    När du anropar HttpTrigger-slutpunkten utan att skicka en `name` parameter antingen som en frågeparameter eller i bröd texten, returnerar funktionen ett `BadRequest` fel. När du granskar koden i run.ps1 ser du att det här felet uppstår i design läge.

1. Information om begäran visas på panelen **Terminal** .

    ![Funktions körning i Terminal panel](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. När du är färdig trycker du på **CTRL + C** för att stoppa Core tools.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.