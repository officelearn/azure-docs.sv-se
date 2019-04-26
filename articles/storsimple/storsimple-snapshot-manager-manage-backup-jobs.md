---
title: StorSimple Snapshot Manager säkerhetskopieringsjobb | Microsoft Docs
description: Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen för att visa och hantera schemalagda pågående och slutförda jobb.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: c34ff487f03d90b16b6660fbad77c3a16699e165
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303493"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Använd StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb

## <a name="overview"></a>Översikt
Den **jobb** nod i den **omfång** fönstret visar den **schemalagd**, **senaste 24 timmarna**, och **kör** säkerhetskopieringsaktiviteter som du har initierat interaktivt eller genom en konfigurerade principen. 

Den här självstudien beskrivs hur du kan använda den **jobb** noder för att visa information om schemalagda, senaste och pågående säkerhetskopieringsjobb. (Listan över jobb och motsvarande information visas i den **resultat** fönstret.) Dessutom kan du högerklicka på ett listade jobb och visa en snabbmeny som visar en lista över tillgängliga åtgärder.

## <a name="view-scheduled-jobs"></a>Visa schemalagda jobb
Använd följande procedur om du vill visa schemalagda säkerhetskopieringsjobb.

#### <a name="to-view-scheduled-jobs"></a>Visa schemalagda jobb
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager. 
2. I den **omfång** fönstret expanderar den **jobb** noden och klicka på **schemalagd**. Följande information visas i den **resultat** fönstret:
   
   * **Namn på** – namnet på den schemalagda ögonblicksbilden
   * **Nästa körning** – datumet och tiden för nästa schemalagda ögonblicksbild
   * **Senaste körning** – datum och tid för senaste schemalagda ögonblicksbild
     
     > [!NOTE]
     > För en enda ögonblicksbilder, den **nästa** och **senaste kör** kommer att vara samma.
     
     ![Schemalagda säkerhetskopieringsjobb](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Om du vill utföra ytterligare åtgärder på ett specifikt jobb, högerklickar du på jobbnamnet i den **resultat** rutan och välj i menyalternativen.

## <a name="view-recent-jobs"></a>Visa senaste jobb
Använda följande procedur för att visa säkerhetskopiering och Återställ jobb som har slutförts under de senaste 24 timmarna.

#### <a name="to-view-recent-jobs"></a>Visa senaste jobb
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret expanderar den **jobb** noden och klicka på **senaste 24 timmarna**. Den **resultat** fönstret visar säkerhetskopieringsjobb för de senaste 24 timmarna (till högst 64 jobb). Följande information visas i den **resultat** fönstret, beroende på den **visa** alternativ som du anger:
   
   * **Namn på** – namnet på den schemalagda ögonblicksbilden.
   * **Igång** – datumet och tiden när ögonblicksbilden började.
   * **Stoppad** – datumet och tiden när ögonblicksbilden är klar eller har avslutats.
   * **Förfluten tid** – hur lång tid mellan den **startad** och **stoppad** gånger.
   * **Status för** – nyligen utförda jobbets tillstånd. **Lyckade** anger att säkerhetskopian skapades. **Det gick inte** anger att jobbet inte kördes.
   * **Information** – orsaken till felet.
   * **Byte bearbetas (MB)** – hur mycket data från gruppen volym som har bearbetats (i MB). 
     
     ![Jobb som körts under de senaste 24 timmarna](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Om du vill utföra ytterligare åtgärder på ett specifikt jobb, högerklickar du på jobbnamnet i den **resultat** rutan och välj i menyalternativen.
   
    ![Ta bort ett jobb](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Visa pågående jobb
Använd följande procedur för att visa jobb som körs för närvarande.

#### <a name="to-view-currently-running-jobs"></a>Visa pågående jobb
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret expanderar den **jobb** noden och klicka på **kör**. Beroende på den **visa** alternativ som du anger följande information visas i den **resultat** fönstret:
   
   * **Namn på** – namnet på den schemalagda ögonblicksbilden.
   * **Igång** – datumet och tiden när ögonblicksbilden började.
   * **Kontrollpunkt** – den aktuella åtgärden av säkerhetskopian.
   * **Status för** – procent färdigt.
   * **Förfluten tid** – hur lång tid som har gått sedan säkerhetskopieringen påbörjades. 
   * **Genomsnittligt dataflöde (MB)** – förhållandet mellan totalt antal byte som bearbetas med total tid för bearbetning av (MB).
   * **Byte bearbetas (MB)** – Totalt antal byte av data som bearbetas (i MB).
   * **Byte som skrivs (MB)** – Totalt antal byte data som skrivs (i MB). Den inkluderar data samt metadata och är därför vanligtvis är större än den byte som bearbetas.
     
     ![Jobb som körs](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Om du vill utföra ytterligare åtgärder på ett specifikt jobb, högerklickar du på jobbnamnet i den **resultat** rutan och välj i menyalternativen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att hantera säkerhetskopieringskatalogen](storsimple-snapshot-manager-manage-backup-catalog.md).

