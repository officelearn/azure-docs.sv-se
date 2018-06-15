---
title: Klona en virtuell StorSimple-matris säkerhetskopia | Microsoft Docs
description: Lär dig mer om att klona en säkerhetskopia och återställa en fil från din virtuella StorSimple-matris.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 768c9a1c906999f4690c9c8f7d075743ab1678ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875993"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klona från en säkerhetskopia av din virtuella StorSimple-matris

## <a name="overview"></a>Översikt

Den här artikeln beskriver steg för steg hur du klona en säkerhetskopia av resurser eller volymer på Microsoft Azure StorSimple virtuell matrisen. Klonade säkerhetskopian används för att återställa en borttagen eller förlorade-fil. Artikeln innehåller också detaljerade anvisningar för att utföra en återställning på objektnivå på din virtuella StorSimple-matrisen som konfigurerats som en filserver.

## <a name="clone-shares-from-a-backup-set"></a>Klona resurser från en säkerhetskopia

**Innan du försöker klona resurser, se till att du har tillräckligt med utrymme på enheten för att slutföra åtgärden.** Att klona från en säkerhetskopia i den [Azure-portalen](https://portal.azure.com/), utför följande steg.

#### <a name="to-clone-a-share"></a>Att klona en resurs

1. Bläddra till **enheter** bladet. Välj och klicka på enheten och klicka sedan på **resurser**. Markera resursen som du vill klona, högerklicka på resursen ska anropa på snabbmenyn. Välj **klona**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. I den **klona** bladet, klickar du på **säkerhetskopiering > Välj** och gör sedan följande: 
   
   a.    Filtrera en säkerhetskopiering på den här enheten baserat på vilket tidsintervall. Du kan välja mellan **senaste 7 dagarna**, **de senaste 30 dagarna**, och **senaste året**.
   
   b.    I listan över filtrerade säkerhetskopieringar visas, väljer du en säkerhetskopia för att klona från.
   
   c.    Klicka på **OK**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. I den **klona** bladet, klickar du på **mål inställningar** och gör sedan följande:
   
   a.    Ange ett resursnamn. Resursnamnet får innehålla 3-127 tecken.
   
   b.    Du kan också ange en beskrivning för den klonade resursen.
   
   c.    Du kan inte ändra typ av resurs som du återställer till. En skiktad resurs klonas som en skiktad och en lokalt Fäst resurs lokalt Fäst.
   
   d.    Kapaciteten har angetts som är lika med storleken på resursen du klona från.
   
   e.    Tilldela administratörer för den här resursen. Du kommer att kunna ändra resursegenskaper för via Utforskaren när klonen är klar.
   
   f.    Klicka på **OK**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Klicka på **klona** ett jobb för klon. När jobbet är slutfört kopieringen startar och du får ett meddelande. För att övervaka förloppet för kloning, gå till den **jobb** bladet och klicka på jobbet om du vill visa jobbinformation.
5. När klonade har skapats, gå tillbaka till den **resurser** bladet på enheten.
6. Du kan nu visa den nya klonade resursen i listan över resurser på enheten. En skiktad resurs är klonad eftersom nivåer och en lokalt Fäst resurs som en lokalt Fäst resurs.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klona volymer från en säkerhetskopia

Du måste utföra stegen liknar som när du klonar en resurs för att klona från en säkerhetskopia i Azure-portalen. Åtgärden klona klonar säkerhetskopiering till en ny volym på samma virtuella enheter; Du kan inte klona till en annan enhet.

#### <a name="to-clone-a-volume"></a>Att klona en volym

1. Bläddra till **enheter** bladet. Välj och klicka på enheten och klicka sedan på **volymer**. Markera den volym som du vill klona, högerklicka på volymen för att anropa snabbmenyn. Välj **klona**.
   
   ![Klona en volym](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. I den **klona** bladet, klickar du på **säkerhetskopiering** och gör sedan följande: 
   
   a.    Filtrera en säkerhetskopiering på den här enheten baserat på vilket tidsintervall. Du kan välja mellan **senaste 7 dagarna**, **de senaste 30 dagarna**, och **senaste året**. 
   
   b.    I listan över filtrerade säkerhetskopieringar visas, väljer du en säkerhetskopia för att klona från.
   
   c.    Klicka på **OK**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. I den **klona** bladet, klickar du på **mål volyminställningar** och gör sedan följande::
   
   a. Enhetsnamnet fylls i automatiskt.
   
   b. Ange ett volymnamn på för de **klonas volym**. Namn på volymer måste innehålla 3 till 127 tecken.
   
   c. Volymtypen anges automatiskt till den ursprungliga volymen. En nivåindelad volym är klonad eftersom nivåer och en lokalt Fäst volym lokalt Fäst.
   
   d. För den **anslutna värdar**, klickar du på **Välj**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. I den **anslutna värdar** bladet Välj en befintlig ACR eller lägga till en ny ACR. Om du vill lägga till en ny ACR, behöver du ange en ACR-namn och värden IQN. Klicka på **Välj**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Klicka på **klona** att starta ett jobb för klon.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Kloningen startar efter jobb för klon skapas. När klonade har skapats visas den på bladet volymer på enheten. Observera att en nivåindelad volym är klonad eftersom nivåer och en lokalt Fäst volym klonas när en lokalt Fäst volym.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Volymen är tillgänglig för användning när volymen visas online på en lista över volymer. Uppdatera listan med mål i egenskapsfönstret för iSCSI-initieraren på iSCSI-initieraren värden. Ett nytt mål som innehåller det klonade volymnamnet ska visas som ”inaktiva” under statuskolumnen.
8. Välj målobjektet och klickar på **Anslut**. När initieraren är ansluten till målservern, bör statusen ändras till **ansluten**.
9. I den **Diskhantering** fönstret, visas monterade volymer som visas i följande bild. Högerklicka på den identifierade volymen (klicka på diskens namn) och klicka sedan på **Online**.

> [!IMPORTANT]
> När du försöker klona en volym eller en resurs från en säkerhetskopia om det inte går att klona jobbet, ange en målvolym eller resursen kan fortfarande skapas i portalen. Det är viktigt att du tar bort den här målvolymen eller dela i portalen för att minimera eventuella framtida problem som härrör från det här elementet.
> 
> 

## <a name="item-level-recovery-ilr"></a>Återställning på objektnivå (ILR)

Den här versionen introducerar återställning på objektnivå (ILR) på en virtuell StorSimple-matrisen som konfigurerats som en filserver. Funktionen kan du göra detaljerade återställning av filer och mappar från en säkerhetskopiering i molnet för alla resurser på StorSimple-enhet. Du kan hämta borttagna filer från de senaste säkerhetskopior med hjälp av en självbetjäningsmodell.

Varje resurs har en *.backups* mapp som innehåller de senaste säkerhetskopiorna. Du kan gå till önskad säkerhetskopia, kopiera relevanta filer och mappar från säkerhetskopian och återställa dem. Den här funktionen eliminerar anrop till administratörer för att återställa filer från säkerhetskopior.

1. När du utför den återställning på Objektnivå kan visa du säkerhetskopieringar via Utforskaren. Klicka på resursen som du vill titta på säkerhetskopian av. Du ser en *.backups* mappen som skapas under den resurs som lagrar alla säkerhetskopior. Expandera den *.backups* mappen för att visa säkerhetskopiorna. Mappen innehåller Utvidgad vy i hela hierarkin för säkerhetskopiering. Den här vyn skapas på begäran och tar vanligtvis bara några sekunder att skapa.
   
   De sista fem säkerhetskopiorna visas på detta sätt och kan användas för att utföra en återställning på objektnivå. De fem senaste säkerhetskopiorna inkluderar både standard schemalagda och manuella säkerhetskopieringar.
   
   * **Schemalagda säkerhetskopieringar** med samma namn som &lt;enhetsnamn&gt;DailySchedule-ÅÅÅÅMMDD-HHMMSS-UTC.
   * **Manuell säkerhetskopiering** med samma namn som Ad hoc-ÅÅÅÅMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identifiera den säkerhetskopia som innehåller den senaste versionen av den borttagna filen. Om namnet på mappen innehåller en UTC-tidsstämpel i föregående fall, är när mappen skapades den tid faktisk enhet när säkerhetskopieringen startade. Använda mappen tidsstämpel för att hitta och identifiera säkerhetskopiorna.

3. Leta upp mappen eller filen som du vill återställa i säkerhetskopian som du identifierade i föregående steg. Obs Du kan bara visa filer eller mappar som du har behörighet för. Kontakta en resurs-administratören om du inte kan komma åt vissa filer eller mappar. Administratören kan använda Utforskaren för att redigera behörigheter till resursen och ge dig åtkomst till en specifik fil eller mapp. Det är rekommenderad praxis att administratören resursen är en grupp i stället för en enskild användare.

4. Kopiera filen eller mappen till lämplig resurs på din StorSimple-filserver.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du [administrera din virtuella StorSimple-matris med lokala webbgränssnittet](storsimple-ova-web-ui-admin.md).

