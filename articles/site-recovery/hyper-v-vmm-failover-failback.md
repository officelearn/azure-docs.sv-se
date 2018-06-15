---
title: Växla över och misslyckas tillbaka Hyper-V-datorer som replikeras till ett sekundärt datacenter med Site Recovery | Microsoft Docs
description: Lär dig hur du växla över Hyper-V virtuella datorer till en sekundär lokal plats och växla tillbaka till primär plats med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: ecb0b9395ce7071442ddf0dd976e1ca57b8be906
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161149"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Växla över och misslyckas tillbaka Hyper-V virtuella datorer som replikeras till en sekundär lokal plats

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten hanterar och samordnar replikering, redundans och återställning efter fel för lokala datorer och virtuella Azure-datorer (VM).

Den här artikeln beskriver hur du redundansväxlar en Hyper-V virtuell dator som hanteras i ett moln med System Center Virtual Machine Manager (VMM), till en sekundär plats för VMM. När du har redundansväxlat kan du återställa till den lokala platsen när den är tillgänglig. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Växla över en Hyper-V virtuell dator från en primär VMM-moln till en sekundär VMM-moln
> * Skydda igen från den sekundära platsen till den primära servern och återställas
> * Du kan också starta replikering från primär till sekundär igen

## <a name="failover-and-failback"></a>Redundans och återställning efter fel

Redundans och återställning efter fel har tre steg:

1. **Växla över till sekundär plats**: Redundansväxla datorer från den primära platsen till sekundärt.
2. **Växla tillbaka från den sekundära platsen**: replikera virtuella datorer från sekundär till primär och kör en planerad redundans ska återställas.
3. Efter den planerade redundansväxlingen om du vill starta replikering från den primära platsen för sekundärt igen.


## <a name="prerequisites"></a>Förutsättningar

- Kontrollera att du har slutfört en [disaster recovery ökad](hyper-v-vmm-test-failover.md) och kontrollera att allt fungerar som förväntat.
- Kontrollera att de primära och sekundära VMM-servrarna är anslutna till Site Recovery för att slutföra återställning efter fel.



## <a name="run-a-failover-from-primary-to-secondary"></a>Kör en redundansväxling från primär till sekundär

Du kan köra en vanlig eller planerad redundans för Hyper-V virtuella datorer.

- Använda en vanlig redundans för oväntade avbrott. När du kör den här redundansen Site Recovery skapar en virtuell dator på den sekundära platsen och används upp. Data kan gå förlorade beroende på väntande data som inte har synkroniserats.
- En planerad redundans kan användas för underhåll, eller under förväntade avbrott. Det här alternativet ger noll dataförlust. När en planerad redundans utlöses är virtuella källdatorer avstängda. Osynkroniserade data synkroniseras och växling vid fel utlöses. 
- 
Den här proceduren beskriver hur du kör en vanlig redundans.


1. I **Inställningar** > **Replikerade objekt** klickar du på VM > **+Redundans**.
1. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery att göra en avstängning av virtuella källdatorer innan växling vid fel. Site Recovery försöker synkronisera lokala data som ännu inte har skickats till den sekundära platsen innan växling vid fel. Observera att redundansväxlingen fortsätter även om avstängning misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
2. Nu bör du kunna se den virtuella datorn i sekundära VMM-molnet.
3. När du har kontrollerat VM, **genomför** växling vid fel. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Innan redundansen startas stoppas replikeringen av den virtuella datorn. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.  


## <a name="reverse-replicate-and-failover"></a>Omvänd replikering och redundans

Starta replikering från den sekundära platsen till den primära servern och växla tillbaka till den primära platsen. När virtuella datorer som körs på den primära platsen igen, kan du replikera dem till den sekundära platsen.  

 
1. Klicka på den virtuella datorn > klickar du på **omvänd replikera**.
2. När jobbet har slutförts klickar du på den virtuella datorn > i **redundans**kontrollerar redundansriktning (från sekundär VMM-moln) och välj käll- och målplatserna. 
4. Påbörja redundans. Du kan följa förloppet för växling vid fel på den **jobb** fliken.
5. Kontrollera att den virtuella datorn är tillgänglig i det primära molnet i VMM.
6. Om du vill starta replikera den primära virtuella datorn tillbaka till den sekundära platsen igen klickar du på **omvänd replikera**.

## <a name="next-steps"></a>Nästa steg
[Granska steget](hyper-v-vmm-disaster-recovery.md) för att replikera virtuella Hyper-V-datorer till en sekundär plats.
