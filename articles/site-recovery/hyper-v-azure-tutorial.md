---
title: Konfigurera haveriberedskap för lokala Hyper-V-datorer (utan VMM) till Azure med Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar haveriberedskap för lokala Hyper-V-datorer (utan VMM) till Azure med Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 96a1a91f49754386de7127cb981d38acd1852e94
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241434"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer till Azure

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten bidrar till din strategi för katastrofåterställning genom att hantera och samordna replikering, redundans och återställning efter fel för lokala datorer och virtuella Azure-datorer (VM).

Detta är den tredje självstudien i en serie. Den visar hur du konfigurerar haveriberedskap för lokala Hyper-V-datorer till Azure. Den här självstudiekursen gäller Hyper-V-datorer som inte hanteras av Microsoft System Center Virtual Machine Manager (VMM).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Väljer replikeringskälla och mål.
> * Konfigurera källmiljön för replikering, inklusive lokala Site Recovery-komponenter och målmiljön för replikeringen.
> * Skapar en replikeringsprincip.
> * Aktiverar replikering för en virtuell dator.

> [!NOTE]
> Självstudier visar den enklaste distribution sökvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artiklarna i den **instruktionsguider** delen av den [dokumentation om Site Recovery](https://docs.microsoft.com/azure/site-recovery).

## <a name="before-you-begin"></a>Innan du börjar

Detta är den tredje självstudien i en serie. Det förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Välj ett replikeringsmål

1. I Azure-portalen går du till **Recovery Services-valv** och väljer valvet. Vi förberedde valvet **ContosoVMVault** i föregående självstudie.
2. I **komma igång**väljer **Site Recovery**, och välj sedan **förbereda infrastrukturen**.
3. I **skyddsmål** > **var finns dina datorer??** väljer **lokala**.
4. I **var vill du dina datorer replikeras?** väljer **till Azure**.
5. I **är dina datorer virtualiserade?** väljer **Ja, med Hyper-V**.
6. I **använder du System Center VMM för att hantera Hyper-V-värdar?** väljer **nr**.
7. Välj **OK**.

    ![Replikeringsmål](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

1. I **distributionsplanering**, om du planerar en stor distribution, ladda ned Deployment Planner för Hyper-V från länken på sidan. [Läs mer](hyper-v-deployment-planner-overview.md) om Hyper-V distributionsplanering.
2. Den här självstudien behöver vi inte Deployment Planner. I **har du planerat distributionen?** väljer **jag gör det senare**, och välj sedan **OK**.

    ![Distributionsplanering](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Om du vill konfigurera källmiljön att skapa en Hyper-V-plats och Lägg till platsen som innehåller virtuella datorer som du vill replikera Hyper-V-värdar. Sedan du ladda ned och installera Azure Site Recovery-providern och Azure Recovery Services-agenten på varje värd, och registrera Hyper-V-platsen i valvet.

1. Under **förbereda infrastrukturen**väljer **källa**.
2. I **Förbered källa**väljer **+ Hyper-V-platsen**.
3. I **skapa Hyper-V-platsen**, ange namnet på webbplatsen. Vi använder **ContosoHyperVSite**.

    ![Hyper-V-plats](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. När webbplatsen har skapats i **Förbered källa** > **steg 1: Välj Hyper-V-platsen**, Välj den plats som du skapade.
5. Välj **+ Hyper-V Server**.

    ![Hyper-V-server](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Hämta installationsprogrammet för Microsoft Azure Site Recovery-providern.
7. Ladda ned valvregistreringsnyckeln. Du behöver den här för att installera providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Ladda ned providern och nyckel för tjänstregistrering](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installera providern

Installera den hämta installationsfilen (AzureSiteRecoveryProvider.exe) på varje Hyper-V-värd som du vill lägga till Hyper-V-platsen. Installationsprogrammet installerar Azure Site Recovery-providern och Recovery Services-agenten på varje Hyper-V-värd.

1. Kör installationsfilen.
2. I installationsguiden för Azure Site Recovery-providern > **Microsoft Update** väljer du att använda Microsoft Update för att söka efter uppdateringar för providern.
3. I **Installation**accepterar du standardinstallationsplatsen för providern och agenten och välj **installera**.
4. Efter installationen: i Registreringsguiden för Microsoft Azure Site Recovery > **Valvinställningar**väljer **Bläddra**, och i **nyckelfilen**väljer vault-nyckeln filen som du har hämtat.
5. Ange Azure Site Recovery-prenumerationen, valvnamnet (**ContosoVMVault**) och Hyper-V-platsen (**ContosoHyperVSite**) som Hyper-V-servern tillhör.
6. I **Proxyinställningar** väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
7. I **registrering**, när servern är registrerad i valvet, väljer **Slutför**.

Metadata från Hyper-V-servern hämtas av Azure Site Recovery och servern visas i **Site Recovery-infrastruktur** > **Hyper-V-värdar**. Processen kan ta upp till 30 minuter.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Installera providern på en Hyper-V-core-server

Om du kör en server för Hyper-V-kärnor, hämta installationsfilen och följ de här stegen:

1. Extrahera filerna från AzureSiteRecoveryProvider.exe till en lokal katalog genom att köra det här kommandot:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Kör `.\setupdr.exe /i`. Resultaten loggas % Programdata%\ASRLogs\DRASetupWizard.log.

3. Registrera servern genom att köra det här kommandot:

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna:

1. Välj **Förbered infrastrukturen** > **Mål**.
2. Välj prenumerationen och resursgruppen **ContosoRG** i som virtuella Azure-datorer som ska skapas efter en redundansväxling.
3. Välj **Resource Manager**-distributionsmodellen.

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

1. Välj **Förbered infrastruktur** > **replikeringsinställningar** >  **+ skapa och koppla**.
2. I **Princip för att skapa och koppla** anger du ett principnamn. Vi använder **ContosoReplicationPolicy**.
3. Lämna standardinställningarna i den här självstudien:
    - **Kopieringsfrekvens** anger hur ofta deltadata (efter den inledande replikeringen) replikeras. Standardfrekvensen är var femte minut.
    - **Kvarhållning av återställningspunkt** indikerar att återställningspunkter kvar för två timmar.
    - **Frekvens för appkonsekvent ögonblicksbild** anger att återställningspunkterna som innehåller appkonsekventa ögonblicksbilder skapas varje timme.
    - **Starttid för inledande replikering** anger att den inledande replikeringen ska börja direkt.
4. När principen har skapats väljer **OK**. När du skapar en ny princip associeras den automatiskt med den angivna Hyper-V-platsen. I våra självstudier ska det **ContosoHyperVSite**.

    ![Replikeringsprincip](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Aktivera replikering

1. I **replikera program**väljer **källa**.
2. I **Källa** väljer du platsen **ContosoHyperVSite**. Välj **OK**.
3. I **Target**, kontrollera målet (Azure), även valvprenumerationen och **Resource Manager** distributionsmodell.
4. Om du använder självstudien inställningar, väljer du den **contosovmsacct1910171607** storage-konto som skapats i den tidigare självstudiekursen för replikerade data. Även välja den **ContosoASRnet** nätverket där Azure de virtuella datorerna ska finnas efter redundansväxling.
5. I **virtuella datorer** > **Välj**, Välj den virtuella dator som du vill replikera. Välj **OK**.

   Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. Efter den **Slutför skydd** jobbet har slutförts, den inledande replikeringen är klar och den virtuella datorn är redo för redundans.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Köra ett återställningstest](tutorial-dr-drill-azure.md)
