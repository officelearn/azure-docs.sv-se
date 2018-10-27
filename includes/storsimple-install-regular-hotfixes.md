---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 77fc95c53ba89c3905ebd1aec785e22f42339369
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165577"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Du installerar regelbundna snabbkorrigeringar via Windows PowerShell för StorSimple
1. Ansluta till enhetens seriekonsol. Mer information finns i [steg 1: ansluta till seriekonsolen](../articles/storsimple/storsimple-update-device.md#step1).
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

