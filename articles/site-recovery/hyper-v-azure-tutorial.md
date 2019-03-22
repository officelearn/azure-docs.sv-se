---
title: Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer (utan VMM) till Azure med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar haveriberedskap för lokala virtuella Hyper-V-datorer (utan VMM) till Azure med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5a7161d05b153a556cce20ec4f4d0cbbfdf1d2d1
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315512"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer till Azure

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudiekursen visar hur du konfigurerar haveriberedskap för lokala virtuella Hyper-V-datorer till Azure. Kursen gäller för virtuella Hyper-V-datorer som inte hanteras av System Center Virtual Machine Manager (VMM). I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Väljer replikeringskälla och mål.
> * Konfigurerar källmiljön för replikering, inklusive lokala Site Recovery-komponenter och målmiljön för replikeringen.
> * Skapar en replikeringsprincip.
> * Aktiverar replikering för en virtuell dator.

Detta är den tredje självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

Innan du börjar är det bra att [granska arkitekturen](concepts-hyper-v-to-azure-architecture.md) för detta haveriberedskapsscenario.

## <a name="select-a-replication-goal"></a>Välj ett replikeringsmål


1. I **Alla tjänster** > **Recovery Services-valv** klickar du på det valv som vi förberedde i den tidigare självstudiekursen, d.v.s. **ContosoVMVault**.
2. I **Komma igång** klickar du på **Site Recovery**. Klicka sedan på **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina datorer?** väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. Välj **Nej** i **Använder du System Center VMM för att hantera dina Hyper-V-värdar**. Klicka sedan på **OK**.

    ![Replikeringsmål](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

När du planerar en stor distribution, bör du kontrollera att du har slutfört [distributionsplaneringen för Hyper-V-replikering](hyper-v-deployment-planner-overview.md). Välj **Jag gör det senare** i listrutan i **Har du slutfört distributionsplanering?**.

![Distributionsplanering](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Konfigurera källmiljön genom att skapa en Hyper-V-webbplats och lägga till Hyper-V-värdar på webbplatsen. Ladda sedan ned och installera Azure Site Recovery-providern och Azure Recovery Services-agenten på varje värd och registrera Hyper-V-webbplatsen i valvet. 

1. Klicka på **Källa** i **Förbereda infrastrukturen**.
2. Klicka på **+Hyper-V-platsen** och ange namnet på den plats som vi skapade i den tidigare självstudiekursen, d.v.s. **ContosoHyperVSite**.

    ![Hyper-V-plats](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. När du har skapat webbplatsen klickar du på **+Hyper-V Server**.

    ![Hyper-V-server](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Ladda ned installationsfilen för providern.
6. Ladda ned valvregistreringsnyckeln. Du behöver den här nyckeln för att köra installationsprogrammet för providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Ladda ned providern](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installera providern

Kör installationsfilen för providern (AzureSiteRecoveryProvider.exe) på varje Hyper-V-värd som du lade till på **ContosoHyperVSite**-platsen. Installationsprogrammet installerar Azure Site Recovery-providern och Recovery Services-agenten på varje Hyper-V-värd.

1. I installationsguiden för Azure Site Recovery-providern > **Microsoft Update** väljer du att använda Microsoft Update för att söka efter uppdateringar för providern.
2. I **Installation** accepterar du standardinstallationsplatsen för providern och agenten och klickar på **Installera**.
3. Efter installationen: I registreringsguiden för Microsoft Azure Site Recovery > **Valvinställningar** klickar du på **Bläddra** och i **Nyckelfil** väljer du valvnyckelfilen som du laddade ned. 
4. Ange Azure Site Recovery-prenumerationen, valvnamnet (**ContosoVMVault**) och Hyper-V-platsen (**ContosoHyperVSite**) som Hyper-V-servern tillhör.
5. I **Proxyinställningar** väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
6. När servern har registrerats i valvet klickar du på **Slutför** i **Registrering**.

Metadata från Hyper-V-servern hämtas av Azure Site Recovery och servern visas i **Site Recovery-infrastruktur** > **Hyper-V-värdar**. Processen kan ta upp till 30 minuter.        

Om du använder en Hyper-V-core-server kan du följa nedanstående steg när du har laddat ned autentiseringsuppgifterna för providern och valvet enligt beskrivningen [här](#set-up-the-source-environment)

1. Extrahera filerna från AzureSiteRecoveryProvider.exe genom att köra

    ``AzureSiteRecoveryProvider.exe /x:. /q``
 
    Detta gör så att filerna extraheras till den lokala katalogen.
 
2.  Kör ``.\setupdr.exe /i ``

    Resultatet loggas till %Programdata%\ASRLogs\DRASetupWizard.log

3.  Registrera servern med hjälp av kommandot:

``cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved" ``
 

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna. 

1. Klicka på **Förbered infrastrukturen** > **Mål**.
2. Välj prenumerationen och resursgruppen **ContosoRG** som de virtuella Azure-datorerna ska skapas i efter redundansväxling.
3. Välj **Resource Manager**-distributionsmodellen.

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.


## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

> [!NOTE]
> Vad gäller replikeringspolicyer för Hyper-V till Azure så dras alternativet 15-minuters kopieringsfrekvens tillbaka och ersätts av inställningarna 5 minuter och 30 sekunder. Replikeringspolicyer med inställningen 15 minuter uppdateras automatiskt till kopieringsfrekvensen 5 minuter. Kopieringsinställningarna 5 minuter och 30 sekunder ger bättre replikeringsprestanda och återställningspunkter jämfört med 15 minuter. Inverkan på bandbreddsanvändning och dataöverföringsvolymer är minimal.

1. Klicka på **Förbered infrastruktur** > **Replikeringsinställningar** > **+Skapa och koppla**.
2. I **Princip för att skapa och koppla** anger du principnamnet **ContosoReplicationPolicy**.
3. Låt standardvärdet stå kvar och klicka på **OK**.
    - **Kopieringsfrekvens** anger att deltadata (efter den inledande replikeringen) replikeras var femte minut.
    - **Kvarhållning av återställningspunkt** anger att kvarhållningsfönstret för varje återställningspunkt är två timmar.
    - **Frekvens för appkonsekvent ögonblicksbild** anger att återställningspunkterna som innehåller appkonsekventa ögonblicksbilder skapas varje timme.
    - **Starttid för inledande replikering** anger att den inledande replikeringen ska börja direkt.
4. Klicka på **OK** när principen har skapats. När du skapar en ny princip associeras den automatiskt med den angivna Hyper-V-platsen (**ContosoHyperVSite**)

    ![Replikeringsprincip](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Aktivera replikering


1. Klicka på **Källa** i **Replikera program**. 
2. I **Källa** väljer du platsen **ContosoHyperVSite**. Klicka sedan på **OK**.
3. I **Mål** kontrollerar du att Azure har angetts som mål och även valvprenumerationen och **Resource Manager**-distributionsmodellen.
4. Välj lagringskontot **contosovmsacct1910171607** som vi skapade i den tidigare självstudiekursen för replikerade data och **ContosoASRnet**-nätverket där de virtuella Azure-datorerna ska finnas efter redundansväxling.
5. I **Virtuella datorer** > **Välj** väljer du den virtuella dator som du vill replikera. Klicka sedan på **OK**.

   Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skyddet** är klart är den inledande replikeringen slutförd och den virtuella datorn är redo för redundans.

## <a name="next-steps"></a>Nästa steg
[Köra ett återställningstest](tutorial-dr-drill-azure.md)
