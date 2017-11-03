---
title: Klona StorSimple-volym | Microsoft Docs
description: "Beskriver olika klona typer och när de ska användas och förklarar hur du kan använda en säkerhetskopia att klona en enskild volym."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b5d615f2-02a7-4222-9867-6c0385ce748c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 8f1936fac543f559a44ad0f9c35b30d1a92dce68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>Använda StorSimple Manager-tjänsten för att klona en volym
[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Översikt
StorSimple Manager-tjänsten **säkerhetskopieringskatalogen** sidan visas alla säkerhetskopior som skapas när manuell eller automatisk säkerhetskopiering utförs. Du kan använda den här sidan för att visa alla säkerhetskopior för en princip för säkerhetskopiering eller en volym, Välj eller ta bort säkerhetskopior eller använda en säkerhetskopia att återställa eller klona en volym.

![Säkerhetskopieringskatalogen sida](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

Den här självstudiekursen beskrivs hur du kan använda en säkerhetskopia att klona en enskild volym. Här förklaras också skillnaden mellan *tillfälligt* och *permanent* klonar. 

## <a name="create-a-clone-of-a-volume"></a>Skapa en klon av en volym
Du kan skapa en klon på samma enhet, en annan enhet eller även en virtuell dator med hjälp av en lokal eller en ögonblicksbild i molnet.

#### <a name="to-clone-a-volume"></a>Att klona en volym
1. På tjänstsidan StorSimple Manager-på **säkerhetskopieringskatalog** fliken och markera en säkerhetskopia.
2. Expandera den säkerhetskopia du vill visa associerade volymer. Klicka och välj en volym från säkerhetskopian.
   
     ![Klona en volym](./media/storsimple-clone-volume/HCS_Clone.png) 
3. Klicka på **klona** att påbörja Kloningen av den valda volymen.
4. I guiden klona volym under **ange namn och plats**:
   
   1. Identifiera en målenhet. Detta är den plats där klonen ska skapas. Du kan välja samma enhet eller ange en annan enhet. Om du väljer en volym som är associerade med andra leverantörer av molntjänster (inte Azure) fysiska enheter visas bara i listrutan för målenheten. Du kan inte klona en volym som är associerade med andra molntjänstleverantörer på en virtuell enhet.
      
      > [!NOTE]
      > Se till att den kapacitet som krävs för klonade är lägre än den tillgängliga kapaciteten på målenheten.
      > 
      > 
   2. Ange ett unikt volymnamn för din kloning. Namnet måste innehålla mellan 3 och 127 tecken.
   3. Klicka på pilikonen ![pilikon](./media/storsimple-clone-volume/HCS_ArrowIcon.png) för att fortsätta till nästa sida.
5. Under **ange värdar som kan använda den här volymen**:
   
   1. Ange en åtkomstkontrollpost (ACR) för klonade. Du kan lägga till en ny ACR eller välj i listan befintliga.
   2. Klicka på kryssikonen ![kryssikon](./media/storsimple-clone-volume/HCS_CheckIcon.png)att slutföra åtgärden.
6. Ett jobb för klon initieras och du meddelas när klonen har skapats. Klicka på **visa jobb** övervaka jobb för klon på den **jobb** sidan.
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
Tillfälliga och permanenta kloner skapas bara när du klona in på en annan enhet. Du kan också klona en viss volym från en säkerhetskopia till en annan enhet. En klon som skapats i det här sättet är en *tillfälligt* kloning. Tillfälligt klonen har referenser till den ursprungliga volymen och använder volymen för att läsa vid skrivning till lokalt. 

När du har tagit en ögonblicksbild i molnet med tillfälliga klonen resulterande klonen blir en *permanent* kloning. Permanent klonen är oberoende och har inte några referenser till den ursprungliga volymen som den har klonats från.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenarier för tillfälliga och permanenta kloner
I följande avsnitt beskrivs exempel situationer där tillfälliga och permanenta kloner kan användas.

### <a name="item-level-recovery-with-a-transient-clone"></a>Återställning på objektnivå med en tillfälliga klon
Du behöver återställa en en-år-gamla Microsoft PowerPoint-fil. IT-administratören identifierar säkerhetskopieringen från den aktuella tidsperioden och sedan filtrerar volymen. Administratören sedan klonar volymen, letar du upp filen som du söker efter och ger dig. I det här scenariot används ett tillfälligt kloning. 

![Video tillgänglig](./media/storsimple-clone-volume/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du kan använda klonen och återställa funktioner i StorSimple för att återställa borttagna filer, klickar du på [här](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testa i produktionsmiljö med en permanent klon
Du behöver verifiera ett tester programfel i produktionsmiljön. Du kan skapa en klon av volymen i produktionsmiljön genom att ta en ögonblicksbild i molnet för den här klonen. Den klonade volymen är nu oberoende. I det här scenariot används en permanent kloning.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [återställer en StorSimple-volym från en säkerhetskopia](storsimple-restore-from-backup-set.md).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

