---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806532"
---
## <a name="enable-microphone"></a>Aktivera mikrofon

Anslut och aktivera din PC-mikrofon och Stäng av alla appar som också kan använda mikrofonen. Vissa datorer har en inbyggd mikrofon, medan andra kräver konfiguration av en Bluetooth-enhet.

## <a name="run-the-speech-cli"></a>Kör tal-CLI

Nu är du redo att köra tal-CLI för att känna igen tal från mikrofonen.

1. **Starta appen** – från kommando raden, ändra till den katalog som innehåller den binära filen för tal-CLI och skriv:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > Tal-CLI är som standard engelska. Du kan välja ett annat språk [från tal-till-text-tabellen](../../../../language-support.md).
    > Du kan till exempel lägga till `--source de-DE` för att identifiera tyska tal.

2. **Starta igenkänning** – prata i mikrofonen. Avskrift av dina ord visas i text i real tid. Tal-CLI stoppas efter en period av tystnad eller när du trycker på CTRL-C.
