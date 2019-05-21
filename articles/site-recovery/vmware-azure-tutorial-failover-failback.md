---
title: Redundansväxla och återställa virtuella VMware-datorer och fysiska servrar vid haveriberedskap till Azure med Site Recovery | Microsoft Docs
description: Lär dig hur du redundansväxlar virtuella VMware-datorer och fysiska servrar till Azure och hur du växlar tillbaka till den lokala platsen under haveriberedskap till Azure med Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7a089b3e4d7b8a38f2bf88c8ccf6e269331589be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966277"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Redundansväxla och återställa virtuella VMware-datorer

Den här artikeln beskriver hur du växlar över en lokal VMware-dator (VM) till [Azure Site Recovery](site-recovery-overview.md).

Det här är den femte självstudien i en serie som visar hur du konfigurerar haveriberedskap till Azure för lokala datorer.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera att VMware-VM-egenskaperna överensstämmer med kraven för Azure.
> * Kör en redundansväxling till Azure.

> [!NOTE]
> Självstudier visar den enklaste distribution sökvägen för ett scenario. De använder standardalternativen där det är möjligt och visa inte alla möjliga inställningar och sökvägar. Om du vill lära dig om redundans i detalj [växla över virtuella datorer och fysiska servrar](site-recovery-failover.md).

## <a name="before-you-start"></a>Innan du börjar

Slutföra de föregående självstudierna:

1. Kontrollera att du har [ställa in Azure](tutorial-prepare-azure.md) för lokal haveriberedskap för virtuella VMware-datorer, Hyper-V-datorer och fysiska datorer till Azure.
2. Förbered dina lokala [VMware](vmware-azure-tutorial-prepare-on-premises.md) eller [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) miljön för katastrofåterställning. Om du konfigurerar haveriberedskap för fysiska servrar, granska de [stödmatris](vmware-physical-secondary-support-matrix.md).
3. Konfigurera haveriberedskap för [virtuella VMware-datorer](vmware-azure-tutorial.md), [Hyper-V-datorer](hyper-v-azure-tutorial.md), eller [fysiska datorer](physical-azure-disaster-recovery.md).
4. Kör en [programåterställningstest](tutorial-dr-drill-azure.md) att se till att allt fungerar som förväntat.

## <a name="failover-and-failback"></a>Redundans och återställning efter fel

Redundans och återställning efter fel har fyra stadier:

1. **Växla över till Azure:** Om din primära lokala plats kraschar redundansväxla datorer till Azure. Efter en redundansväxling skapas virtuella Azure-datorer från replikerade data.
2. **Återaktivera skyddet av virtuella Azure-datorer:** Återaktivera skyddet virtuella Azure-datorer i Azure, så att de börjar replikera tillbaka till den lokala virtuella VMware-datorer. Den lokala virtuella datorn stängs av under återaktiveringen av skyddet för att säkerställa datakonsekvens.
3. **Växla över till den lokala:** När den lokala platsen är igång och körs, kör en redundansväxling till redundansväxla från Azure.
4. **Återaktivera skyddet av lokala virtuella datorer:** När data har återställts, skydd de lokala virtuella datorer som växlas tillbaka, så att de börjar replikera till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Innan du kör en redundans, kontrollera VM-egenskaperna för att kontrollera att de virtuella datorerna uppfyller [krav för Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Kontrollera egenskaper enligt följande:

1. I **skyddade objekt**väljer **replikerade objekt**, och välj sedan den virtuella datorn som du vill kontrollera.

2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Välj **egenskaper** att visa mer information.

3. I **beräkning och nätverk**, du kan ändra egenskaperna efter behov:
    * Namn på Azure
    * Resursgrupp
    * Måltorlek
    * [Tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md)
    * Hanterade Diskinställningar

4. Du kan visa och ändra inställningar, inklusive:

    * Nätverk och undernät där den virtuella Azure-datorn kommer att finnas efter redundansväxling.
    * IP-adressen som ska tilldelas till den.

5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="run-a-failover-to-azure"></a>Köra en redundans i Azure

1. I **inställningar** > **replikerade objekt**, Välj den virtuella datorn som du vill redundansväxla och välj sedan **redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
   * **Senaste**: Det här alternativet bearbetar först alla data som skickas till Site Recovery. De ger den lägsta mål för återställningspunkt (RPO) eftersom den virtuella Azure-datorer som skapas efter en redundansväxling har alla data som replikerades till Site Recovery när redundansen utlöstes.
   * **Senaste bearbetade**: Det här alternativet redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery. Det här alternativet ger ett lågt värde för Återställningstid (RTO) eftersom läggs ingen tid på bearbetning av data.
   * **Senaste appkonsekventa**: Det här alternativet redundansväxlar den virtuella datorn till den senaste appkonsekventa återställningspunkten som bearbetats av Site Recovery.
   * **Anpassat**: Det här alternativet kan du ange en återställningspunkt.

3. Välj **Stäng datorn innan du påbörjar redundans** att stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter för att slutföra. Du kanske märker längre redundanstiden för:

* Virtuella VMware-datorer som kör en äldre än 9.8 mobilitetstjänstversionen.
* Fysiska servrar.
* VMware Linux-datorer.
* Hyper-V-datorer som skyddas som fysiska servrar.
* Virtuella VMware-datorer som inte har aktiverat DHCP-tjänsten.
* Virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Avbryt inte en pågående redundansväxling. Innan redundans startas stoppas den virtuella datorreplikeringen. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

## <a name="connect-to-failed-over-vm"></a>Ansluta till redundansväxlade virtuella datorn

1. Om du vill ansluta till virtuella Azure-datorer efter redundans genom att använda Remote Desktop Protocol (RDP) och Secure Shell (SSH), [Kontrollera att kraven är uppfyllda](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Gå till den virtuella datorn efter en redundansväxling, och verifiera med [ansluter](../virtual-machines/windows/connect-logon.md) till den.
3. Använd **ändra återställningspunkt** om du vill använda en annan återställningspunkt efter en redundansväxling. När du etablerar redundansen i nästa steg, längre det här alternativet inte tillgänglig.
4. Efter valideringen kan välja **genomför** att slutföra återställningspunkten för den virtuella datorn efter redundans.
5. När du genomför tas alla andra tillgängliga återställningspunkter bort. Det här steget Slutför redundansväxlingen.

>[!TIP]
> Om det uppstår några problem med nätverksanslutningen efter en redundansväxling kan du följa den [felsökningsguide för](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

Återaktivera skyddet av virtuella Azure-datorer till den lokala efter redundansväxlingen. Sedan, när de virtuella datorerna är skyddade och replikering till den lokala platsen kan redundansväxla från Azure när du är klar.

> [!div class="nextstepaction"]
> [Återaktivera skyddet av virtuella Azure-datorer](vmware-azure-reprotect.md)
> [Redundansväxla från Azure](vmware-azure-failback.md)
