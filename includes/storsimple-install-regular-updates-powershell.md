---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 36a014fbff30c32a7149a83907c9586bc3b2f01a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165357"
---
<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Så här installerar regelbundna uppdateringar via Windows PowerShell för StorSimple
1. Öppna enhetens seriekonsol och välj alternativ 1, **logga in med fullständig åtkomst**. Ange lösenordet. Standardlösenordet är *Password1*. 
2. Skriv följande i kommandotolken:
   
     `Get-HcsUpdateAvailability`
   
    Du kommer att meddelas om uppdateringar är tillgängliga och oavsett om uppdateringarna är störande eller utan avbrott.
3. Skriv följande i kommandotolken:
   
     `Start-HcsUpdate`
   
    Uppdateringen startar.

> [!IMPORTANT]
> * Detta kommando gäller endast för regelbundna uppdateringar. Du kör det här kommandot på endast en domänkontrollant, men båda styrenheterna kommer att uppdateras. 
> * Du kanske ser en kontrollenhetsredundans under uppdateringen; redundansen påverkar dock inte systemets tillgänglighet eller åtgärden.
> 
> 

