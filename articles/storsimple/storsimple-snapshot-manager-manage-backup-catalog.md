---
title: Säkerhetskatalog för StorSimple Snapshot Manager | Microsoft-dokument
description: Beskriver hur du använder MMC-snapin-modulen StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringskatalogen.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 38ef7774263e4b28b7c316fd0870ca8f7b89d6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931710"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Använda StorSimple Snapshot Manager för att hantera säkerhetskopieringskatalogen

## <a name="overview"></a>Översikt
Den primära funktionen för StorSimple Snapshot Manager är att du kan skapa programkonsekventa säkerhetskopior av StorSimple-volymer i form av ögonblicksbilder. Ögonblicksbilder visas sedan i en XML-fil som kallas en *säkerhetskopia*. Säkerhetskopieringskatalogen ordnar ögonblicksbilder efter volymgrupp och sedan efter lokal ögonblicksbild eller ögonblicksbild av molnet.

I den här självstudien beskrivs hur du kan använda **noden Säkerhetskopieringskatalog** för att utföra följande uppgifter:

* Återställa en volym
* Klona en volym- eller volymgrupp
* Ta bort en säkerhetskopia
* Återställa en fil
* Återställa Storsimple Snapshot Manager-databasen

Du kan visa säkerhetskopieringskatalogen genom att expandera **noden Säkerhetskopieringskatalog** i **scopefönstret** och sedan expandera volymgruppen.

* Om du klickar på volymgruppsnamnet visar **fönstret Resultat** antalet lokala ögonblicksbilder och ögonblicksbilder av molnet som är tillgängliga för volymgruppen. 
* Om du klickar på **Lokal ögonblicksbild** eller Ögonblicksbild **av molnet**visar **resultatfönstret** följande information om varje ögonblicksbild av säkerhetskopiering (beroende på **visningsinställningarna):**
  
  * **Namn** – den tidpunkt då ögonblicksbilden togs.
  * **Skriv** – om det här är en lokal ögonblicksbild eller en ögonblicksbild i molnet.
  * **Ägare** – innehållsägaren. 
  * **Tillgänglig** – om ögonblicksbilden är tillgänglig för tillfället. **True** anger att ögonblicksbilden är tillgänglig och kan återställas. **False** anger att ögonblicksbilden inte längre är tillgänglig. 
  * **Importerad** – om säkerhetskopian importerades. **True** anger att säkerhetskopian importerades från Tjänsten StorSimple Device Manager när enheten konfigurerades i StorSimple Snapshot Manager. **False** anger att den inte importerades, men skapades av StorSimple Snapshot Manager. (Du kan enkelt identifiera en importerad volymgrupp eftersom ett suffix läggs till som identifierar den enhet som volymgruppen importerades från.)
    
    ![Katalog för säkerhetskopiering](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Om du expanderar **Lokal ögonblicksbild** eller Ögonblicksbild av **molnet**och sedan klickar på ett enskilt ögonblicksbildnamn visas följande information om ögonblicksbilden som du har valt i fönstret **Resultat:**
  
  * **Namn** – volymen som identifieras med enhetsbeteckning. 
  * **Lokalt namn** – det lokala namnet på enheten (om tillgängligt). 
  * **Enhet** – namnet på den enhet där volymen finns. 
  * **Tillgänglig** – om ögonblicksbilden är tillgänglig för tillfället. **True** anger att ögonblicksbilden är tillgänglig och kan återställas. **False** anger att ögonblicksbilden inte längre är tillgänglig. 

## <a name="restore-a-volume"></a>Återställa en volym
Använd följande procedur för att återställa en volym från säkerhetskopian.

#### <a name="prerequisites"></a>Krav
Om du inte redan har gjort det skapar du en volym- och volymgrupp och tar sedan bort volymen. Som standard säkerhetskopierar StorSimple Snapshot Manager en volym innan den tillåts tas bort. Denna försiktighetsåtgärd kan förhindra dataförlust om volymen tas bort oavsiktligt eller om data måste återställas av någon anledning. 

StorSimple Snapshot Manager visar följande meddelande medan det skapar försiktighetssäkerhetskopiering.

![Automatiskt meddelande om ögonblicksbilder](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Du kan inte ta bort en volym som ingår i en volymgrupp. Alternativet borttagning är inte tillgängligt. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Så här återställer du en volym
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager. 
2. Expandera noden **Säkerhetskopieringskatalog** i **fönstret Omfattning,** expandera en volymgrupp och klicka sedan på **Lokala ögonblicksbilder** eller **Ögonblicksbilder i molnet**. En lista över ögonblicksbilder av säkerhetskopiering visas i **resultatfönstret.**
3. Leta reda på den säkerhetskopia som du vill återställa, högerklicka och klicka sedan på **Återställ**.
   
    ![Återställa säkerhetskopieringskatalog](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. På bekräftelsesidan granskar du informationen, skriver **Bekräfta**och klickar sedan på **OK**. StorSimple Snapshot Manager använder säkerhetskopian för att återställa volymen.
   
    ![Återställa bekräftelsemeddelande](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Du kan övervaka återställningsåtgärden när den körs. Expandera noden **Jobb** i fönstret **Omfattning** och klicka sedan på **Kör**. Jobbinformationen visas i **fönstret Resultat.** När återställningsjobbet är klart överförs jobbinformationen till listan **Senaste 24 timmar.**

## <a name="clone-a-volume-or-volume-group"></a>Klona en volym- eller volymgrupp
Använd följande procedur för att skapa en dubblett (klon) av en volym eller volymgrupp.

#### <a name="to-clone-a-volume-or-volume-group"></a>Så här klonar du en volym- eller volymgrupp
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Expandera noden **Säkerhetskopieringskatalog** i **fönstret Omfattning,** expandera en volymgrupp och klicka sedan på **Ögonblicksbilder av molnet**. En lista över säkerhetskopior visas i **resultatfönstret.**
3. Leta reda på den volym eller volymgrupp som du vill klona, högerklicka på volym- eller volymgruppsnamnet och klicka på **Klona**. Dialogrutan **Ögonblicksbild av klonmoln** visas.
   
    ![Klona en ögonblicksbild av molnet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Fyll i dialogrutan **Ögonblicksbild av Klonmoln** enligt följande: 
   
   1. Skriv ett namn på den klonade volymen i textrutan **Namn.** Det här namnet visas i **noden Volymer.** 
   2. (Valfritt) väljer **Enhet**och väljer sedan en enhetsbeteckning i listrutan.
   3. (Valfritt) välj **Mapp (NTFS)** och skriv en mappsökväg eller klicka på Bläddra och välj en plats för mappen. 
   4. Klicka på **Skapa**.
5. När kloningsprocessen är klar måste du initiera den klonade volymen. Starta Serverhanteraren och starta sedan Diskhantering. Detaljerade instruktioner finns i [Montera volymer](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). När den har initierats visas volymen under noden **Volymer** i **fönstret Omfattning.** Om volymen inte visas i listan uppdaterar du volymlistan (högerklicka på **noden Volymer** och klicka sedan på **Uppdatera**).

## <a name="delete-a-backup"></a>Ta bort en säkerhetskopia
Använd följande procedur för att ta bort en ögonblicksbild från säkerhetskopieringskatalogen. 

> [!NOTE]
> Om du tar bort en ögonblicksbild tas de säkerhetskopierade data som är associerade med ögonblicksbilden bort. Processen för att rensa upp data från molnet kan dock ta lite tid.<br>


#### <a name="to-delete-a-backup"></a>Så här tar du bort en säkerhetskopia
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Expandera noden **Säkerhetskopieringskatalog** i **fönstret Omfattning,** expandera en volymgrupp och klicka sedan på **Lokala ögonblicksbilder** eller **Ögonblicksbilder i molnet**. En lista över ögonblicksbilder visas i **resultatfönstret.**
3. Högerklicka på den ögonblicksbild som du vill ta bort och klicka sedan på **Ta bort**.
4. När bekräftelsemeddelandet visas klickar du på **OK**.

## <a name="recover-a-file"></a>Återställa en fil
Om en fil tas bort av misstag från en volym kan du återställa filen genom att hämta en ögonblicksbild som föregår borttagningen, med hjälp av ögonblicksbilden för att skapa en klon av volymen och sedan kopiera filen från den klonade volymen till den ursprungliga volymen.

#### <a name="prerequisites"></a>Krav
Innan du börjar kontrollerar du att du har en aktuell säkerhetskopia av volymgruppen. Ta sedan bort en fil som lagras på en av volymerna i den volymgruppen. Slutligen kan du använda följande steg för att återställa den borttagna filen från säkerhetskopian. 

#### <a name="to-recover-a-deleted-file"></a>Så här återställer du en borttagen fil
1. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. Konsolfönstret För StorSimple Snapshot Manager visas. 
2. Expandera noden **Säkerhetskopieringskatalog** i **fönstret Scope** och bläddra till en ögonblicksbild som innehåller den borttagna filen. Vanligtvis bör du välja en ögonblicksbild som skapades strax före borttagningen.
3. Leta reda på den volym som du vill klona, högerklicka och klicka på **Klona**. Dialogrutan **Ögonblicksbild av klonmoln** visas.
   
    ![Klona en ögonblicksbild av molnet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Fyll i dialogrutan **Ögonblicksbild av Klonmoln** enligt följande: 
   
   1. Skriv ett namn på den klonade volymen i textrutan **Namn.** Det här namnet visas i **noden Volymer.** 
   2. (Valfritt) Välj **Enhet**och välj sedan en enhetsbeteckning i listrutan. 
   3. (Valfritt) Välj **Mapp (NTFS)** och skriv en mappsökväg eller klicka på **Bläddra** och välj en plats för mappen. 
   4. Klicka på **Skapa**. 
5. När kloningsprocessen är klar måste du initiera den klonade volymen. Starta Serverhanteraren och starta sedan Diskhantering. Detaljerade instruktioner finns i [Montera volymer](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). När den har initierats visas volymen under noden **Volymer** i **fönstret Omfattning.** 
   
    Om volymen inte visas i listan uppdaterar du volymlistan (högerklicka på **noden Volymer** och klicka sedan på **Uppdatera**).
6. Öppna mappen NTFS som innehåller den klonade volymen, expandera **noden Volymer** och öppna sedan den klonade volymen. Leta reda på filen som du vill återställa och kopiera den till den primära volymen.
7. När du har återställt filen kan du ta bort NTFS-mappen som innehåller den klonade volymen.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Återställa StorSimple Snapshot Manager-databasen
Du bör regelbundet säkerhetskopiera StorSimple Snapshot Manager-databasen på värddatorn. Om en katastrof inträffar eller om värddatorn av någon anledning misslyckas kan du sedan återställa den från säkerhetskopian. Att skapa säkerhetskopian av databasen är en manuell process.

#### <a name="to-back-up-and-restore-the-database"></a>Så här säkerhetskopierar och återställer du databasen
1. Stoppa Microsoft StorSimple Management Service:
   
   1. Starta Serverhanteraren.
   2. Välj **Tjänster**på **Instrumentpanelen i Serverhanteraren på Instrumentpanelen i Serverhanteraren** .
   3. Välj **Microsoft StorSimple Management Service**i fönstret **Tjänster** .
   4. Klicka på **Stoppa tjänsten**under **Microsoft StorSimple Management Service**i den högra rutan.
2. På värddatorn bläddrar du till C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData är en dold mapp.
   > 
   > 
3. Leta reda på katalog-XML-filen, kopiera filen och lagra kopian på en säker plats eller i molnet. Om värden misslyckas kan du använda den här säkerhetskopian för att återställa de principer för säkerhetskopiering som du skapade i StorSimple Snapshot Manager.
   
    ![Katalogfil för azure storsimple-säkerhetskopiering](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Starta om Microsoft StorSimple Management Service: 
   
   1. Välj **Tjänster**på **Instrumentpanelen i Serverhanteraren på Instrumentpanelen i Serverhanteraren** .
   2. Välj **Microsoft StorSimple Management Service**i fönstret **Tjänster** .
   3. Klicka på **Starta om tjänsten**under Microsoft **StorSimple Management Service**i den högra rutan.
5. På värddatorn bläddrar du till C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Ta bort katalog-XML-filen och ersätt den med den säkerhetskopia som du skapade. 
7. Klicka på ikonen StorSimple Snapshot Manager för skrivbordet för att starta StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hur du använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Läs mer om [Uppgifter och arbetsflöden för StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

