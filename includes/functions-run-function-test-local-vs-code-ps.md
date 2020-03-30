---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191074"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Azure Functions Core Tools integreras med Visual Studio-kod så att du kan köra och felsöka ett Azure Functions-projekt lokalt. Mer information om hur du felsöker i Visual Studio-kod finns i [Felsökning av PowerShell Azure-funktioner lokalt](../articles/azure-functions/functions-debug-powershell-local.md). 

1. Tryck på F5 för att starta funktionsappprojektet. Utdata från Core Tools visas på panelen **Terminal**.

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Lägg till frågesträngen `?name=<yourname>` i den `Invoke-RestMethod` här URL:en och använd sedan i en andra PowerShell-kommandotolk för att köra begäran enligt följande:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Du kan också köra GET-begäran från en webbläsare från följande WEBBADRESS:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    När du anropar httptrigger-slutpunkten utan att skicka en `name` parameter, antingen `BadRequest` som frågeparameter eller i brödtexten, returnerar funktionen ett fel. När du granskar koden i run.ps1 ser du att det här felet uppstår avsiktligt.

1. Information om begäran visas **Terminal** på terminalpanelen.

    ![Funktionskörning på terminalpanelen](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. När du är klar trycker du på **Ctrl + C** för att stoppa Core Tools.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.