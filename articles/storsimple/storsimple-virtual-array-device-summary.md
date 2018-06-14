---
title: StorSimple virtuell matris enheten sammanfattning bladet | Microsoft Docs
description: Beskriver bladet enheten sammanfattning för StorSimple Enhetshanteraren och förklarar hur du använder den för att övervaka hälsotillståndet för din virtuella StorSimple-matris.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 35413d597c3b6b1c7600241a78572b63f982d175
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875685"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Använd bladet enheten sammanfattning för StorSimple Enhetshanteraren anslutna till virtuella StorSimple-matris

## <a name="overview"></a>Översikt

Bladet Enhetshanteraren för StorSimple-enheten innehåller en översikt över av en virtuell StorSimple-matris som har registrerats med en viss StorSimple Enhetshanteraren, syntaxmarkering dessa problem med enheter som behöver åtgärdas av en systemadministratör. Den här kursen introducerar bladet enheten sammanfattning, förklarar innehåll och funktionen och beskrivs vilka aktiviteter som du kan utföra från det här bladet.

Sammanfattning bladet enheten visar följande information:

![Instrumentpanel för enhet](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Hantering

I bladet StorSimple-enhet visas alternativ för att hantera din StorSimple-enhet. Du kan se kommandona management överst på bladet och till vänster. Använd dessa alternativ för att lägga till resurser eller volymer, eller uppdatera eller växla över dina virtuella matris.

Området essentials samlar in några viktiga egenskaper, till exempel status, modell, programvaruversionen samt en länk till den **Webbgränssnittet** i matrisen. Om du är i ett internt nätverk kan du direkt kan starta den [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) att administrera virtuella matrisen.

![Enheten essentials](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Översikt över StorSimple-enhet

* Den **aviseringar** panelen innehåller en ögonblicksbild av alla aktiva aviseringar för virtuella matrisen, grupperat efter allvarlighetsgrad. Klicka på rutan öppnas den **aviseringar** bladet och klicka sedan på en enskild varning för att visa ytterligare information om den här aviseringen, inklusive de rekommenderade åtgärder. Du kan också ta bort aviseringen om problemet har lösts.

* Den **kapacitet** panelen visar den primära lagring som är allokerade och återstående i den virtuella enheten i förhållande till den totala lagringsstorleken för samma. **Etablerad** avser mängden lagringsutrymme som är förberedd och tilldelat för användning, **återstående** refererar till den återstående kapacitet som kan etableras mellan den här enheten. Den **återstående nivåer** kapacitet är den tillgängliga kapaciteten som kan etableras inklusive molnet, medan den **återstående lokala** är kvar på diskar som är kopplade till den här virtuella matris-kapacitet.

* I den **användning** diagram, du kan visa den primära lagringsutrymme som används över dina virtuella matris, samt molnlagring som används under de senaste 7 dagarna standard tidsperiod. Använd den **redigera** alternativ i det övre högra hörnet i diagrammet att välja en annan tidsskala.

* Den **resurser** eller **volymer** panelen innehåller en sammanfattning av antalet resurser eller volymer i enheten grupperade efter status. Klicka på rutan öppnas den **resurser** eller **volymer** listan bladet och klicka på en enskild resurs eller volymen för att visa eller ändra dess egenskaper. Mer information finns i så här [hanterar resurser](storsimple-virtual-array-manage-shares.md) eller [hantera volymer](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Nästa steg
Lär dig att:
- [Hantera resurser på en virtuell StorSimple-matris](storsimple-virtual-array-manage-shares.md)
    
- [Hantera volymer på en virtuell StorSimple-matris](storsimple-virtual-array-manage-volumes.md)

