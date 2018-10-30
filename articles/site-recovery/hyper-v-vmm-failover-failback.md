---
title: Redundansväxla och återställa virtuella Hyper-V-datorer som replikeras till ett sekundärt Datacenter för haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du växlar över Hyper-V-datorer till din sekundära lokala platsen och växla tillbaka till primär plats under haveriberedskap med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: raynew
ms.openlocfilehash: 80a26f7f2c97fdbc5342d8845277facd79f22a62
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210014"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Redundansväxla och återställa Hyper-V-datorer som replikeras till din sekundära lokala platsen

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten hanterar och samordnar replikering, redundans och återställning efter fel för lokala datorer och virtuella Azure-datorer (VM).

Den här artikeln beskriver hur du redundansväxlar en Hyper-V virtuell dator som hanteras i ett System Center Virtual Machine Manager (VMM)-moln till en sekundär VMM-plats. När du har redundansväxlat kan du återställa till den lokala platsen när den är tillgänglig. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Redundansväxla en Hyper-V virtuell dator från en primär VMM-moln till en sekundär VMM-moln
> * Skydda datorer igen från den sekundära platsen till primärt och återställning
> * Du kan också starta replikering från primära till sekundära igen

## <a name="failover-and-failback"></a>Redundans och återställning efter fel

Redundans och återställning efter fel har tre steg:

1. **Växla över till en sekundär plats**: Redundansväxlar datorer från den primära platsen till sekundärt.
2. **Redundansväxla från den sekundära platsen**: replikera virtuella datorer från sekundär till primär och kör en planerad redundans för att återställa efter felet.
3. Efter den planerade redundansväxlingen att du kan också starta replikeras från den primära platsen till sekundärt igen.


## <a name="prerequisites"></a>Förutsättningar

- Kontrollera att du har slutfört en [programåterställningstest](hyper-v-vmm-test-failover.md) att kontrollera att allt fungerar som förväntat.
- Kontrollera att den primära och sekundära VMM-servern är ansluten till Site Recovery för att slutföra återställning efter fel.



## <a name="run-a-failover-from-primary-to-secondary"></a>Kör en växling från primär till sekundär

Du kan köra en vanlig eller planerad redundans för Hyper-V-datorer.

- Använda en vanlig växling vid fel för oväntade avbrott. När du kör den här redundansen, Site Recovery skapar en virtuell dator på den sekundära platsen och aktiverar den upp. Dataförlust kan inträffa beroende på väntar på data som inte har synkroniserats.
- En planerad redundans kan användas för underhåll, eller under förväntade avbrott. Det här alternativet ger ingen dataförlust. När en planerad redundans utlöses, Stäng virtuella källdatorn av. Osynkroniserade data synkroniseras och redundansen utlöses. 
- 
Den här proceduren beskriver hur du kör en vanlig växling vid fel.


1. I **Inställningar** > **Replikerade objekt** klickar du på VM > **+Redundans**.
1. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery för att stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Site Recovery kommer också att försöka synkronisera lokala data som ännu inte har skickats till den sekundära platsen innan du utlöser redundansväxlingen. Observera att redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
2. Du bör nu kunna se den virtuella datorn i sekundär VMM-molnet.
3. När du har kontrollerat den virtuella datorn, **genomför** växling vid fel. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Innan redundansen startas stoppas replikeringen av den virtuella datorn. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.  


## <a name="reverse-replicate-and-failover"></a>Omvänd replikering och redundans

Börja replikera från den sekundära platsen till primärt och växla tillbaka till den primära platsen. När virtuella datorer har körts på den primära platsen igen kan du replikera dem till den sekundära platsen.  

 
1. Klicka på den virtuella datorn > Klicka på **omvänd replikera**.
2. När jobbet har slutförts, klickar du på den virtuella datorn > i **redundans**kontrollerar redundansriktning (från sekundär VMM-moln) och välj käll- och målplatserna. 
4. Starta redundansväxlingen. Du kan följa redundansförloppet på fliken **Jobb**.
5. Kontrollera att den virtuella datorn är tillgänglig i det primära VMM-molnet.
6. Om du vill börja replikera den primära virtuella datorn till den sekundära platsen igen, klickar du på **omvänd replikera**.

## <a name="next-steps"></a>Nästa steg
[Granska steget](hyper-v-vmm-disaster-recovery.md) för att replikera Hyper-V-datorer till en sekundär plats.
