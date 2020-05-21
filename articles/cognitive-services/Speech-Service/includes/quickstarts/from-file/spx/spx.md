---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9ff21105e61be59b0000d86455ee19ba6f0d1aeb
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714713"
---
## <a name="find-a-file-that-contains-speech"></a>Hitta en fil som innehåller tal

SPX-verktyget kan känna igen tal i många fil format och naturliga språk. I den här snabb starten kan du använda en WAV-fil (16kHz eller 8kHz, 16-bitars och mono PCM) som innehåller engelskt tal.

1. Ladda ned <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. wav <span class="docon docon-download x-hidden-focus"></span> </a>.
2. Kopiera `whatstheweatherlike.wav` filen till samma katalog som den binära filen för SPX-verktyget.

## <a name="run-the-spx-tool"></a>Köra SPX-verktyget

Nu är du redo att köra SPX-verktyget för att identifiera tal som finns i ljud filen.

Från kommando raden ändrar du till den katalog som innehåller den binära fil som innehåller SPX-verktyget och skriver:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> SPX-verktyget får engelska som standard. Du kan välja ett annat språk [från tal-till-text-tabellen](../../../../language-support.md).
> Du kan till exempel lägga till `--source de-DE` för att identifiera tyska tal.

SPX-verktyget visar en text avskrift av talfunktionen på skärmen. Sedan stängs SPX-verktyget.
