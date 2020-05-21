---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 57383668ef025b46d0bae1f98c5ed5cd75417c63
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715041"
---
## <a name="run-the-spx-tool"></a>Köra SPX-verktyget

Nu är du redo att köra SPX-verktyget för att översätta tal till text på ett annat språk.

Från kommando raden ändrar du till den katalog som innehåller den binära fil som innehåller SPX-verktyget och skriver:

```bash
spx translate --microphone --target de-DE
```

SPX-verktyget översätter naturligt språk som talas på engelska i text som skrivs ut på tyska.
Stoppa verktyget genom att trycka på RETUR.

> [!NOTE]
> SPX-verktyget får engelska som standard. Du kan välja ett annat språk [från tal-till-text-tabellen](../../../../language-support.md).
> Exempel: Lägg till `--source ja-JP` för att identifiera japanskt tal.
