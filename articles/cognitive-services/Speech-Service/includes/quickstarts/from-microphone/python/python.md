---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 9002df0a596eec1dcf7aca425a45d806fb94805b
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658956"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure Speech-resurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md)
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

[!INCLUDE [footer](../footer.md)]
