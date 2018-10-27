---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 01612b32e6c1b363df8a5c70405d0c709210328e
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165200"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installera uppdateringar av underhållsläge via Windows PowerShell för StorSimple
1. Om du inte redan gjort det, få åtkomst till enhetens seriekonsol och välj alternativ 1, **logga in med fullständig åtkomst**. 
2. Ange lösenordet. Standardlösenordet är **Password1**.
3. Skriv följande i kommandotolken:
   
     `Get-HcsUpdateAvailability` 
4. Du kommer att meddelas om uppdateringar är tillgängliga och oavsett om uppdateringarna är störande eller utan avbrott. Om du vill tillämpa störande uppdateringar, måste du placera enheten i underhållsläge. Se [steg 2: Ange underhållsläge](../articles/storsimple/storsimple-update-device.md#step2) anvisningar.
5. När enheten är i underhållsläge i Kommandotolken skriver du: `Start-HcsUpdate`
6. Du uppmanas att bekräfta. När du har bekräftat uppdateringarna kommer att installeras på den styrenhet som du använder för närvarande. När uppdateringarna har installerats startas styrenheten. 
7. Övervaka statusen för uppdateringar. Ange:
   
    `Get-HcsUpdateStatus`
   
    Om den `RunInProgress` är `True`, uppdateringen pågår fortfarande. Om `RunInProgress` är `False`, betyder det att uppdateringen har slutförts.  
8. När uppdateringen har installerats på den aktuella kontrollanten och den har startats om, ansluta till den andra styrenheten och utföra steg 1 till 6.
9. När båda styrenheterna har uppdateras avsluta underhållsläget. Se [steg 4: avsluta underhållsläget](../articles/storsimple/storsimple-update-device.md#step4) anvisningar.

