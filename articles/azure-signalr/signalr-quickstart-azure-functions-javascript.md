---
title: Azure SignalR Service utan Server Snabbstart – JavaScript
description: En snabbstart för att använda Azure SignalR Service och Azure Functions för att skapa ett chattrum.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 0ca73ed314b254f9f73833bb2c4311f03f62508f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264207"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-javascript"></a>Snabbstart: Skapa ett chattrum med Azure Functions och SignalR Service med hjälp av JavaScript

Med Azure SignalR Service kan du enkelt lägga till realtidsfunktioner i ditt program. Azure Functions är en serverlös plattform som gör att du kan köra din kod utan att behöva hantera någon infrastruktur. I den här snabbstarten lär du dig hur du använder SignalR Service och Functions för att skapa ett serverlöst realtidschattprogram.

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten kan köras på macOS, Windows eller Linux.

Se till att du har en kodredigerare såsom [Visual Studio Code](https://code.visualstudio.com/) installerad.

Installera [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) för att köra Azure Functions-appar lokalt.

För att kunna installera tillägg kräver Azure Functions Core Tools för närvarande att [.NET Core SDK](https://www.microsoft.com/net/download) är installerad. Dock krävs ingen kunskap om .NET för att skapa Azure Functions-appar med JavaScript.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com/> med ditt Azure-konto.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurera och köra Azure Functions-appen

1. I den webbläsare där Azure-portalen är öppnad bekräftar du att den SignalR Service-instans som du distribuerade tidigare skapades korrekt genom att söka efter dess namn i sökrutan längst upp i portalen. Välj instansen för att öppna den.

    ![Söka efter SignalR Service-instansen](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

    ![Skapa SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Öppna i ditt kodredigeringsprogram den *src/chatt/javascript* mapp i den klonade lagringsplatsen.

1. Byt namn på *local.settings.sample.json* till *local.settings.json*.

1. I **local.settings.json** klistrar du in anslutningssträngen i värdet för inställningen **AzureSignalRConnectionString**. Spara filen.

1. JavaScript-funktioner är ordnade i mappar. Det finns två filer i varje mapp: *function.json* definierar de bindningar som används i funktionen, och *index.js* är brödtexten till funktionen. Det finns två HTTP-utlösta funktioner i den här funktionsappen:

    - **negotiate** (förhandla) – använder indatabindningen *SignalRConnectionInfo* för att skapa och returnera giltig anslutningsinformation.
    - **messages** (meddelanden) – tar emot ett chattmeddelande i begärandetexten och använder utdatabindningen *SignalR* för att skicka meddelandet till alla anslutna klientprogram.

1. I terminalen kontrollerar du att du är i den *src/chatt/javascript* mapp. Använd Azure Functions Core Tools för att installera tillägg som krävs för att köra appen.

    ```bash
    func extensions install
    ```

1. Kör funktionsappen.

    ```bash
    func start
    ```

    ![Skapa SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat och kört ett serverlöst program i realtid i VS Code. Som nästa steg ska du lära dig mer om hur du distribuerar Azure Functions via VS Code.

> [!div class="nextstepaction"]
> [Distribuera Azure Functions med VS Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)