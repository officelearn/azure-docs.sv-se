---
title: Klona en volym på StorSimple 8000-serien | Microsoft-dokument
description: Beskriver de olika klontyperna och klonanvändningen och förklarar hur du kan använda en säkerhetskopia för att klona en enskild volym på en StorSimple 8000-serieenhet.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255007"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Använda StorSimple Device Manager-tjänsten på Azure-portalen för att klona en volym

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du kan använda en säkerhetskopia för att klona en enskild volym via **katalogbladet För säkerhetskopiering.** Det förklarar också skillnaden mellan *övergående* och *permanenta* kloner. Vägledningen i den här självstudien gäller för alla StorSimple 8000-serieenheter som kör uppdatering 3 eller senare.

Katalogbladet StorSimple Device Manager **Backup** visar alla säkerhetskopieringsuppsättningar som skapas när manuella eller automatiska säkerhetskopior görs. Du kan sedan välja en volym i en säkerhetskopia inställd på klon.

 ![Lista över säkerhetskopior](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Överväganden för kloning av en volym

Tänk på följande information när du klonar en volym.

- En klon beter sig på samma sätt som en vanlig volym. Alla åtgärder som är möjliga på en volym är tillgängliga för klonen.

- Övervakning och standardsäkerhetskopiering inaktiveras automatiskt på en klonad volym. Du måste konfigurera en klonad volym för alla säkerhetskopior.

- En lokalt fäst volym klonas som en nivåindelad volym. Om du behöver den klonade volymen som lokalt fästs kan du konvertera klonen till en lokalt fäst volym när kloningen har slutförts. Information om hur du konverterar en nivåindelad volym till en lokalt fäst volym finns i [Ändra volymtyp .](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

- Om du försöker konvertera en klonad volym från nivåindelade till lokalt fästa omedelbart efter kloning (när det fortfarande är en tillfällig kloning) misslyckas konverteringen med följande felmeddelande:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Det här felet tas bara emot om du klonar på en annan enhet. Du kan konvertera volymen till lokalt fäst om du först konverterar den tillfälliga klonen till en permanent klon. Ta en ögonblicksbild av den tillfälliga klonen för att konvertera den till en permanent klon.

## <a name="create-a-clone-of-a-volume"></a>Skapa en klon av en volym

Du kan skapa en klon på samma enhet, en annan enhet eller till och med en molninstallation med hjälp av en lokal ögonblicksbild eller ögonblicksbild av molnet.

Proceduren nedan beskriver hur du skapar en klon från säkerhetskopieringskatalogen.  En alternativ metod för att initiera klon är att gå till **Volymer,** välja en volym, högerklicka för att anropa snabbmenyn och välj **Klona**.

Gör följande för att skapa en klon av volymen från säkerhetskopieringskatalogen.

#### <a name="to-clone-a-volume"></a>Så här klonar du en volym

1. Gå till tjänsten StorSimple Device Manager och klicka sedan på **Säkerhetskopieringskatalog**.

2. Välj en säkerhetskopia enligt följande:
   
   1. Välj lämplig enhet.
   2. I listrutan väljer du volym- eller säkerhetskopieringsprincipen för den säkerhetskopia som du vill välja.
   3. Ange tidsintervallet.
   4. Klicka på **Använd** för att köra den här frågan.

      De säkerhetskopior som är associerade med den valda volym- eller säkerhetskopieringsprincipen ska visas i listan över säkerhetskopior.
   
      ![Lista över säkerhetskopior](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Expandera säkerhetskopian för att visa den tillhörande volymen och välj en volym i en säkerhetskopia. Högerklicka och **välj**klona på snabbmenyn .

   ![Lista över säkerhetskopior](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Gör följande i **klonbladet:**
   
   1. Identifiera en målenhet. Det här är platsen där klonen skapas. Du kan välja samma enhet eller ange en annan enhet.

      > [!NOTE]
      > Kontrollera att den kapacitet som krävs för klonen är lägre än den kapacitet som finns på målenheten.
       
   2. Ange ett unikt volymnamn för klonen. Namnet måste innehålla mellan 3 och 127 tecken.
      
       > [!NOTE]
       > Fältet **Klonvolym som** kommer att **nivåindelas** även om du klonar en lokalt fäst volym. Du kan inte ändra den här inställningen. Om du behöver den klonade volymen för att fästas lokalt kan du konvertera klonen till en lokalt fäst volym när du har skapat klonen. Information om hur du konverterar en nivåindelad volym till en lokalt fäst volym finns i [Ändra volymtyp .](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)
          
   3. Under **Anslutna värdar**anger du en åtkomstkontrollpost (ACR) för klonen. Du kan lägga till en ny ACR eller välja från den befintliga listan. ACR avgör vilka värdar som kan komma åt den här klonen.
      
       ![Lista över säkerhetskopior](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Klicka på **Klona** för att slutföra åtgärden.

4. Ett klonjobb initieras och du meddelas när klonen har skapats. Klicka på jobbaviseringen eller gå till **jobbbladet** för att övervaka klonjobbet.

    ![Lista över säkerhetskopior](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. När klonjobbet är klart går du till enheten och klickar sedan på **Volymer**. I volymlistan bör du se klonen som just har skapats i samma volymbehållare som har källvolymen.

    ![Lista över säkerhetskopior](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

En klon som skapas på detta sätt är en övergående klon. Mer information om klontyper finns i [Transienta kontra permanenta kloner](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Övergående kontra permanenta kloner
Tillfälliga kloner skapas bara när du klonar till en annan enhet. Du kan klona en viss volym från en säkerhetskopia till en annan enhet som hanteras av StorSimple-enhetshanteraren. Den tillfälliga klonen har referenser till data i den ursprungliga volymen och använder dessa data för att läsa och skriva lokalt på målenheten.

När du har tagit en ögonblicksbild av en tillfällig klon är den resulterande klonen en *permanent* klon. Under den här processen skapas en kopia av data i molnet och tiden för att kopiera dessa data bestäms av storleken på data och Azure-svarstider (detta är en Azure-to-Azure-kopia). Denna process kan ta dagar till veckor. Den tillfälliga klonen blir en permanent klon och har inga referenser till de ursprungliga volymdata som den klonades från.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenarier för tillfälliga och permanenta kloner
I följande avsnitt beskrivs exempelsituationer där tillfälliga och permanenta kloner kan användas.

### <a name="item-level-recovery-with-a-transient-clone"></a>Återställning på objektnivå med en övergående klon
Du måste återställa en ettårig Microsoft PowerPoint-presentationsfil. IT-administratören identifierar den specifika säkerhetskopian från den tiden och filtrerar sedan volymen. Administratören klonar sedan volymen, hittar filen som du letar efter och ger den till dig. I det här fallet används en övergående klon.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testning i produktionsmiljön med en permanent klon
Du måste verifiera ett testfel i produktionsmiljön. Du skapar en klon av volymen i produktionsmiljön och tar sedan en ögonblicksbild av den här klonen för att skapa en oberoende klonad volym. I det här fallet används en permanent klon.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [återställer en StorSimple-volym från en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md).
* Lär dig hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

