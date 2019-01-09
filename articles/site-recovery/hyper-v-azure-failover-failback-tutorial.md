---
title: Redundansväxla och återställa virtuella Hyper-V-datorer vid haveriberedskap till Azure med Site Recovery | Microsoft Docs
description: Lär dig hur du redundansväxlar och återställer virtuella Hyper-V-datorer vid haveriberedskap till Azure med hjälp av Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 03b8dd9bd0a7acc63fdae66330904142384429a8
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794217"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Redundans och återställning vid fel för virtuella Hyper-V-datorer som replikeras till Azure

I den här självstudien beskrivs hur du redundansväxlar en virtuell Hyper-V-dator till Azure. När du har redundansväxlat kan du återställa till den lokala platsen när den är tillgänglig. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera egenskaperna för den virtuella Hyper-V-datorn för att se att de överensstämmer med kraven för Azure
> * Köra en redundans i Azure
> * Redundansväxla från Azure till lokalt
> * Använd omvänd replikeringen av lokala virtuella datorer för att börja replikera till Azure igen

Detta är den femte självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna.    

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Konfigurera katastrofåterställning för [virtuella Hyper-V datorer](tutorial-hyper-v-to-azure.md) eller för [virtuella Hyper-V-datorer som hanterats i System Center VMM-moln](tutorial-hyper-v-vmm-to-azure.md)
4. [Köra ett återställningstest](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Förbered för redundansväxling och återställning efter fel

Kontrollera att det inte finns några ögonblicksbilder på den virtuella datorn och att den lokala virtuella datorn stängs av under återställningen. Det hjälper till att säkerställa datakonsekvens vid replikering. Aktivera inte en lokal virtuell dator under återställning efter fel. 

Redundansväxling och återställning efter fel har tre stadier:

1. **Redundansväxling till Azure**: Redundansväxling av virtuella Hyper-V-datorer från den lokala platsen till Azure.
2. **Redundansväxling till lokal plats**: Redundansväxling av virtuella Azure-datorer till den lokala platsen när den lokala platsen är tillgänglig. Data börjar synkroniseras från Azure till den lokala platsen och när det slutförts blir alla virtuella datorer lokala.  
3. **Omvänd replikering av lokala virtuella datorer**: Efter återställning till lokal plats kan du utföra en omvänd replikering av lokala virtuella datorer för att börja replikera dem till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Innan redundansväxlingen utförs kontrollerar du VM-egenskaperna och ser till att den virtuella datorn uppfyller [Azure-kraven](hyper-v-azure-support-matrix.md#replicated-vms).

I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.

2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.

3. I **Beräkning och nätverk** kan du ändra Azure-namnet, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) och hanterade diskinställningar.

4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.

5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="failover-to-azure"></a>Redundansväxla till Azure

1. I **Inställningar** > **Replikerade objekt** klickar du på VM > **Redundans**.
2. I **Redundans** väljer du den **senaste** återställningspunkten. 
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker att stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
4. När du har verifierat redundansväxlingen väljer du **Genomför**. Detta raderar alla tillgängliga återställningspunkter.

> [!WARNING]
> **Avbryt inte en redundansväxling som pågår**: Om du avbryter en pågående redundansväxling stoppas den, men den virtuella datorn kommer inte att replikeras igen.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Återställ virtuella Azure-datorer till lokala platser och utför omvänd replikering av lokala virtuella datorn

Återställningsåtgärder är i princip en redundansväxling från Azure till den lokala platsen och om omvänd replikering används igen börjar virtuella datorer replikeras från den lokala platsen till Azure igen.

1. I **Inställningar** > **Replikerade objekt** klickar du på VM > **Planerad redundans**.
2. I **Confirm Planned Failover** (Bekräfta planerad redundans) kontrollerar du redundansriktningen (från Azure) och väljer käll- och målplatser.
3. Välj **Synchronize data before failover (synchronize delta changes only)** (Synkronisera data före redundansväxling (synkronisera endast deltaändringar)). Det här alternativet minskar stilleståndstiden för den virtuella datorn eftersom den synkroniserar utan att stänga av den virtuella datorn.
4. Starta redundansväxlingen. Du kan följa redundansförloppet på fliken **Jobb**.
5. När den inledande datasynkroniseringen är klar och du är redo att stänga av de virtuella Azure-datorerna klickar du på **Jobb** > planned-failover-job-name (namn på planerat redundansjobb) > **Slutföra redundans**. Den virtuella Azure-datorn stängs då av och de senaste lokala ändringarna överförs och startar den lokala virtuella datorn.
6. Logga in på den lokala virtuella datorn för att kontrollera att den är tillgänglig som förväntat.
7. Den lokala virtuella datorn har nu statusen **Bekräfta väntande**. Klicka på **Genomför**. Det tar bort de virtuella Azure-datorerna och tillhörande diskar och förbereder den lokala virtuella datorn för omvänd replikering.
Starta omvänd replikering av lokala virtuella datorer genom att aktivera **Omvänd replikering**. Det utlöser replikering av deltaändringar som har skett sedan den virtuella Azure-datorn stängdes av.  
