---
title: Azure SignalR Service serverlös snabbstart - Python
description: En snabbstart för att använda Azure SignalR Service och Azure Functions för att skapa ett chattrum.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.openlocfilehash: 0cf8705cf2567a60129681c2db41b0868f8fe182
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75392157"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Snabbstart: Skapa ett chattrum med Azure Functions och SignalR-tjänst med Python

Med Azure SignalR Service kan du enkelt lägga till realtidsfunktioner i ditt program. Azure Functions är en serverlös plattform som gör att du kan köra din kod utan att behöva hantera någon infrastruktur. I den här snabbstarten lär du dig hur du använder SignalR Service och Functions för att skapa ett serverlöst realtidschattprogram.

## <a name="prerequisites"></a>Krav

Den här snabbstarten kan köras på macOS, Windows eller Linux.

Se till att du har en kodredigerare såsom [Visual Studio Code](https://code.visualstudio.com/) installerad.

Installera [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (version 2.7.1505 eller senare) för att köra Python Azure-funktionsappar lokalt.

Azure Functions kräver [Python 3.6 eller 3.7](https://www.python.org/downloads/).

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

1. Öppna *mappen src/chat/python* i den klonade databasen i kodredigeraren.

1. Om du vill utveckla och testa Python-funktioner lokalt måste du arbeta i en Python 3.6- eller 3.7-miljö. Kör följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv`.

    **Linux eller macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows:**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Byt namn på *local.settings.sample.json* till *local.settings.json*.

1. I **local.settings.json** klistrar du in anslutningssträngen i värdet för inställningen **AzureSignalRConnectionString**. Spara filen.

1. Python-funktioner är ordnade i mappar. I varje mapp finns två filer: *function.json* definierar bindningar som används i funktionen, och * \_ \_\_\_init .py* är kraften i funktionen. Det finns två HTTP-utlösta funktioner i den här funktionsappen:

    - **negotiate** (förhandla) – använder indatabindningen *SignalRConnectionInfo* för att skapa och returnera giltig anslutningsinformation.
    - **messages** (meddelanden) – tar emot ett chattmeddelande i begärandetexten och använder utdatabindningen *SignalR* för att skicka meddelandet till alla anslutna klientprogram.

1. I terminalen med den virtuella miljön aktiverad, se till att du är i *mappen src/chat/python.* Installera de nödvändiga Python-paketen med PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Kör funktionsappen.

    ```bash
    func start
    ```

    ![Kör funktionsapp](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat och kört ett serverlöst program i realtid i VS-kod. Som nästa steg ska du lära dig mer om hur du distribuerar Azure Functions via VS Code.

> [!div class="nextstepaction"]
> [Distribuera Azure Functions med VS Code](/azure/javascript/tutorial-vscode-serverless-node-01)
