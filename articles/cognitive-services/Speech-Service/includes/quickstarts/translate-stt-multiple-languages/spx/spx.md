---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 94d50a15b0d5d7bed2bd43516952036044d7f0c0
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715124"
---
## <a name="run-the-spx-tool"></a>Köra SPX-verktyget

Nu är du redo att köra SPX-verktyget för att översätta tal till text på två olika språk.

Från kommando raden ändrar du till den katalog som innehåller den binära fil som innehåller SPX-verktyget och skriver:

```bash
spx translate --microphone --target de-DE --target es-MX
```

SPX-verktyget översätter naturligt språk som talas på engelska i text som skrivs på tyska och (mexikanska) spanska.
Stoppa verktyget genom att trycka på RETUR.

> [!NOTE]
> SPX-verktyget får engelska som standard. Du kan välja ett annat språk [från tal-till-text-tabellen](../../../../language-support.md).
> Exempel: Lägg till `--source ja-JP` för att identifiera japanskt tal.
