---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 5e4921be3116754f146ed0845513010f2642c97b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164375"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Installera snabbkorrigeringar i underhållsläge via Windows PowerShell för StorSimple
> [!IMPORTANT]
> I underhållsläge måste du aktivera snabbkorrigeringen först på en domänkontrollant och sedan på den andra styrenheten.
> 
> 

1. Placera enheten i underhållsläge. Se [steg 2: Ange underhållsläge](../articles/storsimple/storsimple-update-device.md#step2) för anvisningar om hur du anger underhållsläge.
2. Om du vill aktivera snabbkorrigeringen, skriver du:
   
     `Start-HcsHotfix` 
3. När du uppmanas, anger du sökvägen till den delade nätverksmappen som innehåller filerna för snabbkorrigeringen.
4. Du uppmanas att bekräfta. Typ **Y** att fortsätta med installationen av snabbkorrigeringen.
5. När du har tillämpat snabbkorrigeringen på en domänkontrollant, kan du logga in på den andra styrenheten. Installera snabbkorrigeringen som du gjorde för tidigare styrenheten.
6. Avsluta underhållsläget när snabbkorrigeringarna tillämpas. Se [steg 4: avsluta underhållsläget](../articles/storsimple/storsimple-update-device.md#step4) anvisningar.

