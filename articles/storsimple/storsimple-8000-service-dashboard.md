---
title: "Använd StorSimple 8000-serien sammanfattningen | Microsoft Docs"
description: "Beskriver bladet StorSimple-tjänsten sammanfattning och förklarar hur du använder den för att övervaka hälsotillståndet för din StorSimple-lösning."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: d987a4ae170f21532a886552cbe1eb5a0d25fc3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Använda tjänsten sammanfattning bladet för StorSimple 8000-serieenhet

## <a name="overview"></a>Översikt

StorSimple Enhetshanteraren service sammanfattning bladet innehåller en sammanfattning av alla enheter som är anslutna till tjänsten StorSimple Enhetshanteraren, markera de enheter som behöver åtgärdas av en systemadministratör. Den här kursen introducerar bladet service sammanfattning, förklarar instrumentpanelinnehåll och funktionen och beskrivs vilka aktiviteter som du kan utföra från den här sidan.

![Sammanfattning av tjänst](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Kommandon för hantering

I StorSimple-tjänsten sammanfattning bladet visas alternativ för att hantera din StorSimple Device Manager-tjänst och StorSimple 8000-serien enheter som registrerats för den här tjänsten. Du kan se kommandona management överst på bladet och till vänster.

![Kommandofältet](./media/storsimple-8000-service-dashboard/service-summary2.png)

Använd dessa alternativ för att utföra olika åtgärder som kan du lägga till resurser eller volymer eller övervaka olika jobb som körs på StorSimple-enheter.


## <a name="essentials"></a>Essentials

Området essentials samlar in några viktiga egenskaper som resursgruppens namn, plats och prenumeration som din StorSimple-Enhetshanteraren skapades.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Sammanfattning av StorSimple Enhetshanteraren tjänst

* Den **aviseringar** panelen ger en ögonblicksbild av alla aktiva aviseringar över alla enheter, grupperat efter allvarlighetsgrad.

    ![Ikonen aviseringar](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Om du klickar på ikonen öppnas den **aviseringar** bladet där du kan klicka på en enskild varning för att visa ytterligare information om den här aviseringen, inklusive de rekommenderade åtgärder. Du kan också ta bort aviseringen om problemet har lösts.

    ![Klicka på ikonen för aviseringar](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Den **kapacitet** panelen visar visar den primära lagring som är etablerad och återstående på alla enheter i förhållande till det totala lagringsutrymmet som är tillgängliga på alla enheter. **Etablerad** avser mängden lagringsutrymme som är förberedd och tilldelat för användning, **återstående** refererar till den återstående kapacitet som kan etableras på alla enheter.

    ![Kapacitet sida vid sida](./media/storsimple-8000-service-dashboard/service-summary6.png)

    Den **återstående nivåer** kapacitet är den tillgängliga kapaciteten som kan etableras inklusive molnet, medan den **återstående lokala** är kapacitet kvar på diskar som är kopplade till StorSimple 8000-serien enheter.


* I den **användning** diagram, du kan se mätvärdena som är relevanta för dina enheter. Du kan visa den primära lagringsutrymme som används på alla enheter och molnlagring som används av enheter under de senaste 7 dagarna standard tidsperiod. 

    ![Ikonen användning](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Om du vill välja en annan tidsskala, Använd den **redigera** alternativ i det övre högra hörnet i diagrammet.

     ![Klicka på ikonen användning](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportera diagram](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Den **enheter** panelen innehåller en sammanfattning av antalet StorSimple 8000-serien enheter i din StorSimple Enhetshanteraren grupperade efter status för enheter. 

    ![Panelen för enheter](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Klicka på den här rutan för att öppna den **enheter** bladet och klicka sedan på en enskild enhet att visa detaljerad information om sammanfattningen för enheter som är specifik för enheten. Du kan också utföra specifika åtgärder från en viss enhet sammanfattning-bladet. Mer information om enheten sammanfattning bladet går du till [enheten sammanfattning bladet](storsimple-8000-device-dashboard.md).

    ![Klicka på ikonen för enheter](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Kontrollera händelseloggarna för aktiviteten

Om du vill visa olika åtgärder som utförs inom din StorSimple-Enhetshanteraren, klickar du på den **aktivitetsloggar** länk på vänster sida av din StorSimple-tjänsten sammanfattning-bladet. Då kommer du att den **aktivitetsloggar** bladet, där du kan se en översikt över de senaste genomförda transaktioner.

![Aktivitetsloggar](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Nästa steg

* Mer information om hur du [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

