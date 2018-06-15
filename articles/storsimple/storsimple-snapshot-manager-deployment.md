---
title: Distribuera StorSimple Snapshot Manager | Microsoft Docs
description: Lär dig hur du hämtar och installerar StorSimple Snapshot Manager, en snapin-modul för MMC för att hantera StorSimple skydd och säkerhetskopiering datafunktioner.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: cde355381b0d726a1ab340bc4230b2dc8f6e2c56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876322"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Distribuera StorSimple Snapshot Manager MMC-snapin-modulen

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en snapin-modul i Microsoft Management Console (MMC) som förenklar dataskydd och hantering av säkerhetskopiering i en miljö med Microsoft Azure StorSimple. Med StorSimple Snapshot Manager som du kan hantera Microsoft Azure StorSimple lokala och molnlagring som om det vore en helt integrerad lagringssystemet därmed förenkla säkerhetskopiering och återställning och minska kostnaderna. 

Den här självstudiekursen beskriver konfigurationskrav, samt procedurer för att installera, ta bort och uppgradera StorSimple Snapshot Manager.

> [!NOTE]
> * Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple virtuell matriser (även kallat StorSimple lokala virtuella enheter).
> * Om du planerar att installera StorSimple uppdatering 2 på din StorSimple-enhet, måste du hämta den senaste versionen av StorSimple Snapshot Manager och installera den **innan du installerar StorSimple uppdatering 2**. Den senaste versionen av StorSimple Snapshot Manager är bakåtkompatibla och fungerar med alla versioner av Microsoft Azure StorSimple. Om du använder den tidigare versionen av StorSimple Snapshot Manager behöver du uppdatera den (du inte behöver avinstallera den tidigare versionen innan du installerar den nya versionen).


## <a name="storsimple-snapshot-manager-installation"></a>Installation av StorSimple Snapshot Manager
StorSimple Snapshot Manager kan installeras på datorer som kör Windows Server 2008 R2 SP1, Windows Server 2012 eller Windows Server 2012 R2-operativsystemet. På servrar som kör Windows 2008 R2, måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3.0.

Innan du installerar eller uppgraderar snapin-modulen StorSimple Snapshot Manager för Microsoft Management Console (MMC), se till att server för Microsoft Azure StorSimple enheten och värden är korrekt konfigurerade.

## <a name="configure-prerequisites"></a>Konfigurera krav
Följande steg ger en översikt av konfigurationsåtgärder som du måste slutföra innan du installerar StorSimple Snapshot Manager. Slutföra konfigurationen av Microsoft Azure StorSimple och installationsprogrammet information, inklusive systemkrav och stegvisa instruktioner finns [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Innan du börjar bör du granska den [checklista för distributionskonfiguration](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) och och [kraven för distribution av](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Innan du installerar StorSimple Snapshot Manager
1. Packa upp, montera och ansluta Microsoft Azure StorSimple-enhet som beskrivs i [installerar StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [installera din StorSimple-8600-enhet](storsimple-8600-hardware-installation.md).
2. Kontrollera att värddatorn kör något av följande operativsystem:
   
   * Windows Server 2008 R2 (på servrar som kör Windows 2008 R2, måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Värden måste vara en Microsoft Azure-dator för en virtuell StorSimple-enhet.
3. Kontrollera att du uppfyller alla krav för Microsoft Azure StorSimple-konfiguration. Mer information går du till [kraven för distribution av](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Anslut enheten till värden och den första konfigurationen. Mer information går du till [distributionssteg](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Skapa volymer på enheten, tilldela dem till värden och kontrollera att värden kan montera och använda dem. StorSimple Snapshot Manager stöder följande typer av volymer:
   
   * Enkla volymer
   * Enkla volymer
   * Dynamiska volymer
   * Dynamiska volymer (RAID 1)
   * Klusterdelade volymer
     
     Information om hur du skapar volymer på StorSimple-enhet eller virtuell StorSimple-enhet går du till [steg 6: skapa en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installera en ny StorSimple Snapshot Manager
Innan du installerar StorSimple Snapshot Manager, se till att de volymer som du skapade i StorSimple-enhet eller virtuella StorSimple-enheten är monterad, initiera och formaterad enligt beskrivningen i [Konfigurera förutsättningar](#configure-prerequisites).

> [!IMPORTANT]
> * Värden måste vara en Microsoft Azure-dator för en virtuell StorSimple-enhet. 
> * Värden måste köra Windows 2008 R2, Windows Server 2012 eller Windows Server 2012 R2. Om servern kör Windows Server 2008 R2, måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3.0.
> * Innan du kan ansluta enheten till StorSimple Snapshot Manager måste du konfigurera en iSCSI-anslutning från värden till StorSimple-enhet.

Följ dessa steg för att slutföra en ny installation av StorSimple Snapshot Manager. Om du installerar en uppgradering, gå till [uppgradera eller installera om StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Steg 1: Installera StorSimple Snapshot Manager 
* Steg 2: Anslut StorSimple Snapshot Manager till en enhet 
* Steg 3: Kontrollera anslutningen till enheten 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Steg 1: Installera StorSimple Snapshot Manager
Använd följande steg för att installera StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Så här installerar du StorSimple Snapshot Manager
1. Ladda ned programvaran StorSimple Snapshot Manager (Gå till [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) i Microsoft Download Center) och spara den lokalt på värden.
2. I Utforskaren, högerklicka på den komprimerade mappen och klicka sedan på **extrahera alla**.
3. I den **extrahera komprimerade (Zipped) mappar** fönster i den **Välj ett mål och extrahera filer** Skriv eller bläddra till sökvägen där du vill filen ska extraheras.
   
    > [!IMPORTANT]
    > Du måste installera StorSimple Snapshot Manager på enhet C:.
    
4. Välj den **Visa extraherade filer när du är färdig** kryssrutan och klicka sedan på **extrahera**.
   
    ![Extrahera filerna i dialogrutan](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. När extraheringen är klar, öppnar målmappen. Dubbelklicka på ikonen för installationen av programmet som visas i målmappen.
6. När den **installationen lyckas** visas klickar du på **Stäng**. Du bör se ikonen StorSimple Snapshot Manager på skrivbordet.
   
    ![skrivbordsikon](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Steg 2: Anslut StorSimple Snapshot Manager till en enhet
Använd följande steg för att ansluta StorSimple Snapshot Manager till en StorSimple-enhet.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Ansluta StorSimple Snapshot Manager till en enhet
1. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. StorSimple Snapshot Manager öppnas. Fönstret innehåller en **omfång** fönstret en **resultat** rutan och en **åtgärder** fönstret. 
   
    ![Användargränssnittet för StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * Den **omfång** rutan (till vänster) innehåller en lista över ordnade i en trädstruktur noder. Du kan expandera vissa noder för att välja en vy eller specifika data som är relaterade till noden. Klicka på pilikonen för att expandera eller komprimera en nod. Högerklicka på ett objekt i den **omfång** fönstret om du vill se en lista över tillgängliga åtgärder för objektet.
   * Den **resultat** fönstret (mittrutan) innehåller detaljerad statusinformation om noden, visa eller data som du har valt i den **omfång** fönstret.
   * Den **åtgärder** fönstret visas de åtgärder som du kan utföra på noden, vyn eller data som du har valt i den **omfång** fönstret.
     
     En fullständig beskrivning av användargränssnittet för StorSimple Snapshot Manager finns [StorSimple Snapshot Manager användargränssnittet](storsimple-use-snapshot-manager.md).
2. I den **omfång** fönstret högerklickar du på den **enheter** noden och klicka sedan på **konfigurera en enhet**. Den **konfigurera en enhet** dialogrutan visas.
   
    ![Konfigurera en enhet](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. I den **enhet** Välj IP-adressen för Microsoft Azure StorSimple-enheten eller virtuella enheten. I den **lösenord** text Skriv lösenordet för StorSimple Snapshot Manager som du skapade för enheten i Azure-portalen. Klicka på **OK**.
4. StorSimple Snapshot Manager söker efter den enhet som du har identifierats. Om enheten inte är tillgänglig, StorSimple Snapshot Manager lägger till en anslutning. Du kan [verifiera anslutningen till enheten](#to-verify-the-connection) att bekräfta att anslutningen lades till.
   
    Om enheten inte är tillgänglig av någon anledning, returnerar StorSimple Snapshot Manager ett felmeddelande. Klicka på **OK** Stäng felmeddelandet och klicka sedan på **Avbryt** att stänga den **konfigurera en enhet** dialogrutan.
5. När den ansluter till en enhet, importerar StorSimple Snapshot Manager varje volym-grupp som konfigurerats för denna enhet, förutsatt att gruppen volymen har associerade säkerhetskopior. Volymen grupper som inte har tillhörande säkerhetskopior har inte importerats. Dessutom har principer för säkerhetskopiering som har skapats för en grupp för volymen inte importerats. Om du vill visa de importerade grupperna högerklickar du på översta **volym grupper** nod i den **omfång** rutan och klicka på **växla importerade grupperna**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Steg 3: Kontrollera anslutningen till enheten
Använd följande steg för att verifiera att StorSimple Snapshot Manager är ansluten till StorSimple-enhet.

#### <a name="to-verify-the-connection"></a>Att verifiera anslutningen
1. I den **omfång** rutan klickar du på den **enheter** nod.
   
    ![Status för StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Kontrollera den **resultat** fönstret: 
   
   * Om en grön markör som visas på enhetens ikon och **tillgänglig** visas i den **Status** kolumn, och sedan enheten är ansluten. 
   * Om en röd indikator visas på enhetens ikon och inte tillgänglig i den **Status** kolumn, och sedan enheten inte är ansluten. 
   * Om **uppdaterar** visas i den **Status** kolumn, och sedan StorSimple Snapshot Manager hämtar volym grupper och tillhörande säkerhetskopior för en ansluten enhet.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Uppgradera eller installera om StorSimple Snapshot Manager
Du måste avinstallera StorSimple Snapshot Manager helt innan du uppgraderar eller installera om programvaran. 

Innan du installerar om StorSimple Snapshot Manager säkerhetskopierar du den befintliga StorSimple Snapshot Manager-databasen på värddatorn. Detta sparar informationen om säkerhetskopiering principerna och konfigurationen så att du lätt kan återställa data från en säkerhetskopia.

Följ dessa steg om du uppgraderar eller installerar om StorSimple Snapshot Manager:

* Steg 1: Avinstallera StorSimple Snapshot Manager 
* Steg 2: Säkerhetskopiera StorSimple Snapshot Manager-databasen 
* Steg 3: Installera om StorSimple Snapshot Manager och återställa databasen 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Steg 1: Avinstallera StorSimple Snapshot Manager
Använd följande steg för att avinstallera StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Så här avinstallerar du StorSimple Snapshot Manager
1. På värddatorn, öppna den **Kontrollpanelen**, klickar du på **program**, och klicka sedan på **program och funktioner**.
2. I den vänstra rutan klickar du på **avinstallera eller ändra ett program**.
3. Högerklicka på **StorSimple Snapshot Manager**, och klicka sedan på **avinstallera**.
4. Detta startar installationsprogrammet för StorSimple Snapshot Manager. Klicka på **ändra installationen**, och klicka sedan på **avinstallera**.
   
   > [!NOTE]
   > Om det finns några MMC-processer som körs i bakgrunden, till exempel StorSimple Snapshot Manager eller Diskhantering, avinstallationen misslyckas och du får ett meddelande att stänga alla instanser av MMC innan du försöker avinstallera programmet. Välj **automatiskt Stäng programmen och försök starta om dem när installationen är klar**, och klicka sedan på **OK**.
   > 
   > 
5. När avinstallationen är klar visas en **installationen lyckas** meddelandet visas. Klicka på **Stäng**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Steg 2: Säkerhetskopiera StorSimple Snapshot Manager-databasen
Använd följande steg för att skapa och spara en kopia av StorSimple Snapshot Manager-databasen.

#### <a name="to-back-up-the-database"></a>Att säkerhetskopiera databasen
1. Stoppa tjänsten Microsoft StorSimple Management:
   
   1. Starta Serverhanteraren.
   2. På instrumentpanelen i Serverhanteraren på den **verktyg** väljer du **Services**.
   3. På den **Services** väljer **Management-tjänsten för Microsoft StorSimple**.
   4. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **stoppa tjänsten**.
      
        ![Stoppa tjänsten StorSimple Device Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData är en dold mapp.
  
3. Hitta katalogen XML-filen, kopiera filen och lagra kopian på en säker plats eller i molnet.
   
    ![StorSimple säkerhetskopiering katalogfil](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Starta om tjänsten Microsoft StorSimple Management: 
   
   1. På instrumentpanelen i Serverhanteraren på den **verktyg** väljer du **Services**.
   2. På den **Services** väljer den **Microsoft StorSimple Management Servic**e.
   3. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **starta om tjänsten**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Steg 3: Installera om StorSimple Snapshot Manager och återställa databasen
Installera om StorSimple Snapshot Manager genom att följa stegen i [installera en ny StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Använd sedan följande procedur för att återställa StorSimple Snapshot Manager-databasen.

#### <a name="to-restore-the-database"></a>Att återställa databasen
1. Stoppa tjänsten Microsoft StorSimple Management:
   
   1. Starta Serverhanteraren.
   2. På instrumentpanelen i Serverhanteraren på den **verktyg** väljer du **Services**.
   3. På den **Services** väljer **Management-tjänsten för Microsoft StorSimple**.
   4. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **stoppa tjänsten**.
2. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData är en dold mapp.
   > 
   > 
3. Ta bort katalogen XML-filen och Ersätt den med den version som du sparade tidigare.
4. Starta om tjänsten Microsoft StorSimple Management: 
   
   1. På instrumentpanelen i Serverhanteraren på den **verktyg** väljer du **Services**.
   2. På den **Services** väljer **Management-tjänsten för Microsoft StorSimple**.
   3. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **starta om tjänsten**.

## <a name="next-steps"></a>Nästa steg
* Läs mer om StorSimple Snapshot Manager går du till [vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Mer information om användargränssnittet för StorSimple Snapshot Manager går du till [StorSimple Snapshot Manager användargränssnittet](storsimple-use-snapshot-manager.md).
* Mer information om hur du använder StorSimple Snapshot Manager går du till [Använd StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).

