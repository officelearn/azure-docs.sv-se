---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838928"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure tal-resurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning

## <a name="source-code"></a>Källkod

Skapa en fil med namnet *QuickStart.py* och klistra in följande python-kod i den.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Kod förklaring

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Skapa och kör app

Nu är du redo att testa tal igenkänning med hjälp av tal tjänsten. 

Om du kör detta på macOS och det är den första python-app som du har skapat som använder en mikrofon, behöver du förmodligen ge Terminal åtkomst till mikrofonen. Öppna **Systeminställningar** och välj **säkerhet & sekretess**. Välj sedan **Sekretess** och leta upp **mikrofonen** i listan. Sist väljer du **Terminal** och spara. 

1. **Starta appen** – från kommando raden skriver du:
    ```bash
    python quickstart.py
    ```
2. **Starta igenkänning** – du uppmanas att tala om en fras på engelska. Ditt tal skickas till tal tjänsten, skrivs som text och återges i-konsolen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
