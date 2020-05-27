---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806188"
---
## <a name="run-the-speech-cli"></a>Kör tal-CLI

Nu är du redo att köra tal-CLI för att syntetisera tal från text till en ny ljudfil.

Från kommando raden ändrar du till den katalog som innehåller den binära filen för tal-CLI och skriver:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Tal-CLI kommer att producera naturligt språk på engelska i `greetings.wav` ljud filen.
I Windows kan du spela upp ljud filen genom att ange `start greetings.wav` .
