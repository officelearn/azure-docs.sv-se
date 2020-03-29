---
title: Klona en Säkerhetskopia av StorSimple Virtual Array | Microsoft-dokument
description: Lär dig hur du klonar en säkerhetskopia och återställer en fil från den virtuella storsimple-matrisen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580939"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klona från en säkerhetskopia av storsimple-virtual array

## <a name="overview"></a>Översikt

I den här artikeln beskrivs steg för steg hur du klonar en säkerhetskopia av dina resurser eller volymer på din Microsoft Azure StorSimple Virtual Array. Den klonade säkerhetskopian används för att återställa en borttagen eller förlorad fil. Artikeln innehåller också detaljerade steg för att utföra en återställning på objektnivå på den virtuella storsimple-matrisen som konfigurerats som en filserver.

## <a name="clone-shares-from-a-backup-set"></a>Klona resurser från en uppsättning säkerhetskopior

**Innan du försöker klona resurser måste du se till att du har tillräckligt med utrymme på enheten för att slutföra den här åtgärden.** Om du vill klona från en säkerhetskopia utför du följande steg i [Azure-portalen.](https://portal.azure.com/)

#### <a name="to-clone-a-share"></a>Så här klonar du en resurs

1. Bläddra till **bladet Enheter.** Markera och klicka på enheten och klicka sedan på **Resurser**. Markera den resurs som du vill klona och högerklicka på resursen för att anropa snabbmenyn. Välj **Klona**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Klicka på **Säkerhetskopiera > markera i** **bladet Klon** och gör sedan följande: 
   
   a.    Filtrera en säkerhetskopia på den här enheten baserat på tidsintervallet. Du kan välja mellan **senaste 7 dagarna,** **senaste 30 dagarna**och **Föregående år.**
   
   b.    I listan över filtrerade säkerhetskopior som visas väljer du en säkerhetskopia att klona från.
   
   c.    Klicka på **OK**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Klicka på **Målinställningar i** **bladet Klon** och gör sedan följande:
   
   a.    Ange ett resursnamn. Resursnamnet måste innehålla 3-127 tecken.
   
   b.    Du kan också ange en beskrivning av den klonade resursen.
   
   c.    Du kan inte ändra typen av resurs som du återställer till. En nivåindelad resurs klonas som en nivåindelade och en lokalt fäst resurs som lokalt fäst.
   
   d.    Kapaciteten är inställd som lika med storleken på den resurs du klonar från.
   
   e.    Tilldela administratörerna för den här resursen. Du kommer att kunna ändra resursegenskaperna via Utforskaren när klonen är klar.
   
   f.    Klicka på **OK**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Klicka på **Klona** om du vill starta ett klonjobb. När jobbet är klart startar klonåtgärden och du meddelas. Om du vill övervaka klonens förlopp går du till bladet **Jobb** och klickar på jobbet för att visa jobbinformation.
5. När klonen har skapats navigerar du tillbaka till bladet **Resurser** på enheten.
6. Nu kan du visa den nya klonade resursen i listan över resurser på enheten. En nivåindelad resurs klonas som nivåindelade och en lokalt fäst resurs som en lokalt fäst resurs.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klona volymer från en uppsättning säkerhetskopior

Om du vill klona från en säkerhetskopia måste du i Azure-portalen utföra steg som liknar de när du klonar en resurs. Klonåtgärden klonar säkerhetskopian till en ny volym på samma virtuella enhet. Du kan inte klona till en annan enhet.

#### <a name="to-clone-a-volume"></a>Så här klonar du en volym

1. Bläddra till **bladet Enheter.** Markera och klicka på enheten och klicka sedan på **Volymer**. Markera den volym som du vill klona och högerklicka på volymen för att anropa snabbmenyn. Välj **Klona**.
   
   ![Klona en volym](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Klicka på **Säkerhetskopiering** i **bladet Klon** och gör sedan följande: 
   
   a.    Filtrera en säkerhetskopia på den här enheten baserat på tidsintervallet. Du kan välja mellan **senaste 7 dagarna,** **senaste 30 dagarna**och **Föregående år.** 
   
   b.    I listan över filtrerade säkerhetskopior som visas väljer du en säkerhetskopia att klona från.
   
   c.    Klicka på **OK**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. Klicka på **Målvolyminställningar** i **bladet Klon** och gör sedan följande:In the Clone blade, click Target volume settings and then do the following::
   
   a. Enhetsnamnet fylls i automatiskt.
   
   b. Ange ett volymnamn för den **klonade volymen**. Volymnamnet måste innehålla 3 till 127 tecken.
   
   c. Volymtypen ställs automatiskt in på den ursprungliga volymen. En nivåindelad volym klonas som nivåindelade och en lokalt fäst volym som lokalt fäst.
   
   d. För **anslutna värdar**klickar du på **Välj**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. I bladet **Anslutna värdar** väljer du från en befintlig ACR eller lägger till en ny ACR. Om du vill lägga till en ny ACR måste du ange ett ACR-namn och värden IQN. Klicka på **Markera**.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Klicka på **Klon för** att starta ett klonjobb.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. När klonjobbet har skapats startar kloningen. När klonen har skapats visas den på bladet Volymer på enheten. Observera att en nivåindelad volym klonas som nivåindelade och att en lokalt fäst volym klonas som en lokalt fäst volym.
   
   ![Klona en säkerhetskopia](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. När volymen visas online i volymlistan är volymen tillgänglig för användning. Uppdatera listan över mål i fönstret iSCSI-initieraregenskaper i iSCSI-initieraren. Ett nytt mål som innehåller det klonade volymnamnet ska visas som "inaktivt" under statuskolumnen.
8. Markera målet och klicka på **Anslut**. När initieraren är ansluten till målet ska statusen ändras till **Ansluten**.
9. I fönstret **Diskhantering** visas de monterade volymerna som visas i följande bild. Högerklicka på den identifierade volymen (klicka på diskens namn) och klicka sedan på **Online**.

> [!IMPORTANT]
> När du försöker klona en volym eller en resurs från en säkerhetskopia, om klonjobbet misslyckas, kan en målvolym eller resurs fortfarande skapas i portalen. Det är viktigt att du tar bort den här målvolymen eller resursen i portalen för att minimera eventuella framtida problem som uppstår till följd av det här elementet.
> 
> 

## <a name="item-level-recovery-ilr"></a>Återställning på objektnivå (ILR)

Den här versionen introducerar återställning på objektnivå (ILR) på en StorSimple Virtual Array som konfigurerats som en filserver. Funktionen kan du göra detaljerad återställning av filer och mappar från en molnsäkerhetskopia av alla resurser på StorSimple-enheten. Du kan hämta borttagna filer från de senaste säkerhetskopiorna med hjälp av en självbetjäningsmodell.

Varje resurs har en *.backups-mapp* som innehåller de senaste säkerhetskopiorna. Du kan navigera till önskad säkerhetskopia, kopiera relevanta filer och mappar från säkerhetskopian och återställa dem. Den här funktionen eliminerar anrop till administratörer för att återställa filer från säkerhetskopior.

1. När du utför ILR kan du visa säkerhetskopiorna via Utforskaren. Klicka på den specifika resurs som du vill titta på säkerhetskopian för. Du kommer att se en *.backups-mapp* som skapats under resursen som lagrar alla säkerhetskopior. Expandera mappen *.backups* för att visa säkerhetskopiorna. Mappen visar den uppdelade vyn för hela säkerhetskopieringshierarkin. Den här vyn skapas på begäran och tar vanligtvis bara ett par sekunder att skapa.
   
   De senaste fem säkerhetskopiorna visas på det här sättet och kan användas för att utföra en återställning på objektnivå. De fem senaste säkerhetskopiorna innehåller både standardplanerade och manuella säkerhetskopior.
   
   * **Schemalagda säkerhetskopior** namngivna som &lt;Enhetsnamnet&gt;DailySchedule-YYYMMDD-HHMMSS-UTC.
   * **Manuella säkerhetskopior** som namnges som Ad-hoc-YYYMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identifiera säkerhetskopian som innehåller den senaste versionen av den borttagna filen. Även om mappnamnet innehåller en UTC-tidsstämpel i vart och ett av föregående fall, är den tidpunkt då mappen skapades den faktiska enhetstiden när säkerhetskopieringen startade. Använd mapptidsstämpeln för att hitta och identifiera säkerhetskopiorna.

3. Leta reda på mappen eller filen som du vill återställa i säkerhetskopian som du identifierade i föregående steg. Du kan bara visa de filer eller mappar som du har behörighet för. Om du inte kan komma åt vissa filer eller mappar kontaktar du en resursadministratör. Administratören kan använda Utforskaren för att redigera resursbehörigheterna och ge dig åtkomst till den specifika filen eller mappen. Det rekommenderas att resursadministratören är en användargrupp i stället för en enskild användare.

4. Kopiera filen eller mappen till lämplig resurs på StorSimple-filservern.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [administrerar den virtuella storsimple-matrisen med hjälp av det lokala webbgränssnittet](storsimple-ova-web-ui-admin.md).

