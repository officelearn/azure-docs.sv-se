---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9f1da13efc9f75d14fdf2d158e8b7547d4a9fa94
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715329"
---
## <a name="enable-microphone"></a>Aktivera mikrofon

Anslut och aktivera din PC-mikrofon och Stäng av alla appar som också kan använda mikrofonen. Vissa datorer har en inbyggd mikrofon, medan andra kräver konfiguration av en Bluetooth-enhet.

## <a name="run-the-spx-tool"></a>Köra SPX-verktyget

Nu är du redo att köra SPX-verktyget för att känna igen tal från mikrofonen.

1. **Starta appen** – från kommando raden, ändra till den katalog som innehåller den binära fil som innehåller SPX-verktyget och skriv:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > SPX-verktyget får engelska som standard. Du kan välja ett annat språk [från tal-till-text-tabellen](../../../../language-support.md).
    > Du kan till exempel lägga till `--source de-DE` för att identifiera tyska tal.

2. **Starta igenkänning** – prata i mikrofonen. Avskrift av dina ord visas i text i real tid. SPX-verktyget stoppas efter en tystnads period eller när du trycker på CTRL-C.
