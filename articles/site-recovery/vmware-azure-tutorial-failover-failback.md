---
title: Redundansväxla och återställa virtuella VMware-datorer och fysiska servrar vid haveriberedskap till Azure med Site Recovery | Microsoft Docs
description: Lär dig hur du växlar över virtuella VMware-datorer och fysiska servrar till Azure och hur du växlar tillbaka till den lokala platsen under haveri beredskap till Azure med hjälp av Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 08/22/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 852193e137eab10d1e46c5ba6ae6636d530095be
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972199"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Redundansväxla och återställa virtuella VMware-datorer

Den här artikeln beskriver hur du växlar över en lokal virtuell VMware-dator (VM) till [Azure Site Recovery](site-recovery-overview.md).

Det här är den femte självstudien i en serie som visar hur du konfigurerar haveri beredskap till Azure för lokala datorer.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Verifiera att VMware VM-egenskaperna uppfyller Azure-kraven.
> * Kör en redundansväxling till Azure.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standard alternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Om du vill veta mer om redundans i detalj kan du läsa mer i [redundansväxla virtuella datorer och fysiska servrar](site-recovery-failover.md).

## <a name="before-you-start"></a>Innan du börjar

Slutför de föregående självstudierna:

1. Se till att du har [konfigurerat Azure](tutorial-prepare-azure.md) för lokal katastrof återställning av virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska datorer till Azure.
2. Förbered din lokala [VMware](vmware-azure-tutorial-prepare-on-premises.md) [-eller Hyper-V-](hyper-v-prepare-on-premises-tutorial.md) miljö för haveri beredskap. Om du konfigurerar haveri beredskap för fysiska servrar granskar du [support mat ris](vmware-physical-secondary-support-matrix.md).
3. Konfigurera katastrof återställning för [virtuella VMware-datorer](vmware-azure-tutorial.md), [virtuella Hyper-V-datorer](hyper-v-azure-tutorial.md)eller [fysiska datorer](physical-azure-disaster-recovery.md).
4. Kör en [granskning av haveri beredskap](tutorial-dr-drill-azure.md) för att se till att allt fungerar som förväntat.

## <a name="failover-and-failback"></a>Redundans och återställning efter fel

Redundans och återställning efter fel har fyra stadier:

1. **Redundansväxla till Azure:** När den lokala primära platsen kraschar går det inte att köra datorer över till Azure. Efter redundansväxlingen skapas virtuella Azure-datorer från replikerade data.
2. **Skydda virtuella Azure-datorer:** I Azure skyddar du de virtuella Azure-datorerna så att de börjar replikera tillbaka till lokala virtuella VMware-datorer. Den lokala virtuella datorn är inaktive rad under återskydd för att säkerställa data konsekvens.
3. **Redundansväxla till lokalt:** När din lokala plats är igång kan du köra en redundansväxling för att växla tillbaka från Azure.
4. **Återaktivera skydd för lokala virtuella datorer:** När data har misslyckats igen skyddar du de lokala virtuella datorerna till vilka du inte kunde återställa igen, så att de börjar replikera till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Innan du kör en redundansväxling kontrollerar du VM-egenskaperna för att se till att de virtuella datorerna uppfyller [Azure-kraven](vmware-physical-azure-support-matrix.md#replicated-machines).

Verifiera egenskaperna enligt följande:

1. I **skyddade objekt**väljer du **replikerade objekt**och väljer sedan den virtuella dator som du vill verifiera.

2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Välj **Egenskaper** om du vill visa mer information.

3. I **beräkning och nätverk**kan du ändra dessa egenskaper efter behov:
    * Azure-namn
    * Resource group
    * Måltorlek
    * [Tillgänglighets uppsättning](../virtual-machines/windows/tutorial-availability-sets.md)
    * Inställningar för hanterade diskar

4. Du kan visa och ändra nätverks inställningar, inklusive:

    * Nätverket och under nätet där den virtuella Azure-datorn finns efter redundansväxlingen.
    * Den IP-adress som ska tilldelas till den.

5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="run-a-failover-to-azure"></a>Köra en redundans i Azure

1. I **Inställningar** > **replikerade objekt**väljer du den virtuella dator som du vill redundansväxla och väljer sedan **redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
   * **Senaste**: Det här alternativet bearbetar först alla data som skickas till Site Recovery. Det ger det lägsta återställnings punkt målet, eftersom den virtuella Azure-datorn som skapas efter redundansväxlingen har alla data som repliker ATS till Site Recovery När redundansväxlingen utlöstes.
   * **Senaste bearbetade**: Det här alternativet Miss lyckas den virtuella datorn till den senaste återställnings punkten som bearbetades av Site Recovery. Det här alternativet ger en låg RTO (återställnings tid) eftersom ingen tid har använts för bearbetning av obearbetade data.
   * **Senaste appkonsekventa**: Det här alternativet Miss lyckas den virtuella datorn till den senaste programkonsekventa återställnings punkten som bearbetas av Site Recovery.
   * **Anpassat**: Med det här alternativet kan du ange en återställnings punkt.

3. Välj **Stäng datorn innan du påbörjar redundans** för att försöka stänga av virtuella käll datorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen Miss lyckas. Du kan följa redundansförloppet på sidan **Jobb**.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kan lägga märke till längre redundanstest för:

* Virtuella VMware-datorer som kör en mobilitets tjänst version som är äldre än 9,8.
* Fysiska servrar.
* Virtuella VMware Linux-datorer.
* Virtuella Hyper-V-datorer som är skyddade som fysiska servrar.
* Virtuella VMware-datorer som inte har DHCP-tjänsten aktive rad.
* Virtuella VMware-datorer som inte har följande start driv rutiner: storvsc, VMBus, storflt, intelide, ATAPI.

> [!WARNING]
> Avbryt inte en pågående redundansväxling. Innan redundans startas stoppas den virtuella datorreplikeringen. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

## <a name="connect-to-failed-over-vm"></a>Anslut till misslyckad virtuell dator

1. Om du vill ansluta till virtuella Azure-datorer efter en redundansväxling med hjälp av Remote Desktop Protocol (RDP) och SSH (Secure Shell) [kontrollerar du att kraven har](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)uppfyllts.
2. Efter redundansväxlingen går du till den virtuella datorn och validerar genom att [ansluta](../virtual-machines/windows/connect-logon.md) till den.
3. Använd **ändrings återställnings punkt** om du vill använda en annan återställnings punkt efter redundansväxlingen. När du har bekräftat redundansväxlingen i nästa steg kommer det här alternativet inte längre att vara tillgängligt.
4. När du har verifierat väljer du **genomför** för att slutföra återställnings punkten för den virtuella datorn efter redundansväxlingen.
5. När du har bekräftat raderas alla andra tillgängliga återställnings punkter. I det här steget slutförs redundansväxlingen.

>[!TIP]
> Om det uppstår anslutnings problem efter redundansväxlingen följer du [fel söknings guiden](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

Skydda virtuella Azure-datorer på plats igen efter redundansväxlingen. Efter det att de virtuella datorerna har återskyddats och repliker ATS till den lokala platsen går du tillbaka från Azure när du är klar.

> [!div class="nextstepaction"]
> [Skydda virtuella Azure-datorer](vmware-azure-reprotect.md)
> [från Azure igen](vmware-azure-failback.md)
