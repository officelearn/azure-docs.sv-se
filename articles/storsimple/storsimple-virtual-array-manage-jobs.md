---
title: Visa och hantera virtuella StorSimple-matris jobb | Microsoft Docs
description: Beskriver sidan StorSimple Enhetshanteraren jobb och hur du använder den för att spåra senaste och aktuella jobb för den virtuella StorSimple-matrisen.
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
ms.openlocfilehash: 3fd1c262a8ce94d8e98f2b066a8028d974b15b1d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "23927721"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att visa jobb för den virtuella StorSimple-matrisen
## <a name="overview"></a>Översikt
Den **jobb** bladet innehåller en enda central portal för att visa och hantera jobb som har startats på virtuella lagringsmatriser som är anslutna till din StorSimple Device Manager-tjänst. Du kan visa körs slutförda och misslyckade jobb för flera virtuella enheter. Resultat visas i tabellformat.

![Jobb-bladet](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Du kan snabbt hitta de jobb som du är intresserad av genom att filtrera på fält som:

* **Tidsintervallet** – jobb kan filtreras baserat på det datum och tid intervallet.
* **Enheter** – jobb startas på en specifik enhet ansluten till din tjänst. Filtrerade jobben visas sedan som en tabell baserat på följande attribut:
  
  * **Namnet** – Jobbnamnet kan vara **alla**, **säkerhetskopiering**, **klona**, **Redundansväxla**, **hämta uppdateringar**, eller **installera uppdateringar**.
  * **Status för** – jobb kan vara **alla**, **pågår**, **lyckades**, eller **misslyckades**, eller **avbruten**.
  * **Entiteten** – jobb kan vara kopplad till en volym, resurs eller enhet.
  * **Enheten** – namnet på den enhet som då jobbet startades.
  * **Startats på** – den tid då jobbet startades.
  * **Varaktighet** – varaktighet för som jobbet körs.
* **Status för** – du kan söka efter alla, körs slutförda och misslyckade jobb.
* **Jobbtyp** – jobbtypen kan all, säkerhetskopiering, återställning, redundans, ladda ned uppdateringar, eller installera uppdateringar.

Lista över jobb uppdateras var 30: e sekund.

## <a name="view-job-details"></a>Visa jobbinformation
Utför följande steg om du vill visa information om jobb.

#### <a name="to-view-job-details"></a>Visa jobbinformation
1. På den **jobb** bladet visar de jobb som du är intresserad av genom att köra en fråga med lämpliga filter. Du kan söka efter slutförda eller inte körs jobb.
2. Välj ett jobb i tabellform listan över jobb.
   
    ![Jobb-bladet](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Längst ned på sidan klickar du på **information**.
4. I den **information** dialogrutan kan du visa status, detaljer och statistik. Följande bild visar ett exempel på den **säkerhetskopiering jobbinformation** dialogrutan.
   
    ![Jobbinformation](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Jobbfel när den virtuella datorn är pausad i hypervisor-programmet
När ett jobb är i statusen för din virtuella StorSimple-matris och enhet (virtuell dator etablerats i hypervisor-program) har pausats av fler än 15 minuter, jobbet har misslyckats. Detta på grund av din virtuella StorSimple-matris tid inte är synkroniserad med Microsoft Azure-tid. 

Följande felmeddelande visas: ”din enhet tid är inte synkroniserat med Microsoft Azure-tid med mer än 15 minuter. Se till att hypervisor-programmet och enheten gånger synkroniseras med en NTP-servern. Kontrollera att det inte finns några anslutningsproblem. Om du vill felsöka problem med nätverksanslutningen, kör diagnostiktest från lokala webbgränssnittet för din virtuella enhet ”.

Dessa fel gäller för säkerhetskopiering, återställning, uppdatering och växling vid fel jobb. Om den virtuella datorn har etablerats i Hyper-V, synkroniserar datorn slutligen tid med din hypervisor. När det händer kan du starta om jobbet.

## <a name="next-steps"></a>Nästa steg
[Lär dig hur du använder lokala webbgränssnittet för att administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

