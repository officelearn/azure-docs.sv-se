---
title: "Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer (utan VMM) till Azure med Azure Site Recovery | Microsoft Docs"
description: "Lär dig hur du konfigurerar haveriberedskap för lokala virtuella Hyper-V-datorer (utan VMM) till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/14/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b946964c162f47a283c37c6eae7e7152e27b6033
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer till Azure

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudiekursen visar hur du konfigurerar haveriberedskap för lokala virtuella Hyper-V-datorer till Azure. Kursen gäller för virtuella Hyper-V-datorer som inte hanteras av System Center Virtual Machine Manager (VMM). I den här guiden får du lära dig hur man:

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


1. I **Alla tjänster** > **Recovery Services-valv** klickar du på namnet på det valv som vi förberedde i den tidigare självstudiekursen, d.v.s. **ContosoVMVault**.
2. I **Komma igång** klickar du på **Site Recovery**. Klicka sedan på **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina datorer?** väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Nej**. Klicka sedan på **OK**.

    ![Replikeringsmål](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Om du vill konfigurera källmiljön lägger du till Hyper-V-värden på en Hyper-V-plats, laddar ned och installerar Azure Site Recovery-providern och Azure Recovery Services-agenten och registrerar Hyper-V-platsen i valvet. 

1. Klicka på **Källa** i **Förbereda infrastrukturen**.
2. Klicka på **+Hyper-V-plats** och ange namnet på den plats som vi skapade i den tidigare kursen, d.v.s. **ContosoHyperVSite**.
3. Klicka på **+Hyper-V Server**.
4. Ladda ned installationsfilen för providern.
5. Ladda ned valvregistreringsnyckeln. Du behöver den när du kör installationsprogrammet för providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Ladda ned providern](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installera providern

Kör installationsfilen för providern (AzureSiteRecoveryProvider.exe) på varje Hyper-V-värd som du lade till på **ContosoHyperVSite**-platsen. Installationsprogrammet installerar Azure Site Recovery-providern och Recovery Services-agenten på varje Hyper-V-värd.

1. I installationsguiden för Azure Site Recovery-providern > **Microsoft Update** väljer du att använda Microsoft Update för att söka efter uppdateringar för providern.
2. I **Installation** accepterar du standardinstallationsplatsen för providern och agenten och klickar på **Installera**.
3. Efter installationen: I registreringsguiden för Microsoft Azure Site Recovery > **Valvinställningar** klickar du på **Bläddra** och i **Nyckelfil** väljer du valvnyckelfilen som du laddade ned. 
4. Ange Azure Site Recovery-prenumerationen, valvnamnet (**ContosoVMVault**) och Hyper-V-platsen (**ContosoHyperVSite**) som Hyper-V-servern tillhör.
5. I **Proxyinställningar** väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
6. När servern har registrerats i valvet klickar du på **Slutför** i **Registrering**.

Metadata från Hyper-V-servern hämtas av Azure Site Recovery och servern visas i **Site Recovery-infrastruktur** > **Hyper-V-värdar**. Det här kan ta upp till 30 minuter.


## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna. 

1. Klicka på **Förbered infrastrukturen** > **Mål**.
2. Välj prenumerationen och resursgruppen **ContosoRG** som de virtuella Azure-datorerna ska skapas i efter redundansväxling.
3. Välj **Resource Manager**-distributionsmodellen.

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.


## <a name="set-up-a-replication-policy"></a>Konfigurera en replikeringsprincip

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
