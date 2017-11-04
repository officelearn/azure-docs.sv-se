---
title: Visa och hantera StorSimple jobb | Microsoft Docs
description: "Beskriver sidan StorSimple Manager-jobb och hur du använder den för att spåra senaste aktuella och schemalagda säkerhetskopieringsjobb."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 55922cd0-d490-48eb-938a-012a67c1c09e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 7d9377bb8f3cb8c587823c2d71d61dfb9b50f52f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>Använda StorSimple Manager-tjänsten för att visa och hantera jobb för StorSimple
[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Översikt
Den **jobb** innehåller en enda central portal för visning och hantera jobb som har startats på enheter anslutna till din StorSimple Manager-tjänsten. Du kan visa schemalagda, körs, slutförda och misslyckade jobb för flera enheter. Resultat visas i tabellformat. 

![Sidan jobb](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Du kan snabbt hitta de jobb som du är intresserad av genom att filtrera på fält som:

* **Status för** – jobb kan köras schemalagda misslyckades, slutförda, stoppar eller avbröts.
* **Typen** – jobb kan skapas på grund av en schemalagd eller en säkerhetskopiering på begäran (**ta säkerhetskopiering**), kloning, en återställning av enheten, eller en uppdatering.
* **Enheter** – jobb startas på en viss enhet ansluten till din tjänst.
* **Från och till** – jobb kan filtreras baserat på det datum och tid intervallet.

Filtrerade jobben visas sedan som en tabell på grundval av följande attribut:

* **Typen** – säkerhetskopiering, kloning, återställning, redundans eller update.
* **Status för** – kör schemalagda, misslyckades, slutfört, avbryter eller avbrutits.
* **Entiteten** – jobb kan vara kopplad till en volym, en princip för säkerhetskopiering eller en enhet. Ett jobb för klon är kopplat till en volym, en schemalagd säkerhetskopiering är associerad med en princip för säkerhetskopiering. En enhet jobb skapas på grund av en katastrofåterställning (DR) eller en återställning.
* **Enheten** – namnet på den enhet som då jobbet startades.
* **Igång på** – den tid då jobbet startades.
* **Förlopp** – procentandel slutförandet av ett jobb som körs. Detta bör alltid vara 100% för slutförda jobb.

Lista över jobb uppdateras var 30: e sekund.

Du kan utföra följande åtgärder för jobbrelaterade på den här sidan:

* Visa jobbinformation
* Avbryta ett jobb

## <a name="view-job-details"></a>Visa jobbinformation
Utför följande steg om du vill visa information om jobb.

#### <a name="to-view-job-details"></a>Visa jobbinformation
1. På den **jobb** kan du visa de jobb som du är intresserad av genom att köra en fråga med lämpliga filter. Du kan söka för slutförd, kör eller avbröts jobb.
2. Välj ett jobb.
3. Längst ned på sidan klickar du på **information**.
4. I den **säkerhetskopiering jobbinformation** dialogrutan kan du visa status, information, statistik och data statistik.

## <a name="cancel-a-job"></a>Avbryta ett jobb
Utför följande steg om du vill avbryta ett jobb som körs.

### <a name="to-cancel-a-job"></a>Avbryta ett jobb
1. På den **jobb** sidan, visa pågående jobb som du vill avbryta genom att köra en fråga med lämpliga filter.
2. Markera jobbet.
3. Längst ned på sidan klickar du på **Avbryt**.
4. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

Det här jobbet avbryts nu.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hantera din StorSimple-säkerhetskopieringsprinciper](storsimple-manage-backup-policies.md).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

