---
title: StorSimple Snapshot Manager säkerhetskopieringskatalogen | Microsoft Docs
description: Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen för att visa och hantera katalogen för säkerhetskopieringen.
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
ms.openlocfilehash: b97753e6f1b67e3c8d247281c5e5208033a56eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876714"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Använd StorSimple Snapshot Manager för att hantera katalogen för säkerhetskopieringen

## <a name="overview"></a>Översikt
Huvudfunktion för StorSimple Snapshot Manager är att du kan skapa programkonsekventa säkerhetskopior av StorSimple-volymer i form av ögonblicksbilder. Ögonblicksbilder visas sedan i en XML-fil som kallas en *säkerhetskopieringskatalogen*. Katalogen för säkerhetskopieringen organiserar ögonblicksbilder av volymen grupp och sedan efter lokal ögonblicksbild eller ögonblicksbild i molnet.

Den här självstudiekursen beskriver hur du kan använda den **säkerhetskopieringskatalog** noden för att utföra följande uppgifter:

* Återställa en volym
* Klona en volym eller en volym grupp
* Ta bort en säkerhetskopia
* Återställa en fil
* Återställ databasen Storsimple Snapshot Manager

Du kan visa den säkerhetskopiera katalogen genom att expandera den **säkerhetskopieringskatalogen** nod i den **omfång** fönstret och expandera sedan gruppen volym.

* Om du klickar på gruppnamn volymen, den **resultat** visar antalet lokala ögonblicksbilder och molnögonblicksbilder som är tillgängliga för gruppen volym. 
* Om du klickar på **lokal ögonblicksbild** eller **ögonblicksbild i molnet**, **resultat** visar följande information om varje ögonblicksbild (beroende på din  **Visa** inställningar):
  
  * **Namnet** – när ögonblicksbilden skapades.
  * **Typen** – om det här är en lokal ögonblicksbild eller en ögonblicksbild i molnet.
  * **Ägare** – innehållets ägare. 
  * **Tillgängliga** – om ögonblicksbilden som är tillgängliga. **True** anger att ögonblicksbilden är tillgängligt och kan återställas. **FALSKT** anger ögonblicksbilden är inte längre tillgänglig. 
  * **Importera** – om säkerhetskopieringen har importerats. **True** anger att säkerhetskopieringen har importerats från StorSimple Device Manager-tjänsten när enheten konfigurerades i StorSimple Snapshot Manager; **FALSKT** anger att den inte har importerats men har skapats av StorSimple Snapshot Manager. (Det lätt att identifiera en grupp importerade volymen eftersom ett suffix läggs till som identifierar enheten som gruppen volym importerades.)
    
    ![Säkerhetskopieringskatalogen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Om du expanderar **lokal ögonblicksbild** eller **ögonblicksbild i molnet**, och klicka sedan på namnet på en enskild ögonblicksbild av **resultat** visar följande information om ögonblicksbilden du valde:
  
  * **Namnet** – volymen som identifieras med en enhetsbeteckning. 
  * **Lokalt namn** – det lokala namnet på enheten (om tillgängligt). 
  * **Enheten** – namnet på den enhet som finns i volymen. 
  * **Tillgängliga** – om ögonblicksbilden som är tillgängliga. **True** anger att ögonblicksbilden är tillgängligt och kan återställas. **FALSKT** anger ögonblicksbilden är inte längre tillgänglig. 

## <a name="restore-a-volume"></a>Återställa en volym
Använd följande procedur för att återställa en volym från en säkerhetskopia.

#### <a name="prerequisites"></a>Krav
Om du inte redan har gjort det skapar en volym och volymen gruppen och ta bort volymen. Som standard säkerhetskopierar StorSimple Snapshot Manager en volym innan du gör det möjligt för att kunna tas bort. Den här försiktighetsåtgärden kan förhindra förlust av data om volymen tas bort oavsiktligt eller om data måste återställas av någon anledning. 

StorSimple Snapshot Manager visar följande meddelande när den skapar förebyggande säkerhetskopian.

![Automatisk ögonblicksbild meddelande](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Du kan inte ta bort en volym som är en del av en grupp för volymen. Alternativet Ta bort är inte tillgänglig. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Så här återställer du en volym
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager. 
2. I den **omfång** rutan Expandera den **säkerhetskopieringskatalog** , expandera grupperna volym och klicka sedan på **lokala ögonblicksbilder** eller **Molnögonblicksbilder**. En lista över ögonblicksbilder av säkerhetskopior som visas i den **resultat** fönstret.
3. Hitta den säkerhetskopia som du vill återställa, högerklicka och klicka sedan på **återställa**.
   
    ![Återställa säkerhetskopierade katalogen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Granska informationen, på bekräftelsesidan typen **Bekräfta**, och klicka sedan på **OK**. StorSimple Snapshot Manager använder säkerhetskopian för att återställa volymen.
   
    ![Återställa bekräftelsemeddelande](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Du kan övervaka åtgärden Återställ medan den körs. I den **omfång** rutan Expandera den **jobb** noden och klicka sedan på **kör**. Jobbinformationen visas i den **resultat** fönstret. När Återställningsjobbet har slutförts Jobbinformationen överförs till den **senaste 24 timmarna** lista.

## <a name="clone-a-volume-or-volume-group"></a>Klona en volym eller en volym grupp
Använd följande procedur för att skapa en dubblett (klona) av en volym eller en volym grupp.

#### <a name="to-clone-a-volume-or-volume-group"></a>Att klona en volym eller en volym grupp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan Expandera den **säkerhetskopieringskatalog** , expandera grupperna volym och klicka sedan på **Molnögonblicksbilder**. En lista över säkerhetskopieringar visas i den **resultat** fönstret.
3. Hitta volymen eller volym-grupp som du vill klona, högerklicka på volymen eller volym gruppnamn och klickar på **klona**. Den **ögonblicksbild i molnet klona** dialogrutan visas.
   
    ![Klona en ögonblicksbild i molnet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Slutför den **ögonblicksbild i molnet klona** dialogrutan enligt följande: 
   
   1. I den **namnet** skriver du ett namn för den klonade volymen. Det här namnet visas i den **volymer** nod. 
   2. (Valfritt) Markera **enhet**, och väljer en enhetsbeteckning från den nedrullningsbara listan.
   3. (Valfritt) Markera **mappen NTFS**, och ange en sökväg till mappen eller klicka på Bläddra och välj en plats för mappen. 
   4. Klicka på **Skapa**.
5. När kloningsprocessen är klar måste du initiera klonade volymen. Starta Serverhanteraren och starta sedan Diskhantering. Detaljerade instruktioner finns [montera volymer](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). När den har initierats volymen kommer att visas under den **volymer** nod i den **omfång** fönstret. Om du inte ser den volym som visas kan du uppdatera listan över volymer (Högerklicka på den **volymer** noden och klicka sedan på **uppdatera**).

## <a name="delete-a-backup"></a>Ta bort en säkerhetskopia
Använd följande procedur för att ta bort en ögonblicksbild från katalogen för säkerhetskopieringen. 

> [!NOTE]
> Tar bort en ögonblicksbild av säkerhetskopierade data som är associerade med ögonblicksbilden. Processen för att rensa data från molnet kan dock ta lite tid.<br>


#### <a name="to-delete-a-backup"></a>Ta bort en säkerhetskopia
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan Expandera den **säkerhetskopieringskatalog** , expandera grupperna volym och klicka sedan på **lokala ögonblicksbilder** eller **Molnögonblicksbilder**. En lista över ögonblicksbilder som visas i den **resultat** fönstret.
3. Högerklicka på den ögonblicksbild som du vill ta bort och klicka sedan på **ta bort**.
4. När bekräftelsemeddelandet visas, klickar du på **OK**.

## <a name="recover-a-file"></a>Återställa en fil
Om en fil av misstag tas bort från en volym kan du återställa filen genom att hämta en ögonblicksbild som datum före borttagningen, med ögonblicksbilden för att skapa en klon av volymen och kopiera filen från den klonade volymen till den ursprungliga volymen.

#### <a name="prerequisites"></a>Krav
Innan du börjar bör du kontrollera att du har en aktuell säkerhetskopia av gruppen volym. Sedan tar du bort en fil som lagras på en av volymerna i gruppen volym. Slutligen, Använd följande steg för att återställa borttagna filer från säkerhetskopian. 

#### <a name="to-recover-a-deleted-file"></a>Att återställa borttagna filer
1. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. Konsolfönstret StorSimple Snapshot Manager-visas. 
2. I den **omfång** rutan Expandera den **säkerhetskopieringskatalog** nod och bläddra till en ögonblicksbild som innehåller den borttagna filen. Normalt bör du välja en ögonblicksbild skapades innan borttagningen.
3. Hitta den volym som du vill klona, högerklicka och klicka på **klona**. Den **ögonblicksbild i molnet klona** dialogrutan visas.
   
    ![Klona en ögonblicksbild i molnet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Slutför den **ögonblicksbild i molnet klona** dialogrutan enligt följande: 
   
   1. I den **namnet** skriver du ett namn för den klonade volymen. Det här namnet visas i den **volymer** nod. 
   2. (Valfritt) Välj **enhet**, och väljer en enhetsbeteckning från den nedrullningsbara listan. 
   3. (Valfritt) Välj **mappen NTFS**, och ange en sökväg till mappen eller klicka på **Bläddra** och välj en plats för mappen. 
   4. Klicka på **Skapa**. 
5. När kloningsprocessen är klar måste du initiera klonade volymen. Starta Serverhanteraren och starta sedan Diskhantering. Detaljerade instruktioner finns [montera volymer](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). När den har initierats volymen kommer att visas under den **volymer** nod i den **omfång** fönstret. 
   
    Om du inte ser den volym som visas kan du uppdatera listan över volymer (Högerklicka på den **volymer** noden och klicka sedan på **uppdatera**).
6. Öppna den NTFS-mapp som innehåller den klonade volymen, expandera den **volymer** noden och sedan öppna den klonade volymen. Efter den fil som du vill återställa och kopiera den till den primära volymen.
7. När du återställer filen kan du ta bort den NTFS-mapp som innehåller den klonade volymen.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Återställ databasen StorSimple Snapshot Manager
Du bör regelbundet säkerhetskopiera StorSimple Snapshot Manager-databasen på värddatorn. Om en olycka inträffar eller värddatorn misslyckas av någon anledning kan återställa du den från säkerhetskopian. Att skapa säkerhetskopian av databasen är en manuell process.

#### <a name="to-back-up-and-restore-the-database"></a>Säkerhetskopiera och återställa databasen
1. Stoppa tjänsten Microsoft StorSimple Management:
   
   1. Starta Serverhanteraren.
   2. På instrumentpanelen i Serverhanteraren på den **verktyg** väljer du **Services**.
   3. På den **Services** väljer den **Management-tjänsten för Microsoft StorSimple**.
   4. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **stoppa tjänsten**.
2. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog på värddatorn. 
   
   > [!NOTE]
   > ProgramData är en dold mapp.
   > 
   > 
3. Hitta katalogen XML-filen, kopiera filen och lagra kopian på en säker plats eller i molnet. Om värden misslyckas, kan du använda den här säkerhetskopian för att återställa principer för säkerhetskopiering som du skapade i StorSimple Snapshot Manager.
   
    ![Azure StorSimple säkerhetskopiering katalogfil](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Starta om tjänsten Microsoft StorSimple Management: 
   
   1. På instrumentpanelen i Serverhanteraren på den **verktyg** väljer du **Services**.
   2. På den **Services** väljer den **Management-tjänsten för Microsoft StorSimple**.
   3. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **starta om tjänsten**.
5. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog på värddatorn. 
6. Ta bort katalogen XML-filen och Ersätt den med den säkerhetskopiera versionen som du skapade. 
7. Klicka på ikonen skrivbord StorSimple Snapshot Manager för att starta StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig mer om [StorSimple Snapshot Manager aktiviteter och arbetsflöden](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

