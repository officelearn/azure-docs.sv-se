---
title: Sammanfattningsbladet för StorSimple Virtual Array-enheter | Microsoft Docs
description: Beskriver sammanfattningsbladet för device för StorSimple Device Manager och förklarar hur du använder den för att övervaka hälsotillståndet för StorSimple Virtual Array.
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
ms.openlocfilehash: 9edc0b552f5c2f38e646bc4b44dd8df5c16b0457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408514"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Använd bladet sammanfattning för StorSimple Device Manager är ansluten till StorSimple Virtual Array

## <a name="overview"></a>Översikt

Bladet StorSimple Device Manager innehåller en sammanfattning av StorSimple Virtual Array som har registrerats med en viss StorSimple Device Manager, markering dessa problem med enheter som behöver åtgärdas av en systemadministratör. Den här självstudien introducerar sammanfattningsbladet för device, förklarar innehåll och funktionen och beskrivs vilka aktiviteter som du kan utföra från det här bladet.

Sammanfattningsbladet för enheten visar följande information:

![Instrumentpanel för enhet](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Hantering

På bladet för StorSimple-enhet kan du se alternativ för att hantera din StorSimple-enhet. Du kan se kommandon för hantering överst på bladet och till vänster. Med dessa alternativ för att lägga till resurser eller volymer, eller uppdatera eller växla över din virtuella matris.

Området essentials samlar in några av de viktiga egenskaperna, till exempel status, modell, programvaruversion samt en länk till den **Webbgränssnittet** i matrisen. Om du har ett internt nätverk kan du direkt kan starta den [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) att administrera din virtuella matris.

![Enheten essentials](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Sammanfattning av StorSimple-enhet

* Den **aviseringar** panelen innehåller en ögonblicksbild av alla aktiva aviseringar för din virtuella matris, grupperade efter allvarlighetsgrad. Klicka på ikonen för att öppna den **aviseringar** bladet och klicka sedan på en enskild varning att visa ytterligare information om den här aviseringen, inklusive alla rekommenderade åtgärder. Du kan även radera aviseringen om problemet har lösts.

* Den **kapacitet** panelen visar den primära lagringen som är etablerade och återstående för den virtuella enheten i förhållande till det totala lagringsutrymmet för samma. **Etablerade** refererar till det lagringsutrymme som är förberedd och är allokerad för användning, **återstående** refererar till den återstående kapacitet som kan etableras i den här enheten. Den **återstående Nivåindelad** kapacitet är den tillgängliga kapaciteten som kan etableras inklusive molnet, medan den **återstående lokala** är den kapacitet kvar på diskar som är anslutna till den här virtuella matrisen.

* I den **användning** diagrammet, du kan visa den primära lagringen som används av din virtuella matris, samt molnlagring som används under de senaste 7 dagarna standard tidsperiod. Använd den **redigera** alternativet i det övre högra hörnet i diagrammet för att välja en annan tidsskala.

* Den **resurser** eller **volymer** panelen innehåller en sammanfattning av hur många resurser eller volymer i enheten för grupperade efter status. Klicka på ikonen för att öppna den **resurser** eller **volymer** listan bladet och klicka sedan på en enskild resurs eller en volym för att visa eller ändra dess egenskaper. Läs mer om hur du [hantera filresurser](storsimple-virtual-array-manage-shares.md) eller [hantera volymer](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Nästa steg
Lär dig att:
- [Hantera filresurser på en StorSimple Virtual Array](storsimple-virtual-array-manage-shares.md)
    
- [Hantera volymer på en StorSimple Virtual Array](storsimple-virtual-array-manage-volumes.md)

