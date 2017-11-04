---
title: Klona StorSimple 8000-serien volume | Microsoft Docs
description: "Beskriver olika klona typer och när de ska användas och förklarar hur du kan använda en säkerhetskopia att klona en enskild volym."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 070ac53e-7388-4c48-b8a5-8ed7f9108b2c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 381cd1de089d5281351e289869ebc0e6fdabea06
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume-update-2"></a>Använda StorSimple Manager-tjänsten för att klona en volym (uppdatering 2)
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [använda StorSimple Manager-tjänsten för att klona en volym (uppdatering 2)](storsimple-8000-clone-volume-u2.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Översikt
StorSimple Manager-tjänsten **säkerhetskopieringskatalogen** sidan visas alla säkerhetskopior som skapas när manuell eller automatisk säkerhetskopiering utförs. Du kan använda den här sidan för att visa alla säkerhetskopior för en princip för säkerhetskopiering eller en volym, Välj eller ta bort säkerhetskopior eller använda en säkerhetskopia att återställa eller klona en volym.

![Säkerhetskopieringskatalogen sida](./media/storsimple-clone-volume-u2/backupCatalog.png)  

Den här självstudiekursen beskrivs hur du kan använda en säkerhetskopia att klona en enskild volym. Här förklaras också skillnaden mellan *tillfälligt* och *permanent* klonar.

> [!NOTE]
> En lokalt Fäst volym kommer att klonas när en nivåindelad volym. Om du behöver klonade volymen för att lokalt Fäst kan du konvertera klonen till en lokalt Fäst volym när kopieringen har slutförts. Information om hur du konverterar en nivåindelad volym till en lokalt Fäst volym, gå till [ändra volymtypen av](storsimple-manage-volumes-u2.md#change-the-volume-type).
> 
> Om du försöker konvertera en klonade volym från misslyckas nivåer att lokalt Fäst omedelbart efter att Kloningen (när det är fortfarande ett tillfälligt klona), konverteringen med följande felmeddelande:
> 
> `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.` 
> 
> Det här felmeddelandet endast om du klona in på en annan enhet. Har kan du konvertera volymen till lokalt Fäst om du konverterar tillfälligt klonen först till en permanent kloning. Om du vill konvertera tillfälligt klonen till en permanent kloning, ta en ögonblicksbild i molnet av den.
> 
> 

## <a name="create-a-clone-of-a-volume"></a>Skapa en klon av en volym
Du kan skapa en klon på samma enhet, en annan enhet eller även en virtuell dator med hjälp av en lokal eller ögonblicksbild i molnet.

#### <a name="to-clone-a-volume"></a>Att klona en volym
1. På tjänstsidan StorSimple Manager-på **säkerhetskopieringskatalog** fliken och markera en säkerhetskopia.
2. Expandera den säkerhetskopia du vill visa associerade volymer. Klicka och välj en volym från säkerhetskopian.
   
     ![Klona en volym](./media/storsimple-clone-volume-u2/CloneVol.png) 
3. Klicka på **klona** att påbörja Kloningen av den valda volymen.
4. I guiden klona volym under **ange namn och plats**:
   
   1. Identifiera en målenhet. Detta är den plats där klonen ska skapas. Du kan välja samma enhet eller ange en annan enhet. Om du väljer en volym som är associerade med andra leverantörer av molntjänster (inte Azure) fysiska enheter visas bara i listrutan för målenheten. Du kan inte klona en volym som är associerade med andra molntjänstleverantörer på en virtuell enhet.
      
      > [!NOTE]
      > Se till att den kapacitet som krävs för klonade är lägre än den tillgängliga kapaciteten på målenheten.
      > 
      > 
   2. Ange ett unikt volymnamn för din kloning. Namnet måste innehålla mellan 3 och 127 tecken. 
      
      > [!NOTE]
      > Den **klona volymen som** fältet kommer att vara **skiktindelade** även om kloning av en lokalt Fäst volym. Du kan inte ändra den här inställningen. men om du behöver klonade volymen för att lokalt Fäst samt, kan du konvertera klonen till en lokalt Fäst volym när du har skapat klonen. Information om hur du konverterar en nivåindelad volym till en lokalt Fäst volym, gå till [ändra volymtypen av](storsimple-manage-volumes-u2.md#change-the-volume-type).
      > 
      > 
      
        ![Klona guiden 1](./media/storsimple-clone-volume-u2/clone1.png) 
   3. Klicka på pilikonen ![pilikon](./media/storsimple-clone-volume-u2/HCS_ArrowIcon.png) för att fortsätta till nästa sida.
5. Under **ange värdar som kan använda den här volymen**:
   
   1. Ange en åtkomstkontrollpost (ACR) för klonade. Du kan lägga till en ny ACR eller välj i listan befintliga.
      
        ![Klona guiden 2](./media/storsimple-clone-volume-u2/clone2.png) 
   2. Klicka på kryssikonen ![kryssikon](./media/storsimple-clone-volume-u2/HCS_CheckIcon.png)att slutföra åtgärden.
6. Ett jobb för klon initieras och du meddelas när klonen har skapats. Klicka på **visa jobb** övervaka jobb för klon på den **jobb** sidan. Följande meddelande visas när klona jobbet har slutförts:
   
    ![Klona meddelande](./media/storsimple-clone-volume-u2/CloneMsg.png) 
7. Efter kloning jobbet har slutförts:
   
   1. Gå till den **enheter** och väljer den **Volymbehållare** fliken. 
   2. Välj volymbehållaren som är associerad med källvolymen som du har klonat. Du bör se klonen som just skapades i en lista över volymer.

> [!NOTE]
> Övervakning och standard säkerhetskopieringen inaktiveras automatiskt på en klonade volym.
> 
> 

En klon som har skapats på detta sätt är ett tillfälligt kloning. Mer information om kloning typer finns [tillfälliga och permanenta kloner](#transient-vs-permanent-clones).

Den här klonen är nu en vanlig volym och åtgärder som är möjligt på en volym är tillgänglig för klonen. Du måste konfigurera den här volymen för säkerhetskopior.

## <a name="transient-vs-permanent-clones"></a>Tillfälligt kontra permanent kloner
Tillfälligt kloner skapas bara när du klona till en annan enhet. Du kan också klona en viss volym från en säkerhetskopia till en annan enhet som hanteras av StorSimple Manager. Tillfälligt klonen har referenser till data i den ursprungliga volymen och använder informationen för att läsa och skriva lokalt på målenheten. 

När du har tagit en ögonblicksbild i molnet med tillfälliga klonen resulterande klonen blir en *permanent* kloning. Under den här processen skapas en kopia av data i molnet och tiden för att kopiera data bestäms av storleken på data och Azure latens (detta är en kopia av Azure till Azure). Den här processen kan ta dagar till veckor. Tillfälligt klonen blir en permanent klon sätt och har inte några referenser till volymdata som den har klonats från. 

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenarier för tillfälliga och permanenta kloner
I följande avsnitt beskrivs exempel situationer där tillfälliga och permanenta kloner kan användas.

### <a name="item-level-recovery-with-a-transient-clone"></a>Återställning på objektnivå med en tillfälliga klon
Du behöver återställa en en-år-gamla Microsoft PowerPoint-fil. IT-administratören identifierar säkerhetskopieringen från den aktuella tidsperioden och sedan filtrerar volymen. Administratören sedan klonar volymen, letar du upp filen som du söker efter och ger dig. I det här scenariot används ett tillfälligt kloning. 

![Video tillgänglig](./media/storsimple-clone-volume-u2/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du kan använda klonen och återställa funktioner i StorSimple för att återställa borttagna filer, klickar du på [här](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testa i produktionsmiljö med en permanent klon
Du behöver verifiera ett tester programfel i produktionsmiljön. Du skapar en klon av volymen i produktionsmiljön och sedan ta en ögonblicksbild i molnet för den här klonen för att skapa en oberoende klonade volym. I det här scenariot används en permanent kloning.  

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [återställer en StorSimple-volym från en säkerhetskopia](storsimple-restore-from-backup-set-u2.md).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

