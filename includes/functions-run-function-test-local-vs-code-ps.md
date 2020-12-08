---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: a5c113849296275432acf1f5603377a1909a2c04
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842277"
---
## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Azure Functions Core Tools integreras med Visual Studio Code så att du kan köra och felsöka ett Azure Functions-projekt lokalt. Mer information om hur du felsöker i Visual Studio Code finns i [Felsöka PowerShell Azure Functions lokalt](../articles/azure-functions/functions-debug-powershell-local.md). 
1. Du anropar funktionen genom att trycka på <kbd>F5</kbd> för att starta projektet för Function-appen. Utdata från Core Tools visas på panelen **Terminal**. Om du har problem med att köra i Windows måste du kontrol lera att standard terminalen för Visual Studio Code inte är inställd på **Wsl bash**.

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
