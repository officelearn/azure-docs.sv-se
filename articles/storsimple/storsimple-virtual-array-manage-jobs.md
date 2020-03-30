---
title: Visa och hantera StorSimple Virtual Array-jobb | Microsoft-dokument
description: Beskriver tjänsten StorSimple-tjänsthanterarens tjänst jobb och hur du använder den för att spåra senaste och aktuella jobb för StorSimple Virtual Array.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60302507"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Använda Tjänsten StorSimple Device Manager för att visa jobb för den virtuella storsimple-matrisen
## <a name="overview"></a>Översikt
**Jobbbladet** tillhandahåller en enda central portal för visning och hantering av jobb som startas på virtuella matriser som är anslutna till Tjänsten StorSimple Device Manager. Du kan visa jobb som körs, slutförs och misslyckades för flera virtuella enheter. Resultaten presenteras i tabellformat.

![Jobb blad](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Du kan snabbt hitta de jobb du är intresserad av genom att filtrera på fält som:

* **Tidsintervall** – Jobb kan filtreras baserat på datum och tidsintervall.
* **Enheter** – Jobb initieras på en viss enhet som är ansluten till din tjänst. De filtrerade jobben tabuleras sedan baserat på följande attribut:
  
  * **Namn** – Jobbnamnet kan vara **Alla**, **Säkerhetskopiering,** **Klona**, **Växla över,** **Hämta uppdateringar**eller **Installera uppdateringar**.
  * **Status** – Jobb kan vara **Alla,** **Pågår,** **Lyckades**eller **Misslyckades**eller **Avbröt .**
  * **Entitet** – Jobben kan associeras med en volym, resurs eller enhet.
  * **Enhet** – Namnet på den enhet där jobbet startades.
  * **Började** på – Den tid då jobbet startades.
  * **Varaktighet** – Varaktigheten för jobbet som jobbet kördes på.
* **Status** – Du kan söka efter alla jobb som körs, har slutförts eller misslyckats.
* **Jobbtyp** – Jobbtypen kan vara allt, säkerhetskopiera, återställa, redundans, hämta uppdateringar eller installera uppdateringar.

Listan över jobb uppdateras var 30:e sekund.

## <a name="view-job-details"></a>Visa jobbinformation
Gör följande för att visa information om ett jobb.

#### <a name="to-view-job-details"></a>Så här visar du jobbinformation
1. På bladet **Jobb** visar du de jobb som du är intresserad av genom att köra en fråga med lämpliga filter. Du kan söka efter slutförda eller pågående jobb.
2. Välj ett jobb i tabelllistan med jobb.
   
    ![Jobbblad](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Klicka på **Detaljer**längst ned på sidan .
4. I dialogrutan **Information** kan du visa status, information och tidsstatistik. Följande bild visar ett exempel på dialogrutan **Information om säkerhetskopieringsjobb.**
   
    ![Information om jobb](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Jobbfel när den virtuella datorn pausas i hypervisorn
När ett jobb pågår på den virtuella storsimple-matrisen och enheten (virtuell dator som etableras i hypervisor) pausas i mer än 15 minuter misslyckas jobbet. Detta beror på att din StorSimple Virtual Array-tid är osynkroniserad med Microsoft Azure-tiden. 

Följande felmeddelande visas: "Din enhetstid är inte synkroniserad med Microsoft Azure-tiden med mer än 15 minuter. Kontrollera att hypervisorn och enhetstiderna synkroniseras med en NTP-server. Kontrollera att det inte finns några anslutningsproblem. Om du vill felsöka anslutningsproblem kör du diagnostiska tester från det lokala webbgränssnittet på den virtuella enheten."

Dessa fel gäller för säkerhetskopierings-, återställnings-, uppdaterings- och redundansjobb. Om den virtuella datorn etableras i Hyper-V synkroniseras datorn så småningom tiden med hypervisorn. När det händer kan du starta om jobbet.

## <a name="next-steps"></a>Nästa steg
[Lär dig hur du använder det lokala webbgränssnittet för att administrera din StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

