---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187378"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Så här installerar du uppdateringar av underhållsläge via Windows PowerShell för StorSimple
1. Om du inte redan har gjort det öppnar du seriekonsolen för enheten och väljer alternativ 1, **Logga in med full åtkomst**. 
2. Skriv lösenordet. Standardlösenordet är **Password1**.
3. Skriv följande i kommandotolken:
   
     `Get-HcsUpdateAvailability` 
4. Du får ett meddelande om uppdateringar är tillgängliga och om uppdateringarna är störande eller inte störande. Om du vill installera störande uppdateringar måste du placera enheten i underhållsläge. Se [steg 2: Ange underhållsläge](../articles/storsimple/storsimple-update-device.md#step2) för instruktioner.
5. När enheten är i underhållsläge skriver du i kommandotolken:`Start-HcsUpdate`
6. Du uppmanas att bekräfta. När du har bekräftat uppdateringarna installeras de på den styrenhet som du för närvarande använder. När uppdateringarna har installerats startas handkontrollen om. 
7. Övervaka status för uppdateringar. Ange:
   
    `Get-HcsUpdateStatus`
   
    Om `RunInProgress` är `True`, uppdateringen pågår fortfarande. Om `RunInProgress` `False`är , anger det att uppdateringen har slutförts.  
8. När uppdateringen är installerad på den aktuella styrenheten och den har startats om ansluter du till den andra styrenheten och utför steg 1 till och med 6.
9. När båda styrenheterna har uppdaterats avslutar du underhållsläget. Se [steg 4: Avsluta underhållsläge](../articles/storsimple/storsimple-update-device.md#step4) för instruktioner.

