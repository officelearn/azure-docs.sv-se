---
title: Använd StorSimple 8000-serien sammanfattning av enhet | Microsoft Docs
description: Beskriver sammanfattning av StorSimple Device Manager service enhet och hur du använder det för att visa mätvärden i storage och anslutna initierare och hitta serienumret och IQN.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 1d88af2c0739c30b2562bad7660015b890e8159c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578325"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Använda enhets-sammanfattning i StorSimple Device Manager-tjänsten

## <a name="overview"></a>Översikt
Sammanfattningsbladet för StorSimple-enhet ger dig en översikt över information för en specifik StorSimple-enhet, till skillnad från sammanfattningsbladet för tjänsten, vilket ger dig information om de enheter som ingår i Microsoft Azure StorSimple-lösningen.

Bladet sammanfattning innehåller en sammanfattning av en enhet i StorSimple 8000-serien som har registrerats med en viss StorSimple Device Manager, markera dessa problem med enheter som behöver åtgärdas av en systemadministratör. Den här självstudien introducerar sammanfattningsbladet för device, förklarar innehåll och funktionen och beskrivs vilka aktiviteter som du kan utföra från det här bladet.

Sammanfattningsbladet för enheten visar följande information:

![Sammanfattningsbladet för enhet](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Hantering av kommandofältet

På bladet för StorSimple-enhet kan du se alternativ för att hantera din StorSimple-enhet. Du kan se kommandon för hantering överst på bladet och till vänster. Lägg till resurser eller volymer, eller uppdatera eller redundansväxla din enhet med hjälp av dessa alternativ.

![Hantering av kommandofältet](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

Området essentials samlar in några av de viktiga egenskaperna som, status, modell, mål-IQN och programvaruversionen. 

![Enheten essentials](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Övervakning

* Den **aviseringar** panelen innehåller en ögonblicksbild av alla aktiva aviseringar för enheten, grupperade efter allvarlighetsgrad.

    ![Alert panel](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Klicka på ikonen för att öppna den **aviseringar** bladet och klicka sedan på en enskild varning att visa ytterligare information om den här aviseringen, inklusive alla rekommenderade åtgärder. Du kan även radera aviseringen om problemet har lösts.

    ![Klicka på aviseringen panel](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Den **Status och hälsa** panel ger insikter om maskinvara komponentens hälsostatus för en enhet, inklusive enhetens status. Enhetens status kan vara offline, online, inaktiverad eller redo för installation.

    ![Status och hälsa panel](./media/storsimple-8000-device-dashboard/device-summary5.png)

* Den **volymer** panelen innehåller en sammanfattning av antalet volymer i enheten för grupperade efter status.

    ![Volymbrickan](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Klicka på ikonen för att öppna den **volymer** listan bladet och klicka sedan på en enskild volym för att visa eller ändra dess egenskaper.
    
    ![Klicka på volymer panel](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Läs mer om hur du [hantera volymer](storsimple-8000-manage-volumes-u2.md).

* I den **användning** diagrammet, du kan visa den primära lagringen som används av enheten och den molnlagring som används under de senaste 7 dagarna standard tidsperiod.

     ![Användningsikonen](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Välj en annan tidsskala genom att använda den **redigera** alternativ i det övre högra hörnet av diagrammet.

     ![Redigera Användningsdiagram](./media/storsimple-8000-device-dashboard/device-summary12.png)

     I det här diagrammet kan du visa mått för total primär lagring (mängden data som skrivits av värdar till din enhet) och den totala molnlagring som används av din enhet under en viss tidsperiod.
  
     I det här sammanhanget *primärlagring* refererar till den totala mängden data som skrivits av värden och kan delas upp på volymtyp: *primär nivåindelad lagring* omfattar både lokalt lagrade data och data nivåindelade molnet. *Primär lokalt fixerad lagring* innehåller bara data som lagras lokalt. *Molnlagring*, å andra sidan är ett mått på den totala mängden data som lagras i molnet. Den här lagringen omfattar nivåindelade data och säkerhetskopieringar. De data som lagras i molnet är deduplicerad och komprimeras, medan primärlagring Anger mängden lagringsutrymme som används innan data dedupliceras och komprimeras. (Du kan jämföra dessa två tal för att få en uppfattning om hastigheten med komprimering.) För båda primära och molnlagring, de mängder som anges är baserat på åtkomstfrekvensen för spårning som du konfigurerar. Exempel: Om du väljer en frekvens på en vecka sedan diagrammet visar data för varje dag under föregående vecka.

     Om du vill se hur mycket molnlagring som används över tid, Välj den **CLOUD STORAGE används** alternativet. Om du vill se det totala lagringsutrymmet som har skrivits av värden, den **primär NIVÅINDELAD lagring används** och **primära LOKALT FÄST STORAGE används** alternativ. 
     Mer information finns i [använda StorSimple Device Manager-tjänsten för att övervaka din StorSimple-enhet](storsimple-monitor-device.md).


* Den **kapacitet** panelen visar den primära lagringen som är etablerade och återstående för enheten i förhållande till det totala lagringsutrymmet för samma. **Etablerade** refererar till det lagringsutrymme som är förberedd och är allokerad för användning, **återstående** refererar till den återstående kapacitet som kan etableras i den här enheten. 

    ![Användningsikonen](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Klicka på den här panelen för att visa hur kapaciteten etableras mellan nivåindelade och lokalt fixerade volymer. Den **återstående Nivåindelad** kapacitet är den tillgängliga kapaciteten som kan etableras inklusive molnet, medan den **återstående lokala** är den kapacitet kvar på diskar som är kopplade till den här enheten.

    ![Klicka på Användningsdiagram](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Nästa steg
* Läs mer om den [sammanfattningsbladet för tjänsten StorSimple](storsimple-8000-service-dashboard.md).
* Läs mer om [med StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

