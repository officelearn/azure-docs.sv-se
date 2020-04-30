---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78191074"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Azure Functions Core Tools integreras med Visual Studio Code så att du kan köra och felsöka ett Azure Functions-projekt lokalt. Mer information om hur du felsöker i Visual Studio Code finns i [Felsöka PowerShell Azure Functions lokalt](../articles/azure-functions/functions-debug-powershell-local.md). 

1. Tryck på F5 för att starta projektet för Function-appen. Utdata från Core Tools visas på panelen **Terminal**.

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Lägg till frågesträngen `?name=<yourname>` till denna URL och Använd `Invoke-RestMethod` sedan i en andra PowerShell kommando tolk för att köra begäran, enligt följande:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Du kan också köra GET-begäran från en webbläsare från följande URL:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    När du anropar HttpTrigger-slutpunkten `name` utan att skicka en parameter antingen som en frågeparameter eller i bröd texten, returnerar `BadRequest` funktionen ett fel. När du granskar koden i Run. ps1 ser du att felet uppstår i design läge.

1. Information om begäran visas på panelen **Terminal** .

    ![Funktions körning i Terminal panel](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. När du är färdig trycker du på **CTRL + C** för att stoppa Core tools.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.