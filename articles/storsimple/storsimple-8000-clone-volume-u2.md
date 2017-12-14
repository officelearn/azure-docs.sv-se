---
title: "Klona en volym på StorSimple 8000-serien | Microsoft Docs"
description: "Beskriver olika klona typer och användning och förklarar hur du kan använda en säkerhetskopia att klona en enskild volym på en enhet för StorSimple 8000-serien."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: d3965c74d51996b3e4144130fad01fae9e265897
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Använda tjänsten StorSimple Enhetshanteraren i Azure-portalen för att klona en volym

## <a name="overview"></a>Översikt

Den här självstudiekursen beskriver hur du kan använda en säkerhetskopia att klona en enskild volym via den **säkerhetskopieringskatalog** bladet. Här förklaras också skillnaden mellan *tillfälligt* och *permanent* klonar. Riktlinjerna i den här kursen gäller för alla StorSimple 8000-serien enheten med uppdatering 3 eller senare.

Tjänsten StorSimple Enhetshanteraren **säkerhetskopieringskatalog** bladet visar alla säkerhetskopior som skapas när manuell eller automatisk säkerhetskopiering utförs. Du kan sedan välja en volym i en säkerhetskopia att klona.

 ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Överväganden för kloning av en volym

Tänk på följande när du klonar en volym.

- En klon fungerar på samma sätt som en vanlig volym. Alla åtgärder som är möjligt på en volym är tillgänglig för klonade.

- Övervakning och standard säkerhetskopieringen inaktiveras automatiskt på en klonade volym. Du skulle behöva konfigurera en klonade volym för säkerhetskopior.

- En lokalt Fäst volym klonas som en nivåindelad volym. Om du behöver klonade volymen för att lokalt Fäst kan du konvertera klonen till en lokalt Fäst volym när kopieringen har slutförts. Information om hur du konverterar en nivåindelad volym till en lokalt Fäst volym, gå till [ändra volymtypen av](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Om du försöker konvertera en klonade volym från misslyckas nivåer att lokalt Fäst omedelbart efter att Kloningen (när det är fortfarande ett tillfälligt klona), konverteringen med följande felmeddelande:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Det här felmeddelandet endast om du klona in på en annan enhet. Har kan du konvertera volymen till lokalt Fäst om du konverterar tillfälligt klonen först till en permanent kloning. Ta en ögonblicksbild i molnet tillfälligt klonens att konvertera den till en permanent kloning.

## <a name="create-a-clone-of-a-volume"></a>Skapa en klon av en volym

Du kan skapa en klon på samma enhet, en annan enhet eller även en moln-installation med hjälp av en lokal eller ögonblicksbild i molnet.

Nedan beskrivs hur du skapar en klon från katalogen för säkerhetskopieringen.  En alternativ metod för att initiera klona är att gå till **volymer**, Välj en volym och högerklicka sedan för att anropa snabbmenyn och välj **klona**.

Utför följande steg för att skapa en klon av volymen från katalogen för säkerhetskopieringen.

#### <a name="to-clone-a-volume"></a>Att klona en volym

1. Gå till Enhetshanteraren för StorSimple-tjänsten och klicka sedan på **säkerhetskopieringskatalog**.

2. Välj en säkerhetskopia på följande sätt:
   
   1. Välj lämplig enhet.
   2. Välj den volym eller säkerhetskopiering principen för säkerhetskopiering som du vill välja i den nedrullningsbara listan.
   3. Ange tidsintervall.
   4. Klicka på **tillämpa** att köra frågan.

    Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.
   
    ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Expandera den säkerhetskopia du vill visa associerade volymen och välj en volym i en säkerhetskopia. Högerklicka och klicka sedan på snabbmenyn **klona**.

   ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. I den **klona** bladet gör du följande:
   
    1. Identifiera en målenhet. Detta är den plats där klonen ska skapas. Du kan välja samma enhet eller ange en annan enhet.

      > [!NOTE]
      > Se till att den kapacitet som krävs för klonade är lägre än den tillgängliga kapaciteten på målenheten.
       
    2. Ange ett unikt volymnamn för din kloning. Namnet måste innehålla mellan 3 och 127 tecken.
      
        > [!NOTE]
        > Den **klona volymen som** fältet kommer att vara **skiktindelade** även om kloning av en lokalt Fäst volym. Du kan inte ändra den här inställningen. men om du behöver klonade volymen för att lokalt Fäst samt, kan du konvertera klonen till en lokalt Fäst volym när du har skapat klonen. Information om hur du konverterar en nivåindelad volym till en lokalt Fäst volym, gå till [ändra volymtypen av](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
    3. Under **anslutna värdar**, ange en åtkomstkontrollpost (ACR) för klonade. Du kan lägga till en ny ACR eller välj i listan befintliga. ACR avgör vilka värdar som har åtkomst till den här klonen.
      
        ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

    4. Klicka på **klona** att slutföra åtgärden.

4. Ett jobb för klon initieras och du meddelas när klonen har skapats. Klicka på meddelandet jobb eller gå till **jobb** bladet för att övervaka jobb för klon.

    ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Efter kloning jobbet är klart, gå till din enhet och klicka sedan på **volymer**. Du bör se klonen som just skapades i volymbehållaren för samma som har källvolymen i en lista över volymer.

    ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

En klon som har skapats på detta sätt är ett tillfälligt kloning. Mer information om kloning typer finns [tillfälliga och permanenta kloner](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Tillfälligt kontra permanent kloner
Tillfälligt kloner skapas bara när du klonar till en annan enhet. Du kan också klona en viss volym från en säkerhetskopia till en annan enhet som hanteras av StorSimple Enhetshanteraren. Tillfälligt klonen har referenser till data i den ursprungliga volymen och använder informationen för att läsa och skriva lokalt på målenheten.

När du har tagit en ögonblicksbild i molnet med tillfälliga klonen resulterande klon är en *permanent* kloning. Under den här processen skapas en kopia av data i molnet och tiden för att kopiera data bestäms av storleken på data och Azure latens (detta är en kopia av Azure till Azure). Den här processen kan ta dagar till veckor. Tillfälligt klonen blir en permanent kloning och har inte några referenser till volymdata som den har klonats från.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenarier för tillfälliga och permanenta kloner
I följande avsnitt beskrivs exempel situationer där tillfälliga och permanenta kloner kan användas.

### <a name="item-level-recovery-with-a-transient-clone"></a>Återställning på objektnivå med en tillfälliga klon
Du behöver återställa en en-år-gamla Microsoft PowerPoint-fil. IT-administratören identifierar säkerhetskopieringen från den tidpunkten och filtrerar volymen. Administratören sedan klonar volymen, letar du upp filen som du söker efter och ger dig. I det här scenariot används ett tillfälligt kloning.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testa i produktionsmiljö med en permanent klon
Du behöver verifiera ett tester programfel i produktionsmiljön. Du skapar en klon av volymen i produktionsmiljön och sedan ta en ögonblicksbild i molnet för den här klonen för att skapa en oberoende klonade volym. I det här scenariot används en permanent kloning.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [återställer en StorSimple-volym från en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md).
* Lär dig hur du [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

