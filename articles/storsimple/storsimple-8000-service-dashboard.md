---
title: Använd StorSimple 8000-serien sammanfattning av enhet | Microsoft Docs
description: Beskriver sammanfattningsbladet för StorSimple-tjänsten och förklarar hur du använder den för att övervaka hälsotillståndet för din StorSimple-lösning.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633202"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Använda sammanfattningsbladet för tjänsten för enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt

Sammanfattningsbladet för StorSimple Device Manager-tjänsten innehåller en sammanfattning av alla enheter som är anslutna till StorSimple Device Manager-tjänsten, markera de enheter som behöver åtgärdas av en systemadministratör. Den här självstudien introducerar sammanfattningsbladet för tjänsten, förklarar instrumentpanelens innehåll och funktionen och beskrivs vilka aktiviteter som du kan utföra från den här sidan.

![Sammanfattning av tjänst](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Kommandon för hantering

I StorSimple sammanfattningsbladet för tjänsten, kan du se alternativ för att hantera din StorSimple Device Manager-tjänst och StorSimple 8000-serieenheter som är registrerade på den här tjänsten. Du kan se kommandon för hantering överst på bladet och till vänster.

![Kommandofältet](./media/storsimple-8000-service-dashboard/service-summary2.png)

Använd dessa alternativ för att utföra olika åtgärder som lägger till resurser eller volymer eller övervaka de olika jobb som körs på StorSimple-enheter.


## <a name="essentials"></a>Essentials

Området essentials samlar in några av de viktiga egenskaperna som resursgruppens namn, plats och prenumeration som StorSimple Device Manager skapades.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Sammanfattning av StorSimple Device Manager-tjänst

* Den **aviseringar** panel ger en ögonblicksbild av alla aktiva aviseringar över alla enheter, grupperat efter allvarlighetsgrad.

    ![Ikonen aviseringar](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Om du klickar på panelen öppnas den **aviseringar** bladet där du kan klicka på en enskild varning att visa ytterligare information om den här aviseringen, inklusive alla rekommenderade åtgärder. Du kan även radera aviseringen om problemet har lösts.

    ![Klicka på panelen för aviseringar](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Den **kapacitet** panel visar visar den primära lagringen som är upprättad och återstående på alla enheter i förhållande till det totala lagringsutrymmet som är tillgängliga på alla enheter. **Etablerade** refererar till det lagringsutrymme som är förberedd och är allokerad för användning, **återstående** refererar till den återstående kapacitet som kan etableras på alla enheter.

    ![Kapacitet panel](./media/storsimple-8000-service-dashboard/service-summary6.png)

    Den **återstående Nivåindelad** kapacitet är den tillgängliga kapaciteten som kan etableras inklusive molnet, medan den **återstående lokala** är den kapacitet kvar på diskar som är kopplade till StorSimple 8000 enheter i serien.


* I den **användning** diagrammet, du kan se mått som är relevanta för dina enheter. Du kan visa den primära lagringen som används på alla enheter och den molnlagring som används av enheter under de senaste 7 dagarna standard tidsperiod. 

    ![Användningsikonen](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Välj en annan tidsskala genom att använda den **redigera** alternativ i det övre högra hörnet av diagrammet.

     ![Klicka på användningsikonen](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportera diagramdata](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Den **enheter** panelen innehåller en sammanfattning av antalet enheter i StorSimple 8000-serien i StorSimple-Enhetshanteraren grupperade efter enhetens status. 

    ![Panel för enheter](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Klicka på den här panelen för att öppna den **enheter** listan bladet och klicka sedan på en enskild enhet att visa detaljerad information om sammanfattningen för enheter som är specifik för enheten. Du kan också utföra specifika åtgärder från en given enhet sammanfattningsbladet. Mer information om sammanfattningsbladet för enheten går du till [sammanfattningsbladet för Device](storsimple-8000-device-dashboard.md).

    ![Klicka på panelen för enheter](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Visa aktivitetsloggar

Om du vill visa de olika åtgärderna som utförs inom din StorSimple Device Manager klickar du på den **aktivitetsloggar** länk på vänster sida av din sammanfattningsbladet för StorSimple-tjänsten. Då kommer du till den **aktivitetsloggar** bladet, där du kan se en sammanfattning av de senaste genomförda transaktioner.

![Aktivitetsloggar](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Nästa steg

* Mer information om hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

