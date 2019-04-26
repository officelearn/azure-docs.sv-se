---
title: Visa och hantera StorSimple Virtual Array-jobb | Microsoft Docs
description: Beskriver sidan StorSimple Device Manager-jobb och hur du använder den för att spåra senaste och aktuella jobb för StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: dbab2aaab2c12bef07748f54e5864d042f1c982a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60302507"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Använda StorSimple Device Manager-tjänsten för att visa jobb för StorSimple Virtual Array
## <a name="overview"></a>Översikt
Den **jobb** bladet ger en enda central portal för att visa och hantera jobb som startas på virtuella matriser som är anslutna till din StorSimple Device Manager-tjänsten. Du kan visa körs, slutförda och misslyckade jobb för flera virtuella enheter. Resultaten visas i tabellformat.

![Jobb-bladet](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Du kan snabbt hitta de jobb som du är intresserad av genom att filtrera på fält som:

* **Tidsintervall** – jobb kan filtreras baserat på det datum och tid-intervallet.
* **Enheter** – jobb startas på en specifik enhet är ansluten till din tjänst. Filtrerade jobben visas sedan som en tabell baserat på följande attribut:
  
  * **Namnet** – Jobbnamnet kan vara **alla**, **Backup**, **klona**, **Redundansväxla**, **hämta uppdateringar**, eller **installera uppdateringar**.
  * **Status för** – jobb kan vara **alla**, **pågår**, **lyckades**, eller **misslyckades**, eller **avbruten**.
  * **Entiteten** – jobb kan associeras med en volym, resurs eller enhet.
  * **Enheten** – med namnet på den enhet som då jobbet startades.
  * **Startad** – tiden då jobbet startades.
  * **Varaktighet** – varaktigheten för som jobbet körs.
* **Status för** – du kan söka efter alla, som körs, slutförda och misslyckade jobb.
* **Typ av utskriftsjobb** – jobbtypen kan all, säkerhetskopiering, återställning, växling vid fel, ladda ned uppdateringar, eller installera uppdateringar.

Listan över jobb uppdateras var 30: e sekund.

## <a name="view-job-details"></a>Visa jobbinformation
Utför följande steg om du vill visa information om alla jobb.

#### <a name="to-view-job-details"></a>Visa jobbinformation
1. På den **jobb** bladet visar de jobb som du är intresserad av genom att köra en fråga med lämpliga filter. Du kan söka efter slutförd eller pågående jobb.
2. Välj ett jobb från listan med jobb.
   
    ![Jobb-bladet](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Längst ned på sidan klickar du på **information**.
4. I den **information** dialogrutan kan du visa status, information och statistik. Följande bild visar ett exempel på den **säkerhetskopiering jobbinformation** dialogrutan.
   
    ![Information om jobb](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Misslyckade jobb när den virtuella datorn har pausats i hypervisor-programmet
När ett jobb är i förloppet på StorSimple Virtual Array och enhet (virtuell dator som etablerats i hypervisor-program) har pausats för längre än 15 minuter, jobbet har misslyckats. Detta på grund av din StorSimple Virtual Array-tid är osynkroniserad med Microsoft Azure-tiden. 

Följande fel visas: ”Din enhets tidsinställningar är inte synkroniserat med Microsoft Azure-tiden med mer än 15 minuter. Se till att hypervisor-programmet och enheten servertiderna är synkroniserade med en NTP-server. Kontrollera att det inte finns några anslutningsproblem. Om du vill felsöka problem med nätverksanslutningen, köra diagnostiktest från det lokala webbgränssnittet på den virtuella enheten ”.

De här felen gäller för säkerhetskopiering, återställning, uppdatering och redundans jobb. Om den virtuella datorn har etablerats i Hyper-V, synkroniseras datorn så småningom tid med din hypervisor-programmet. När det händer kan du starta om jobbet.

## <a name="next-steps"></a>Nästa steg
[Lär dig hur du använder det lokala webbgränssnittet för att administrera din StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

