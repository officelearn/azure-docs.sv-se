---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187373"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Så här installerar du snabbkorrigeringar för underhållsläge via Windows PowerShell för StorSimple
> [!IMPORTANT]
> I underhållsläge måste du använda snabbkorrigeringen först på en styrenhet och sedan på den andra styrenheten.
> 
> 

1. Placera enheten i underhållsläge. Se [steg 2: Ange underhållsläge](../articles/storsimple/storsimple-update-device.md#step2) för instruktioner om hur du går in i underhållsläge.
2. Om du vill använda snabbkorrigeringen skriver du:
   
     `Start-HcsHotfix` 
3. När du uppmanas till det nätverk som delas mappen angers anger du sökvägen till den delade mappen för nätverket som innehåller snabbkorrigeringsfilerna.
4. Du uppmanas att bekräfta. Skriv **Y** för att fortsätta med snabbkorrigeringsinstallationen.
5. När du har använt snabbkorrigeringen på den ena styrenheten loggar du in på den andra handkontrollen. Använd snabbkorrigeringen som du gjorde för den tidigare handkontrollen.
6. Avsluta underhållsläge när snabbkorrigeringarna har tillämpats. Se [steg 4: Avsluta underhållsläge](../articles/storsimple/storsimple-update-device.md#step4) för instruktioner.

