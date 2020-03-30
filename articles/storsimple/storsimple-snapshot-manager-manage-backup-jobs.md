---
title: Säkerhetskopieringsjobb för StorSimple Snapshot Manager | Microsoft-dokument
description: Beskriver hur du använder MMC-snapin-modulen StorSimple Snapshot Manager för att visa och hantera schemalagda, körs och slutförda säkerhetskopieringsjobb.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 094b996cd3227903995c7a74ef14ed8c0561f59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933340"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Använda StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb

## <a name="overview"></a>Översikt
Noden **Jobb** i fönstret **Scope** visar de **schemalagda,** **senaste 24 timmarna**och **Säkerhetskopieringsaktiviteterna som** du initierade interaktivt eller av en konfigurerad princip. 

I den här självstudien beskrivs hur du kan använda noden **Jobb** för att visa information om schemalagda, senaste och senaste säkerhetskopieringsjobb. (Listan över jobb och motsvarande **Results** information visas i resultatfönstret.) Dessutom kan du högerklicka på ett jobb i listan och se en snabbmeny med en lista över tillgängliga åtgärder.

## <a name="view-scheduled-jobs"></a>Visa schemalagda jobb
Använd följande procedur för att visa schemalagda säkerhetskopieringsjobb.

#### <a name="to-view-scheduled-jobs"></a>Så här visar du schemalagda jobb
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager. 
2. Expandera noden **Jobb** i fönstret **Omfattning** och klicka på **Schemalagd**. Följande information visas i **resultatfönstret:**
   
   * **Namn** – namnet på den schemalagda ögonblicksbilden
   * **Nästa körning** – datum och tid för nästa schemalagda ögonblicksbild
   * **Senaste körningen** – datum och tid för den senaste schemalagda ögonblicksbilden
     
     > [!NOTE]
     > För engångsögonblicksbilder kommer **nästa körning** och sista **körning** att vara desamma.
     
     ![Schemalagda säkerhetskopieringsjobb](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Om du vill utföra ytterligare åtgärder på ett visst jobb högerklickar du på jobbnamnet i **resultatfönstret** och väljer från menyalternativen.

## <a name="view-recent-jobs"></a>Visa de senaste jobben
Använd följande procedur för att visa säkerhetskopierings- och återställningsjobb som har slutförts under de senaste 24 timmarna.

#### <a name="to-view-recent-jobs"></a>Så här visar du nya jobb
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Expandera noden **Jobb** i fönstret **Omfattning** och klicka på **Senaste 24 timmarna**. I **fönstret Resultat** visas säkerhetskopieringsjobb för de senaste 24 timmarna (till högst 64 jobb). Följande information visas i **resultatfönstret,** beroende på vilka **visningsalternativ** du anger:
   
   * **Namn** – namnet på den schemalagda ögonblicksbilden.
   * **Startad** – datum och tid då ögonblicksbilden började.
   * **Stoppad** – datum och tid då ögonblicksbilden avslutades eller avslutades.
   * **Förfluten** – hur lång tid mellan **start-** och **stopptiderna.**
   * **Status** – tillståndet för det nyligen slutförda jobbet. **Lyckades** indikera att säkerhetskopian har skapats. **Det** gick inte att ange att jobbet inte kördes.
   * **Information** – orsaken till felet.
   * **Bearbetade byte (MB)** – mängden data från volymgruppen som bearbetades (i MBs). 
     
     ![Jobb som har körts de senaste 24 timmarna](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Om du vill utföra ytterligare åtgärder på ett visst jobb högerklickar du på jobbnamnet i **resultatfönstret** och väljer från menyalternativen.
   
    ![Ta bort ett jobb](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Visa jobb som körs för tillfället
Använd följande procedur för att visa jobb som körs för tillfället.

#### <a name="to-view-currently-running-jobs"></a>Så här visar du jobb som körs för närvarande
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Expandera noden **Jobb** i fönstret **Omfattning** och klicka på **Kör**. Beroende på vilka **visningsalternativ** du anger visas följande information i **resultatfönstret:**
   
   * **Namn** – namnet på den schemalagda ögonblicksbilden.
   * **Startad** – datum och tid då ögonblicksbilden började.
   * **Kontrollpunkt** – den aktuella åtgärden för säkerhetskopian.
   * **Status** – slutförandeprocenten.
   * **Förflutit** – den tid som har gått sedan säkerhetskopieringen började. 
   * **Genomsnittligt dataflöde (MB)** – förhållandet mellan totala byte av data som bearbetats till den totala tid som tagits för bearbetning (MBs).
   * **Bearbetade byte (MB)** – totalt antal data som bearbetas (i MBs).
   * **Antal skrivna byte (MB)** – totalt antal data som skrivits (i MB). Den innehåller data samt metadata och är därför vanligtvis större än de bearbetade bytena.
     
     ![Jobb som körs](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Om du vill utföra ytterligare åtgärder på ett visst jobb högerklickar du på jobbnamnet i **resultatfönstret** och väljer från menyalternativen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att hantera säkerhetskopieringskatalogen](storsimple-snapshot-manager-manage-backup-catalog.md).

