---
title: Använda Java för att skapa ett chattrum med Azure Functions and SignalR Service
description: En snabbstart för att använda Azure SignalR Service och Azure Functions för att skapa ett chattrum.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 890fc381afe0146e721e084e2dcd7eae9215d004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77083198"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Snabbstart: Använd Java för att skapa ett chattrum med Azure Functions and SignalR Service

Med Azure SignalR-tjänsten kan du enkelt lägga till funktioner i realtid i ditt program och Azure Functions är en serverlös plattform som gör att du kan köra din kod utan att hantera någon infrastruktur. I den här snabbstarten använder du Java för att skapa ett serverlöst chattprogram i realtid med SignalR-tjänsten och -funktioner.

## <a name="prerequisites"></a>Krav

- En kodredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions core tools](https://github.com/Azure/azure-functions-core-tools#installing). Används för att köra Azure Function-appar lokalt.

   > [!NOTE]
   > De signalR-bindningar som krävs i Java stöds endast i Azure Function Core Tools version 2.4.419 (värdversion 2.0.12332) eller senare.

   > [!NOTE]
   > För att installera tillägg kräver Azure Functions Core Tools [.NET Core SDK](https://www.microsoft.com/net/download) installerat. Dock krävs ingen kunskap om .NET för att skapa Azure Functions-appar med JavaScript.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 eller senare

> [!NOTE]
> Den här snabbstarten kan köras på macOS, Windows eller Linux.

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

1. Öppna *mappen src/chat/java* i den klonade databasen i kodredigeraren.

1. Byt namn på *local.settings.sample.json* till *local.settings.json*.

1. I **local.settings.json** klistrar du in anslutningssträngen i värdet för inställningen **AzureSignalRConnectionString**. Spara filen.

1. Huvudfilen som innehåller funktionerna finns i *src/chat/java/src/main/java/com/function/Functions.java*:

    - **negotiate** (förhandla) – använder indatabindningen *SignalRConnectionInfo* för att skapa och returnera giltig anslutningsinformation.
    - **sendMessage** - Tar emot ett chattmeddelande i begäran kroppen och använder *SignalR* utdata bindande för att sända meddelandet till alla anslutna klientprogram.

1. I terminalen, se till att du är i *src / chat / java* mapp. Skapa funktionsappen.

    ```bash
    mvn clean package
    ```

1. Kör funktionsappen lokalt.

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat och kört ett serverlöst program i realtid med Maven. Läs sedan om hur du skapar Java Azure-funktioner från grunden.

> [!div class="nextstepaction"]
> [Skapa din första funktion med Java och Maven](../azure-functions/functions-create-first-java-maven.md)