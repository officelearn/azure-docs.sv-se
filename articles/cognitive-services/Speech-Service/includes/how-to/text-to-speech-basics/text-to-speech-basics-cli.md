---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: b68025c1b33a94f03e2d84693a7d6407a18abd88
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332462"
---
I den här snabb starten lär du dig vanliga design mönster för att skapa text till tal-Sammanfattning med hjälp av tal-SDK. Du börjar med att utföra grundläggande konfiguration och syntes och går vidare till mer avancerade exempel för anpassad program utveckling, inklusive:

* Få svar som minnes strömmar
* Anpassa samplings frekvens och bit hastighet för utdata
* Skicka syntes begär Anden med SSML (tal syntes Markup Language)
* Använda neurala-röster

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Syntetisera tal till en högtalare

Nu är du redo att köra tal-CLI för att syntetisera tal från text. Från kommando raden ändrar du till den katalog som innehåller den binära filen för tal-CLI. Kör sedan följande kommando.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

Tal-CLI kommer att producera naturligt språk på engelska via datorns högtalare.

## <a name="synthesize-speech-to-a-file"></a>Syntetisera tal till en fil

Kör följande kommando för att ändra utdata från din talare till en `.wav` fil.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Tal-CLI kommer att producera naturligt språk på engelska i `greetings.wav` ljud filen.
I Windows kan du spela upp ljud filen genom att ange `start greetings.wav` .