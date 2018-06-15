---
title: Visa och hantera jobb för StorSimple 8000-serien | Microsoft Docs
description: Beskriver StorSimple Enhetshanteraren service jobb-bladet och hur du använder den för att spåra senaste aktuella och schemalagda säkerhetskopieringsjobb.
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
ms.openlocfilehash: bf8038b7171053b75eeb9aed88bff4246e65a8a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874817"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att visa och hantera jobb (uppdatering 3 och senare)

## <a name="overview"></a>Översikt
Den **jobb** bladet innehåller en enda central portal för visning och hantera jobb som har startats på enheter anslutna till din StorSimple Device Manager-tjänst. Du kan visa schemalagda, körs, slutfört, avbrutet och misslyckade jobb för flera enheter. Resultat visas i tabellformat.

![Jobb-bladet](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Du kan snabbt hitta de jobb som du är intresserad av genom att filtrera på fält som:

* **Status för** – jobb kan vara pågår, lyckades, avbröts, misslyckades, stoppar eller har slutförts med fel.
* **Tidsintervallet** – jobb kan filtreras baserat på det datum och tid intervallet. Intervall som har passerat 1 timme, senaste 24 timmarna, de senaste 7 dagarna efter 30 dagar, senaste året eller anpassade datum.
* **Typen** – jobbtypen kan vara schemalagd säkerhetskopiering, manuell säkerhetskopiering, återställning, säkerhetskopiering, klona volymen, växla över volymbehållare, skapa lokalt Fäst volym, ändra volymen, installera uppdateringar, samla in loggar för support och skapa moln-enhet.
* **Enheter** – jobb startas på en viss enhet ansluten till din tjänst.
  
Filtrerade jobben visas sedan som en tabell på grundval av följande attribut:
  
* **Namnet** – schemalagd säkerhetskopiering, manuell säkerhetskopiering, återställning, säkerhetskopiering, klona volym växla över volymbehållare, skapar lokalt Fäst volym, ändra volymen, installera uppdateringar, samla in loggar för stöd eller skapa moln-enhet.
* **Status för** – körs, slutförda, avbrutna, misslyckades, stoppar eller slutfördes med fel.
* **Entiteten** – jobb kan vara kopplad till en volym, en princip för säkerhetskopiering eller en enhet. Till exempel associeras ett jobb för klon med en volym, en schemalagd säkerhetskopiering är associerad med en princip för säkerhetskopiering. En enhet jobb skapas på grund av en katastrofåterställning (DR) eller en återställning.
* **Enheten** – namnet på den enhet som då jobbet startades.
* **Startats på** – den tid då jobbet startades.
* **Varaktighet** – den tid som krävs för att slutföra jobbet.

Lista över jobb uppdateras var 30: e sekund.

Du kan utföra följande åtgärder för jobbrelaterade på den här sidan:

* Visa jobbinformation
* Avbryta ett jobb

## <a name="view-job-details"></a>Visa jobbinformation
Utför följande steg om du vill visa information om jobb.

#### <a name="to-view-job-details"></a>Visa jobbinformation
1. Gå till Enhetshanteraren för StorSimple-tjänsten och klicka sedan på **jobb**.

2. I den **jobb** bladet visar de jobb som du är intresserad av genom att köra en fråga med lämpliga filter. Du kan söka för slutförd, kör eller avbröts jobb.

    ![Jobb-bladet](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Välj och klicka på ett jobb.

    ![Jobb-bladet](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. I bladet jobbet information kan du visa status, information, statistik och data statistik.
   
    ![Jobbinformation](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Avbryta ett jobb
Utför följande steg om du vill avbryta ett jobb som körs.

> [!NOTE]
> Kan inte annulleras vissa jobb, t.ex ändra en volym om du vill ändra volymtypen eller expandera en volym.


### <a name="to-cancel-a-job"></a>Avbryta ett jobb
1. På den **jobb** sidan, visa pågående jobb som du vill avbryta genom att köra en fråga med lämpliga filter. Markera jobbet.

2. Högerklicka på det valda jobbet att anropa snabbmenyn och klicka på **Avbryt**.

    ![Jobbinformation](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Det här jobbet avbryts nu.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hantera din StorSimple-säkerhetskopieringsprinciper](storsimple-8000-manage-backup-policies-u2.md).
* Lär dig hur du [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

