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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "67187714"
---
## <a name="run-the-web-application"></a>Köra webbprogrammet

1. Det finns ett exempelwebbprogram med en sida som hanteras på GitHub. Öppna webbläsaren [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)till .

    > [!NOTE]
    > Källan till HTML-filen finns på [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. När du tillfrågas om funktionsappens bas-URL anger du `http://localhost:7071`.

1. Ange ett användarnamn när du tillfrågas.

1. Webbprogrammet anropar funktionen *GetSignalRInfo* i funktionsappen för att hämta anslutningsinformation för att ansluta till Azure SignalR Service. När anslutningen är klar visas inmatningsrutan för chattmeddelanden.

1. Skriv ett meddelande och tryck på Retur. Programmet skickar meddelandet till funktionen *SendMessage* i Azure Functions-appen, som använder SignalR-utdatabindning för att skicka meddelandet till alla anslutna klienter. Om allt fungerar korrekt bör meddelandet visas i programmet.

    ![Köra appen](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Öppna en till instans av webbprogrammet i ett annat webbläsarfönster. Där ser du att alla meddelanden som skickas visas i alla instanser av programmet.

> [!IMPORTANT]
> Eftersom HTML-sidan visas med HTTPS, men den lokala Azure Functions-körningen använder HTTP som standard, kan din webbläsare (till exempel Firefox) framtvinga en policy för blandat innehåll som blockerar begäranden från webbsidan till dina funktioner. För att lösa detta, använd en webbläsare som inte har denna begränsning eller starta en lokal [HTTP-server som http-server](https://www.npmjs.com/package/http-server) i katalogen */docs/demo/chat-v2.* Kontrollera att ursprunget `CORS` läggs till i inställningen i *local.settings.json*.