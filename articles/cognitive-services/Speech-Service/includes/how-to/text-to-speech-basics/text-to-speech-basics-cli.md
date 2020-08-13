---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173152"
---
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