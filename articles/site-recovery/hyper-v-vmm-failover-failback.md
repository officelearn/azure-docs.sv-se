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
ms.openlocfilehash: f4207b8def3a5cd240b7a3ecdffde34a27f2a833
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
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

- Använda en vanlig redundans för oväntade avbrott. När du kör den här redundansen Site Recovery skapar en virtuell dator på den sekundära platsen och används upp. Du kör redundansväxlingen mot en specifik återställningspunkt. Data kan gå förlorade beroende på den återställningspunkt som du använder.
- En planerad redundans kan användas för underhåll, eller under förväntade avbrott. Det här alternativet ger noll dataförlust. När en planerad redundans utlöses är virtuella källdatorer avstängda. Osynkroniserade data synkroniseras och växling vid fel utlöses. 
- 
Den här proceduren beskriver hur du kör en vanlig redundans.


1. I **Inställningar** > **Replikerade objekt** klickar du på VM > **+Redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
    - **Senaste** (standard): Det här alternativet bearbetar först alla data som skickas till Site Recovery. De ger den lägsta RPO (mål för återställningspunkt) eftersom replikerade virtuella datorn skapas efter växling vid fel har alla data som har replikerats till Site Recovery när redundans utlöstes.
    - **Senaste bearbetade**: Alternativet redundansväxlar den virtuella datorn till den senaste återställningspunkt som bearbetats av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
    - **Senaste appkonsekventa**: Det här alternativet redundansväxlar den virtuella datorn till den senaste appkonsekventa återställningspunkten som bearbetats av Site Recovery. 
3. Krypteringsnyckeln är inte relevant i det här scenariot.
4. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery att göra en avstängning av virtuella källdatorer innan växling vid fel. Site Recovery försöker synkronisera lokala data som ännu inte har skickats till den sekundära platsen innan växling vid fel. Observera att redundansväxlingen fortsätter även om avstängning misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
5. Nu bör du kunna se den virtuella datorn i sekundära VMM-molnet.
6. När du har kontrollerat VM, **genomför** växling vid fel. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Innan redundansen startas stoppas replikeringen av den virtuella datorn. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.  


## <a name="reprotect-and-fail-back"></a>Skydda igen och återställas

Starta replikering från den sekundära platsen till den primära servern och växla tillbaka till den primära platsen. När virtuella datorer som körs på den primära platsen igen, kan du replikera dem till den sekundära platsen igen.  

1. I **inställningar** > **replikerade objekt** klickar du på den virtuella datorn och aktivera **omvänd replikera**. Den virtuella datorn börjar replikeras tillbaka till den primära platsen.
2. Klicka på den virtuella datorn > **planerad redundans**.
3. I **bekräfta planerad redundans**kontrollerar redundansriktning (från sekundär VMM-moln) och välj käll- och målplatserna. 
4. I **datasynkronisering**, ange hur du vill synkronisera:
    - **Synkronisera data innan redundans (synkronisera endast deltaändringar)**– det här alternativet minimerar avbrottstid för virtuell dator eftersom det synkroniserar utan att stänga av den virtuella datorn. Här är syfte:
        - Tar en ögonblicksbild av replikerade virtuella datorn och kopierar den till den primära Hyper-V-värden. Repliken VM körs.
        - Stänger replikerade virtuella datorn, så att inga nya ändringar sker det. Den slutgiltiga uppsättningen deltaändringar överförs till den primära platsen och den virtuella datorn i den primära platsen har startats.
    - **Synkronisera data under växling vid fel endast (fullständig hämtning)**– Använd det här alternativet om du har kört på den sekundära platsen för lång tid. Det här alternativet är snabbare, eftersom vi räknar med flera disk ändringar och inte ägna tid åt kontrollsumma beräkningar. Det här alternativet utför en disk hämtning. Det är också användbart när den primära virtuella datorn har tagits bort.
5. Krypteringsnyckeln är inte relevant i det här scenariot.
6. Påbörja redundans. Du kan följa förloppet för växling vid fel på den **jobb** fliken.
7. Om du har valt för att synkronisera data innan växling vid fel, efter den inledande datasynkroniseringen är klar och du är redo att stänga av replikerade virtuella datorn på den sekundära platsen och klickar på **jobb** > planerad redundans jobbnamn >  **Slutför Redundansväxlingen**. Detta stängs av den sekundära virtuella datorn, överförs de senaste ändringarna till den primära platsen och startar den primära virtuella datorn.
8. Kontrollera att den virtuella datorn är tillgänglig i det primära molnet i VMM.
9. Den primära virtuella datorn är nu i ett bekräfta väntande tillstånd. Klicka på **Commit**, för att genomföra redundans.
10. Om du vill starta replikera den primära virtuella datorn tillbaka till den sekundära platsen igen genom att aktivera **omvänd replikera**.


> [!NOTE]
> Omvänd replikering replikeras endast ändringar som har gjorts sedan repliken VM har varit avstängd och skickas endast deltaändringar.

## <a name="next-steps"></a>Nästa steg
[Granska steget](hyper-v-vmm-disaster-recovery.md) för att replikera virtuella Hyper-V-datorer till en sekundär plats.
