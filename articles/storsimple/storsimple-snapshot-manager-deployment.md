---
title: Distribuera StorSimple Snapshot Manager | Microsoft-dokument
description: Lär dig hur du hämtar och installerar StorSimple Snapshot Manager, en MMC-snapin-modul för hantering av StorSimple-funktioner för dataskydd och säkerhetskopiering.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 6d3e3d6cdf7a831bf09d9c4709c1a60d27683438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933370"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Distribuera MMC-snapin-modulen StorSimple Snapshot Manager

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en MMC-snapin-modul (Microsoft Management Console) som förenklar dataskydd och säkerhetskopieringshantering i en Microsoft Azure StorSimple-miljö. Med StorSimple Snapshot Manager kan du hantera Microsoft Azure StorSimple lokalt och molnlagring som om det vore ett helt integrerat lagringssystem, vilket förenklar säkerhetskopierings- och återställningsprocesser och minskar kostnaderna. 

Den här självstudien beskriver konfigurationskrav samt procedurer för installation, borttagning och uppgradering av StorSimple Snapshot Manager.

> [!NOTE]
> * Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple Virtual Arrays (kallas även StorSimple lokala virtuella enheter).
> * Om du planerar att installera StorSimple Update 2 på din StorSimple-enhet måste du hämta den senaste versionen av StorSimple Snapshot Manager och installera den **innan du installerar StorSimple Update 2**. Den senaste versionen av StorSimple Snapshot Manager är bakåtkompatibel och fungerar med alla utgivna versioner av Microsoft Azure StorSimple. Om du använder den tidigare versionen av StorSimple Snapshot Manager måste du uppdatera den (du behöver inte avinstallera den tidigare versionen innan du installerar den nya versionen).


## <a name="storsimple-snapshot-manager-installation"></a>Installation av StorSimple Snapshot Manager
StorSimple Snapshot Manager kan installeras på datorer som kör operativsystemet Windows Server 2008 R2 SP1, Windows Server 2012 eller Windows Server 2012 R2. På servrar som kör Windows 2008 R2 måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3.0.

Innan du installerar eller uppgraderar snapin-modulen StorSimple Snapshot Manager för Microsoft Management Console (MMC) kontrollerar du att Microsoft Azure StorSimple-enheten och värdservern är korrekt konfigurerade.

## <a name="configure-prerequisites"></a>Konfigurera förutsättningar
Följande steg ger en översikt över konfigurationsuppgifter på hög nivå som du måste slutföra innan du installerar StorSimple Snapshot Manager. Fullständig konfigurations- och installationsinformation för Microsoft Azure StorSimple, inklusive systemkrav och steg-för-steg-instruktioner, finns [i Distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Innan du börjar bör du granska checklista för [distributionskonfiguration](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) och [distributionsförutsättningarna](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) i [Distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Innan du installerar StorSimple Snapshot Manager
1. Packa upp, montera och anslut Microsoft Azure StorSimple-enheten enligt beskrivningen i [Installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [Installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
2. Kontrollera att värddatorn kör något av följande operativsystem:
   
   * Windows Server 2008 R2 (på servrar med Windows 2008 R2 måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     För en storSimple virtuell enhet måste värden vara en Virtuell Microsoft Azure-dator.
3. Se till att du uppfyller alla konfigurationskrav för Microsoft Azure StorSimple. Mer information finns [i Distributionsförutsättningar](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Anslut enheten till värden och utför den första konfigurationen. Mer information finns [i distributionsstegen](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Skapa volymer på enheten, tilldela dem till värden och kontrollera att värden kan montera och använda dem. StorSimple Snapshot Manager stöder följande typer av volymer:
   
   * Grundläggande volymer
   * Enkla volymer
   * Dynamiska volymer
   * Speglade dynamiska volymer (RAID 1)
   * Klusterdelade volymer
     
     Information om hur du skapar volymer på StorSimple-enheten eller den virtuella storsimpleenheten finns i [steg 6: Skapa en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)i Distribuera din lokala [StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installera en ny StorSimple Snapshot Manager
Innan du installerar StorSimple Snapshot Manager kontrollerar du att de volymer som du skapade på StorSimple-enheten eller StorSimple-enheten är monterade, initierade och formaterade enligt beskrivningen i [Konfigurera förutsättningar](#configure-prerequisites).

> [!IMPORTANT]
> * För en storSimple virtuell enhet måste värden vara en Virtuell Microsoft Azure-dator. 
> * Värden måste köra Windows 2008 R2, Windows Server 2012 eller Windows Server 2012 R2. Om servern kör Windows Server 2008 R2 måste du också installera Windows Server 2008 SP1 och Windows Management Framework 3.0.
> * Du måste konfigurera en iSCSI-anslutning från värden till StorSimple-enheten innan du kan ansluta enheten till StorSimple Snapshot Manager.

Följ dessa steg för att slutföra en ny installation av StorSimple Snapshot Manager. Om du installerar en uppgradering går du till [Uppgradera eller installera om StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Steg 1: Installera StorSimple Snapshot Manager 
* Steg 2: Anslut StorSimple Snapshot Manager till en enhet 
* Steg 3: Verifiera anslutningen till enheten 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Steg 1: Installera StorSimple Snapshot Manager
Använd följande steg för att installera StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Så här installerar du StorSimple Snapshot Manager
1. Ladda ned Programvaran StorSimple Snapshot Manager (gå till [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) i Microsoft Download Center) och spara den lokalt på värden.
2. Högerklicka på den komprimerade mappen i Utforskaren och klicka sedan på **Extrahera alla**.
3. Skriv eller bläddra till sökvägen där du vill fil extrahera **i** fönstret Välj en mål- och utdragsfil i fönstret **Välj en mål- och utdragsfil.**
   
    > [!IMPORTANT]
    > Du måste installera StorSimple Snapshot Manager på C:-enheten.
    
4. Markera kryssrutan **Visa extraherade filer när** du är klar och klicka sedan på **Extrahera**.
   
    ![Dialogrutan Extrahera filer](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. När extraheringen är klar öppnas målmappen. Dubbelklicka på programinställningsikonen som visas i målmappen.
6. När meddelandet **Konfigurera installationsprogrammet** visas klickar du på **Stäng**. Du bör se storsimple Snapshot Manager-ikonen på skrivbordet.
   
    ![Skrivbordsikon](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Steg 2: Anslut StorSimple Snapshot Manager till en enhet
Följ följande steg för att ansluta StorSimple Snapshot Manager till en StorSimple-enhet.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Så här ansluter du StorSimple Snapshot Manager till en enhet
1. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. Fönstret StorSimple Snapshot Manager visas. Fönstret innehåller ett **scopefönster,** ett **resultatfönster** och ett **åtgärdsfönster.** 
   
    ![Användargränssnittet i StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * **Fönstret Scope** (den vänstra rutan) innehåller en lista över noder som är ordnade i en trädstruktur. Du kan expandera vissa noder för att välja en vy eller specifika data som är relaterade till den noden. Klicka på pilikonen om du vill expandera eller komprimera en nod. Högerklicka på ett objekt i **fönstret Omfattning om** du vill visa en lista över tillgängliga åtgärder för objektet.
   * **Fönstret Resultat** (mittfönstret) innehåller detaljerad statusinformation om noden, vyn eller data som du har markerat i **fönstret Scope.**
   * I fönstret **Åtgärder** visas de åtgärder som du kan utföra på noden, vyn eller data som du har markerat i **fönstret Omfattning.**
     
     En fullständig beskrivning av användargränssnittet i StorSimple Snapshot Manager finns i [StorSimple Snapshot Manager användargränssnitt](storsimple-use-snapshot-manager.md).
2. Högerklicka på noden **Enheter** i **fönstret Omfattning** och klicka sedan på Konfigurera **en enhet**. Dialogrutan **Konfigurera en enhet** visas.
   
    ![Konfigurera en enhet](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. I listrutan **Enhet** väljer du IP-adressen för Microsoft Azure StorSimple-enheten eller den virtuella enheten. Skriv lösenordet för StorSimple Snapshot Manager som du skapade för enheten i Azure-portalen i textrutan **Lösenord.** Klicka på **OK**.
4. StorSimple Snapshot Manager söker efter den enhet som du identifierade. Om enheten är tillgänglig lägger StorSimple Snapshot Manager till en anslutning. Du kan [verifiera anslutningen till enheten](#to-verify-the-connection) för att bekräfta att anslutningen har lagts till.
   
    Om enheten inte är tillgänglig av någon anledning returnerar StorSimple Snapshot Manager ett felmeddelande. Klicka på **OK** för att stänga felmeddelandet och klicka sedan på **Avbryt** för att stänga dialogrutan **Konfigurera en enhet.**
5. När den ansluter till en enhet importerar StorSimple Snapshot Manager varje volymgrupp som konfigurerats för den enheten, förutsatt att volymgruppen har associerade säkerhetskopior. Volymgrupper som inte har associerade säkerhetskopior importeras inte. Dessutom importeras inte principer för säkerhetskopiering som har skapats för en volymgrupp. Om du vill visa de importerade grupperna högerklickar du på noden **volymgrupper** längst upp i **fönstret Scope** och klickar på Växla **importerade grupper**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Steg 3: Verifiera anslutningen till enheten
Följ följande steg för att kontrollera att StorSimple Snapshot Manager är ansluten till StorSimple-enheten.

#### <a name="to-verify-the-connection"></a>Så här verifierar du anslutningen
1. Klicka på noden **Enheter** i **fönstret Omfattning.**
   
    ![Enhetsstatus för StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Kontrollera **resultatfönstret:** 
   
   * Om en grön indikator visas på enhetsikonen och **Tillgänglig** visas i **kolumnen Status** ansluts enheten. 
   * Om en röd indikator visas på enhetsikonen och inte finns i **kolumnen Status** är enheten inte ansluten. 
   * Om **Uppdatering** visas i kolumnen **Status** hämtar StorSimple Snapshot Manager volymgrupper och tillhörande säkerhetskopior för en ansluten enhet.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Uppgradera eller installera om StorSimple Snapshot Manager
Du bör avinstallera StorSimple Snapshot Manager helt innan du uppgraderar eller installerar om programvaran. 

Innan du installerar om StorSimple Snapshot Manager säkerhetskopierar du den befintliga StorSimple Snapshot Manager-databasen på värddatorn. Detta sparar principer för säkerhetskopiering och konfigurationsinformation så att du enkelt kan återställa dessa data från säkerhetskopiering.

Gör så här om du uppgraderar eller installerar om StorSimple Snapshot Manager:

* Steg 1: Avinstallera StorSimple Snapshot Manager 
* Steg 2: Säkerhetskopiera StorSimple Snapshot Manager-databasen 
* Steg 3: Installera om StorSimple Snapshot Manager och återställa databasen 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Steg 1: Avinstallera StorSimple Snapshot Manager
Följ följande steg för att avinstallera StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Så här avinstallerar du StorSimple Snapshot Manager
1. Öppna **Kontrollpanelen**på värddatorn, klicka på **Program**och klicka sedan på **Program och funktioner**.
2. Klicka på Avinstallera **eller ändra ett program**i den vänstra rutan.
3. Högerklicka på **StorSimple Snapshot Manager**och klicka sedan på **Avinstallera**.
4. Då startas installationsprogrammet för StorSimple Snapshot Manager. Klicka på **Ändra installation och**sedan på **Avinstallera**.
   
   > [!NOTE]
   > Om det finns några MMC-processer som körs i bakgrunden, till exempel StorSimple Snapshot Manager eller Diskhantering, misslyckas avinstallationen och du kommer att få ett meddelande om att stänga alla instanser av MMC innan du försöker avinstallera programmet. Markera **Stäng program automatiskt och försök starta om dem när installationen är klar**och klicka sedan på **OK**.
   > 
   > 
5. När avinstallationen är klar visas ett meddelande **om lyckad installation.** Klicka på **Stäng**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Steg 2: Säkerhetskopiera StorSimple Snapshot Manager-databasen
Använd följande steg för att skapa och spara en kopia av StorSimple Snapshot Manager-databasen.

#### <a name="to-back-up-the-database"></a>Så här säkerhetskopierar du databasen
1. Stoppa Microsoft StorSimple Management Service:
   
   1. Starta Serverhanteraren.
   2. Välj **Tjänster**på **Instrumentpanelen i Serverhanteraren på Instrumentpanelen i Serverhanteraren** .
   3. På sidan **Tjänster** väljer du **Microsoft StorSimple Management Service**.
   4. Klicka på **Stoppa tjänsten**under **Microsoft StorSimple Management Service**i den högra rutan.
      
        ![Stoppa Tjänsten StorSimple Device Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData är en dold mapp.
  
3. Leta reda på katalog-XML-filen, kopiera filen och lagra kopian på en säker plats eller i molnet.
   
    ![Katalogfil för säkerhetskopiering av StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Starta om Microsoft StorSimple Management Service: 
   
   1. Välj **Tjänster**på **Instrumentpanelen i Serverhanteraren på Instrumentpanelen i Serverhanteraren** .
   2. På sidan **Tjänster** väljer du **Microsoft StorSimple Management Service**.
   3. Klicka på **Starta om tjänsten**under Microsoft **StorSimple Management Service**i den högra rutan. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Steg 3: Installera om StorSimple Snapshot Manager och återställa databasen
Om du vill installera om StorSimple Snapshot Manager följer du stegen i [Installera en ny StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Använd sedan följande procedur för att återställa StorSimple Snapshot Manager-databasen.

#### <a name="to-restore-the-database"></a>Så här återställer du databasen
1. Stoppa Microsoft StorSimple Management Service:
   
   1. Starta Serverhanteraren.
   2. Välj **Tjänster**på **Instrumentpanelen i Serverhanteraren på Instrumentpanelen i Serverhanteraren** .
   3. På sidan **Tjänster** väljer du **Microsoft StorSimple Management Service**.
   4. Klicka på **Stoppa tjänsten**under **Microsoft StorSimple Management Service**i den högra rutan.
2. Bläddra till C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData är en dold mapp.
   > 
   > 
3. Ta bort katalog-XML-filen och ersätt den med den version som du sparade tidigare.
4. Starta om Microsoft StorSimple Management Service: 
   
   1. Välj **Tjänster**på **Instrumentpanelen i Serverhanteraren på Instrumentpanelen i Serverhanteraren** .
   2. På sidan **Tjänster** väljer du **Microsoft StorSimple Management Service**.
   3. Klicka på **Starta om tjänsten**under Microsoft **StorSimple Management Service**i den högra rutan.

## <a name="next-steps"></a>Nästa steg
* Mer information om StorSimple Snapshot Manager finns i [Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Om du vill veta mer om användargränssnittet i StorSimple Snapshot Manager går du till [StorSimple Snapshot Manager användargränssnitt .](storsimple-use-snapshot-manager.md)
* Om du vill veta mer om hur du använder StorSimple Snapshot Manager går du till [Använd StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).

