---
title: Konfigurera haveriberedskap för lokala Hyper-V-datorer i VMM-moln till Azure med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar haveriberedskap för lokala Hyper-V-datorer i System Center VMM-moln till Azure med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 64559f653ba8a466de7bec10db34383b508e3e4b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361286"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Konfigurera haveriberedskap för lokala Hyper-V-datorer i VMM-moln till Azure

Den här artikeln beskriver hur du aktiverar replikering för den lokala Hyper-V-datorer som hanteras av System Center Virtual Machine Manager (VMM), på haveriberedskap till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service. Om du inte använder VMM kan sedan [i den här kursen](hyper-v-azure-tutorial.md).

Det här är den tredje självstudien i en serie som visar hur du konfigurerar haveriberedskap till Azure för lokala virtuella VMware-datorer. I den tidigare självstudiekursen, vi [förberett lokala Hyper-V-miljö](hyper-v-prepare-on-premises-tutorial.md) för haveriberedskap till Azure. 

I den här guiden får du lära dig att:


> [!div class="checklist"]
> * Väljer replikeringskälla och mål.
> * Konfigurerar källmiljön för replikering, inklusive lokala Site Recovery-komponenter och målmiljön för replikeringen.
> * Konfigurera nätverksmappning, för att mappa mellan VMM-VM-nätverk och virtuella Azure-nätverk.
> * Skapa replikeringsprincip
> * Aktivera replikering för en virtuell dator


> [!NOTE]
> Självstudier visar den enklaste distribution sökvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artikel i avsnittet How To i Site Recovery i innehållsförteckningen.

## <a name="before-you-begin"></a>Innan du börjar

Detta är den tredje självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md) detta är den tredje självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna:


## <a name="select-a-replication-goal"></a>Välj ett replikeringsmål

1. Välj valvet på **Recovery Services-valv**. Vi förberedde valvet **ContosoVMVault** i föregående självstudie.
2. I **Komma igång** klickar du på **Site Recovery**. Klicka sedan på **Förbered infrastrukturen**.
3. I **skyddsmål** > **var finns dina datorer??** väljer **lokala**.
4. I **var vill du dina datorer replikeras?** väljer **till Azure**.
5. I **är dina datorer virtualiserade?** Välj **Ja, med Hyper-V**.
6. I **använder du System Center VMM**väljer **Ja**. Klicka sedan på **OK**.

    ![Replikeringsmål](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)


## <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

1. I **distributionsplanering**, om du planerar en stor distribution, ladda ned Deployment Planner för Hyper-V från länken på sidan. [Läs mer](hyper-v-deployment-planner-overview.md) om Hyper-V distributionsplanering.
2. För den här självstudien behöver vi inte deployment planner. I **har du planerat distributionen?** väljer **jag gör det senare**. Klicka sedan på **OK**.


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

När du har konfigurerat källmiljön kan du installera Azure Site Recovery-providern på VMM-servern och registrera servern i valvet. Du installerar Azure Recovery Services-agenten på varje Hyper-V-värd. 

1. Klicka på **Källa** i **Förbereda infrastrukturen**.
2. I **Förbered källa** klickar du på **+ VMM** för att lägga till en VMM-server. I **Lägg till server** kontrollerar du att **System Center VMM-server** visas i **Servertyp**.
3. Hämta installationsprogrammet för Microsoft Azure Site Recovery-providern.
4. Ladda ned valvregistreringsnyckeln. Du behöver den när du kör installationsprogrammet för providern. Nyckeln är giltig i fem dagar efter att du har genererat den.
5. Hämta installationsprogrammet för Microsoft Azure Recovery Services-agenten.

    ![Ladda ned](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installera providern på VMM-servern

1. I installationsguiden för Azure Site Recovery-providern > **Microsoft Update** väljer du att använda Microsoft Update för att söka efter uppdateringar för providern.
2. I **Installation**accepterar du standardinstallationsplatsen för providern och klickar på **installera**. 
3. Efter installationen: i Registreringsguiden för Microsoft Azure Site Recovery > **Valvinställningar**, klickar du på **Bläddra**, och i **nyckelfilen**väljer vault-nyckeln fil som du laddas ned.
4. Ange Azure Site Recovery-prenumerationen och valvnamnet (**ContosoVMVault**). Ange ett eget namn för VMM-server, för att identifiera den i valvet.
5. I **Proxyinställningar** väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
6. Acceptera standardplatsen för det certifikatet som används för att kryptera data. Krypterade data dekrypteras när du växlar över.
7. I **synkronisera molnmetadata**väljer **synka molnmetadata till Site Recovery-portalen**. Den här åtgärden behöver bara göras en gång på varje server. Klicka sedan på **registrera**.
8. När servern är registrerad i valvet, klickar du på **Slutför**.

När registreringen är klar metadata från servern hämtas av Azure Site Recovery och VMM-servern visas i **Site Recovery-infrastruktur**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Installera Recovery Services-agenten på Hyper-V-värdar

Installera agenten på varje Hyper-V-värd som innehåller virtuella datorer du vill replikera.

1. I installationsguiden för Microsoft Azure Recovery Services Agent > **Kravkontroll**, klickar du på **nästa**. Alla nödvändiga komponenter som saknas installeras automatiskt.
2. I **installationsinställningar**, acceptera installationsplatsen och cachelagringsplatsen. Cacheenhet behöver minst 5 GB lagringsutrymme. Vi rekommenderar att en enhet med 600 GB eller mer ledigt utrymme. Klicka på **Installera**.
3. I **Installation**när installationen är klar, klickar på **Stäng** avsluta guiden.

    ![Installera agenten](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

1. Klicka på **Förbered infrastrukturen** > **Mål**.
2. Välj prenumerationen och resursgrupp (**ContosoRG**) i som virtuella Azure-datorer som ska skapas efter en redundansväxling.
3. Välj den **Resource Manager** distributionsmodell.

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.


## <a name="configure-network-mapping"></a>Konfigurera nätverksmappning

1. Under **Site Recovery-infrastruktur** > **Nätverksmappningar** > **Nätverksmappning** klickar du på ikonen **+Nätverksmappning**.
2. I **Lägg till nätverksmappning**, Välj VMM-källservern. Välj **Azure** som mål.
3. Kontrollera prenumerationen och distributionsmodellen efter redundansväxling.
4. I **källnätverket**, Välj en lokal VM-källnätverket.
5. I **målnätverket**, Välj Azure-nätverket i vilka Azure-Replikdatorerna ska finnas när de skapas efter en redundansväxling. Klicka sedan på **OK**.

    ![Nätverksmappning](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

1. Klicka på **Förbered infrastruktur** > **Replikeringsinställningar** > **+Skapa och koppla**.
2. I **Princip för att skapa och koppla** anger du ett principnamn. Vi använder **ContosoReplicationPolicy**.
3. Låt standardvärdet stå kvar och klicka på **OK**.
    - **Kopieringsfrekvens** anger att deltadata (efter den inledande replikeringen) replikeras var femte minut.
    - **Kvarhållning av återställningspunkt** anger för varje återställningspunkt kommer att hållas kvar för två timmar.
    - **Frekvens för appkonsekvent ögonblicksbild** anger att återställningspunkterna som innehåller appkonsekventa ögonblicksbilder skapas varje timme.
    - **Starttid för inledande replikering** anger att den inledande replikeringen ska börja direkt.
    - **Kryptera data lagrade på Azure** -standard **av** inställningen visar att vilande data i Azure inte är krypterad.
4. Klicka på **OK** när principen har skapats. När du skapar en ny princip associeras den automatiskt med VMM-molnet.

## <a name="enable-replication"></a>Aktivera replikering

1. Klicka på **Källa** i **Replikera program**. 
2. I **källa**, väljer du VMM-moln. Klicka sedan på **OK**.
3. I **Target**, kontrollerar du att Azure som mål, även valvprenumerationen och välj den **Resource Manager** modellen.
4. Välj den **contosovmsacct1910171607** storage-konto, och **ContosoASRnet** Azure-nätverk.
5. I **Virtuella datorer** > **Välj** väljer du den virtuella dator som du vill replikera. Klicka sedan på **OK**.

   Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skyddet** är klart är den inledande replikeringen slutförd och den virtuella datorn är redo för redundans.



## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Köra ett återställningstest](tutorial-dr-drill-azure.md)
