---
title: Snabbstart för serverlös Azure SignalR Service – C#
description: En snabbstart för att använda Azure SignalR Service och Azure Functions för att skapa ett chattrum.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/23/2018
ms.author: zhshang
ms.openlocfilehash: db54282563acc6afac4dada7a45f6dff0716879a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255065"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>Snabbstart: Skapa ett chattrum med Azure Functions och SignalR Service med hjälp av C\#

Med Azure SignalR Service kan du enkelt lägga till realtidsfunktioner i ditt program. Azure Functions är en serverlös plattform som gör att du kan köra din kod utan att behöva hantera någon infrastruktur. I den här snabbstarten lär du dig hur du använder SignalR Service och Functions för att skapa ett serverlöst realtidschattprogram.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com/> med ditt Azure-konto.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurera och köra Azure Functions-appen

1. Starta Visual Studio och öppna lösningen i mappen *chat\src\csharp* på den klonade lagringsplatsen.

1. I den webbläsare där Azure-portalen är öppnad bekräftar du att den SignalR Service-instans som du distribuerade tidigare skapades korrekt genom att söka efter dess namn i sökrutan längst upp i portalen. Välj instansen för att öppna den.

    ![Söka efter SignalR Service-instansen](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Välj **Nycklar** för att visa anslutningssträngarna för SignalR Service-instansen.

1. Markera och kopiera den primära anslutningssträngen.

1. Tillbaka i Visual Studio, i Solution Explorer, byter du namn på *local.settings.sample.json* till *local.settings.json*.

1. I **local.settings.json** klistrar du in anslutningssträngen i värdet för inställningen **AzureSignalRConnectionString**. Spara filen.

1. Öppna **Functions.cs**. Det finns två HTTP-utlösta funktioner i den här funktionsappen:

    - **GetSignalRInfo** – använder indatabindningen *SignalRConnectionInfo* för att skapa och returnera giltig anslutningsinformation.
    - **SendMessage** – tar emot ett chattmeddelande i begärandetexten och använder utdatabindningen *SignalR* för att skicka meddelandet till alla anslutna klientprogram.

1. I menyn **Debug** (Felsökning) väljer du **Start debugging** (Starta felsökning) för att köra programmet.

    ![Felsök programmet](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade och körde du ett serverlöst realtidsprogram i VS Code. Som nästa steg ska du lära dig mer om hur du distribuerar Azure Functions via VS Code.

> [!div class="nextstepaction"]
> [Distribuera Azure Functions med VS Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)