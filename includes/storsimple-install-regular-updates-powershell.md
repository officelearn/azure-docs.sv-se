---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889233"
---
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

