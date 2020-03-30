---
title: Konfigurera redundans för virtuella hyper-virtuella datorer till Azure i Azure Site Recovery
description: Lär dig hur du växlar över virtuella hyper-virtuella datorer till Azure med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 03826abf6da94859c510f4c127dfce035aa79370
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498173"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Växla över virtuella hyper-virtuella datorer till Azure

Den här självstudien beskriver hur du växlar över virtuella hyper-virtuella datorer till Azure med [Azure Site Recovery](site-recovery-overview.md). När du har redundansväxlat kan du återställa till den lokala platsen när den är tillgänglig. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Verifiera hyper-V-vm-egenskaperna för att kontrollera att de uppfyller Azure-kraven.
> * Växla över specifika virtuella datorer till Azure.


Detta är den femte självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna.    

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Konfigurera katastrofåterställning för [virtuella Hyper-V datorer](tutorial-hyper-v-to-azure.md) eller för [virtuella Hyper-V-datorer som hanterats i System Center VMM-moln](tutorial-hyper-v-vmm-to-azure.md)
4. [Köra ett återställningstest](tutorial-dr-drill-azure.md)

[Läs mer om](failover-failback-overview.md#types-of-failover) olika typer av redundans. Om du vill växla över flera virtuella datorer i en återställningsplan läser du [den här artikeln](site-recovery-failover.md).

## <a name="prepare-for-failover"></a>Förbereda för redundansväxling 
Kontrollera att det inte finns några ögonblicksbilder på den virtuella datorn och att den lokala virtuella datorn stängs av under återställningen. Det hjälper till att säkerställa datakonsekvens vid replikering. Aktivera inte en lokal virtuell dator under återställning efter fel. 

Redundansväxling och återställning efter fel har tre stadier:

1. **Redundansväxling till Azure**: Redundansväxling av virtuella Hyper-V-datorer från den lokala platsen till Azure.
2. **Återställning efter fel till lokal plats**: Redundansväxling av virtuella Azure-datorer till den lokala platsen när den lokala platsen är tillgänglig. Data börjar synkroniseras från Azure till den lokala platsen och när det slutförts blir alla virtuella datorer lokala.  
3. **Omvänd replikering av lokala virtuella datorer**: Efter återställning till lokal plats kan du utföra en omvänd replikering av lokala virtuella datorer för att börja replikera dem till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Innan redundansväxlingen utförs kontrollerar du VM-egenskaperna och ser till att den virtuella datorn uppfyller [Azure-kraven](hyper-v-azure-support-matrix.md#replicated-vms).

I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.

1. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.

1. I **Beräkning och nätverk** kan du ändra Azure-namnet, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) och hanterade diskinställningar.

1. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.

1. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="fail-over-to-azure"></a>Redundansväxla till Azure

1. Klicka på den virtuella datorn > **redundans**i **Inställningar** > **replikerade objekt.**
2. I **Redundans** väljer du den **senaste** återställningspunkten. 
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker att stänga av den virtuella källdatorn innan redundansen utlöses. Redundansen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**.
4. När du har verifierat redundansväxlingen väljer du **Genomför**. Detta raderar alla tillgängliga återställningspunkter.

> [!WARNING]
> **Avbryt inte en pågående redundans**: Om du avbryter en pågående redundansväxling så stoppas den, men den virtuella datorn kommer inte att replikera igen.

## <a name="connect-to-failed-over-vm"></a>Ansluta till en virtuell virtuell dator med fel övergående

1. Om du vill ansluta till virtuella Azure-datorer efter redundans med hjälp av RdP (Remote Desktop Protocol) och Secure Shell (SSH) [kontrollerar du att kraven har uppfyllts](failover-failback-overview.md#connect-to-azure-after-failover).
2. Efter redundans går du till den virtuella datorn och validerar genom att [ansluta](../virtual-machines/windows/connect-logon.md) till den.
3. Använd **Ändra återställningspunkt** om du vill använda en annan återställningspunkt efter redundans. När du har genomför redundansen i nästa steg är det här alternativet inte längre tillgängligt.
4. Efter verifiering väljer du **Commit** för att slutföra återställningspunkten för den virtuella datorn efter redundans.
5. När du har begått det tas alla andra tillgängliga återställningspunkter bort. Det här steget slutför redundansen.

>[!TIP]
> Om du stöter på anslutningsproblem efter redundans följer du [felsökningsguiden](site-recovery-failover-to-azure-troubleshoot.md).


## <a name="next-steps"></a>Nästa steg

Efter redundans, återrotera Azure virtuella datorer så att de replikeras från Azure till lokalt. Sedan, när de virtuella datorerna har reprotecteds och replikera till den lokala platsen, misslyckas tillbaka från Azure när du är redo.
