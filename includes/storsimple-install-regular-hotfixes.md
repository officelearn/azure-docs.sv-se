---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187375"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Så här installerar du vanliga snabbkorrigeringar via Windows PowerShell för StorSimple
1. Anslut till enhetens seriekonsol. Mer information finns i [steg 1: Anslut till seriekonsolen](../articles/storsimple/storsimple-update-device.md#step1).
2. I menyn för seriell konsol väljer du alternativ 1, **Logga in med full åtkomst**. Skriv lösenordet. Standardlösenordet är **Password1**.
3. Skriv följande i kommandotolken:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Det här kommandot gäller endast vanliga snabbkorrigeringar. Du kör det här kommandot på endast en handkontroll, men båda styrenheterna uppdateras.
    > Du kan märka en rederingsunderhandla under uppdateringsprocessen. Redundansen påverkar dock inte systemets tillgänglighet eller åtgärd.

4. När du uppmanas till det nätverk som delas mappen angers anger du sökvägen till den delade mappen för nätverket som innehåller snabbkorrigeringsfilerna.
5. Du uppmanas att bekräfta. Skriv **Y** för att fortsätta med snabbkorrigeringsinstallationen.

