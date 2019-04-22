---
title: Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer (utan VMM) till Azure med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar haveriberedskap för lokala virtuella Hyper-V-datorer (utan VMM) till Azure med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: da643a4d7a1dc74385b3854c1952af5ba93bd241
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358095"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer till Azure

c

> [!div class="checklist"]
> * Väljer replikeringskälla och mål.
> * Konfigurerar källmiljön för replikering, inklusive lokala Site Recovery-komponenter och målmiljön för replikeringen.
> * Skapar en replikeringsprincip.
> * Aktiverar replikering för en virtuell dator.

> [!NOTE]
> Självstudier visar den enklaste distribution sökvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artikel i avsnittet How To i Site Recovery i innehållsförteckningen.

## <a name="before-you-begin"></a>Innan du börjar
Detta är den tredje självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md)



## <a name="select-a-replication-goal"></a>Välj ett replikeringsmål

1. Välj valvet på **Recovery Services-valv**. Vi förberedde valvet **ContosoVMVault** i föregående självstudie.
2. I **Komma igång** klickar du på **Site Recovery**. Klicka sedan på **Förbered infrastrukturen**.
3. I **skyddsmål** > **var finns dina datorer??** Välj **lokala**.
4. I **var vill du dina datorer replikeras?** väljer **till Azure**.
5. I **är dina datorer virtualiserade?** Välj **Ja, med Hyper-V**.
6. Välj **Nej** i **Använder du System Center VMM för att hantera dina Hyper-V-värdar**. Klicka sedan på **OK**.

    ![Replikeringsmål](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

1. I **distributionsplanering**, om du planerar en stor distribution, ladda ned Deployment Planner för Hyper-V från länken på sidan. [Läs mer](hyper-v-deployment-planner-overview.md) om Hyper-V distributionsplanering.
2. För den här självstudien behöver vi inte deployment planner. I **har du planerat distributionen?** Välj **jag gör det senare**. Klicka sedan på **OK**.

    ![Distributionsplanering](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Om du vill konfigurera källmiljön du skapar en Hyper-V-plats och lägga till Hyper-V-värdar som innehåller virtuella datorer du vill replikera till webbplatsen. Ladda sedan ned och installera Azure Site Recovery-providern och Azure Recovery Services-agenten på varje värd och registrera Hyper-V-webbplatsen i valvet. 

1. Under **förbereda infrastrukturen**, klickar du på **källa**.
2. I **Förbered källa**, klickar du på **+ Hyper-V-platsen**.
3. I **skapa Hyper-V-platsen**, och ange platsnamn. Vi använder **ContosoHyperVSite**.

    ![Hyper-V-plats](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. När webbplatsen har skapats i **Förbered källa** > **steg 1: Välj Hyper-V-platsen**, Välj den plats som du skapade.
4. Klicka på **+Hyper-V Server**.

    ![Hyper-V-server](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Hämta installationsprogrammet för Microsoft Azure Site Recovery-providern.
6. Ladda ned valvregistreringsnyckeln. Du behöver den här för att installera providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Ladda ned providern](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installera providern

Installera den hämta installationsfilen (AzureSiteRecoveryProvider.exe) på varje Hyper-V-värd som du vill lägga till Hyper-V-platsen. Installationsprogrammet installerar Azure Site Recovery-providern och Recovery Services-agenten på varje Hyper-V-värd.

1. Kör installationsfilen.
2. I installationsguiden för Azure Site Recovery-providern > **Microsoft Update** väljer du att använda Microsoft Update för att söka efter uppdateringar för providern.
3. I **Installation** accepterar du standardinstallationsplatsen för providern och agenten och klickar på **Installera**.
4. Efter installationen: I registreringsguiden för Microsoft Azure Site Recovery > **Valvinställningar** klickar du på **Bläddra** och i **Nyckelfil** väljer du valvnyckelfilen som du laddade ned. 
5. Ange Azure Site Recovery-prenumerationen, valvnamnet (**ContosoVMVault**) och Hyper-V-platsen (**ContosoHyperVSite**) som Hyper-V-servern tillhör.
6. I **Proxyinställningar** väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
7. När servern har registrerats i valvet klickar du på **Slutför** i **Registrering**.

Metadata från Hyper-V-servern hämtas av Azure Site Recovery och servern visas i **Site Recovery-infrastruktur** > **Hyper-V-värdar**. Processen kan ta upp till 30 minuter.        

#### <a name="install-on-hyper-v-core-server"></a>Installera på servern för Hyper-V-kärnor

Om du kör en core-server för Hyper-V, hämta installationsfilen och gör följande:

1. Extrahera filerna från AzureSiteRecoveryProvider.exe till en lokal katalog genom att köra

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2.  Run `.\setupdr.exe /i. Resultaten loggas % Programdata%\ASRLogs\DRASetupWizard.log.

3.  Registrera servern med det här kommandot:

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```
 

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna. 

1. Klicka på **Förbered infrastrukturen** > **Mål**.
2. Välj prenumerationen och resursgruppen **ContosoRG** som de virtuella Azure-datorerna ska skapas i efter redundansväxling.
3. Välj **Resource Manager**-distributionsmodellen.

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.


## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

1. Klicka på **Förbered infrastruktur** > **Replikeringsinställningar** > **+Skapa och koppla**.
2. I **Princip för att skapa och koppla** anger du ett principnamn. Vi använder **ContosoReplicationPolicy**.
3. För den här självstudiekursen kommer vi att lämna standardinställningarna.
    - **Kopieringsfrekvens** anger hur ofta deltadata (efter den inledande replikeringen) replikeras, var femte minut som standard.
    - **Kvarhållning av återställningspunkt** indikerar att återställningspunkter kvar för två timmar.
    - **Frekvens för appkonsekvent ögonblicksbild** anger att återställningspunkterna som innehåller appkonsekventa ögonblicksbilder skapas varje timme.
    - **Starttid för inledande replikering** anger att den inledande replikeringen ska börja direkt.
4. Klicka på **OK** när principen har skapats. När du skapar en ny princip associeras den automatiskt med den angivna Hyper-V-platsen (i våra självstudier är **ContosoHyperVSite**)

    ![Replikeringsprincip](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Aktivera replikering


1. Klicka på **Källa** i **Replikera program**. 
2. I **Källa** väljer du platsen **ContosoHyperVSite**. Klicka sedan på **OK**.
3. I **Mål** kontrollerar du att Azure har angetts som mål och även valvprenumerationen och **Resource Manager**-distributionsmodellen.
4. Om du använder självstudien inställningar, väljer du den **contosovmsacct1910171607** storage-konto som skapats i den tidigare självstudiekursen för replikerade data och **ContosoASRnet** nätverk i vilka virtuella datorer i Azure kommer finnas efter redundansväxling.
5. I **Virtuella datorer** > **Välj** väljer du den virtuella dator som du vill replikera. Klicka sedan på **OK**.

   Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skyddet** är klart är den inledande replikeringen slutförd och den virtuella datorn är redo för redundans.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Köra ett återställningstest](tutorial-dr-drill-azure.md)
