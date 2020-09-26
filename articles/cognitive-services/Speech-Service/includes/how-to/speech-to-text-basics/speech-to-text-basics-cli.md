---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 905eacc3751b3d5d6c66a2fdb0e1391a747ab895
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327124"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att känna igen mänskligt tal (kallas ofta tal till text). I den här snabb starten får du lära dig hur du använder tal-CLI i dina appar och produkter för att utföra högkvalitativt tal-till-text-konvertering.

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Tal till text från mikrofonen

Anslut och aktivera din PC-mikrofon och Stäng av alla appar som också kan använda mikrofonen. Vissa datorer har en inbyggd mikrofon, medan andra kräver konfiguration av en Bluetooth-enhet.

Nu är du redo att köra tal-CLI för att känna igen tal från mikrofonen. Från kommando raden ändrar du till den katalog som innehåller den binära filen för tal-CLI och kör följande kommando.

```bash
spx recognize --microphone
```

> [!NOTE]
> Tal-CLI är som standard engelska. Du kan välja ett annat språk [från tal-till-text-tabellen](../../../../language-support.md).
> Du kan till exempel lägga till `--source de-DE` för att identifiera tyska tal.

Tala i mikrofonen och se avskrift av dina ord i text i real tid. Tal-CLI stoppas efter en period av tystnad eller när du trycker på CTRL-C.

## <a name="speech-to-text-from-audio-file"></a>Tal till text från ljud filen

Tal-CLI: n kan känna igen tal i många fil format och naturliga språk. I det här exemplet kan du använda valfri WAV-fil (16kHz eller 8kHz, 16-bitars och mono-PCM) som innehåller engelskt tal. Eller om du vill ha ett snabb exempel hämtar du filen <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. <span class="docon docon-download x-hidden-focus"></span> WAV</a> och kopierar den till samma katalog som den binära filen för tal-cli.

Nu är du redo att köra tal-CLI för att identifiera tal som finns i ljud filen genom att köra följande kommando.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Tal-CLI är som standard engelska. Du kan välja ett annat språk [från tal-till-text-tabellen](../../../../language-support.md).
> Du kan till exempel lägga till `--source de-DE` för att identifiera tyska tal.

Tal-CLI visar en text avskrift av talet på skärmen.