---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187376"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Så här installerar du vanliga uppdateringar via Windows PowerShell för StorSimple
1. Öppna enhetens seriekonsol och välj alternativ 1, **Logga in med full åtkomst**. Skriv lösenordet. Standardlösenordet är *Password1*. 
2. Skriv följande i kommandotolken:
   
     `Get-HcsUpdateAvailability`
   
    Du får ett meddelande om uppdateringar är tillgängliga och om uppdateringarna är störande eller inte störande.
3. Skriv följande i kommandotolken:
   
     `Start-HcsUpdate`
   
    Uppdateringsprocessen startar.

> [!IMPORTANT]
> * Det här kommandot gäller endast vanliga uppdateringar. Du kör det här kommandot på endast en handkontroll, men båda styrenheterna uppdateras. 
> * Du kan märka en rederingsunderhandla under uppdateringsprocessen. Redundansen påverkar dock inte systemets tillgänglighet eller åtgärd.
> 
> 

