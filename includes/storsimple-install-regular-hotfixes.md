---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61410017"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Du installerar regelbundna snabbkorrigeringar via Windows PowerShell för StorSimple
1. Ansluta till enhetens seriekonsol. Mer information finns i [steg 1: Ansluta till seriekonsolen](../articles/storsimple/storsimple-update-device.md#step1).
2. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. Ange lösenordet. Standardlösenordet är **Password1**.
3. Skriv följande i kommandotolken:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Detta kommando gäller endast för vanliga snabbkorrigeringar. Du kör det här kommandot på endast en domänkontrollant, men båda styrenheterna kommer att uppdateras.
    > Du kanske ser en kontrollenhetsredundans under uppdateringen; redundansen påverkar dock inte systemets tillgänglighet eller åtgärden.

4. När du uppmanas, anger du sökvägen till den delade nätverksmappen som innehåller filerna för snabbkorrigeringen.
5. Du uppmanas att bekräfta. Typ **Y** att fortsätta med installationen av snabbkorrigeringen.

