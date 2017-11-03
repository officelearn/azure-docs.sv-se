---
title: "Använd StorSimple 8000-serien sammanfattningen | Microsoft Docs"
description: "Beskriver sammanfattning StorSimple Enhetshanteraren service enheten och hur du använder den för att visa storage-mätvärden och anslutna initierare och hitta serienummer och IQN."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 784d3ce9d8f926b00ac1c6fbf48a05c0b04f900a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Använda sammanfattningen i Enhetshanteraren för StorSimple-tjänsten

## <a name="overview"></a>Översikt
Bladet StorSimple enheten sammanfattning ger dig en översikt över information för en specifik StorSimple-enhet, till skillnad från tjänsten sammanfattning bladet som ger information om de enheter som ingår i Microsoft Azure StorSimple-lösningen.

Enheten sammanfattning bladet innehåller en översikt över av en StorSimple 8000-serieenhet som har registrerats med en viss StorSimple Enhetshanteraren, syntaxmarkering dessa problem med enheter som behöver åtgärdas av en systemadministratör. Den här kursen introducerar bladet enheten sammanfattning, förklarar innehåll och funktionen och beskrivs vilka aktiviteter som du kan utföra från det här bladet.

Sammanfattning bladet enheten visar följande information:

![Översikt över bladet för enhet](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Hantering av kommandofältet

I bladet StorSimple-enhet visas alternativ för att hantera din StorSimple-enhet. Du kan se kommandona management överst på bladet och till vänster. Använd dessa alternativ för att lägga till resurser eller volymer, eller uppdatera eller växla över din enhet.

![Hantering av kommandofältet](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

Området essentials avbildar vissa viktiga egenskaper som, status, modell, mål IQN och programvaruversionen. 

![Enheten essentials](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Övervakning

* Den **aviseringar** panelen ger en ögonblicksbild av alla aktiva aviseringar för enheten, grupperat efter allvarlighetsgrad.

    ![Aviseringen sida vid sida](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Klicka på rutan öppnas den **aviseringar** bladet och klicka sedan på en enskild varning för att visa ytterligare information om den här aviseringen, inklusive de rekommenderade åtgärder. Du kan också ta bort aviseringen om problemet har lösts.

    ![Klicka på aviseringen sida vid sida](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Den **statusen och hälsan** panelen ger insikter om maskinvara komponentens hälsostatus för en enhet, inklusive enhetens status. Enhetens status kan vara offline, online, inaktiverat eller redo att konfigurera.

    ![Statusen och hälsan panel](./media/storsimple-8000-device-dashboard/device-summary5.png)

* Den **volymer** panelen innehåller en sammanfattning av antalet volymer i enheten grupperade efter status.

    ![Volymbrickan](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Klicka på rutan öppnas den **volymer** listan bladet och klicka på en enskild volym för att visa eller ändra dess egenskaper.
    
    ![Klicka på panelen volymer](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Mer information finns i så här [hantera volymer](storsimple-8000-manage-volumes-u2.md).

* I den **användning** diagram, kan du visa den primära lagringsutrymme som används på enheten och molnlagring som används under de senaste 7 dagarna standard tidsperiod.

     ![Ikonen användning](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Om du vill välja en annan tidsskala, Använd den **redigera** alternativ i det övre högra hörnet i diagrammet.

     ![Redigera Användningsdiagram](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Du kan visa egenskaperna för totalt antal primära lagring (mängden data som skrivs av värdar på enheten) och totala molnlagring som används av din enhet under en viss tidsperiod i det här diagrammet.
  
     I den här kontexten *primära lagringsplatsen* refererar till den totala mängden data som skrivs av värden, och kan delas upp på volymtyp: *primära nivåer lagring* innehåller både lokalt lagrade data och data nivåer till molnet. *Primär lokalt Fäst lagring* innehåller bara data som lagras lokalt. *Molnlagring*, å andra sidan är ett mått på den totala mängden data som lagras i molnet. Den här innehåller nivåindelade data och säkerhetskopieringar. Data som lagras i molnet är deduplicerad och komprimerade, medan primära lagringsplatsen anger hur mycket lagringsutrymme som används innan data är deduplicerad och komprimeras. (Du kan jämföra dessa två tal för att få en uppfattning om hastigheten med komprimering.) För både primär och lagringsutrymmet i molnet, de belopp som visas baseras på frekvensen för spårning av som du konfigurerar. Till exempel om du väljer en frekvens som en vecka visar sedan diagrammet data för varje dag i föregående vecka.

     Om du vill visa mängden molnlagring som används över tid, Välj den **MOLN lagring används** alternativet. Om du vill se det totala lagringsutrymmet som har skrivits av värden, Välj den **primära nivåer lagring används** och **primära LOKALT FÄST lagring används** alternativ. 
     Mer information finns i [använda Enhetshanteraren för StorSimple-tjänsten för att övervaka din StorSimple-enhet](storsimple-monitor-device.md).


* Den **kapacitet** panelen visar den primära lagring som är allokerade och återstående i enheten i förhållande till den totala lagringsstorleken för samma. **Etablerad** avser mängden lagringsutrymme som är förberedd och tilldelat för användning, **återstående** refererar till den återstående kapacitet som kan etableras mellan den här enheten. 

    ![Ikonen användning](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Klicka på den här panelen om du vill visa hur kapaciteten etableras mellan nivåindelade och lokalt fästa volymer. Den **återstående nivåer** kapacitet är den tillgängliga kapaciteten som kan etableras inklusive molnet, medan den **återstående lokala** är kapacitet kvar på diskar som är kopplade till den här enheten.

    ![Klicka på Användningsdiagram](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [StorSimple-tjänsten sammanfattning bladet](storsimple-8000-service-dashboard.md).
* Lär dig mer om [använder Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

