---
title: Använd enhetssammanfattning i StorSimple 8000-serien | Microsoft-dokument
description: Beskriver sammanfattningsbladet för StorSimple-tjänsten och förklarar hur du använder det för att övervaka hälsotillståndet för din StorSimple-lösning.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: c174f6ce0fb3d40af953be205a7bfcca60fbfeec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267656"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Använd servicesammanfattningsbladet för StorSimple 8000-seriens enhet

## <a name="overview"></a>Översikt

Sammanfattningsbladet för Tjänsten StorSimple Device Manager ger en sammanfattande vy över alla enheter som är anslutna till Tjänsten StorSimple Device Manager, vilket belyser de enheter som behöver en systemadministratörs uppmärksamhet. Den här självstudien introducerar bladet för servicesammanfattning, förklarar instrumentpanelens innehåll och funktion och beskriver de uppgifter som du kan utföra från den här sidan.

![Sammanfattning av tjänsten](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Kommandon för hantering

I sammanfattningsbladet för StorSimple-tjänsten visas alternativen för att hantera tjänsten StorSimple Device Manager och storsimple 8000-seriens enheter som är registrerade i den här tjänsten. Du ser hanteringskommandona över bladets överkant och på vänster sida.

![Kommandofältet](./media/storsimple-8000-service-dashboard/service-summary2.png)

Använd de här alternativen för att utföra olika åtgärder, till exempel lägga till resurser eller volymer, eller övervaka de olika jobb som körs på StorSimple-enheterna.


## <a name="essentials"></a>Essentials

Essentials-området fångar några av de viktiga egenskaperna, till exempel resursgruppen, platsen och prenumerationen där Din StorSimple-enhetshanteraren skapades.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Sammanfattning av Tjänsten StorSimple Enhetshanteraren

* Panelen **Aviseringar** innehåller en ögonblicksbild av alla aktiva aviseringar på alla enheter, grupperade efter allvarlighetsgrad för aviseringar.

    ![Panelen Aviseringar](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Om du klickar på panelen öppnas bladet **Aviseringar,** där du kan klicka på en enskild avisering för att visa ytterligare information om den aviseringen, inklusive rekommenderade åtgärder. Du kan också rensa aviseringen om problemet har lösts.

    ![Klicka på panelen Aviseringar](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Panelen **Kapacitet** visar den primära lagring som är etablerad och som finns kvar på alla enheter i förhållande till den totala tillgängliga lagringen på alla enheter. **Etablerat** avser mängden lagringsutrymme som förbereds och allokeras för användning, **Återstående** refererar till den återstående kapaciteten som kan etableras på alla enheter.

    ![Kapacitet kakel](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **Återstående nivåindelad** kapacitet är den tillgängliga kapacitet som kan etableras inklusive molnet, medan **återstående lokal** är den återstående lokala kapaciteten som finns kvar på diskarna som är anslutna till StorSimple 8000-seriens enheter.


* I **användningsdiagrammet** kan du se relevanta mått för dina enheter. Du kan visa den primära lagring som används på alla enheter och molnlagringen som förbrukats av enheter under de senaste 7 dagarna, standardtidsperioden. 

    ![Användningspanel](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Om du vill välja en annan tidsskala använder du alternativet **Redigera** i diagrammets övre högra hörn.

     ![Klicka på användningspanelen](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportera diagramdata](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Panelen **Enheter** innehåller en sammanfattning av antalet Enheter i StorSimple 8000-serien i StorSimple-enhetshanteraren grupperade efter enhetsstatus. 

    ![Panel för enheter](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Klicka på den här panelen om du vill öppna listbladet **Enheter** och sedan på en enskild enhet för att öka enhetssammanfattningen som är specifik för enheten. Du kan också utföra enhetsspecifika åtgärder från ett visst enhetssammanfattningsblad. Mer information om enhetssammanfattningsbladet finns i [Sammanfattningsblad för enhet](storsimple-8000-device-dashboard.md).

    ![Klicka på enhetspanelen](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Visa aktivitetsloggarna

Om du vill visa de olika åtgärder som utförs i StorSimple-enhetshanteraren klickar du på länken **Aktivitetsloggar** till vänster om sammanfattningsbladet för StorSimple-tjänsten. Detta tar dig till **bladet Aktivitetsloggar,** där du kan se en sammanfattning av de senaste åtgärderna som utförts.

![Aktivitetsloggar](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

