---
title: Använd enhetssammanfattning i StorSimple 8000-serien | Microsoft-dokument
description: Beskriver sammanfattningen av StorSimple Device Manager-tjänstenheten och hur du använder den för att visa lagringsmått och anslutna initierare och hitta serienumret och IQN.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60578325"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Använda enhetssammanfattningen i StorSimple Enhetshanteraren

## <a name="overview"></a>Översikt
Sammanfattningsbladet för StorSimple-enheten ger dig en översikt över information för en viss StorSimple-enhet, till skillnad från bladet för servicesammanfattning, som ger dig information om alla enheter som ingår i Microsoft Azure StorSimple-lösningen.

Enhetssammanfattningsbladet ger en sammanfattande vy över en StorSimple 8000-serieenhet som är registrerad med en viss StorSimple-enhetshanterare, vilket belyser de enhetsproblem som behöver en systemadministratörs uppmärksamhet. Den här självstudien introducerar enhetssammanfattningsbladet, förklarar innehållet och funktionen och beskriver de uppgifter som du kan utföra från det här bladet.

Enhetssammanfattningsbladet visar följande information:

![Sammanfattningsblad för enhet](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Kommandofältet Hantering

I StorSimple-enhetens blad ser du alternativen för att hantera din StorSimple-enhet. Du ser hanteringskommandona över bladets överkant och på vänster sida. Använd de här alternativen om du vill lägga till resurser eller volymer, eller uppdatera eller växla över enheten.

![Kommandofältet Hantering](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

Essentials-området fångar några av de viktiga egenskaperna som status, modell, mål-IQN och programvaruversionen. 

![Det väsentliga enhetsdespekterna](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Övervakning

* Panelen **Aviseringar** innehåller en ögonblicksbild av alla aktiva aviseringar för enheten, grupperade efter allvarlighetsgrad för aviseringar.

    ![Varningspanel](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Klicka på panelen för att öppna bladet **Aviseringar** och klicka sedan på en enskild avisering för att visa ytterligare information om den aviseringen, inklusive rekommenderade åtgärder. Du kan också rensa aviseringen om problemet har lösts.

    ![Klicka på varningspanel](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Panelen **Status och hälsa** ger insikter om maskinvarukomponentens hälsotillstånd för en enhet, inklusive enhetens status. Enhetsstatusen kan vara offline, online, inaktiverad eller klar att konfigureras.

    ![Panelen Status och hälsa](./media/storsimple-8000-device-dashboard/device-summary5.png)

* Panelen **Volymer** innehåller en sammanfattning av antalet volymer i enheten grupperade efter status.

    ![Panel för volymer](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Klicka på panelen för att öppna listbladet **Volymer** och klicka sedan på en enskild volym för att visa eller ändra dess egenskaper.
    
    ![Klicka på panelen Volymer](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Mer information finns i hur du [hanterar volymer](storsimple-8000-manage-volumes-u2.md).

* I **användningsdiagrammet** kan du visa den primära lagring som används över enheten och molnlagringen som förbrukats under de senaste 7 dagarna, standardtidsperioden.

     ![Användningspanel](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Om du vill välja en annan tidsskala använder du alternativet **Redigera** i diagrammets övre högra hörn.

     ![Redigera användningsdiagram](./media/storsimple-8000-device-dashboard/device-summary12.png)

     I det här diagrammet kan du visa mått för den totala primära lagringen (mängden data som skrivits av värdar till din enhet) och den totala molnlagring som används av enheten under en viss tidsperiod.
  
     I det här sammanhanget refererar *primär lagring* till den totala mängden data som skrivits av värden och kan delas upp efter volymtyp: *primär nivåindelad lagring* omfattar både lokalt lagrade data och data som nivåindelas till molnet. *Primär lokalt fäst lagring* innehåller bara data som lagras lokalt. *Molnlagring*, å andra sidan, är ett mått på den totala mängden data som lagras i molnet. Den här lagringen innehåller nivåindelade data och säkerhetskopior. Data som lagras i molnet dedupliceras och komprimeras, medan primär lagring anger hur mycket lagringsutrymme som används innan data dedupliceras och komprimeras. (Du kan jämföra dessa två siffror för att få en uppfattning om komprimeringsfrekvensen.) För både primär lagring och molnlagring baseras de belopp som visas på spårningsfrekvensen som du konfigurerar. Om du till exempel väljer en enveckorsfrekvens visas data för varje dag i föregående vecka.

     Om du vill se hur mycket molnlagring som förbrukas över tid väljer du alternativet **CLOUD STORAGE USED.** Om du vill se det totala lagringsutrymme som har skrivits av värden väljer du alternativen **PRIMÄR NIVÅINDELAD LAGRING SOM ANVÄNDS** OCH PRIMÄR LOKALT **NÅLAD LAGRING ANVÄNDS.** 
     Mer information finns i [Använda Tjänsten StorSimple Device Manager för att övervaka din StorSimple-enhet](storsimple-monitor-device.md).


* Panelen **Kapacitet** visar den primära lagring som är etablerad och som finns kvar över enheten i förhållande till det totala lagringsutrymme som är tillgängligt för samma. **Etablerat** avser mängden lagringsutrymme som förbereds och allokeras för användning, **Återstående** refererar till den återstående kapaciteten som kan etableras över den här enheten. 

    ![Användningspanel](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Klicka på den här panelen om du vill visa hur kapaciteten etableras över nivåindelade och lokalt fästa volymer. **Återstående nivåindelad** kapacitet är den tillgängliga kapaciteten som kan etableras inklusive molnet, medan **återstående lokal** är den återstående lokala kapaciteten som finns kvar på diskarna som är anslutna till den här enheten.

    ![Klicka på användningsdiagram](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Nästa steg
* Läs mer om [sammanfattningsbladet för StorSimple-tjänsten](storsimple-8000-service-dashboard.md).
* Läs mer om [hur du använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

