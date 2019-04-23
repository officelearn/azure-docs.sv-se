---
title: Redundansväxla och återställa virtuella VMware-datorer och fysiska servrar vid haveriberedskap till Azure med Site Recovery | Microsoft Docs
description: Lär dig hur du redundansväxlar virtuella VMware-datorer och fysiska servrar till Azure och återställer dem till den lokala platsen vid haveriberedskap till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9206e751fadab7a09c696fbe262aecdde002ae74
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797673"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Redundansväxla och återställa virtuella VMware-datorer

Den här artikeln beskrivs hur du växlar över en lokal VMware-dator till Azure [Azure Site Recovery](site-recovery-overview.md) service. 

Det här är den femte självstudien i en serie som visar hur du konfigurerar haveriberedskap till Azure för lokala datorer.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera egenskaperna för den virtuella VMware-datorn för att se att den överensstämmer med kraven för Azure
> * Köra en redundans i Azure


> [!NOTE]
> Självstudier visar den enklaste distribution sökvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Om du vill lära dig om redundans i detalj, [läsa den här artikeln](site-recovery-failover.md).

## <a name="before-you-start"></a>Innan du börjar
Slutföra de föregående självstudierna:

1. Kontrollera att du har [ställa in Azure](tutorial-prepare-azure.md) för lokal haveriberedskap för virtuella VMware-datorer, Hyper-V-datorer och fysiska datorer till Azure.
2. Förbered dina lokala [VMware](vmware-azure-tutorial-prepare-on-premises.md) eller [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) miljön för katastrofåterställning. Om du konfigurerar haveriberedskap för fysiska servrar, granska de [stödmatris](vmware-physical-secondary-support-matrix.md).
3. Konfigurera haveriberedskap för [virtuella VMware-datorer](vmware-azure-tutorial.md), [Hyper-V-datorer](hyper-v-azure-tutorial.md), eller [fysiska datorer](physical-azure-disaster-recovery.md).
4. Kör en [programåterställningstest](tutorial-dr-drill-azure.md) att se till att allt fungerar som förväntat.


## <a name="failover-and-failback"></a>Redundans och återställning efter fel

Redundans och återställning efter fel har fyra stadier:

1. **Redundansväxla till Azure**: Om din primära lokala plats kraschar redundansväxla datorer till Azure. Efter en redundansväxling skapas virtuella Azure-datorer från replikerade data.
2. **Återaktivera skydd av virtuella Azure-datorer**: Återaktivera skyddet på virtuella Azure-datorer i Azure, så att de börjar replikera tillbaka till den lokala virtuella VMware-datorer. Den lokala virtuella datorn stängs av under återaktiveringen av skyddet för att säkerställa datakonsekvens.
3. **Redundansväxla till lokala**: När den lokala platsen är igång och körs, kör en redundansväxling till redundansväxla från Azure.
4. **Återaktivera skyddet av lokala virtuella datorer**: När data har återställts, skydd de lokala virtuella datorer som växlas tillbaka, så att de börjar replikera till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Innan du kör en redundans, kontrollera VM-egenskaperna och se till att virtuella datorer som uppfyller [krav för Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Kontrollera egenskaper enligt följande:

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.

2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.

3. I **Beräkning och nätverk** kan du ändra Azure-namnet, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) samt inställningar för hanterad disk

4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.

5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="run-a-failover-to-azure"></a>Köra en redundans i Azure

1. I **Inställningar** > **Replikerade objekt** klickar du på VM > **Redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
   - **Senaste**: Det här alternativet bearbetar först alla data som skickas till Site Recovery. De ger det lägsta målet för återställningspunkten eftersom Azure VM skapas efter att redundansen har fått alla data som replikerades till Site Recovery när redundansen utlöstes.
   - **Senaste bearbetade**: Det här alternativet redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
   - **Senaste appkonsekventa**: Alternativet redundansväxlar den virtuella datorn till den senaste appkonsekventa återställningspunkt som bearbetats av Site Recovery.
   - **Anpassad**: Ange en återställningspunkt.

3. Välj **Stäng datorn innan du påbörjar redundans** att stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker längre redundanstiden för:
- Virtuella VMware-datorer som kör en äldre än 9.8 mobilitetstjänstversionen
- Fysiska servrar
- Virtuella VMware Linux-datorer
- Hyper-V-datorer som skyddas som fysiska servrar
- Virtuella VMware-datorer som inte har aktiverat DHCP-tjänsten
- Virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Avbryt inte en redundansväxling som pågår**: Innan redundans startas stoppas den virtuella datorreplikeringen. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

## <a name="connect-to-failed-over-vm"></a>Ansluta till redundansväxlad virtuell Maskin

1. Om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter en redundans, [verifiera kraven](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Gå till den virtuella datorn efter en redundansväxling, och verifiera med [ansluter](../virtual-machines/windows/connect-logon.md) till den.
3. Använd **ändra återställningspunkt** om du vill använda en annan återställningspunkt efter en redundansväxling. När du etablerar redundansen i nästa steg, längre det här alternativet inte tillgänglig.
4. Efter valideringen kan du klicka på **genomför** att slutföra återställningspunkten för den virtuella datorn efter redundans.
5. Efter genomförande, alla andra tillgängliga återställningspunkter tas bort. Nu är du klar med redundans.

>[!TIP]
> Om det uppstår några problem med nätverksanslutningen efter en redundansväxling kan du följa den här [felsökningsguide för](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

Återaktivera skyddet av virtuella Azure-datorer till den lokala efter redundansväxlingen. Sedan, när de virtuella datorerna är skyddade och replikering till den lokala platsen kan redundansväxla från Azure när du är klar.

> [!div class="nextstepaction"]
> [Återaktivera skyddet av virtuella Azure-datorer](vmware-azure-reprotect.md)
> [Redundansväxla från Azure](vmware-azure-failback.md) 
