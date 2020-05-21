---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 161138ba09baacdd2a3d18d35c930c645ee78103
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715193"
---
## <a name="run-the-spx-tool"></a>Köra SPX-verktyget

Nu är du redo att köra SPX-verktyget för att syntetisera tal från text till en ny ljudfil.

Från kommando raden ändrar du till katalogen som innehåller den binära filen för SPX-verktyget och skriver:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

SPX-verktyget kommer att producera naturligt språk på engelska i `greetings.wav` ljud filen.
I Windows kan du spela upp ljud filen genom att ange `start greetings.wav` .
