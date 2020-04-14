---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 2ce40495617ec5a1024fd3b4b9faeec2d1c8ff06
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275115"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure Speech-resurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

## <a name="source-code"></a>Källkod

Skapa en C++-källfil med namnet *helloworld.cpp*och klistra in följande kod i den.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Kod förklaring

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Skapa och kör app

1. Välj **Bygg** > **bygglösning på** menyraden för att skapa programmet. Koden bör nu kompileras utan fel.

1. Välj **Felsökning** > **Starta felsökning** (eller tryck på <kbd>F5)</kbd>för att starta **helloworld-programmet.**

1. Säg en engelsk fras eller en mening. Programmet överför ditt tal till taltjänsten, som transkriberar till text och skickar tillbaka det till programmet för visning.

   ![Konsolutdata efter lyckad igenkänning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]