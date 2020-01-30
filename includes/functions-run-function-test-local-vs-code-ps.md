---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: c54145cf48912d3911a39e681d85cb6907be8e52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842324"
---
## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Azure Functions Core Tools integreras med Visual Studio Code så att du kan köra och felsöka ett Azure Functions-projekt lokalt.  

1. Om du vill felsöka din funktion infogar du ett anrop till [`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) cmdlet i funktions koden innan du vill koppla fel sökaren. Tryck sedan på F5 för att starta programmet för Function-appen och koppla fel sökaren. Utdata från Core Tools visas på panelen **Terminal**.

1. På panelen **Terminal** kopierar du URL-slutpunkten för den HTTP-utlösta funktionen.

    ![Lokala Azure-utdata](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Lägg till frågesträngen `?name=<yourname>` till denna URL och Använd sedan `Invoke-RestMethod` i en andra PowerShell kommando tolk för att köra begäran, enligt följande:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Du kan också köra GET-begäran från en webbläsare.

    När du anropar HttpTrigger-slutpunkten utan att skicka en `name` parameter antingen som en frågeparameter eller i bröd texten returnerar funktionen ett [HttpStatusCode]:: BadRequest-fel. När du granskar koden i Run. ps1 ser du att felet uppstår i design läge.

1. Tryck på SKIFT+F5 för att stoppa felsökningen.

När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

> [!NOTE]
> Kom ihåg att ta bort alla anrop till `Wait-Debugger` innan du publicerar dina funktioner till Azure. 