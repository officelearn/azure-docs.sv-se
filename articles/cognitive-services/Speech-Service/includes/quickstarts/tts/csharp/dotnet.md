---
title: 'Snabb start: syntetisera tal, C# (Windows) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett text till tal-konsol program med .NET Framework för Windows och talet SDK. När du är färdig kan du syntetisera tal från text och höra talet på din talare i real tid.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: af101862e824d6527c08c232812913565536bb4a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503152"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna **program.cs** och ersätt den automatiskt genererade koden med det här exemplet:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Leta upp strängen `YourSubscriptionKey`och ersätt den med din prenumerations nyckel för tal tjänster.

1. Hitta sträng `YourServiceRegion`och ersätt den med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnads fria utvärderings prenumerationen är regionen `westus`.

1. Välj **arkiv** > **Spara alla**på Meny raden.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet **HelloWorld** genom att välja **Felsök** > **Starta fel sökning** (eller Välj **F5**).

1. Ange en engelska fras eller en mening. Programmet skickar texten till tal tjänsterna, som skickar syntetiskt tal till programmet som spelas upp på din högtalare.

   ![Användar gränssnitt för tal syntes](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röst exempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
