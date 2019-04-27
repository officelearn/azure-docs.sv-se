---
title: Klona en säkerhetskopia av StorSimple Virtual Array | Microsoft Docs
description: Lär dig mer om att klona en säkerhetskopia och återställa en fil från din StorSimple Virtual Array.
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
ms.openlocfilehash: feffbb634af62d70a840febcf2a04afb7bdeeddd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580939"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klona från en säkerhetskopia av StorSimple Virtual Array

## <a name="overview"></a>Översikt

Den här artikeln beskriver steg för steg hur du klonar en säkerhetskopia av dina resurser eller volymer på Microsoft Azure StorSimple Virtual Array. Den klonade säkerhetskopian används för att återställa en borttagen eller borttappade fil. Artikeln innehåller också detaljerade anvisningar för att utföra en objektnivååterställning på StorSimple Virtual Array konfigurerad som en filserver.

## <a name="clone-shares-from-a-backup-set"></a>Klona resurser från en säkerhetskopia

**Innan du försöker klona resurser, se till att du har tillräckligt med utrymme på enheten för att slutföra åtgärden.** Klona från en säkerhetskopia i den [Azure-portalen](https://portal.azure.com/), utför följande steg.

#### <a name="to-clone-a-share"></a>Klona en resurs

1. Bläddra till **enheter** bladet. Markera och klicka på din enhet och klicka sedan på **resurser**. Välj den resurs som du vill klona, högerklickar på resursen för att öppna snabbmenyn. Välj **Klona**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. I den **klona** bladet klickar du på **säkerhetskopiering > Välj** och gör sedan följande: 
   
   a.    Filtrera en säkerhetskopiering på den här enheten baserat på vilket tidsintervall. Du kan välja mellan **senaste 7 dagarna**, **de senaste 30 dagarna**, och **senaste året**.
   
   b.    I listan över filtrerade säkerhetskopior som visas, väljer du en säkerhetskopia som ska klonas från.
   
   c.    Klicka på **OK**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. I den **klona** bladet klickar du på **rikta inställningar** och gör sedan följande:
   
   a.    Ange ett resursnamn. Resursnamnet måste innehålla 3 127 tecken.
   
   b.    Du kan också ange en beskrivning för den klonade resursen.
   
   c.    Du kan inte ändra typ av resurs som du återställer till. En nivåindelad resurs klonas en nivåindelad och en lokalt Fäst resurs som lokalt fixerade.
   
   d.    Kapaciteten anges som lika med storleken på den resurs du klona från.
   
   e.    Tilldela administratörer för den här resursen. Du kommer att kunna ändra egenskaperna för distributionsresursen via Utforskaren när klonen är klar.
   
   f.    Klicka på **OK**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Klicka på **klona** att starta en klon-jobbet. När jobbet har slutförts, kopieringen startar och du får ett meddelande. För att övervaka förloppet för kloning, går du till den **jobb** bladet och klicka på jobbet om du vill visa jobbinformation.
5. När klonade har skapats, gå tillbaka till den **resurser** bladet på din enhet.
6. Du kan nu visa den nya klonade resursen i listan över resurser på din enhet. En nivåindelad resurs är klonad eftersom nivåer och en lokalt Fäst resurs som en lokalt Fäst.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klona volymer från en säkerhetskopia

Du måste utföra stegen liknar som när du klonar en resurs för att klona från en säkerhetskopia i Azure-portalen. Åtgärden klona klonar säkerhetskopiering till en ny volym på samma virtuella enhet; Du kan inte klona till en annan enhet.

#### <a name="to-clone-a-volume"></a>Klona en volym

1. Bläddra till **enheter** bladet. Markera och klicka på din enhet och klicka sedan på **volymer**. Välj den volym som du vill klona, högerklicka på volymen för att öppna snabbmenyn. Välj **Klona**.
   
   ![Klona en volym](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. I den **klona** bladet klickar du på **Backup** och gör sedan följande: 
   
   a.    Filtrera en säkerhetskopiering på den här enheten baserat på vilket tidsintervall. Du kan välja mellan **senaste 7 dagarna**, **de senaste 30 dagarna**, och **senaste året**. 
   
   b.    I listan över filtrerade säkerhetskopior som visas, väljer du en säkerhetskopia som ska klonas från.
   
   c.    Klicka på **OK**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. I den **klona** bladet klickar du på **rikta volyminställningar** och gör sedan följande:
   
   a. Namnet på enheten fylls i automatiskt.
   
   b. Ange ett volymnamn för den **klona volymen**. Volymens namn måste innehålla 3 till 127 tecken.
   
   c. Typ av volym anges automatiskt till den ursprungliga volymen. En nivåindelad volym är klonad eftersom nivåer och en lokalt Fäst volym lokalt Fäst.
   
   d. För den **anslutna värdar**, klickar du på **Välj**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. I den **anslutna värdar** bladet Välj en befintlig ACR eller lägger till en ny ACR. Om du vill lägga till en ny ACR, behöver du ange en ACR-namn och värden IQN. Klicka på **Välj**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Klicka på **klona** att starta en kloningsjobb.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. När du har skapat kloningsjobb startar Kloningen. När denna har skapats visas den på bladet volymer på enheten. Observera att en nivåindelad volym är klonad eftersom nivåer och en lokalt Fäst volym är klonas en lokalt Fäst volym.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Volymen är tillgänglig för användning när volymen visas online på en lista över volymer. Uppdatera listan med mål i egenskapsfönstret för iSCSI-initieraren på iSCSI-initieraren värden. Ett nytt mål som innehåller klonade volymens namn bör visas som ”inaktiva” under statuskolumnen.
8. Välj målobjektet och klickar på **Connect**. När initieraren är ansluten till målet, bör statusen ändras till **ansluten**.
9. I den **Diskhantering** fönstret monterade volymer visas enligt följande bild. Högerklicka på den identifierade volymen (klicka på diskens namn) och klicka sedan på **Online**.

> [!IMPORTANT]
> När du försöker klona en volym eller en resurs från en säkerhetskopia om det inte går att klona jobb, ange en målvolymen eller resursen fortfarande skapas i portalen. Det är viktigt att du tar bort den här målvolymen eller dela i portalen för att minimera eventuella framtida problem som härrör från det här elementet.
> 
> 

## <a name="item-level-recovery-ilr"></a>Återställning på objektnivå (ILR)

Den här versionen introducerar återställning på objektnivå (ILR) på en StorSimple Virtual Array som konfigurerats som en filserver. Funktionen kan du göra detaljerade återställning av filer och mappar från en säkerhetskopia i molnet för alla resurser på StorSimple-enheten. Du kan hämta borttagna filer från säkerhetskopior som nyligen skapats med hjälp av en självbetjäningsmodell.

Varje resurs har en *.backups* mapp som innehåller de senaste säkerhetskopiorna. Du kan navigera till önskad säkerhetskopia, kopiera relevanta filer och mappar från säkerhetskopieringen och återställa dem. Den här funktionen eliminerar anrop till administratörer för att återställa filer från säkerhetskopior.

1. När du utför den återställning på Objektnivå, kan du visa säkerhetskopior via Utforskaren. Klicka på den specifika resursen som du vill titta på säkerhetskopian för. Du ser en *.backups* mapp som skapats under den resurs som lagrar alla säkerhetskopior. Expandera den *.backups* mappen för att visa säkerhetskopior. Mappen visar Utvidgad vy i hela hierarkin för säkerhetskopiering. Den här vyn skapas på begäran och vanligtvis tar bara några sekunder att skapa.
   
   De sista fem säkerhetskopiorna visas i det här sättet och kan användas för att utföra en återställning på objektnivå. De fem säkerhetskopior som nyligen skapats omfattar både standard schemalagda och manuella säkerhetskopieringar.
   
   * **Schemalagda säkerhetskopieringar** med samma namn som &lt;enhetsnamn&gt;DailySchedule-ÅÅÅÅMMDD-HHMMSS-UTC.
   * **Manuell säkerhetskopiering** med namnet som Ad-hoc-ÅÅÅÅMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identifiera den säkerhetskopia som innehåller den senaste versionen av filen har tagits bort. Även om namnet på mappen innehåller en UTC-tidsstämpel i föregående fall, är den tid då skapa mappen faktisk enhet tidpunkten då säkerhetskopieringen startades. Använd mappen tidsstämpel för att hitta och identifiera säkerhetskopieringar.

3. Leta upp mappen eller filen som du vill återställa i säkerhetskopian som du identifierade i föregående steg. Obs Du kan bara visa filer eller mappar som du har behörigheter för. Kontakta en resurs-administratören om du inte åtkomst till vissa filer eller mappar. Administratören kan använda Utforskaren för att redigera behörigheter till resursen och ge dig åtkomst till en specifik fil eller mapp. Det är rekommenderad praxis att administratören resursen är en grupp i stället för en enskild användare.

4. Kopiera filen eller mappen till lämplig resursen på din StorSimple-filserver.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du [administrera StorSimple Virtual Array med hjälp av det lokala webbgränssnittet](storsimple-ova-web-ui-admin.md).

