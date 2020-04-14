---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: ce694641ef51ae2a1afd33a6220bfb467799896a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275569"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure Speech-resurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

## <a name="source-code"></a>Källkod

Skapa en fil med namnet *quickstart.py* och klistra in följande Python-kod i den.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Kod förklaring

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Skapa och kör app

Nu är du redo att testa appen och verifiera taligenkänningsfunktionen med hjälp av taltjänsten.

1. **Starta appen** - Skriv:
    ```bash
    python quickstart.py
    ```
2. **Starta erkännande** - Det kommer att uppmana dig att tala en fras på engelska. Ditt tal skickas till taltjänsten, transkriberas som text och återges i konsolen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

