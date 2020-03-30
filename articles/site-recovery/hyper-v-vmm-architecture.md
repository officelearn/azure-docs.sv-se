---
title: Arkitektur-Hyper-V-haveriberedskap till en sekundär plats med Azure Site Recovery
description: Den här artikeln innehåller en översikt över arkitekturen för haveriberedskap av lokala virtuella hyper-virtuella datorer till en sekundär VMM-plats i System Center med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 3e81e353d2912f56a932ce118a0424e45e758df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133007"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Arkitektur - Hyper-V-replikering till en sekundär plats

Den här artikeln beskriver de komponenter och processer som ingår i replikeringen av lokala virtuella Hyper-V-datorer i System Center Virtual Machine Manager-moln (VMM) till en sekundär VMM-plats med tjänsten [Azure Site Recovery](site-recovery-overview.md) på Azure-portalen.
a

## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och bild ger en vy på hög nivå av de komponenter som används för Hyper-V-replikering till en sekundär plats.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | Azure-prenumeration | Du kan skapa ett Recovery Services-valv i Azure-prenumerationen för att dirigera och hantera replikeringen mellan VMM-platser.
**VMM-server** | Du behöver en primär och sekundär VMM-plats. | Vi rekommenderar att det finns en VMM-server på den primära platsen och en på den sekundära platsen.
**Hyper-V-server** |  En eller flera Hyper-V-värdservrar i de primära och sekundära VMM-molnen. | Data replikeras mellan de primära och sekundära Hyper-V-värdservrarna via LAN eller VPN med hjälp av Kerberos eller certifikatautentisering.  
**Virtuella Hyper-V-datorer** | På Hyper-V-värdservern. | Källvärdservern måste ha minst en virtuell dator som du vill replikera.

**Lokal arkitektur till lokal arkitektur**

![Lokal till lokal](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replikeringsprocessen

1. När den första replikeringen utlöses tas en ögonblicksbild av virtuella datorer med [Hyper-V-virtuell](https://technet.microsoft.com/library/dd560637.aspx) dator.
2. Virtuella hårddiskar på den virtuella datorn replikeras en efter en till den sekundära platsen.
3. Om diskändringar inträffar medan den första replikeringen pågår spårar Hyper-V Replica Replication Tracker ändringarna som Hyper-V-replikeringsloggar (.hrl). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad HRL-fil som skickas till den sekundära platsen. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den första replikeringen är klar tas ögonblicksbilden av den virtuella datorn bort och deltareplikeringen börjar.
5. Diskförändringarna (delta) i loggen synkroniseras och sammanfogas till den överordnade disken.


## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

- Du kan redundansväxla en enskild dator eller skapa återställningsplaner för att samordna redundans för flera datorer.
- Du kan köra en planerad eller oplanerad redundansväxling mellan lokala platser. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
    - Om du utför en oplanerad redundans till en sekundär plats, efter att redundansdatorerna på den sekundära platsen inte är skyddade.
    - Om du utför en planerad redundans skyddas datorerna på den sekundära platsen efter redundansen.
- När den första redundanskörningen har körts, genomför du den, för att börja komma åt arbetsbelastningen från den virtuella repliken.
- När den primära platsen är tillgänglig igen kan du återställa den.
    - Du initierar omvänd replikering för att börja replikera från den sekundära platsen till den primära. Omvänd replikering skyddar de virtuella datorerna, men det sekundära datacentret är fortfarande den aktiva platsen.
    - För att göra den primära platsen till den aktiva platsen igen, initierar du en planerad redundans från sekundär till primär, följt av en till omvänd replikering.



## <a name="next-steps"></a>Nästa steg


Följ [den här självstudien](hyper-v-vmm-disaster-recovery.md) om du vill aktivera Hyper-V-replikering mellan VMM-moln.
