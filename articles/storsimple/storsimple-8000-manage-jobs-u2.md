---
title: Visa och hantera jobb för StorSimple 8000-serien | Microsoft Docs
description: Beskriver jobb bladet för StorSimple Device Manager-tjänsten och hur du använder den för att spåra de senaste aktuella och schemalagda säkerhetskopieringsjobb.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506221"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Använda StorSimple Device Manager-tjänsten för att visa och hantera jobb (uppdatering 3 och senare)

## <a name="overview"></a>Översikt
Den **jobb** bladet ger en enda central portal för visning och hantera jobb som har startats på enheter som är ansluten till din StorSimple Device Manager-tjänsten. Du kan visa schemalagda, som körs, slutförda, avbrutna och misslyckade jobb för flera enheter. Resultaten visas i tabellformat.

![Jobb-bladet](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Du kan snabbt hitta de jobb som du är intresserad av genom att filtrera på fält som:

* **Status för** – jobb kan vara pågår, lyckades, avbröts, misslyckade, stoppar eller lyckades med fel.
* **Tidsintervall** – jobb kan filtreras baserat på det datum och tid-intervallet. Intervall är senaste 1 timmen eller senaste 24 timmarna, senaste 7 dagarna, de senaste 30 dagarna, senaste året eller anpassat datum.
* **Typ** – jobbtypen kan vara schemalagd säkerhetskopiering, manuell säkerhetskopiering, återställning, säkerhetskopiering, klona volym, redundansväxla volymbehållare, skapa lokalt fixerade volymen, ändra volymen, installera uppdateringar, samla in loggar för support och skapa molninstallation.
* **Enheter** – jobb startas på en viss enhet som är ansluten till din tjänst.
  
Filtrerade jobben visas sedan som en tabell på grundval av följande attribut:
  
* **Namn på** – schemalagd säkerhetskopiering, manuell säkerhetskopiering, återställning, säkerhetskopiering, klona volymen, redundansväxla volymbehållare, skapa lokalt fixerad volym, ändra volym, installera uppdateringar, samla in loggar för support eller skapa molninstallation.
* **Status för** – som körs, slutförda, avbrutna, misslyckade, stoppar eller slutfördes med fel.
* **Entiteten** – jobb kan associeras med en volym, en princip för säkerhetskopiering eller en enhet. Till exempel är en kloningsjobb associerad med en volym, en schemalagd säkerhetskopiering är associerad med en princip för säkerhetskopiering. Ett enhetsjobb skapas till följd av en katastrofåterställning (DR) eller en återställning.
* **Enheten** – med namnet på den enhet som då jobbet startades.
* **Startad** – tiden då jobbet startades.
* **Varaktighet** – tiden som krävs för att slutföra jobbet.

Listan över jobb uppdateras var 30: e sekund.

Du kan utföra följande åtgärder för jobbrelaterade på den här sidan:

* Visa jobbinformation
* Avbryta ett jobb

## <a name="view-job-details"></a>Visa jobbinformation
Utför följande steg om du vill visa information om alla jobb.

#### <a name="to-view-job-details"></a>Visa jobbinformation
1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **jobb**.

2. I den **jobb** bladet visar de jobb som du är intresserad av genom att köra en fråga med lämpliga filter. Du kan söka efter klar körs eller har avbrutits jobb.

    ![Jobb-bladet](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Välj och klicka på ett jobb.

    ![Jobb-bladet](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. På bladet jobb, kan du visa status, information, statistik och statistik för data.
   
    ![Information om jobb](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Avbryta ett jobb
Utför följande steg om du vill avbryta ett jobb som körs.

> [!NOTE]
> Kan inte annulleras vissa jobb, till exempel ändra en volym för att ändra volymtyp eller expandera en volym.


### <a name="to-cancel-a-job"></a>Avbryta ett jobb
1. På den **jobb** sidan, visa pågående jobb som du vill avbryta genom att köra en fråga med lämpliga filter. Välj jobbet.

2. Högerklicka på det valda jobbet att öppna snabbmenyn och klickar på **Avbryt**.

    ![Information om jobb](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Det här jobbet avbryts nu.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hantera din StorSimple-säkerhetskopieringsprinciper](storsimple-8000-manage-backup-policies-u2.md).
* Lär dig hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

