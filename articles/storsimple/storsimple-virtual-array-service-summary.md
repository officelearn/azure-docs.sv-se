---
title: Sammanfattningsblad för StorSimple Virtual Array-tjänsten | Microsoft-dokument
description: Beskriver servicesammanfattningsbladet för StorSimple Device Manager och förklarar hur du använder det för att övervaka hälsotillståndet för den virtuella storsimple-matrisen.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 9c05bddaeb3c34400db1ec75c624ef00a85d9444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64720734"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Använda servicesammanfattningsbladet för StorSimple Device Manager som är ansluten till StorSimple Virtual Array
## <a name="overview"></a>Översikt
Servicesammanfattningsbladet för StorSimple-enhetshanteraren ger en sammanfattande vy över De virtuella storsimple-matriserna (kallas även Lokala virtuella enheter eller virtuella enheter för StorSimple) som är anslutna till din tjänst och belyser dem som behöver ett system administratörens uppmärksamhet. Den här självstudien introducerar bladet för servicesammanfattning, förklarar innehållet och funktionen och beskriver de uppgifter som du kan utföra från det här bladet.

![Instrumentpanel för tjänst](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Ledningskommandon och väsentligheter
I sammanfattningsbladet StorSimple visas alternativen för att hantera tjänsten StorSimple Device Manager samt de virtuella matriser som är registrerade för den här tjänsten. Du ser hanteringskommandona över bladets överkant och på vänster sida.

Använd de här alternativen för att utföra olika åtgärder, till exempel lägga till resurser eller volymer, eller övervaka de olika jobb som körs på de virtuella matriserna.

Essentials-området fångar några av de viktiga egenskaperna, till exempel resursgruppen, platsen och prenumerationen där Din StorSimple-enhetshanteraren skapades.

## <a name="storsimple-device-manager-service-summary"></a>Sammanfattning av Tjänsten StorSimple Enhetshanteraren
* Panelen **Aviseringar** innehåller en ögonblicksbild av alla aktiva aviseringar på alla virtuella enheter, grupperade efter allvarlighetsgrad för aviseringar. Om du klickar på panelen öppnas bladet **Aviseringar,** där du kan klicka på en enskild avisering för att visa ytterligare information om den aviseringen, inklusive rekommenderade åtgärder. Du kan också rensa aviseringen om problemet har lösts.
* På panelen **Kapacitet** visas den primära lagring som har etablerats och som finns kvar på alla virtuella enheter i förhållande till den totala tillgängliga lagringen på alla virtuella enheter. **Etablerat** refererar till mängden lagringsutrymme som förbereds och allokeras för användning, **Återstående** refererar till den återstående kapaciteten som kan etableras över alla virtuella enheter. **Återstående nivåindelad** kapacitet är den tillgängliga kapacitet som kan etableras inklusive molnet, medan **återstående lokal** är den kapacitet som finns kvar på diskarna som är kopplade till de virtuella matriserna.
* I **användningsdiagrammet** kan du se relevanta mått för dina virtuella enheter. Du kan visa den primära lagring som används på alla virtuella enheter, samt molnlagring som förbrukas av virtuella enheter under de senaste 7 dagarna, standardperioden. Använd alternativet **Redigera** i diagrammets övre högra hörn för att välja en annan tidsskala.
* Panelen **Enheter** innehåller en sammanfattning av antalet virtuella matriser i StorSimple-enhetshanteraren grupperade efter enhetsstatus. Klicka på den här panelen om du vill öppna listbladet **Enheter** och sedan på en enskild enhet för att öka enhetssammanfattningen som är specifik för enheten. Du kan också utföra enhetsspecifika åtgärder från ett visst enhetssammanfattningsblad. Mer information om enhetssammanfattningsbladet finns i [Sammanfattningsblad för enhet](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Visa aktivitetsloggarna
Om du vill visa de olika åtgärder som utförs i StorSimple-enhetshanteraren klickar du på länken **Aktivitetsloggar** till vänster om sammanfattningsbladet för StorSimple-tjänsten. Detta tar dig till **bladet Aktivitetsloggar,** där du kan se en sammanfattning av de senaste åtgärderna som utförts.

![Aktivitetsloggar](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [använder det lokala webbgränssnittet för att administrera din StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

