---
title: StorSimple Snapshot Manager säkerhetskopieringskatalogen | Microsoft Docs
description: Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen för att visa och hantera säkerhetskopieringskatalogen.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: dc24ebd59fd977ef35766c304aec5824e2c7bb4c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127200"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Använd StorSimple Snapshot Manager för att hantera säkerhetskopieringskatalogen

## <a name="overview"></a>Översikt
Den främsta uppgiften för StorSimple Snapshot Manager är så att du kan skapa programkonsekventa säkerhetskopior av StorSimple-volymer i form av ögonblicksbilder. Ögonblicksbilder visas sedan i en XML-fil som kallas en *säkerhetskopieringskatalogen*. Säkerhetskopieringskatalogen organiserar ögonblicksbilder av volymen grupp och sedan efter lokal ögonblicksbild eller ögonblicksbild i molnet.

Den här självstudien beskrivs hur du kan använda den **säkerhetskopieringskatalog** noden för att utföra följande åtgärder:

* Återställa en volym
* Klona en volym eller en volym grupp
* Ta bort en säkerhetskopia
* Återställa en fil
* Återställ Storsimple Snapshot Manager-databasen

Du kan visa den säkerhetskopiera katalogen genom att expandera den **säkerhetskopieringskatalogen** nod i den **omfång** fönstret och sedan utöka gruppen volym.

* Om du klickar på volymen gruppnamn, den **resultat** visar antalet lokala ögonblicksbilder och molnögonblicksbilder som är tillgängliga för gruppen volym. 
* Om du klickar på **lokal ögonblicksbild** eller **Molnögonblicksbild**, **resultat** visar följande information om varje ögonblicksbild (beroende på din  **Visa** inställningar):
  
  * **Namn på** – tiden ögonblicksbilden togs.
  * **Typ** – om det här är en lokal ögonblicksbild eller en ögonblicksbild i molnet.
  * **Ägare** – innehållets ägare. 
  * **Tillgängliga** – om ögonblicksbilden är tillgänglig för tillfället. **SANT** anger att ögonblicksbilden är tillgängligt och kan återställas. **FALSKT** anger ögonblicksbilden är inte längre tillgänglig. 
  * **Importerade** – om säkerhetskopieringen har importerats. **SANT** anger att säkerhetskopieringen har importerats från StorSimple Device Manager-tjänsten när enheten konfigurerades i StorSimple Snapshot Manager; **FALSKT** anger att den inte har importerats, men har skapats av StorSimple Snapshot Manager. (Du kan enkelt se en grupp för importerade volymen eftersom ett suffix läggs som identifierar enheten som gruppen volym importerades.)
    
    ![Säkerhetskopieringskatalogen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Om du expanderar **lokal ögonblicksbild** eller **Molnögonblicksbild**, och klicka sedan på namnet på en enskild ögonblicksbild den **resultat** visar följande information om ögonblicksbilden du valde:
  
  * **Namn på** – den volym som identifieras av enhetsbeteckning. 
  * **Lokalt namn** – det lokala namnet på enheten (om tillgängligt). 
  * **Enheten** – med namnet på den enhet där volymen är installerade. 
  * **Tillgängliga** – om ögonblicksbilden är tillgänglig för tillfället. **SANT** anger att ögonblicksbilden är tillgängligt och kan återställas. **FALSKT** anger ögonblicksbilden är inte längre tillgänglig. 

## <a name="restore-a-volume"></a>Återställa en volym
Använd följande procedur för att återställa en volym från en säkerhetskopia.

#### <a name="prerequisites"></a>Nödvändiga komponenter
Om du inte redan har gjort det, skapa en volym och volym grupp och sedan ta bort volymen. Som standard säkerhetskopierar StorSimple Snapshot Manager en volym innan du tillåta att det ska tas bort. Den här försiktighetsåtgärden kan förhindra förlust av data om volymen tas bort oavsiktligt eller om data ska återställas av någon anledning. 

Under den skapar förebyggande säkerhetskopian visas StorSimple Snapshot Manager följande meddelande.

![Automatiska ögonblicksbilder meddelande](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Du kan inte ta bort en volym som ingår i en grupp för volymen. Alternativet Ta bort är inte tillgänglig. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Att återställa en volym
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager. 
2. I den **omfång** fönstret expanderar den **säkerhetskopieringskatalog** nod, expandera volymen grupperna och klicka sedan på **lokala ögonblicksbilder** eller **Molnögonblicksbilder**. En lista över ögonblicksbilder av säkerhetskopior som visas i den **resultat** fönstret.
3. Hitta den säkerhetskopia som du vill återställa, högerklicka och klicka sedan på **återställa**.
   
    ![Återställa säkerhetskopieringskatalogen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Granska information på bekräftelsesidan typ **Bekräfta**, och klicka sedan på **OK**. StorSimple Snapshot Manager använder säkerhetskopian för att återställa volymen.
   
    ![Återställa bekräftelsemeddelande](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Du kan övervaka återställningsåtgärden medan den körs. I den **omfång** fönstret expanderar den **jobb** noden och klicka sedan på **kör**. Jobbinformationen visas i den **resultat** fönstret. När Återställningsjobbet har slutförts Jobbinformationen överförs till den **senaste 24 timmarna** lista.

## <a name="clone-a-volume-or-volume-group"></a>Klona en volym eller en volym grupp
Använd följande procedur för att skapa en dubblett (klonar) av en volym eller en grupp för volymen.

#### <a name="to-clone-a-volume-or-volume-group"></a>Klona en volym eller en volym grupp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret expanderar den **säkerhetskopieringskatalog** nod, expandera volymen grupperna och klicka sedan på **Molnögonblicksbilder**. En lista över säkerhetskopior som visas i den **resultat** fönstret.
3. Hitta volymen eller volym-grupp som du vill klona, högerklicka på volymen eller volym gruppnamn och på **klona**. Den **klona Molnögonblicksbild** dialogrutan visas.
   
    ![Klona en ögonblicksbild i molnet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Slutför den **klona Molnögonblicksbild** i dialogrutan som visas på följande sätt: 
   
   1. I den **namn** anger du ett namn för den klonade volymen. Det här namnet visas i den **volymer** noden. 
   2. (Valfritt) Välj **enhet**, och välj sedan en enhetsbeteckning från den nedrullningsbara listan.
   3. (Valfritt) Välj **mapp (NTFS)**, och ange en mappsökväg eller klicka på Bläddra och välj en plats för mappen. 
   4. Klicka på **Skapa**.
5. När kloningsprocessen är klar måste du initiera klonad volym. Starta Serverhanteraren och starta sedan Diskhantering. Detaljerade anvisningar finns i [Monteringsvolymer](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). När det startas volymen kommer att anges under den **volymer** nod i den **omfång** fönstret. Om du inte ser den volym som visas, kan du uppdatera listan över volymer (Högerklicka på den **volymer** noden och klicka sedan på **uppdatera**).

## <a name="delete-a-backup"></a>Ta bort en säkerhetskopia
Använd följande procedur för att ta bort en ögonblicksbild från säkerhetskopieringskatalogen. 

> [!NOTE]
> Tar bort en ögonblicksbild tas bort säkerhetskopierade data som är associerade med ögonblicksbilden. Hur du rensar data från molnet kan dock ta lite tid.<br>


#### <a name="to-delete-a-backup"></a>Att ta bort en säkerhetskopia
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret expanderar den **säkerhetskopieringskatalog** nod, expandera volymen grupperna och klicka sedan på **lokala ögonblicksbilder** eller **Molnögonblicksbilder**. En lista över ögonblicksbilder som visas i den **resultat** fönstret.
3. Högerklicka på den ögonblicksbild som du vill ta bort och klicka sedan på **ta bort**.
4. När bekräftelsemeddelandet visas, klickar du på **OK**.

## <a name="recover-a-file"></a>Återställa en fil
Om en fil tas bort av misstag från en volym, kan du återställa filen genom att hämta en ögonblicksbild som datum före borttagningen, med ögonblicksbilden för att skapa en klon av volymen och sedan kopiera filen från den klonade volymen till den ursprungliga volymen.

#### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar måste du kontrollera att du har en aktuell säkerhetskopia av gruppen volym. Ta sedan bort en fil som lagras på en av volymerna i gruppen volym. Använd slutligen följande steg för att återställa den borttagna filen från säkerhetskopian. 

#### <a name="to-recover-a-deleted-file"></a>Återställa en borttagen fil
1. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. Konsolfönstret för StorSimple Snapshot Manager-visas. 
2. I den **omfång** fönstret expanderar den **säkerhetskopieringskatalog** noden och bläddra till en ögonblicksbild som innehåller filen har tagits bort. Normalt bör du välja en ögonblicksbild som har skapats precis före borttagningen.
3. Hitta den volym som du vill klona, högerklicka och klicka på **klona**. Den **klona Molnögonblicksbild** dialogrutan visas.
   
    ![Klona en ögonblicksbild i molnet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Slutför den **klona Molnögonblicksbild** i dialogrutan som visas på följande sätt: 
   
   1. I den **namn** anger du ett namn för den klonade volymen. Det här namnet visas i den **volymer** noden. 
   2. (Valfritt) Välj **enhet**, och välj sedan en enhetsbeteckning från den nedrullningsbara listan. 
   3. (Valfritt) Välj **mapp (NTFS)**, och ange en sökväg eller klicka på **Bläddra** och välj en plats för mappen. 
   4. Klicka på **Skapa**. 
5. När kloningsprocessen är klar måste du initiera klonad volym. Starta Serverhanteraren och starta sedan Diskhantering. Detaljerade anvisningar finns i [Monteringsvolymer](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). När det startas volymen kommer att anges under den **volymer** nod i den **omfång** fönstret. 
   
    Om du inte ser den volym som visas, kan du uppdatera listan över volymer (Högerklicka på den **volymer** noden och klicka sedan på **uppdatera**).
6. Öppna NTFS-mappen som innehåller den klonade volymen, expandera den **volymer** noden och öppna den klonade volymen. Hitta den fil som du vill återställa och kopiera den till den primära volymen.
7. När du återställer filen kan du ta bort NTFS-mappen som innehåller den klonade volymen.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Återställ StorSimple Snapshot Manager-databasen
Du bör regelbundet säkerhetskopiera StorSimple Snapshot Manager-databasen på värddatorn. Om en olycka inträffar eller värddatorn av någon anledning misslyckas kan återställa du den från säkerhetskopian. Det är en manuell process för att skapa säkerhetskopian av databasen.

#### <a name="to-back-up-and-restore-the-database"></a>Att säkerhetskopiera och återställa databasen
1. Stoppa Microsoft StorSimple Management-tjänsten:
   
   1. Starta Serverhanteraren.
   2. På instrumentpanelen i Serverhanteraren på den **verktyg** menyn och välj **Services**.
   3. På den **Services** väljer den **Management-tjänsten för Microsoft StorSimple**.
   4. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **stoppa tjänsten**.
2. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog på värddatorn. 
   
   > [!NOTE]
   > ProgramData är en dold mapp.
   > 
   > 
3. Hitta katalogen XML-fil, kopiera filen och lagra kopian på en säker plats eller i molnet. Om värden inte, kan du använda den här säkerhetskopian för att återställa principer för säkerhetskopiering som du skapade i StorSimple Snapshot Manager.
   
    ![Azure StorSimple säkerhetskopieringskatalogen fil](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Starta om hanteringstjänsten för Microsoft StorSimple: 
   
   1. På instrumentpanelen i Serverhanteraren på den **verktyg** menyn och välj **Services**.
   2. På den **Services** väljer den **Management-tjänsten för Microsoft StorSimple**.
   3. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **starta om tjänsten**.
5. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog på värddatorn. 
6. Ta bort katalogen XML-filen och Ersätt den med den säkerhetskopiera versionen som du skapade. 
7. Klicka på ikonen skrivbord StorSimple Snapshot Manager för att starta StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Nästa steg
* Läs mer om [med StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Läs mer om [StorSimple Snapshot Manager-aktiviteter och arbetsflöden](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

