---
title: Konfigurera redundans/återställning efter fel på en sekundär Hyper-V-plats med Azure Site Recovery
description: Lär dig hur du växlar över virtuella hyper-virtuella datorer till din sekundära lokala plats och växlar tillbaka till primär plats under haveriberedskap med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082589"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Växla över och växla tillbaka virtuella hyper-v-datorer som replikeras till din sekundära lokala plats

[Azure Site Recovery-tjänsten](site-recovery-overview.md) hanterar och dirigerar replikering, redundans och återställning av lokala datorer och virtuella Azure-datorer (VIRTUELLA datorer).

I den här artikeln beskrivs hur du växlar över en virtuell hyper-V-dator som hanteras i ett VMM-moln (System Center Virtual Machine Manager) till en sekundär VMM-plats. När du har redundansväxlat kan du återställa till den lokala platsen när den är tillgänglig. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Växla över en Virtuell Hyper-V-dator från ett primärt VMM-moln till ett sekundärt VMM-moln
> * Reprotect från den sekundära platsen till den primära, och misslyckas tillbaka
> * Du kan eventuellt börja replikera från primär till sekundär igen

## <a name="failover-and-failback"></a>Redundans och återställning efter fel

Redundans och redundans har tre steg:

1. **Växla över till sekundär plats:** Felställare över från den primära platsen till den sekundära.
2. **Red tillbaka från den sekundära platsen:** Replikera virtuella datorer från sekundär till primär och kör en planerad redundans för att återställas.
3. Efter den planerade redundansen kan du börja replikera från den primära platsen till den sekundära igen.


## <a name="prerequisites"></a>Krav

- Kontrollera att du har slutfört en [borr för haveriberedskap](hyper-v-vmm-test-failover.md) för att kontrollera att allt fungerar som förväntat.
- Om du vill slutföra återställningen av återställningen kontrollerar du att de primära och sekundära VMM-servrarna är anslutna till Platsåterställning.



## <a name="run-a-failover-from-primary-to-secondary"></a>Köra en redundans från primär till sekundär

Du kan köra en vanlig eller planerad redundans för virtuella hyper-virtuella datorer.

- Använd en vanlig redundans för oväntade avbrott. När du kör den här redundansen skapar Site Recovery en virtuell dator på den sekundära platsen och driver upp den. Dataförlust kan uppstå beroende på väntande data som inte har synkroniserats.
- En planerad redundans kan användas för underhåll eller vid förväntat avbrott. Det här alternativet ger noll dataförlust. När en planerad redundans utlöses stängs käll-virtuella datorer av. Osynkroniserade data synkroniseras och redundansen utlöses. 
- 
  Den här proceduren beskriver hur du kör en vanlig redundansväxling.


1. Klicka på den virtuella datorn > **redundans**i **Inställningar** > **replikerade objekt** .
1. Välj **Stäng av datorn innan du påbörjar redundans** om du vill att Site Recovery ska försöka göra en avstängning av virtuella käll-datorer innan du utlöser redundansen. Site Recovery kommer också att försöka synkronisera lokala data som ännu inte har skickats till den sekundära platsen, innan du utlöser redundansen. Observera att redundansen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**.
2. Du bör nu kunna se den virtuella datorn i det sekundära VMM-molnet.
3. När du har verifierat den virtuella datorn **genomför du** redundansen. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Innan redundansen startas så stoppas replikeringen av den virtuella datorn. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.  


## <a name="reverse-replicate-and-failover"></a>Omvänd replikera och redundans

Börja replikera från den sekundära platsen till den primära och växla tillbaka till den primära platsen. När virtuella datorer körs på den primära platsen igen kan du replikera dem till den sekundära platsen.  

 
1. Klicka på den virtuella datorn > klicka på **Omvänd replikera**.
2. När jobbet är klart klickar du på den virtuella datorn >i **redundans**, verifierar redundansriktningen (från sekundärt VMM-moln) och väljer käll- och målplatser. 
4. Starta redundansväxlingen. Du kan följa redundansförloppet på fliken **Jobb**.
5. Kontrollera att den virtuella datorn är tillgänglig i det primära VMM-molnet.
6. Om du vill börja replikera den primära virtuella datorn tillbaka till den sekundära platsen igen klickar du på **Omvänd replikera**.

## <a name="next-steps"></a>Nästa steg
[Granska steget](hyper-v-vmm-disaster-recovery.md) för att replikera virtuella hyper-virtuella datorer till en sekundär plats.
