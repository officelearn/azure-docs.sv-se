---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806541"
---
## <a name="find-a-file-that-contains-speech"></a>Hitta en fil som innehåller tal

Tal-CLI: n kan känna igen tal i många fil format och naturliga språk. I den här snabb starten kan du använda en WAV-fil (16kHz eller 8kHz, 16-bitars och mono PCM) som innehåller engelskt tal.

1. Ladda ned <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. wav <span class="docon docon-download x-hidden-focus"></span> </a>.
2. Kopiera `whatstheweatherlike.wav` filen till samma katalog som den binära filen för tal-cli.

## <a name="run-the-speech-cli"></a>Kör tal-CLI

Nu är du redo att köra tal-CLI för att identifiera tal som finns i ljud filen.

Från kommando raden ändrar du till den katalog som innehåller den binära filen för tal-CLI och skriver:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Tal-CLI är som standard engelska. Du kan välja ett annat språk [från tal-till-text-tabellen](../../../../language-support.md).
> Du kan till exempel lägga till `--source de-DE` för att identifiera tyska tal.

Tal-CLI visar en text avskrift av talet på skärmen. Sedan stängs tal CLI.
