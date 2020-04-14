---
title: 'Snabbstart: Syntetisera tal, C# (Windows) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden om du vill skapa ett text-till-tal-konsolprogram med .NET-ramverket för Windows och Speech SDK. När du är klar kan du syntetisera tal från text och höra talet på högtalaren i realtid.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89f491aae91787ec6b67b9aa2daee262f3bab62e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275011"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna **Program.cs** och ersätt den automatiskt genererade koden med det här exemplet:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Leta reda `YourSubscriptionKey`på strängen och ersätt den med prenumerationsnyckeln för Taltjänsten.

1. Leta reda `YourServiceRegion`på strängen och ersätt den med den [region som](~/articles/cognitive-services/Speech-Service/regions.md) är associerad med din prenumeration. Om du till exempel använder den kostnadsfria utvärderingsprenumerationen är `westus`regionen .

1. Välj Spara**alla** **på** > menyraden .

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **Bygg** > **bygglösning på** menyraden för att skapa programmet. Koden bör nu kompileras utan fel.

1. Välj **Felsökning** > **Avsökstarts felsökning** (eller välj **F5**) för att starta **helloworld-programmet.**

1. Ange en engelsk fras eller mening. Programmet överför din text till taltjänsten, som skickar syntetiserat tal till programmet för att spela upp på högtalaren.

   ![Användargränssnitt för talsyntes](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röstexempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
