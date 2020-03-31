---
title: Visa och hantera jobb för StorSimple 8000-serien | Microsoft-dokument
description: Beskriver bladet StorSimple Device Manager-tjänst jobb och hur du använder det för att spåra senaste, aktuella och schemalagda säkerhetskopieringsjobb.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 462f8dafdffa7ee01e6ccf7945a1abfdff90db42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254877"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Använda Tjänsten StorSimple Device Manager för att visa och hantera jobb (uppdatering 3 och senare)

## <a name="overview"></a>Översikt
**Jobbbladet** tillhandahåller en enda central portal för visning och hantering av jobb som har startats på enheter som är anslutna till Tjänsten StorSimple Device Manager. Du kan visa schemalagda, köra, slutförda, avbrutna och misslyckade jobb för flera enheter. Resultaten presenteras i tabellformat.

![Jobb blad](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Du kan snabbt hitta de jobb du är intresserad av genom att filtrera på fält som:

* **Status** – Jobb kan vara pågående, lyckades, avbrytas, misslyckades, avbryts eller lyckades med fel.
* **Tidsintervall** – Jobb kan filtreras baserat på datum och tidsintervall. Intervallen är senaste 1 timme, senaste 24 timmarna, senaste 7 dagarna, senaste 30 dagarna, det senaste året eller anpassat datum.
* **Typ** – Jobbtypen kan schemaläggas säkerhetskopiering, manuell säkerhetskopiering, återställa säkerhetskopiering, klona volym, felöverstringsvolymbehållare, skapa lokalt fäst volym, ändra volym, installera uppdateringar, samla in supportloggar och skapa molninstallation.
* **Enheter** – Jobb initieras på en viss enhet som är ansluten till din tjänst.
  
De filtrerade jobben ta sedanbuleras på grundval av följande attribut:
  
* **Namn** – schemalagd säkerhetskopiering, manuell säkerhetskopiering, återställning av säkerhetskopiering, klonvolym, redundansvolymbehållare, skapa lokalt fäst volym, ändra volym, installera uppdateringar, samla in supportloggar eller skapa molninstallation.
* **Status** – köra, slutföras, avbröts, misslyckades, avbröt eller slutförs med fel.
* **Entitet** – Jobben kan associeras med en volym, en säkerhetskopieringsprincip eller en enhet. Ett klonjobb associeras till exempel med en volym, medan ett schemalagt säkerhetskopieringsjobb är associerat med en säkerhetskopieringsprincip. Ett enhetsjobb skapas som ett resultat av en haveriberedskap (DR) eller en återställningsåtgärd.
* **Enhet** – Namnet på den enhet där jobbet startades.
* **Började** på – Den tid då jobbet startades.
* **Varaktighet** – Den tid som krävs för att slutföra jobbet.

Listan över jobb uppdateras var 30:e sekund.

Du kan utföra följande jobbrelaterade åtgärder på den här sidan:

* Visa jobbinformation
* Avbryta ett jobb

## <a name="view-job-details"></a>Visa jobbinformation
Gör följande för att visa information om ett jobb.

#### <a name="to-view-job-details"></a>Så här visar du jobbinformation
1. Gå till tjänsten StorSimple Device Manager och klicka sedan på **Jobb**.

2. I bladet **Jobb** visar du de jobb som du är intresserad av genom att köra en fråga med lämpliga filter. Du kan söka efter slutförda, köra eller avbrutna jobb.

    ![Jobbblad](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Markera och klicka på ett jobb.

    ![Jobbblad](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. I jobbinformationsbladet kan du visa status, information, tidsstatistik och datastatistik.
   
    ![Information om jobb](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Avbryta ett jobb
Gör följande steg för att avbryta ett jobb som körs.

> [!NOTE]
> Vissa jobb, till exempel att ändra en volym för att ändra volymtypen eller expandera en volym, kan inte avbrytas.


### <a name="to-cancel-a-job"></a>Så här avbryter du ett jobb
1. På sidan **Jobb** visar du de jobb som körs som du vill avbryta genom att köra en fråga med lämpliga filter. Välj jobbet.

2. Högerklicka på det valda jobbet om du vill anropa snabbmenyn och klicka på **Avbryt**.

    ![Information om jobb](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Det här jobbet är nu inställt.

## <a name="next-steps"></a>Nästa steg
* Läs om hur du hanterar dina principer för [säkerhetskopiering i StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Lär dig hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

