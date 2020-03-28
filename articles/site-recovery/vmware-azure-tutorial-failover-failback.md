---
title: Växla över virtuella virtuella datorer med VMware till Azure med platsåterställning
description: Lär dig hur du växlar över virtuella virtuella datorer med VMware till Azure i Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: 8501bb1a998eb08984a118bfa5d52d1e3f3e4f84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498077"
---
# <a name="fail-over--vmware-vms"></a>Växla över virtuella virtuella datorer med VMware

I den här artikeln beskrivs hur du växlar över en lokal virtuell virtuell dator (VM) till Azure med [Azure Site Recovery](site-recovery-overview.md).

Det här är den femte självstudien i en serie som visar hur du konfigurerar haveriberedskap till Azure för lokala datorer.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Kontrollera att VMware VM-egenskaperna överensstämmer med Azure-kraven.
> * Växla över specifika virtuella datorer till Azure.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario. De använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Om du vill lära dig mer om redundans i detalj läser du [Fela över virtuella datorer och fysiska servrar](site-recovery-failover.md).

[Läs mer om](failover-failback-overview.md#types-of-failover) olika typer av redundans. Om du vill växla över flera virtuella datorer i en återställningsplan läser du [den här artikeln](site-recovery-failover.md).

## <a name="before-you-start"></a>Innan du börjar

Slutför de tidigare självstudierna:

1. Kontrollera att du har [konfigurerat Azure](tutorial-prepare-azure.md) för lokal haveriberedskap av virtuella datorer, virtuella datorer med hyper-V och fysiska datorer till Azure.
2. Förbered din lokala [VMware-miljö](vmware-azure-tutorial-prepare-on-premises.md) för haveriberedskap. 
3. Konfigurera haveriberedskap för virtuella datorer med [VMware](vmware-azure-tutorial.md).
4. Kör en [haveriberedskapsövning](tutorial-dr-drill-azure.md) för att se till att allt fungerar som förväntat.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Innan du kör en redundans kontrollerar du egenskaperna för den virtuella datorn för att se till att de virtuella datorerna uppfyller [Azure-kraven](vmware-physical-azure-support-matrix.md#replicated-machines).

Verifiera egenskaper enligt följande:

1. I **Skyddade objekt**väljer du **Replikerade objekt**och väljer sedan den virtuella dator som du vill verifiera.

2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Välj **Egenskaper** om du vill visa mer information.

3. I **Beräkning och Nätverk**kan du ändra dessa egenskaper efter behov:
    * Azure-namn
    * Resursgrupp
    * Målstorlek
    * [Tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md)
    * Inställningar för hanterade diskar

4. Du kan visa och ändra nätverksinställningar, till exempel:

    * Nätverket och undernätet där Den virtuella Azure-datorn kommer att finnas efter redundans.
    * IP-adressen som ska tilldelas den.

5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="run-a-failover-to-azure"></a>Köra en redundans i Azure

1. I **Inställningar** > **replikerade objekt**väljer du den virtuella dator som du vill växla över och väljer sedan **Redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
   * **Senaste**: Det här alternativet bearbetar först alla data som skickas till Site Recovery. Det ger det lägsta återställningspunktmålet (RPO) eftersom den virtuella Azure-datorn som skapas efter redundans har alla data som replikerades till platsåterställning när redundansen utlöstes.
   * **Senast bearbetad:** Det här alternativet växlar inte den virtuella datorn över till den senaste återställningspunkten som bearbetas av Site Recovery. Det här alternativet ger en låg RTO (Återställningstid mål) eftersom ingen tid går åt att bearbeta obearbetade data.
   * **Senaste app-konsekvent:** Det här alternativet misslyckas den virtuella datorn över till den senaste app-konsekvent återställningspunkt som bearbetas av Site Recovery.
   * **Anpassad:** Med det här alternativet kan du ange en återställningspunkt.

3. Välj **Stäng av datorn innan du börjar redundans** för att försöka stänga av käll-virtuella datorer innan du utlöser redundansen. Redundans fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker längre test redundanstider för:

* Virtuella datorer med VMware som kör en mobilitetstjänstversion som är äldre än 9.8.
* Fysiska servrar.
* Virtuella Linux-datorer.
* Virtuella datorer med hyper-V skyddas som fysiska servrar.
* Virtuella datorer med VMware som inte har DHCP-tjänsten aktiverad.
* Virtuella datorer med VMware som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Avbryt inte en pågående redundans. Innan redundans startas stoppas den virtuella datorreplikeringen. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

## <a name="connect-to-failed-over-vm"></a>Ansluta till en virtuell virtuell dator med fel övergående

1. Om du vill ansluta till virtuella Azure-datorer efter redundans med hjälp av Fjärrskrivbordsprotokoll (RDP) och Secure Shell (SSH), [kontrollera att kraven har uppfyllts]((ailover-failback-overview.md#connect-to-azure-after-failover).
2. Efter redundans går du till den virtuella datorn och validerar genom att [ansluta](../virtual-machines/windows/connect-logon.md) till den.
3. Använd **Ändra återställningspunkt** om du vill använda en annan återställningspunkt efter redundans. När du har genomför redundansen i nästa steg är det här alternativet inte längre tillgängligt.
4. Efter verifiering väljer du **Commit** för att slutföra återställningspunkten för den virtuella datorn efter redundans.
5. När du har begått det tas alla andra tillgängliga återställningspunkter bort. Det här steget slutför redundansen.

>[!TIP]
> Om du stöter på anslutningsproblem efter redundans följer du [felsökningsguiden](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

Efter redundans, återrotera Azure virtuella datorer till lokala. Sedan, när de virtuella datorerna har reprotecteds och replikera till den lokala platsen, misslyckas tillbaka från Azure när du är redo.

> [!div class="nextstepaction"]
> [Reprotect Azure virtuella datorer](vmware-azure-reprotect.md)
> [växla tillbaka från Azure](vmware-azure-failback.md)
