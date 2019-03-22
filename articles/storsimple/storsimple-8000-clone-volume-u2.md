---
title: Klona en volym på StorSimple 8000-serien | Microsoft Docs
description: Beskriver olika klona typer och användning och förklarar hur du kan använda en säkerhetskopia för att klona en enskild volym på en enhet i StorSimple 8000-serien.
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
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097243"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Använda StorSimple Device Manager-tjänsten i Azure-portalen för att klona en volym

## <a name="overview"></a>Översikt

Den här självstudien beskrivs hur du kan använda en säkerhetskopia för att klona en enskild volym via den **säkerhetskopieringskatalog** bladet. Det förklarar också skillnaden mellan *tillfälliga* och *permanent* klonar. Riktlinjerna i den här självstudien gäller för alla StorSimple 8000-serien enheten som kör uppdatering 3 eller senare.

StorSimple Device Manager-tjänsten **säkerhetskopieringskatalog** bladet visar alla säkerhetskopior som skapas när manuella eller automatiska säkerhetskopior tas. Du kan sedan välja en volym i en säkerhetskopia för att klona.

 ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Överväganden för att klona en volym

Tänk på följande när du klonar en volym.

- En klon fungerar på samma sätt som en vanlig volym. Åtgärder som är möjligt på en volym är tillgänglig för klonade.

- Övervaknings- och standard säkerhetskopiering inaktiveras automatiskt på en klonad volym. Du skulle behöva konfigurera en klonad volym för eventuella säkerhetskopior.

- En lokalt Fäst volym är klonas en nivåindelad volym. Om du behöver den klonade volymen till fästas lokalt kan konvertera du klonade till en lokalt Fäst volym när kopieringen har slutförts. Information om hur du konverterar en nivåindelad volym till en lokalt Fäst volym, går du till [ändra volymtyp](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Om du försöker konvertera en klonad volym från misslyckas nivåindelad till lokalt fixerade omedelbart efter att Kloningen (när det är fortfarande en tillfälliga klon), konverteringen med följande felmeddelande visas:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Det här felet visas bara om du klona in på en annan enhet. Du konverterar volymen till lokalt fixerade om du först konvertera tillfälliga klonen till en permanent kloning. Ta en molnögonblicksbild av tillfälliga klonen att konvertera den till en permanent kloning.

## <a name="create-a-clone-of-a-volume"></a>Skapa en klon av en volym

Du kan skapa en klon på samma enhet, en annan enhet eller även en molninstallation med hjälp av en lokal eller ögonblicksbild i molnet.

Proceduren nedan beskriver hur du skapar en klon från säkerhetskopieringskatalogen.  En alternativ metod för att initiera klon är att gå till **volymer**, markera en volym och högerklicka för att öppna snabbmenyn och välja **klona**.

Utför följande steg för att skapa en klon av volymen från säkerhetskopieringskatalogen.

#### <a name="to-clone-a-volume"></a>Klona en volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **säkerhetskopieringskatalog**.

2. Välj en säkerhetskopia på följande sätt:
   
   1. Välj rätt enhet.
   2. I listrutan, väljer du den volym eller säkerhetskopiering principen för säkerhetskopiering som du vill välja.
   3. Ange tidsintervallet.
   4. Klicka på **tillämpa** att köra frågan.

      Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.
   
      ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Expandera den säkerhetskopia du vill visa associerade volymen och välj en volym i en säkerhetskopia. Högerklicka och välj sedan snabbmenyn **klona**.

   ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. I den **klona** bladet gör följande:
   
   1. Identifiera en målenhet. Detta är den plats där klonen ska skapas. Du kan välja samma enhet eller ange en annan enhet.

      > [!NOTE]
      > Se till att den kapacitet som krävs för klonade är lägre än kapaciteten som är tillgängliga på målenheten.
       
   2. Ange ett unikt volymnamn för din klon. Namnet måste innehålla mellan 3 och 127 tecken.
      
       > [!NOTE]
       > Den **klona volymen som** pole bude **Nivåindelad** även om du klona en lokalt Fäst volym. Du kan inte ändra den här inställningen; men om du behöver den klonade volymen till fästas lokalt samt, kan du konvertera klonen till en lokalt Fäst volym när du har skapat klonen. Information om hur du konverterar en nivåindelad volym till en lokalt Fäst volym, går du till [ändra volymtyp](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. Under **anslutna värdar**, ange en åtkomstkontrollpost (ACR) för klonade. Du kan lägga till en ny ACR eller välj i listan befintliga. I ACR avgör vilka värdar som kan komma åt den här klonen.
      
       ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Klicka på **klona** att slutföra åtgärden.

4. En kloningsjobb initieras och du meddelas när klonen har skapats. Klicka på meddelandet för jobbet eller gå till **jobb** bladet för att övervaka jobbet kloning.

    ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. När kloningsjobb är klar går du till din enhet och klicka sedan på **volymer**. I listan över volymer, bör du se klonen som nyss skapades i volymbehållaren för samma som har volymen.

    ![Säkerhetskopian lista](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

En klon som skapas på så sätt är en tillfälliga kloning. Läs mer om klonen typer [tillfälliga och permanenta kloner](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Tillfälligt eller permanent kloner
Tillfälliga kloner skapas bara när du klonar till en annan enhet. Du kan klona en viss volym från en säkerhetskopia till en annan enhet som hanteras av StorSimple Device Manager. Tillfälliga klonen har referenser till data i den ursprungliga volymen och använder dessa data att läsa och skriva lokalt på målenheten.

När du har en ögonblicksbild i molnet med tillfälliga klonen resulterande klonen är en *permanent* kloning. Under den här processen skapas en kopia av data i molnet och tiden för att kopiera dessa data bestäms av storleken på data och Azure fördröjning (detta är en kopia av Azure till Azure). Den här processen kan ta dagar till veckor. Tillfälliga klonen blir en permanent kloning och har inte några referenser till den ursprungliga volymdata som den klonats från.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenarier för tillfälliga och permanenta kloner
I följande avsnitt beskrivs exempel situationer där tillfälliga och permanenta kloner kan användas.

### <a name="item-level-recovery-with-a-transient-clone"></a>Återställning på objektnivå med en tillfälliga klon
Du behöver återställa en en-year-old Microsoft PowerPoint-fil. IT-administratören identifierar säkerhetskopieringen från den tiden och filtrerar volymen. Administratören sedan klonar volymen, söker efter den fil som du söker efter och tillhandahåller den. I det här scenariot används en tillfälliga kloning.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testa i produktionsmiljö med en permanent kloning
Du behöver verifiera en testning bugg i produktionsmiljön. Du skapar en klon av volymen i produktionsmiljön och sedan ta en molnögonblicksbild av den här klonen för att skapa en oberoende klonad volym. I det här scenariot används en permanent kloning.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [återställa en StorSimple-volym från en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md).
* Lär dig hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

