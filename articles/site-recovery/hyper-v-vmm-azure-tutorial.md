---
title: Konfigurera Hyper-V-haveriberedskap (med VMM) med hjälp av Azure Site Recovery
description: Lär dig hur du konfigurerar haveriberedskap för lokala virtuella hyper-virtuella datorer i VMM-moln i System Center till Azure med hjälp av Site Recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: a391d8eb3cf7bc43b52883cbf2e76170338c44c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067582"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer i VMM-moln till Azure

Den här självstudien beskriver hur du aktiverar replikering för lokala virtuella hyper-V-datorer (VM) som hanteras av System Center Virtual Machine Manager (VMM) till Azure med [Azure Site Recovery](site-recovery-overview.md). Om du inte använder VMM [följer du den här självstudien](hyper-v-azure-tutorial.md).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Väljer replikeringskälla och mål.
> * Ställ in källreplikeringsmiljön, inklusive lokala site recovery-komponenter och målreplikeringsmiljön.
> * Konfigurera nätverksmappning mellan VMM VM-nätverk och virtuella Azure-nätverk.
> * Skapar en replikeringsprincip.
> * Aktiverar replikering för en virtuell dator.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i artiklarna i avsnittet **Instruktioner i** [dokumentationen för webbplatsåterställning](/azure/site-recovery/).

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du redan har slutfört följande självstudier:

1. [Förbereda Azure](tutorial-prepare-azure.md)
1. [Förbereda lokala Hyper-V-servrar](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Väljer ett replikeringsmål

1. Gå till **Recovery Services-valv** i Azure-portalen och välj det **ContosoVMVault-valv** som skapades i guiden [Förbereda Azure.](tutorial-prepare-azure.md#create-a-recovery-services-vault)
1. Välj Infrastruktur för förbereda infrastruktur för**förberedad webbplats** **återställning** > från **Komma igång**och konfigurera följande inställningar:
    1. **Skyddsmål** > **Var finns dina maskiner?**, välj **Lokalt**.
    1. **Var vill du replikera dina datorer?** **To Azure**
    1. **Är dina datorer virtualiserade?**, välj **Ja, med Hyper-V**.
    1. **Använder du System Center VMM för att hantera dina Hyper-V-värdar?** **Yes**
1. Välj **OK**.

   ![Replikeringsmål](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

1. Om du planerar en stor distribution i **distributionsplanering**hämtar du distributionsplaneraren för Hyper-V från länken på sidan. [Läs mer](hyper-v-deployment-planner-overview.md) om Hyper-V-distributionsplanering.
1. För den här självstudien behöver vi inte distributionsplaneraren. I **Har du slutfört distributionsplaneringen?** väljer du Jag gör det **senare**och väljer sedan **OK**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

När du konfigurerar källmiljön installerar du Azure Site Recovery Provider på VMM-servern och registrerar servern i valvet. Du installerar Azure Recovery Services-agenten på varje Hyper-V-värd.

1. **Förbered infrastruktur**. Välj **Källa**.
1. **Förbered källa**. Välj **+ VMM** om du vill lägga till en VMM-server. I **Lägg till server** kontrollerar du att **System Center VMM-server** visas i **Servertyp**.
1. Hämta installationsprogrammet för Microsoft Azure Site Recovery Provider.
1. Ladda ned valvregistreringsnyckeln. Du behöver den här nyckeln när du kör providerkonfigurationen. Nyckeln är giltig i fem dagar efter att du har genererat den.
1. Hämta installationsprogrammet för Microsoft Azure Recovery Services-agenten.

   ![Hämta leverantör, registreringsnyckel och agent](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installera providern på VMM-servern

1. Guiden Konfigurera installationsprogram för Azure Site Recovery Provider **Microsoft Update**. Anmäl dig för att använda Microsoft Update för att söka efter provideruppdateringar.
1. **Installation**. Acceptera standardinstallationsplatsen för Providern och välj **Installera**.
1. Efter installationen väljer du **valvnyckelfilen**som **Browse**du hämtade **i**guiden Registrering av Microsoft Azure Site Recovery i Microsoft Azure Site Recovery.
1. Ange azure site recovery-prenumeration och valvets namn (**ContosoVMVault**). Ange ett eget namn för VMM-servern för att identifiera den i valvet.
1. **Proxyinställningar**. Välj **Anslut direkt till Azure Site Recovery utan proxy**.
1. Acceptera standardplatsen för certifikatet som används för att kryptera data. Krypterade data dekrypteras när du växlar över.
1. **Synkronisera molnmetadata**. Välj **Synkronisera molnmetadata till site recovery-portalen**. Den här åtgärden behöver bara ske en gång på varje server. Välj sedan **Registrera**.
1. När servern har registrerats i valvet väljer du **Slutför**.

När registreringen är klar hämtas metadata från servern av Azure Site Recovery och VMM-servern visas i **infrastruktur för platsåterställning**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Installera Recovery Services-agenten på Hyper-V-värdar

Installera agenten på varje Hyper-V-värd som innehåller virtuella datorer som du vill replikera.

Konfigurera de här inställningarna i installationsguiden för Microsoft Azure Recovery Services Agent:

1. **Förkunskaper Kontrollera**. Välj **Nästa**. Eventuella nödvändiga krav som saknas installeras automatiskt.
1. **Installationsinställningar**. Acceptera installationsplatsen och cacheplatsen. Cacheenheten behöver minst 5 GB lagringsutrymme. Vi rekommenderar en enhet med 600 GB eller mer ledigt utrymme. Markera **Installera**.
1. **Installation**. När installationen är klar väljer du **Stäng** för att slutföra guiden.

   ![Installera agent](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

1. Välj **Förbered infrastrukturmål** > **Target**.
1. Välj prenumerationen och resursgruppen (**ContosoRG**) där virtuella Azure-datorer skapas efter redundans.
1. Välj distributionsmodellen **för Resource Manager.**

Site Recovery kontrollerar att det finns ett eller flera kompatibla Azure-lagringskonton och -nätverk.

## <a name="configure-network-mapping"></a>Konfigurera nätverksmappning

1. **Kartläggning av nätverksmappning** > av**nätverksmappning**för platsåterställning.**Network mappings** >  Välj ikonen **+Nätverksmappning.**
1. **Lägg till nätverksmappning**. Välj **VMM-servern för Source System Center.** För **Målet**väljer du Azure.
1. Kontrollera prenumerationen och distributionsmodellen efter redundansväxling.
1. **Källnätverk**. Välj det lokala VM-nätverket.
1. **Målnätverk**. Välj Azure-nätverket där virtuella virtuella repliker Azure-datorer ska finnas när de skapas efter redundans. Välj **OK**.

   ![Nätverksmappning](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

1. Välj **Förbered inställningar för infrastrukturreplikering** > **Replication Settings** > **+Skapa och associera**.
1. Ange ett principnamn i **princip skapa och associera.** Vi använder **ContosoReplicationPolicy**.
1. Acceptera standardinställningarna och välj **OK:**
   - **Kopieringsfrekvensen** anger att deltadata replikeras var femte minut efter den första replikeringen.
   - **Kvarhållning av återställningspunkt** anger att varje återställningspunkt kommer att behållas i två timmar.
   - **Frekvens för appkonsekvent ögonblicksbild** anger att återställningspunkterna som innehåller appkonsekventa ögonblicksbilder skapas varje timme.
   - **Inledande starttid för replikering** indikerar att den första replikeringen startar omedelbart.
   - **Kryptera data som lagras på Azure** är inställd på standard **(Av)** och anger att i vila data i Azure inte är krypterad.
1. När principen har skapats väljer du **OK**. När du skapar en ny princip associeras den automatiskt med VMM-molnet.

## <a name="enable-replication"></a>Aktivera replikering

1. **Replikera program**. Välj **Källa**.
1. **Källa**. Välj VMM-molnet. Välj **OK**.
1. **Mål**. Verifiera målet (Azure), valvprenumerationen och välj **Resource Manager-modellen.**
1. Välj **lagringskontot contosovmsacct1910171607** och **ContosoASRnet** Azure-nätverket.
1. **Virtuella datorer** > **Välj**. Välj den virtuella dator som du vill replikera. Välj **OK**.

   Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har slutförts är den första replikeringen klar och den virtuella datorn är klar för redundans.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra ett återställningstest](tutorial-dr-drill-azure.md)
