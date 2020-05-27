---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 4d548c1b7614503919de2725acb02d7f808ceb93
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806331"
---
## <a name="run-the-speech-cli"></a>Kör tal-CLI

Nu är du redo att köra tal-CLI för att översätta tal till text på ett annat språk.

Från kommando raden ändrar du till den katalog som innehåller den binära filen för tal-CLI och skriver:

```bash
spx translate --microphone --target de-DE
```

Tal-CLI översätter naturligt språk som talas på engelska i text som skrivs ut på tyska.
Stoppa verktyget genom att trycka på RETUR.

> [!NOTE]
> Tal-CLI är som standard engelska. Du kan välja ett annat språk [från tal-till-text-tabellen](../../../../language-support.md).
> Exempel: Lägg till `--source ja-JP` för att identifiera japanskt tal.
