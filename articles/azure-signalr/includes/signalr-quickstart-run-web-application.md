---
title: ta med fil
description: ta med fil
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325409"
---
## <a name="run-the-web-application"></a>Köra webbprogrammet

1. Det finns ett exempelwebbprogram med en sida som hanteras på GitHub. Öppna din webbläsare till [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/).

    > [!NOTE]
    > Källan för HTML-filen finns på [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. När du tillfrågas om funktionen app bas-URL, anger du `http://localhost:7071`.

1. Ange ett användarnamn när du tillfrågas.

1. Webbprogrammet anropar funktionen *GetSignalRInfo* i funktionsappen för att hämta anslutningsinformation för att ansluta till Azure SignalR Service. När anslutningen är klar visas inmatningsrutan för chattmeddelanden.

1. Skriv ett meddelande och tryck på Retur. Programmet skickar meddelandet till funktionen *SendMessage* i Azure Functions-appen, som använder SignalR-utdatabindning för att skicka meddelandet till alla anslutna klienter. Om allt fungerar korrekt bör meddelandet visas i programmet.

    ![Köra programmet](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Öppna en till instans av webbprogrammet i ett annat webbläsarfönster. Där ser du att alla meddelanden som skickas visas i alla instanser av programmet.

> [!IMPORTANT]
> Eftersom HTML-sidan hanteras med hjälp av HTTPS, men den lokala Azure Functions-körningen använder HTTP som standard, kan det finnas en princip som blockerar förfrågningar från webbsidan till dina funktioner för blandat innehåll webbläsaren (till exempel Firefox). Lös detta genom att använda en webbläsare som inte har den här begränsningen eller starta en lokal HTTP-server, till exempel [http-server](https://www.npmjs.com/package/http-server) i den */docs/demo/chat-v2* directory. Se till att ursprunget läggs till i `CORS` i *local.settings.json*.