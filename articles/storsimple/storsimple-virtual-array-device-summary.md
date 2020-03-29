---
title: Sammanfattningsblad för StorSimple Virtual Array-enhet | Microsoft-dokument
description: Beskriver enhetssammanfattningsbladet för StorSimple Device Manager och förklarar hur du använder det för att övervaka hälsotillståndet för den virtuella storsimple-matrisen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61408514"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Använda enhetssammanfattningsbladet för StorSimple Device Manager som är ansluten till StorSimple Virtual Array

## <a name="overview"></a>Översikt

StorSimple Device Manager-enhetsbladet ger en sammanfattande vy över en StorSimple Virtual Array som är registrerad med en viss StorSimple Enhetshanteraren, vilket belyser de enhetsproblem som behöver en systemadministratörs uppmärksamhet. Den här självstudien introducerar enhetssammanfattningsbladet, förklarar innehållet och funktionen och beskriver de uppgifter som du kan utföra från det här bladet.

Enhetssammanfattningsbladet visar följande information:

![Instrumentpanel för enhet](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Hantering

I StorSimple-enhetens blad ser du alternativen för att hantera din StorSimple-enhet. Du ser hanteringskommandona över bladets överkant och på vänster sida. Använd de här alternativen om du vill lägga till resurser eller volymer, eller uppdatera eller växla över den virtuella matrisen.

Essentials-området fångar några av de viktiga egenskaperna, till exempel status, modell, programvaruversion samt en länk till **matrisens webbgränssnitt.** Om du befinner dig i ett internt nätverk kan du starta det [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) direkt för att administrera den virtuella matrisen.

![Det väsentliga enhetsdespekterna](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Sammanfattning av StorSimple-enheten

* Panelen **Aviseringar** innehåller en ögonblicksbild av alla aktiva aviseringar för den virtuella matrisen, grupperad efter allvarlighetsgrad för aviseringar. Klicka på panelen för att öppna bladet **Aviseringar** och klicka sedan på en enskild avisering för att visa ytterligare information om den aviseringen, inklusive rekommenderade åtgärder. Du kan också rensa aviseringen om problemet har lösts.

* Panelen **Kapacitet** visar den primära lagring som har etablerats och som finns kvar över den virtuella enheten i förhållande till det totala lagringsutrymme som är tillgängligt för samma. **Etablerat** avser mängden lagringsutrymme som förbereds och allokeras för användning, **Återstående** refererar till den återstående kapaciteten som kan etableras över den här enheten. **Återstående nivåindelad** kapacitet är den tillgängliga kapacitet som kan etableras inklusive molnet, medan **återstående lokal** är den kapacitet som finns kvar på diskarna som är kopplade till den här virtuella matrisen.

* I **användningsdiagrammet** kan du visa den primära lagring som används i den virtuella matrisen, samt den molnlagring som förbrukats under de senaste 7 dagarna, standardperioden. Använd alternativet **Redigera** i diagrammets övre högra hörn för att välja en annan tidsskala.

* Panelen **Resurser** eller **Volymer** innehåller en sammanfattning av antalet resurser eller volymer i enheten grupperade efter status. Klicka på panelen för att öppna listbladet **Resurser** eller **Volymer** och klicka sedan på en enskild resurs eller volym för att visa eller ändra dess egenskaper. Mer information finns i hur du [hanterar resurser](storsimple-virtual-array-manage-shares.md) eller [hanterar volymer](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Nästa steg
Lär dig att:
- [Hantera resurser på en storsimple virtuell matris](storsimple-virtual-array-manage-shares.md)
    
- [Hantera volymer på en storsimple virtuell array](storsimple-virtual-array-manage-volumes.md)

