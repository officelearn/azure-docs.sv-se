---
title: Arkitektur för haveri beredskap för Hyper-V till en sekundär lokal plats med Azure Site Recovery
description: Den här artikeln innehåller en översikt över arkitekturen för haveri beredskap för lokala virtuella Hyper-V-datorer till en sekundär System Center-VMM-plats med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 2d8e9c3531e031538c593cfd60d83b4ae97b4f4c
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813745"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Arkitektur – Hyper-V-replikering till en sekundär plats

Den här artikeln beskriver de komponenter och processer som ingår i replikeringen av lokala virtuella Hyper-V-datorer i System Center Virtual Machine Manager-moln (VMM) till en sekundär VMM-plats med tjänsten [Azure Site Recovery](site-recovery-overview.md) på Azure-portalen.


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och grafik ger en övergripande bild av de komponenter som används för Hyper-V-replikering till en sekundär plats.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Azure** | Azure-prenumeration | Du kan skapa ett Recovery Services-valv i Azure-prenumerationen för att dirigera och hantera replikeringen mellan VMM-platser.
**VMM-server** | Du behöver en primär och sekundär VMM-plats. | Vi rekommenderar att det finns en VMM-server på den primära platsen och en på den sekundära platsen.
**Hyper-V-server** |  En eller flera Hyper-V-värdservrar i de primära och sekundära VMM-molnen. | Data replikeras mellan de primära och sekundära Hyper-V-värdservrarna via LAN eller VPN med hjälp av Kerberos eller certifikatautentisering.  
**Virtuella Hyper-V-datorer** | På Hyper-V-värdservern. | Källvärdservern måste ha minst en virtuell dator som du vill replikera.

**Lokal till lokal arkitektur**

![Lokal till lokal](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replikeringsprocessen

1. När den inledande replikeringen utlöses tas en ögonblicks bild av en [ögonblicks bild av virtuell dator i Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) .
2. Virtuella hård diskar på den virtuella datorn replikeras en i taget till den sekundära platsen.
3. Om disk ändringar sker medan den inledande replikeringen pågår, spårar spårningen för Hyper-V-replikering ändringarna som Hyper-V-replikeringsinställningar (. HRL). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad. HRL-fil som skickas till den sekundära platsen. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den inledande replikeringen är klar tas ögonblicks bilden av den virtuella datorn bort och delta-replikering börjar.
5. Diskförändringarna (delta) i loggen synkroniseras och sammanfogas till den överordnade disken.


## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

- Du kan redundansväxla en enskild dator eller skapa återställnings planer för att dirigera redundans för flera datorer.
- Du kan köra en planerad eller oplanerad redundansväxling mellan lokala platser. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
    - Om du utför en oplanerad redundansväxling till en sekundär plats efter att redundansväxlingen på den sekundära platsen inte är skyddade.
    - Om du utför en planerad redundans skyddas datorerna på den sekundära platsen efter redundansen.
- När den inledande redundansväxlingen har körts genomför du den för att komma igång med att komma åt arbets belastningen från den virtuella replik datorn.
- När den primära platsen är tillgänglig igen kan du växla tillbaka.
    - Du initierar omvänd replikering för att starta replikeringen från den sekundära platsen till den primära. Omvänd replikering skyddar de virtuella datorerna, men det sekundära datacentret är fortfarande den aktiva platsen.
    - För att göra den primära platsen till den aktiva platsen igen, initierar du en planerad redundans från sekundär till primär, följt av en till omvänd replikering.



## <a name="next-steps"></a>Nästa steg


Följ [den här självstudien](hyper-v-vmm-disaster-recovery.md) för att aktivera Hyper-V-replikering mellan VMM-moln.
