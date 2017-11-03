---
title: "StorSimple Snapshot Manager säkerhetskopieringsjobb | Microsoft Docs"
description: "Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen för att visa och hantera schemalagda pågående och slutförda säkerhetskopieringsjobb."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 03e306b62250f2bb033cc14e856a59760b5406c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Använd StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb

## <a name="overview"></a>Översikt
Den **jobb** nod i den **omfång** visas i åtgärdsfönstret i **schemalagda**, **senaste 24 timmarna**, och **kör** säkerhetskopiera uppgifter som du har initierat interaktivt eller av en konfigurerade principen. 

Den här självstudiekursen beskrivs hur du kan använda den **jobb** noder för att visa information om schemalagda senaste och pågående säkerhetskopieringsjobb. (Lista över jobb och motsvarande information visas i den **resultat** fönstret.) Du kan dessutom högerklickar du på ett listade jobb och se en snabbmeny som visar tillgängliga åtgärder.

## <a name="view-scheduled-jobs"></a>Visa schemalagda jobb
Använd följande procedur om du vill visa schemalagda säkerhetskopieringsjobb.

#### <a name="to-view-scheduled-jobs"></a>Visa schemalagda jobb
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager. 
2. I den **omfång** rutan Expandera den **jobb** och klicka **schemalagda**. Följande information visas i den **resultat** fönstret:
   
   * **Namnet** – namnet på den schemalagda ögonblicksbilden
   * **Kör nästa gång** – datum och tid för nästa schemalagda ögonblicksbild
   * **Senaste körning** – datum och tid för senaste schemalagda ögonblicksbild
     
     > [!NOTE]
     > För en enda ögonblicksbilder, den **nästa** och **senaste körning** densamma.
     
     ![Schemalagda säkerhetskopieringsjobb](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Om du vill utföra ytterligare åtgärder på ett specifikt jobb, högerklickar du på jobbnamnet i den **resultat** fönstret och välj bland menyalternativen.

## <a name="view-recent-jobs"></a>Visa senaste jobb
Använd följande procedur för att visa säkerhetskopiering och återställning jobb som har slutförts under de senaste 24 timmarna.

#### <a name="to-view-recent-jobs"></a>Visa senaste jobb
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan Expandera den **jobb** och klicka **senaste 24 timmarna**. Den **resultat** visar säkerhetskopieringsjobb för de senaste 24 timmarna (till högst 64 jobb). Följande information visas i den **resultat** rutan, beroende på den **visa** alternativ som du anger:
   
   * **Namnet** – namnet på den schemalagda ögonblicksbilden.
   * **Starta** – datum och tid då ögonblicksbilden påbörjades.
   * **Stoppats** – datum och tid när ögonblicksbilden slutförts eller avslutats.
   * **Förfluten tid** – hur lång tid mellan den **igång** och **stoppad** gånger.
   * **Status för** – nyligen utförda jobbets tillstånd. **Lyckade** anger att säkerhetskopieringen skapades. **Det gick inte** anger att jobbet inte kördes.
   * **Information** – orsaken till felet.
   * **Byte bearbetas (MB)** – hur mycket data från gruppen volym som har bearbetats (i MB). 
     
     ![Jobb som körts under de senaste 24 timmarna](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Om du vill utföra ytterligare åtgärder på ett specifikt jobb, högerklickar du på jobbnamnet i den **resultat** fönstret och välj bland menyalternativen.
   
    ![Ta bort ett jobb](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Visa pågående jobb
Använd följande procedur för att visa jobb som körs för närvarande.

#### <a name="to-view-currently-running-jobs"></a>Visa pågående jobb
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan Expandera den **jobb** och klicka **kör**. Beroende på den **visa** alternativ som du anger följande information visas i den **resultat** fönstret:
   
   * **Namnet** – namnet på den schemalagda ögonblicksbilden.
   * **Starta** – datum och tid då ögonblicksbilden påbörjades.
   * **Kontrollpunkt** – den aktuella åtgärden på säkerhetskopian.
   * **Status för** – procent färdigt.
   * **Förfluten tid** – hur lång tid som har gått sedan säkerhetskopieringen påbörjades. 
   * **Genomsnittlig genomströmning (MB)** – förhållandet mellan totalt antal behandlade den totala tid som krävs för bearbetning av (MB) byte.
   * **Byte bearbetas (MB)** – Totalt antal byte av data som bearbetas (i MB).
   * **Byte som skrivits (MB)** – Totalt antal byte skrevs (i MB). Den innehåller både data och metadata och därför är vanligtvis större än den byte som bearbetas.
     
     ![Jobb som körs](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Om du vill utföra ytterligare åtgärder på ett specifikt jobb, högerklickar du på jobbnamnet i den **resultat** fönstret och välj bland menyalternativen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att hantera katalogen för säkerhetskopieringen](storsimple-snapshot-manager-manage-backup-catalog.md).

