---
title: Konfigurera haveri beredskap för Hyper-V (med VMM) med Azure Site Recovery
description: Lär dig hur du konfigurerar haveri beredskap för lokala virtuella Hyper-V-datorer i System Center VMM-moln till Azure med hjälp av Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0c570702e4c3899ef2847883e6fc8649e603a787
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281683"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Konfigurera katastrof återställning av lokala virtuella Hyper-V-datorer i VMM-moln till Azure

I den här artikeln beskrivs hur du aktiverar replikering för lokala virtuella Hyper-V-datorer som hanteras av System Center Virtual Machine Manager (VMM), för haveri beredskap till Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) . Om du inte använder VMM [följer du den här självstudien](hyper-v-azure-tutorial.md) i stället.

Det här är den tredje självstudien i en serie som visar hur du konfigurerar haveri beredskap till Azure för lokala virtuella VMware-datorer. I den föregående själv studie kursen har vi för [berett den lokala Hyper-V-miljön](hyper-v-prepare-on-premises-tutorial.md) för haveri beredskap till Azure.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Väljer replikeringskälla och mål.
> * Konfigurera käll miljön för replikering, inklusive lokala Site Recovery-komponenter och mål miljön för replikering.
> * Konfigurera nätverks mappning för att mappa mellan virtuella VMM-nätverk och virtuella Azure-nätverk.
> * Skapar en replikeringsprincip.
> * Aktiverar replikering för en virtuell dator.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artiklarna i avsnittet om **att gå till guider** i [Site Recovery-dokumentationen](https://docs.microsoft.com/azure/site-recovery).



## <a name="before-you-begin"></a>Innan du börjar

Detta är den tredje självstudien i en serie. Det förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Väljer ett replikeringsmål

1. I Azure Portal går du till **Recovery Services valv** och väljer valvet. Vi för beredde valvet **ContosoVMVault** i föregående självstudie.
2. I **komma igång**väljer du **Site Recovery**och väljer sedan **Förbered infrastruktur**.
3. I **skydds mål** > **var finns dina datorer?** väljer du **lokalt**.
4. I **var vill du replikera dina datorer?** väljer **du till Azure**.
5. **Är dina datorer virtualiserade? väljer du** **Ja, med Hyper-V**.
6. I **använder du System Center VMM för att hantera Hyper-V-värdar?** väljer du **Ja**.
7.  Välj **OK**.

    ![Replikeringsmål](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

1. Om du planerar en stor distribution i **planerings planeringen**, laddar du ned distributions planeraren för Hyper-V från länken på sidan. [Läs mer](hyper-v-deployment-planner-overview.md) om planering av distribution av Hyper-V.
2. I den här självstudien behöver vi inte distributions planeraren. **Har du slutfört distributions planeringen?** väljer du **Jag vill göra det senare**och väljer sedan **OK**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

När du konfigurerar käll miljön installerar du Azure Site Recovery-providern på VMM-servern och registrerar servern i valvet. Du installerar Azure Recovery Services-agenten på varje Hyper-V-värd.

1. I **Förbered infrastruktur**väljer du **källa**.
2. I **Förbered källa**väljer du **+ VMM** för att lägga till en VMM-Server. I **Lägg till server** kontrollerar du att **System Center VMM-server** visas i **Servertyp**.
3. Hämta installations programmet för Microsoft Azure Site Recovery-providern.
4. Ladda ned valvregistreringsnyckeln. Du behöver den här nyckeln när du kör installations programmet för providern. Nyckeln är giltig i fem dagar efter att du har genererat den.
5. Hämta installations programmet för Microsoft Azure Recovery Services agenten.

    ![Hämta Provider, registrerings nyckel och agent](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installera providern på VMM-servern

1. I installationsguiden för Azure Site Recovery-providern > **Microsoft Update** väljer du att använda Microsoft Update för att söka efter uppdateringar för providern.
2. I **installationen**accepterar du standard installations platsen för providern och väljer **Installera**.
3. Efter installationen går du till guiden Microsoft Azure Site Recovery registrering > **valv inställningar**, väljer **Bläddra**och i **nyckel fil**väljer du den valv nyckel fil som du laddade ned.
4. Ange Azure Site Recovery prenumerationen och valv namnet (**ContosoVMVault**). Ange ett eget namn på VMM-servern för att identifiera den i valvet.
5. I **Proxyinställningar** väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
6. Acceptera standard platsen för certifikatet som används för att kryptera data. Krypterade data dekrypteras när du växlar över.
7. I **Synkronisera molnets metadata**väljer **du Sync cloud metadata to Site Recovery Portal**. Den här åtgärden behöver bara inträffa en gång på varje server. Välj sedan **Registrera**.
8. När servern har registrerats i valvet väljer du **Slutför**.

När registreringen är klar hämtas metadata från servern av Azure Site Recovery och VMM-servern visas i **Site Recovery-infrastrukturen**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Installera Recovery Services-agenten på Hyper-V-värdar

Installera agenten på varje Hyper-V-värd som innehåller de virtuella datorer som du vill replikera.

1. I installations guiden för Microsoft Azure Recovery Services agent > **krav kontroll**väljer du **Nästa**. Alla nödvändiga komponenter som saknas kommer att installeras automatiskt.
2. I **installations inställningarna**godkänner du installations platsen och cacheplatsen. Cache-enheten måste ha minst 5 GB lagrings utrymme. Vi rekommenderar en enhet med 600 GB eller mer ledigt utrymme. Markera **Installera**.
3. När installationen är klar väljer du **Stäng** för att slutföra guiden i **installationen**.

    ![Installera agent](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

1. Välj **Förbered infrastrukturen** > **Mål**.
2. Välj den prenumeration och resurs grupp (**conto sorg**) som de virtuella Azure-datorerna ska skapas i efter redundansväxlingen.
3. Välj distributions modell för **Resource Manager** .

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

## <a name="configure-network-mapping"></a>Konfigurera nätverksmappning

1. I **Site Recovery infrastruktur** > **nätverks mappningar** > **nätverks mappning**väljer du ikonen **+ nätverks mappning** .
2. I **Lägg till nätverks mappning**väljer du käll-VMM-servern. Välj **Azure** som mål.
3. Kontrollera prenumerationen och distributionsmodellen efter redundansväxling.
4. I **käll nätverk**väljer du det lokala virtuella dator nätverket.
5. I **mål nätverk**väljer du det Azure-nätverk där virtuella repliker i Azure ska placeras när de skapas efter en redundansväxling. Välj **OK**.

    ![Nätverksmappning](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Konfigurera en replikeringsprincip

1. Välj **Förbered infrastruktur** > **replikeringsinställningar** >  **+ skapa och koppla**.
2. I **Princip för att skapa och koppla** anger du ett principnamn. Vi använder **ContosoReplicationPolicy**.
3. Låt standardinställningarna vara kvar och välj **OK**.
    - **Kopierings frekvensen** visar att delta data kommer att replikeras var femte minut efter den inledande replikeringen.
    - **Kvarhållning av återställnings punkter** anger att varje återställnings punkt kommer att behållas i två timmar.
    - **Frekvens för appkonsekvent ögonblicksbild** anger att återställningspunkterna som innehåller appkonsekventa ögonblicksbilder skapas varje timme.
    - **Start tiden för inledande replikering** anger att den inledande replikeringen kommer att starta omedelbart.
    - **Kryptering av data som lagras på Azure** är inställt på standard (**av**) och indikerar att data i vila-data i Azure inte är krypterade.
4. När principen har skapats väljer du **OK**. När du skapar en ny princip associeras den automatiskt med VMM-molnet.

## <a name="enable-replication"></a>Aktivera replikering

1. I **Replikera program**väljer du **källa**.
2. I **källa**väljer du VMM-molnet. Välj **OK**.
3. I **mål**kontrollerar du målet (Azure), valv prenumerationen och väljer **Resource Manager** -modellen.
4. Välj lagrings konto för **contosovmsacct1910171607** och **ContosoASRnet** Azure-nätverket.
5. I **virtual machines** > **väljer**väljer du den virtuella dator som du vill replikera. Välj **OK**.

   Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** är klart slutförs den inledande replikeringen och den virtuella datorn är klar för redundans.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Köra ett återställningstest](tutorial-dr-drill-azure.md)
