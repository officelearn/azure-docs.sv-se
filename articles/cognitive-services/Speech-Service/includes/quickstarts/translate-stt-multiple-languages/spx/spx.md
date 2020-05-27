---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: c73ee93d89a350763e8ced490187e1c47f8918de
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806519"
---
## <a name="run-the-speech-cli"></a>Kör tal-CLI

Nu är du redo att köra tal-CLI för att översätta tal till text på två olika språk.

Från kommando raden ändrar du till den katalog som innehåller den binära filen för tal-CLI och skriver:

```bash
spx translate --microphone --target de-DE --target es-MX
```

Tal-CLI: en översätter ett naturligt språk som talas på engelska i text som skrivs på tyska och (mexikanska) spanska.
Stoppa verktyget genom att trycka på RETUR.

> [!NOTE]
> Tal-CLI är som standard engelska. Du kan välja ett annat språk [från tal-till-text-tabellen](../../../../language-support.md).
> Exempel: Lägg till `--source ja-JP` för att identifiera japanskt tal.
