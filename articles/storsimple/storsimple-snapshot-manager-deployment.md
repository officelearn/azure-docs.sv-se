---
title: Distribuera StorSimple Snapshot Manager | Microsoft Docs
description: Lär dig hur du hämtar och installerar StorSimple Snapshot Manager, en snapin-modul för MMC för att hantera StorSimple data skydd och säkerhetskopiering funktioner.
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
ms.openlocfilehash: ee17e4b69d1e6c9de465e4241ee2237361e320b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077831"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Distribuera StorSimple Snapshot Manager MMC-snapin-modulen

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en snapin-modul i Microsoft Management Console (MMC) som förenklar dataskydd och hantering av säkerhetskopiering i en miljö med Microsoft Azure StorSimple. Med StorSimple Snapshot Manager kan du hantera Microsoft Azure StorSimple på lokala och molnlagring som om det är en helt integrerad lagringssystemet, vilket förenklar säkerhetskopiering och återställning och minska kostnaderna. 

Den här självstudien beskrivs konfigurationskrav, samt procedurer för att installera, ta bort och uppgradera StorSimple Snapshot Manager.

> [!NOTE]
> * Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple Virtual Array (även kallat StorSimple lokala virtuella enheter).
> * Om du planerar att installera StorSimple Update 2 på StorSimple-enheten, måste du hämta den senaste versionen av StorSimple Snapshot Manager och installera den **innan du installerar StorSimple Update 2**. Den senaste versionen av StorSimple Snapshot Manager är bakåtkompatibla och fungerar med alla versioner av Microsoft Azure StorSimple. Om du använder den tidigare versionen av StorSimple Snapshot Manager behöver du uppdatera den (du inte behöver avinstallera den tidigare versionen innan du installerar den nya versionen).


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple Snapshot Manager-installation
StorSimple Snapshot Manager kan installeras på datorer som kör Windows Server 2008 R2 SP1, Windows Server 2012 och Windows Server 2012 R2-operativsystem. På servrar som kör Windows 2008 R2, måste du även installera Windows Server 2008 SP1 och Windows Management Framework 3.0.

Innan du installerar eller uppgraderar snapin-modulen StorSimple Snapshot Manager för Microsoft Management Console (MMC), se till att server för Microsoft Azure StorSimple-enhet och värden är korrekt konfigurerade.

## <a name="configure-prerequisites"></a>Konfigurera krav
I följande steg finns en översikt över konfigurationsåtgärder som du måste slutföra innan du installerar StorSimple Snapshot Manager. För hela Microsoft Azure StorSimple-konfigurationen och installationsinformation, inklusive systemkrav och stegvisa instruktioner finns i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Innan du börjar bör du granska den [checklista för distributionskonfiguration](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) och och [distributionskraven](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Innan du installerar StorSimple Snapshot Manager
1. Packa upp, montera och ansluta Microsoft Azure StorSimple-enhet enligt beskrivningen i [installerar enheten StorSimple 8100](storsimple-8100-hardware-installation.md) eller [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
2. Kontrollera att värddatorn kör något av följande operativsystem:
   
   * Windows Server 2008 R2 (på servrar som kör Windows 2008 R2, måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Värden måste vara en Microsoft Azure-dator för en virtuell StorSimple-enhet.
3. Kontrollera att du uppfyller alla krav för Microsoft Azure StorSimple-konfiguration. Mer information går du till [distributionskraven](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Anslut enheten till värden och utför inledande konfiguration. Mer information går du till [distributionsstegen](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Skapa volymer på enheten, tilldela dem till värden och kontrollera att värden kan montera och använda dem för. StorSimple Snapshot Manager stöder följande typer av volymer:
   
   * Enkla volymer
   * Enkla volymer
   * Dynamiska volymer
   * Dynamiska speglade volymer (RAID 1)
   * Klusterdelade volymer
     
     Information om hur du skapar volymer på StorSimple-enheten eller StorSimple-enhet går du till [steg 6: Skapa en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installera en ny StorSimple Snapshot Manager
Innan du installerar StorSimple Snapshot Manager, se till att volymer som du skapade på StorSimple-enheten eller StorSimple-enhet är monterad, initierats och formaterats enligt beskrivningen i [konfigurera krav](#configure-prerequisites).

> [!IMPORTANT]
> * Värden måste vara en Microsoft Azure-dator för en virtuell StorSimple-enhet. 
> * Värden måste köra Windows 2008 R2, Windows Server 2012 eller Windows Server 2012 R2. Om din server kör Windows Server 2008 R2, måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3.0.
> * Du måste konfigurera en iSCSI-anslutning från värden till StorSimple-enheten innan du kan ansluta enheten till StorSimple Snapshot Manager.

Följ dessa steg för att slutföra en helt ny installation av StorSimple Snapshot Manager. Om du installerar en uppgradering måste du gå till [uppgradera eller installera om StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Steg 1: Installera StorSimple Snapshot Manager 
* Steg 2: Ansluta StorSimple Snapshot Manager till en enhet 
* Steg 3: Kontrollera anslutningen till enheten 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Steg 1: Installera StorSimple Snapshot Manager
Använd följande steg för att installera StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Installera StorSimple Snapshot Manager
1. Hämta programvaran för StorSimple Snapshot Manager (Gå till [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) i Microsoft Download Center) och spara den lokalt på värden.
2. I Utforskaren högerklickar du på den komprimerade mappen och klicka sedan på **extrahera alla**.
3. I den **extrahera komprimerade (Zipped) mappar** fönstret i den **väljer ett mål och extrahera filerna** Skriv eller bläddra till sökvägen där du vill att filen som ska extraheras.
   
    > [!IMPORTANT]
    > Du måste installera StorSimple Snapshot Manager på C:-enheten.
    
4. Välj den **Show extraherade filerna när du är klar** och klicka sedan på **extrahera**.
   
    ![Extrahera filer dialogrutan](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. När extraheringen är klar öppnas målmappen. Dubbelklicka på ikonen för installationen av programmet som visas i målmappen.
6. När den **installationen lyckas** visas klickar du på **Stäng**. Du bör se StorSimple Snapshot Manager-ikonen på skrivbordet.
   
    ![skrivbordsikonen](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Steg 2: Ansluta StorSimple Snapshot Manager till en enhet
Använd följande steg för att ansluta StorSimple Snapshot Manager till en StorSimple-enhet.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Att ansluta StorSimple Snapshot Manager till en enhet
1. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. StorSimple Snapshot Manager-fönster visas. Fönstret innehåller en **omfång** fönstret en **resultat** fönstret och en **åtgärder** fönstret. 
   
    ![Användargränssnitt för StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * Den **omfång** fönstret (till vänster) innehåller en lista över noder som är ordnade i en trädstruktur. Du kan expandera vissa noder för att välja en vy eller specifika data som är relaterade till noden. Klicka på pilikonen för att visa eller dölja en nod. Högerklicka på ett objekt i den **omfång** fönstret för att se en lista över tillgängliga åtgärder för objektet.
   * Den **resultat** fönstret (mittenrutan) innehåller detaljerad statusinformation om noden, visa eller data som du har valt i den **omfång** fönstret.
   * Den **åtgärder** fönstret visar en lista över de åtgärder som du kan utföra på noden, vyn eller data som du har valt i den **omfång** fönstret.
     
     En fullständig beskrivning av StorSimple Snapshot Manager-användargränssnittet finns [StorSimple Snapshot Manager-användargränssnittet](storsimple-use-snapshot-manager.md).
2. I den **omfång** fönstret högerklickar du på den **enheter** noden och klicka sedan på **konfigurera en enhet**. Den **konfigurera en enhet** dialogrutan visas.
   
    ![Konfigurera en enhet](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. I den **enhet** Välj IP-adressen för Microsoft Azure StorSimple-enheten eller virtuell enhet. I den **lösenord** text skriver du lösenordet för StorSimple Snapshot Manager som du skapade för enheten i Azure-portalen. Klicka på **OK**.
4. StorSimple Snapshot Manager söker efter den enhet som du har identifierat. Om enheten inte är tillgänglig, StorSimple Snapshot Manager lägger till en anslutning. Du kan [Kontrollera anslutningen till enheten](#to-verify-the-connection) att bekräfta att anslutningen lades till.
   
    Om enheten inte är tillgänglig av någon anledning, returnerar StorSimple Snapshot Manager ett felmeddelande. Klicka på **OK** att Stäng felmeddelandet och klicka sedan på **Avbryt** att Stäng den **konfigurera en enhet** dialogrutan.
5. När den ansluter till en enhet, importerar StorSimple Snapshot Manager varje volym-grupp som har konfigurerats för den aktuella enheten, förutsatt att gruppen volymen är kopplad till säkerhetskopieringar. Volymgrupper som saknar tillhörande säkerhetskopior importeras inte. Dessutom kan importeras inte principer för säkerhetskopiering som har skapats för en volym-grupp. Om du vill visa de importera grupperna, högerklickar du på översta **Volymgrupper** nod i den **omfång** , och klicka på **växla importerade grupper**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Steg 3: Kontrollera anslutningen till enheten
Använd följande steg för att kontrollera att StorSimple Snapshot Manager är ansluten till StorSimple-enheten.

#### <a name="to-verify-the-connection"></a>Att verifiera anslutningen
1. I den **omfång** fönstret klickar du på den **enheter** noden.
   
    ![StorSimple Snapshot Manager Enhetsstatus](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Kontrollera den **resultat** fönstret: 
   
   * Om en grön indikator visas på enhetsikonen och **tillgänglig** visas i den **Status** kolumnen och sedan enheten är ansluten. 
   * Om en röd indikatorn visas på enhetsikonen och är inte tillgänglig i den **Status** kolumnen och sedan enheten inte är ansluten. 
   * Om **uppdatering** visas i den **Status** kolumnen och sedan StorSimple Snapshot Manager hämtar volymgrupper och tillhörande säkerhetskopior för en ansluten enhet.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Uppgradera eller installera om StorSimple Snapshot Manager
Du bör avinstallera StorSimple Snapshot Manager helt innan du uppgraderar eller installera om programvaran. 

Innan du installerar om StorSimple Snapshot Manager, säkerhetskopiera du den befintliga StorSimple Snapshot Manager-databasen på värddatorn. Detta sparar säkerhetskopierad information för principer och konfiguration så att du kan enkelt återställa data från en säkerhetskopia.

Följ dessa steg om du uppgraderar eller installerar om StorSimple Snapshot Manager:

* Steg 1: Avinstallera StorSimple Snapshot Manager 
* Steg 2: Säkerhetskopiera StorSimple Snapshot Manager-databasen 
* Steg 3: Installera om StorSimple Snapshot Manager och återställa databasen 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Steg 1: Avinstallera StorSimple Snapshot Manager
Använd följande steg om du vill avinstallera StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Avinstallera StorSimple Snapshot Manager
1. På värddatorn, öppna den **Kontrollpanelen**, klickar du på **program**, och klicka sedan på **program och funktioner**.
2. I den vänstra rutan klickar du på **avinstallera eller ändra ett program**.
3. Högerklicka på **StorSimple Snapshot Manager**, och klicka sedan på **avinstallera**.
4. StorSimple Snapshot Manager-installationsprogrammet startas. Klicka på **ändra installationen**, och klicka sedan på **avinstallera**.
   
   > [!NOTE]
   > Om det finns några MMC-processer som körs i bakgrunden, till exempel StorSimple Snapshot Manager eller Diskhantering, avinstallationen misslyckas och du får ett meddelande att stänga alla instanser av MMC innan du försöker avinstallera programmet. Välj **automatiskt Stäng programmen och försök att starta om dem när installationen är klar**, och klicka sedan på **OK**.
   > 
   > 
5. När avinstallationen är klar visas en **installationen lyckas** meddelandet visas. Klicka på **Stäng**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Steg 2: Säkerhetskopiera StorSimple Snapshot Manager-databasen
Använd följande steg för att skapa och spara en kopia av StorSimple Snapshot Manager-databasen.

#### <a name="to-back-up-the-database"></a>Att säkerhetskopiera databasen
1. Stoppa Microsoft StorSimple Management-tjänsten:
   
   1. Starta Serverhanteraren.
   2. På instrumentpanelen i Serverhanteraren på den **verktyg** menyn och välj **Services**.
   3. På den **Services** väljer **Management-tjänsten för Microsoft StorSimple**.
   4. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **stoppa tjänsten**.
      
        ![Stoppa tjänsten StorSimple Device Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData är en dold mapp.
  
3. Hitta katalogen XML-fil, kopiera filen och lagra kopian på en säker plats eller i molnet.
   
    ![StorSimple säkerhetskopieringskatalogen fil](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Starta om hanteringstjänsten för Microsoft StorSimple: 
   
   1. På instrumentpanelen i Serverhanteraren på den **verktyg** menyn och välj **Services**.
   2. På den **Services** väljer den **Management-tjänsten för Microsoft StorSimple**.
   3. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **starta om tjänsten**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Steg 3: Installera om StorSimple Snapshot Manager och återställa databasen
Om du vill installera om StorSimple Snapshot Manager, följer du stegen i [installera en ny StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Sedan använda följande procedur för att återställa StorSimple Snapshot Manager-databasen.

#### <a name="to-restore-the-database"></a>Återställa databasen
1. Stoppa Microsoft StorSimple Management-tjänsten:
   
   1. Starta Serverhanteraren.
   2. På instrumentpanelen i Serverhanteraren på den **verktyg** menyn och välj **Services**.
   3. På den **Services** väljer **Management-tjänsten för Microsoft StorSimple**.
   4. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **stoppa tjänsten**.
2. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData är en dold mapp.
   > 
   > 
3. Ta bort katalogen XML-filen och Ersätt den med den version som du sparade tidigare.
4. Starta om hanteringstjänsten för Microsoft StorSimple: 
   
   1. På instrumentpanelen i Serverhanteraren på den **verktyg** menyn och välj **Services**.
   2. På den **Services** väljer **Management-tjänsten för Microsoft StorSimple**.
   3. I den högra rutan under **Management-tjänsten för Microsoft StorSimple**, klickar du på **starta om tjänsten**.

## <a name="next-steps"></a>Nästa steg
* Läs mer om StorSimple Snapshot Manager går du till [vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Läs mer om StorSimple Snapshot Manager-användargränssnittet går du till [StorSimple Snapshot Manager-användargränssnittet](storsimple-use-snapshot-manager.md).
* Mer information om hur du använder StorSimple Snapshot Manager går du till [Använd StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).

