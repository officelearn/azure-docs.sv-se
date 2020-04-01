---
title: Konfigurera Hyper-V-haveriberedskap med Hjälp av Azure Site Recovery
description: Lär dig hur du ställer in haveriberedskap av lokala virtuella hyper-virtuella datorer (utan VMM) till Azure med hjälp av Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 741d4718b5e6140f4ddd2bb22e1a2ec830763176
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239849"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer till Azure

[Azure Site Recovery-tjänsten](site-recovery-overview.md) bidrar till din katastrofåterställningsstrategi genom att hantera och iscensätta replikering, redundans och återställning av lokala datorer och virtuella Azure-datorer (VIRTUELLA datorer).

Detta är den tredje självstudien i en serie. Den visar hur du ställer in haveriberedskap för lokala virtuella hyper-virtuella datorer till Azure. Den här självstudien tillämpar virtuella hyper-virtuella datorer som inte hanteras av MICROSOFT System Center Virtual Machine Manager (VMM).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Väljer replikeringskälla och mål.
> * Ställ in källreplikeringsmiljön, inklusive lokala site recovery-komponenter och målreplikeringsmiljön.
> * Skapar en replikeringsprincip.
> * Aktiverar replikering för en virtuell dator.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i artiklarna i avsnittet **Instruktioner i** [dokumentationen för webbplatsåterställning](https://docs.microsoft.com/azure/site-recovery).



## <a name="before-you-begin"></a>Innan du börjar

Detta är den tredje självstudien i en serie. Det förutsätter att du redan har slutfört uppgifterna i föregående självstudier:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Väljer ett replikeringsmål

1. Gå till **Recovery Services-valv** i Azure-portalen och välj valvet. Vi förberedde **valvet ContosoVMVault** i föregående handledning.
2. Välj **Platsåterställning** **i Komma igång**och välj sedan Förbered **infrastruktur**.
3. I **Skyddsmål** > Var finns dina **On-premises****maskiner?**
4. I Var vill du replikera dina **To Azure** **datorer?**
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med Hyper-V**.
6. I **Använder du System Center VMM för att hantera dina Hyper-V-värdar?** **No**
7. Välj **OK**.

    ![Replikeringsmål](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

1. Om du planerar en stor distribution i **distributionsplanering**hämtar du distributionsplaneraren för Hyper-V från länken på sidan. [Läs mer](hyper-v-deployment-planner-overview.md) om Hyper-V-distributionsplanering.
2. För den här självstudien behöver vi inte distributionsplaneraren. I **Har du slutfört distributionsplaneringen?** väljer du Jag gör det **senare**och väljer sedan **OK**.

    ![Distributionsplanering](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Om du vill konfigurera källmiljön skapar du en Hyper-V-plats och lägger till Hyper-V-värdarna som innehåller virtuella datorer som du vill replikera på den platsen. Sedan hämtar och installerar du Azure Site Recovery Provider och Azure Recovery Services-agenten på varje värd och registrerar Hyper-V-platsen i valvet.

1. Under **Förbered infrastruktur**väljer du **Källa**.
2. Välj **+ Hyper-V-plats i** **Förbered källa**.
3. Ange platsnamnet på **webbplatsen Skapa hyper-V.** Vi använder **ContosoHyperVSite.**

    ![Hyper-V-plats](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. När webbplatsen har skapats väljer du den plats du skapade i **Förbered källsteg** > **1: Välj Hyper-V-plats.**
5. Välj **+ Hyper-V-server**.

    ![Hyper-V-server](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Hämta installationsprogrammet för Microsoft Azure Site Recovery Provider.
7. Ladda ned valvregistreringsnyckeln. Du behöver den här nyckeln för att installera providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Hämta provider- och registreringsnyckel](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installera providern

Installera den hämtade installationsfilen (AzureSiteRecoveryProvider.exe) på varje Hyper-V-värd som du vill lägga till på Hyper-V-platsen. Installationsprogrammet installerar Azure Site Recovery Provider och Recovery Services-agenten på varje Hyper-V-värd.

1. Kör installationsfilen.
2. I installationsguiden för Azure Site Recovery-providern > **Microsoft Update** väljer du att använda Microsoft Update för att söka efter uppdateringar för providern.
3. I **Installation**godkänner du standardinstallationsplatsen för Providern och agenten och väljer **Installera**.
4. Efter installationen väljer > du Bläddra i guiden **Bläddra**i guiden För återställning av Microsoft Azure Site Recovery i Microsoft Azure Site **Recovery**och väljer du Bläddra i nyckelfilen för Nyckelfilen i Nyckelfilen för Nyckelfiler i **Nyckelfilen.**
5. Ange Azure Site Recovery-prenumerationen, valvnamnet (**ContosoVMVault**) och Hyper-V-platsen (**ContosoHyperVSite**) som Hyper-V-servern tillhör.
6. I **Proxyinställningar** väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
7. Välj **Slutför**i **Registrering**när servern har registrerats i valvet .

Metadata från Hyper-V-servern hämtas av Azure Site Recovery och servern visas i**Hyper-V-värdar för**infrastruktur för **webbplatsåterställning** > . Processen kan ta upp till 30 minuter.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Installera providern på en Hyper-V-kärnserver

Om du kör en Hyper-V-kärnserver laddar du ned installationsfilen och gör så här:

1. Extrahera filerna från AzureSiteRecoveryProvider.exe till en lokal katalog genom att köra det här kommandot:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Kör `.\setupdr.exe /i`. Resultaten loggas till %Programdata%\ASRLogs\DRASetupWizard.log.

3. Registrera servern genom att köra det här kommandot:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och verifiera målresurser:

1. Välj **Förbered infrastrukturmål** > **Target**.
2. Välj prenumerationen och resursgruppen **ContosoRG** där virtuella Azure-datorer skapas efter redundans.
3. Välj **Resource Manager**-distributionsmodellen.

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

1. Välj **Förbered inställningar för infrastrukturreplikering** > **Replication Settings** > **+Skapa och associera**.
2. Ange ett principnamn i **princip skapa och associera.** Vi använder **ContosoReplicationPolicy**.
3. För den här självstudien lämnar vi standardinställningarna:
    - **Kopieringsfrekvensen** anger hur ofta deltadata (efter inledande replikering) replikeras. Standardfrekvensen är var femte minut.
    - **Kvarhållning av återställningspunkter** anger att återställningspunkter kommer att behållas i två timmar.
    - **Frekvens för appkonsekvent ögonblicksbild** anger att återställningspunkterna som innehåller appkonsekventa ögonblicksbilder skapas varje timme.
    - **Inledande starttid för replikering** indikerar att den första replikeringen startar omedelbart.
4. När principen har skapats väljer du **OK**. När du skapar en ny princip associeras den automatiskt med den angivna Hyper-V-platsen. I vår handledning, det är **ContosoHyperVSite**.

    ![Replikeringsprincip](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Aktivera replikering

1. Välj **Källa**i **replikera program**.
2. I **Källa** väljer du platsen **ContosoHyperVSite**. Välj **OK**.
3. I **Target**verifierar du målet (Azure), valvprenumerationen och **resurshanterarens** distributionsmodell.
4. Om du använder självstudieinställningar väljer du det **contosovmsacct1910171607-lagringskonto** som skapats i föregående självstudie för replikerade data. Välj också **ContosoASRnet-nätverket,** där virtuella Azure-datorer ska placeras efter redundans.
5. I **Virtuella datorer** > **Välj**väljer du den virtuella dator som du vill replikera. Välj **OK**.

   Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har slutförts är den första replikeringen klar och den virtuella datorn är klar för redundans.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Köra ett återställningstest](tutorial-dr-drill-azure.md)
